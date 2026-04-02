// ---------------------------------------------------------------------
// 1. Вспомогательные функции (оставлены как у вас)
// ---------------------------------------------------------------------
function getParam(sName, sDefault) {
    var sValue = PARAMETERS.GetOptProperty(sName);
    if (sDefault != undefined && (sValue == undefined || sValue == "")) {
        sValue = sDefault;
    }
    return sValue;
}

function getFormField(sName, sDefault) {
    var sValue = ArrayOptFind(aFormFields, ("This.name == " + XQueryLiteral(sName)));
    sValue = (sValue != undefined ? sValue.value : sValue);
    if (sDefault != undefined && (sValue == undefined || sValue == "")) {
        sValue = sDefault;
    }
    return sValue;
}

function getFormFieldDefault(sName, sDefault) {
    var sValue = ArrayOptFind(aFormFieldsDef, ("This.name == " + XQueryLiteral(sName)));
    sValue = (sValue != undefined ? sValue.value : sValue);
    if (sDefault != undefined && (sValue == undefined || sValue == "")) {
        sValue = sDefault;
    }
    return sValue;
}

// ---------------------------------------------------------------------
// 2. Функция создания отчёта (полная реализация)
// ---------------------------------------------------------------------
function createReportString(aCollaboratorIDArray, aAssessmentIDArray, dStartDate, dFinishDate) {
    // --- Защита от пустых массивов ---
    if (!aCollaboratorIDArray.length || !aAssessmentIDArray.length) {
        return "<html><body>Не выбраны сотрудники или тесты</body></html>";
    }

    // Преобразуем ID в строку для XQuery (числа через запятую)
    var personIds = aCollaboratorIDArray.join(",");
    var assessmentIds = aAssessmentIDArray.join(",");

    // --- Структуры для сбора данных ---
    var questions = [];      // { id, text }
    var learnings = [];      // { person_fullname, person_code, person_org, person_subdivision,
                             //   person_position, start_usage_date, state_name, score, max_score,
                             //   answers: { id_вопроса: { quest_type, result, answer, correct_answer } } }

    // --- Запрос прохождений (только test_learnings, как в вашей задаче) ---
    var xquery = 
        "for $l in test_learnings " +
        "where $l/person_id in (" + personIds + ") " +
        "and $l/assessment_id in (" + assessmentIds + ") " +
        "and $l/start_usage_date >= date('" + StrDate(dStartDate, false) + "') " +
        "and $l/start_usage_date <= date('" + StrDate(dFinishDate, false) + "') " +
        "return $l";
    var arr = XQuery(xquery);

    // --- Обработка каждого прохождения ---
    for (var i = 0; i < arr.length; i++) {
        var l = arr[i];
        var learningDoc = OpenDoc(UrlFromDocID(l.id)).TopElem;
        var fldAnnals = tools.annals_decrypt(learningDoc);
        if (!fldAnnals) continue;

        var history = fldAnnals.au.history;
        if (!ArrayCount(history.objects)) continue;

        // Базовая информация о попытке
        var obj = {
            person_fullname: l.person_id.ForeignElem.fullname,
            person_code: l.person_id.ForeignElem.code,
            person_org: l.person_id.ForeignElem.org_name,
            person_subdivision: l.person_id.ForeignElem.subdivision_name,
            person_position: l.person_id.ForeignElem.position_name,
            start_usage_date: l.start_usage_date,
            state_name: l.state_id.ForeignElem.name,
            score: l.score,
            max_score: l.max_score,
            answers: {}
        };

        var sections = history.objects[0].section;
        for (var s = 0; s < sections.length; s++) {
            var section = sections[s];
            for (var q = 0; q < section.question.length; q++) {
                var question = section.question[q];
                var qId = question.id;

                // --- Собираем уникальные вопросы (заголовки таблицы) ---
                if (ArrayOptFind(questions, "This.id == " + qId) === undefined) {
                    questions.push({
                        id: qId,
                        text: HtmlToPlainText(question.text)
                    });
                }

                // --- Результат (Верно / Неверно) ---
                var isCorrect = tools_web.is_correct_question(question);
                var resultText = isCorrect ? "Верно" : "Неверно";

                // --- Тип вопроса ---
                var questType = question.qtype.OptForeignElem 
                                ? question.qtype.ForeignElem.name 
                                : question.qtype;

                // --- Правильный ответ и ответ пользователя (логика из old_report) ---
                var correctAnswer = "", userAnswer = "";
                switch (question.qtype) {
                    case "choice":
                    case "select":
                        correctAnswer = ArrayMerge(ArraySelect(question.variant, "correct == '1'"), "HtmlToPlainText(text)", "; ");
                        userAnswer = ArrayMerge(ArraySelect(question.variant, "Trim(value) == '1'"), "HtmlToPlainText(text)", "; ");
                        break;
                    case "range":
                        userAnswer = ArrayMerge(ArraySelect(question.variant, "Trim(value) != ''"), "HtmlToPlainText(value)", "; ");
                        correctAnswer = ArrayMerge(question.variant, "HtmlToPlainText(cor_value)", "; ");
                        break;
                    default:
                        correctAnswer = ArrayMerge(question.variant, "HtmlToPlainText(cor_value)", "; ");
                        userAnswer = ArrayMerge(question.variant, "HtmlToPlainText(value)", "; ");
                }

                obj.answers[qId] = {
                    quest_type: questType,
                    result: resultText,
                    answer: userAnswer,
                    correct_answer: correctAnswer
                };
            }
        }
        learnings.push(obj);
    }

    if (learnings.length === 0) {
        return "<html><body>Нет прохождений за выбранный период</body></html>";
    }

    // -----------------------------------------------------------------
    // 3. Генерация HTML-таблицы в точности как в шаблоне (с ширинами)
    // -----------------------------------------------------------------
    var reportStr = new Binary();
    reportStr.AppendStr(
        "<html xmlns:o='urn:schemas-microsoft-com:office:office' xmlns:x='urn:schemas-microsoft-com:office:excel' xmlns='http://www.w3.org/TR/REC-html40'>" +
        "<head><meta http-equiv='Content-Type' content='text/html; charset=utf-8'/>" +
        "<meta name='ProgId' content='Excel.Sheet'/><meta name='Generator' content='Microsoft Excel 11'/>" +
        "<style>td { mso-number-format: \"\\@\"; }</style></head><body>" +
        "<table border='1' cellpadding='2' cellspacing='0'>"
    );

    // --- Строка 1: объединённые заголовки вопросов (каждый вопрос на 4 колонки) ---
    reportStr.AppendStr("<tr>");
    reportStr.AppendStr("<td colspan='8'>&nbsp;</td>");  // 8 основных колонок
    for (var qi = 0; qi < questions.length; qi++) {
        reportStr.AppendStr("<td align='center' colspan='4' bgcolor='#FF4433'><b>" + questions[qi].text + "</b></td>");
    }
    reportStr.AppendStr("</tr>");

    // --- Строка 2: подзаголовки колонок ---
    reportStr.AppendStr("<tr>");
    var mainHeaders = ["ФИО", "Код", "Организация", "Подразделение", "Должность", "Дата", "Статус", "Баллы"];
    for (var h = 0; h < mainHeaders.length; h++) {
        reportStr.AppendStr("<td align='center' bgcolor='#FFCC99'><b>" + mainHeaders[h] + "</b></td>");
    }
    for (var qi = 0; qi < questions.length; qi++) {
        reportStr.AppendStr("<td align='center' bgcolor='#FFCC99'><b>Тип</b></td>");
        reportStr.AppendStr("<td align='center' bgcolor='#FFCC99'><b>Результат</b></td>");
        reportStr.AppendStr("<td align='center' bgcolor='#FFCC99'><b>Полученный ответ</b></td>");
        reportStr.AppendStr("<td align='center' bgcolor='#FFCC99'><b>Правильный ответ</b></td>");
    }
    reportStr.AppendStr("</tr>");

    // --- Строки с данными ---
    for (var li = 0; li < learnings.length; li++) {
        var l = learnings[li];
        var percent = (l.max_score > 0) ? ((l.score / l.max_score) * 100).toFixed(1) : "0";
        var scoreDisplay = l.score + (l.max_score ? " (" + percent + "%)" : "");

        reportStr.AppendStr("<tr valign='top'>");
        reportStr.AppendStr("<td width='300'>" + l.person_fullname + "</td>");
        reportStr.AppendStr("<td width='300'>" + l.person_code + "</td>");
        reportStr.AppendStr("<td width='300'>" + l.person_org + "</td>");
        reportStr.AppendStr("<td width='300'>" + l.person_subdivision + "</td>");
        reportStr.AppendStr("<td width='300'>" + l.person_position + "</td>");
        reportStr.AppendStr("<td width='110' align='center'>" + StrDate(l.start_usage_date, true, false) + "</td>");
        reportStr.AppendStr("<td width='90' align='center'>" + l.state_name + "</td>");
        reportStr.AppendStr("<td width='50' align='center'>" + scoreDisplay + "</td>");

        for (var qi = 0; qi < questions.length; qi++) {
            var qid = questions[qi].id;
            var ans = l.answers[qid];
            if (!ans) {
                reportStr.AppendStr("<td width='200'></td><td width='80'></td><td width='300'></td><td width='300'></td>");
            } else {
                var bgcolor = (ans.result == "Неверно") ? "#FFCCCC" : (ans.result == "Верно" ? "#CCFFCC" : "");
                reportStr.AppendStr("<td width='200' align='center'>" + ans.quest_type + "</td>");
                reportStr.AppendStr("<td width='80' align='center' bgcolor='" + bgcolor + "'>" + ans.result + "</td>");
                reportStr.AppendStr("<td width='300' align='center'>" + ans.answer + "</td>");
                // Убираем ведущий "=" если есть (как в оригинальном шаблоне)
                var correct = ans.correct_answer;
                if (StrBegins(correct, "=")) correct = StrRightRangePos(correct, 1);
                reportStr.AppendStr("<td width='300' align='center'>" + correct + "</td>");
            }
        }
        reportStr.AppendStr("</tr>");
    }

    reportStr.AppendStr("追赶</body></html>");
    return reportStr.GetStr();
}

// ---------------------------------------------------------------------
// 4. Загрузка данных формы и логика шагов
// ---------------------------------------------------------------------
aFormFields = ParseJson(getParam("form_fields", "[]"));
aFormFieldsDef = ParseJson(getParam("form_fields_default", "[]"));
sSubmitType = getFormField("__submit_type__", getFormFieldDefault("__submit_type__", "main_data"));

oForm = new Object();
oForm.command = "display_form";
oForm.height = 320;
oForm.title = 'Выгрузка отчета по обучению';
oForm.message = 'Здесь вы можете выгрузить отчет по результатам прохождения теста сотрудником';
oForm.form_fields = [
    {
        name: "select_collaborator_ids",
        label: "Выберите сотрудников, по которыми хотите выгрузить отчет",
        title: "Выберите сотрудников",
        type: "foreign_elem",
        value: "",
        mandatory: false,
        multiple: false,
        catalog: "collaborator"
    },
    {
        name: "select_assessment_ids",
        title: "Выберите тесты",
        label: "Выберите тесты, по которыми хотите выгрузить отчет",
        type: "foreign_elem",
        value: "",
        mandatory: false,
        multiple: true,
        catalog: "assessment"
    },
    {
        name: "startDate",
        label: "Выгрузить прохождения ОТ",
        type: "date",
        value: StrDate(Date('01.01.1970'), true, false),
        mandatory: true,
        multiple: false
    },
    {
        name: "finalDate",
        label: "Выгрузить прохождения ДО",
        type: "date",
        value: StrDate(Date(), true, false),
        mandatory: true,
        multiple: false
    }
];

oForm.buttons = [];
oForm.no_buttons = false;

switch (sSubmitType) {
    case "main_data": {
        var aMandatoryFields = ["select_collaborator_ids", "select_assessment_ids"];
        var aVisibleFields = ["select_collaborator_ids", "select_assessment_ids", "startDate", "finalDate"];
        for (var idx = 0; idx < oForm.form_fields.length; idx++) {
            var oField = oForm.form_fields[idx];
            oField.type = (ArrayOptFind(aVisibleFields, "This == oField.name") === undefined) ? "hidden" : oField.type;
            try { oField.validation = (ArrayOptFind(aVisibleFields, "This == oField.name") === undefined) ? "" : oField.validation; } catch(e) {}
            oField.mandatory = (ArrayOptFind(aMandatoryFields, "This == oField.name") !== undefined);
            oField.value = getFormField(oField.name, oField.value);
        }
        oForm.buttons.push({name:"submit", submit_type:"create_report", label:"Создать отчет", type:"submit"});
        oForm.buttons.push({name:"cancel", label:"Отменить", type:"cancel"});
        RESULT = oForm;
        break;
    }
    case "create_report": {
        // -----------------------------------------------------------------
        // ★★★ ИСПРАВЛЕННЫЙ БЛОК – БЕЗ ОШИБКИ .value ★★★
        // -----------------------------------------------------------------
        function getFieldValueAsArray(fieldName) {
            var raw = getFormField(fieldName, "");
            if (!raw) return [];
            // Если raw – объект (например, {value:"id", text:"name"})
            if (typeof raw === "object" && raw.value !== undefined) raw = raw.value;
            // Приводим к строке
            var str = String(raw);
            // Если строка содержит ';' – разбиваем, иначе возвращаем массив из одного элемента
            return str.indexOf(";") >= 0 ? str.split(";") : (str ? [str] : []);
        }

        var oSelect_collaborator_ids = getFieldValueAsArray("select_collaborator_ids");
        var oSelect_assessment_ids   = getFieldValueAsArray("select_assessment_ids");

        var startDateRaw = getFormField("startDate", "01.01.1970");
        var finalDateRaw = getFormField("finalDate", StrDate(Date(), false));

        var sReportStr = createReportString(
            oSelect_collaborator_ids,
            oSelect_assessment_ids,
            Date(startDateRaw),
            Date(finalDateRaw)
        );

        tools_web.set_user_data("excel_html_" + curUserID, { "html": sReportStr }, 3600);
        oForm = {
            command: "new_window",
            url: "assessment_excel_export.html"
        };
        RESULT = oForm;
        break;
    }
    default: {
        oForm = {
            command: "alert",
            msg: "Нет выбранного действия",
            view: "warning",
            title: "Ошибки заполнения"
        };
        RESULT = oForm;
        break;
    }
}
