function createReportString(aCollaboratorIDArray, aAssessmentIDArray, dStartDate, dFinishDate) {
    // Защита от пустых массивов
    if (!aCollaboratorIDArray.length || !aAssessmentIDArray.length) {
        return "<html><body>Не выбраны сотрудники или тесты</body></html>";
    }

    // Превращаем ID в строки для XQuery (в кавычках, т.к. это GUID)
    var personIds = aCollaboratorIDArray.map(function(id) { return "'" + id + "'"; }).join(",");
    var assessmentIds = aAssessmentIDArray.map(function(id) { return "'" + id + "'"; }).join(",");

    var questions = [];   // { id, text }
    var learnings = [];   // массив объектов с полями и answers

    // ОДИН XQuery-запрос (без вложенных циклов)
    var xquery = 
        "for $l in test_learnings " +
        "where $l/person_id in (" + personIds + ") " +
        "and $l/assessment_id in (" + assessmentIds + ") " +
        "and $l/start_usage_date >= date('" + StrDate(dStartDate, false) + "') " +
        "and $l/start_usage_date <= date('" + StrDate(dFinishDate, false) + "') " +
        "return $l";
    var arr = XQuery(xquery);

    // Обрабатываем каждое прохождение
    for (var i = 0; i < arr.length; i++) {
        var l = arr[i];
        var learningDoc = OpenDoc(UrlFromDocID(l.id)).TopElem;
        var fldAnnals = tools.annals_decrypt(learningDoc);
        if (!fldAnnals) continue;

        var history = fldAnnals.au.history;
        if (!ArrayCount(history.objects)) continue;

        // Базовая информация о попытке (все поля как в эталоне)
        var obj = {
            person_fullname: l.person_id.ForeignElem.fullname,
            test_name: l.assessment_id.ForeignElem.name,
            person_code: l.person_id.ForeignElem.code,
            org_name: l.person_id.ForeignElem.org_name,
            subdivision_name: l.person_id.ForeignElem.subdivision_name,
            position_name: l.person_id.ForeignElem.position_name,
            start_date: l.start_usage_date,
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

                // Уникальные вопросы (для заголовков)
                var found = false;
                for (var qi = 0; qi < questions.length; qi++) {
                    if (questions[qi].id == qId) { found = true; break; }
                }
                if (!found) {
                    questions.push({
                        id: qId,
                        text: HtmlToPlainText(question.text)
                    });
                }

                // Результат
                var isCorrect = tools_web.is_correct_question(question);
                var resultText = isCorrect ? "Верно" : "Неверно";

                // Тип вопроса
                var questType = (question.qtype.OptForeignElem != undefined) 
                                ? question.qtype.ForeignElem.name 
                                : question.qtype;

                // Правильный ответ и ответ пользователя
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

    // --- ГЕНЕРАЦИЯ HTML (в точности как в эталоне) ---
    var reportStr = new Binary();
    reportStr.AppendStr(
        "<html xmlns:o='urn:schemas-microsoft-com:office:office' xmlns:x='urn:schemas-microsoft-com:office:excel' xmlns='http://www.w3.org/TR/REC-html40'>" +
        "<head><meta http-equiv='Content-Type' content='text/html; charset=utf-8'/>" +
        "<meta name='ProgId' content='Excel.Sheet'/><meta name='Generator' content='Microsoft Excel 11'/>" +
        "<style>td { mso-number-format: \"\\@\"; }</style></head><body>" +
        "<table border='1' cellpadding='2' cellspacing='0'>"
    );

    // Строка 1: объединённые заголовки вопросов (каждый вопрос на 4 колонки)
    reportStr.AppendStr("<tr>");
    reportStr.AppendStr("<td colspan='9'>&nbsp;</td>"); // 9 основных колонок
    for (var qi = 0; qi < questions.length; qi++) {
        reportStr.AppendStr("<td align='center' colspan='4' bgcolor='#FF4433'><b>" + questions[qi].text + "</b></td>");
    }
    reportStr.AppendStr("</tr>");

    // Строка 2: подзаголовки колонок
    reportStr.AppendStr("<tr>");
    var mainHeaders = ["Пользователь", "Название теста", "Код", "Организация", "Подразделение", "Должность", "Дата", "Статус", "Баллы"];
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

    // Строки с данными
    for (var li = 0; li < learnings.length; li++) {
        var l = learnings[li];
        var percent = (l.max_score > 0) ? StrReal((l.score / l.max_score) * 100, 1) : "0";
        var scoreDisplay = l.score + " (" + percent + "%)"; // как в эталоне: "23 (92%)"

        reportStr.AppendStr("<tr valign='top'>");
        reportStr.AppendStr("<td width='250'>" + l.person_fullname + "</td>");
        reportStr.AppendStr("<td width='200'>" + l.test_name + "</td>");
        reportStr.AppendStr("<td width='100'>" + l.person_code + "</td>");
        reportStr.AppendStr("<td width='200'>" + l.org_name + "</td>");
        reportStr.AppendStr("<td width='200'>" + l.subdivision_name + "</td>");
        reportStr.AppendStr("<td width='200'>" + l.position_name + "</td>");
        reportStr.AppendStr("<td width='100' align='center'>" + StrDate(l.start_date, true, false) + "</td>");
        reportStr.AppendStr("<td width='100' align='center'>" + l.state_name + "</td>");
        reportStr.AppendStr("<td width='100' align='center'>" + scoreDisplay + "</td>");

        for (var qi = 0; qi < questions.length; qi++) {
            var qid = questions[qi].id;
            var ans = l.answers[qid];
            if (!ans) {
                reportStr.AppendStr("<td width='150'></td><td width='80'></td><td width='250'></td><td width='250'></td>");
            } else {
                var color = (ans.result == "Неверно") ? "#FFCCCC" : "#CCFFCC";
                reportStr.AppendStr("<td width='150' align='center'>" + ans.quest_type + "</td>");
                reportStr.AppendStr("<td width='80' align='center' bgcolor='" + color + "'>" + ans.result + "</td>");
                reportStr.AppendStr("<td width='250'>" + ans.answer + "</td>");
                // Убираем ведущий '=', если есть (как в оригинале)
                var correct = ans.correct_answer;
                if (StrBegins(correct, "=")) correct = StrRightRangePos(correct, 1);
                reportStr.AppendStr("<td width='250'>" + correct + "</td>");
            }
        }
        reportStr.AppendStr("</tr>");
    }

    reportStr.AppendStr("</body></html>");
    return reportStr.GetStr();
}


      case "create_report": {
    function getFieldValueAsArray(fieldName) {
        var raw = getFormField(fieldName, "");
        if (!raw) return [];
        // Если raw – объект (например, {value:"GUID", text:"..."})
        if (typeof raw === "object" && raw.value !== undefined) raw = raw.value;
        var str = String(raw);
        // Если строка содержит ';' – разбиваем, иначе массив из одного элемента
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
    break;
}
