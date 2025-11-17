// Редактирование заявки на подбор (полный скрипт)

// --- Вспомогательные функции ---
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

// --- Загружаем входные form_fields и defaults ---
var aFormFields = ParseJson(getParam("form_fields", "[]"));
var aFormFieldsDef = ParseJson(getParam("form_fields_default", "[]"));

// submit type
var sSubmitType = getFormField("__submit_type__", getFormFieldDefault("__submit_type__", "step_0"));

// --- Опции текущего пользователя и входной object_id (request id) ---
var oOptions = {};
oOptions.id = Request.AuthUserID;
oOptions.name = "Менеджер по подбору";
// object_id может приходить в параметрах (например при открытии через ссылку)
oOptions.object_id = OptInt(getParam("object_id", ""), 0);

// --- Форма (блок полей) ---
var oForm = {};
oForm.command = "display_form";
oForm.height = 320;
oForm.title = 'Редактирование заявки на подбор';
oForm.message = "";
oForm.form_fields = [

    { name: "rules", type: "paragraph",
      value: "<iframe id=\"rule_frame\" src=\"/vtbleasing/2025_recruitment/recruitment_application/rules.html\" style=\"width:100%;border:0;height:1200px\"></iframe>",
      mandatory: false, visibility: false },

    { name: "name", label: "Наименование вакансии", type: "string", value:'', placeholder:'Введите название вакансии',
      validation:"noneempty", error_message:"Заполните наименование вакансии", mandatory: false, visibility: false },

    { name: "subdivision_id",
      label: "<div class=\"vtbl_tooltip\">Структурное подразделение<span class=\"vtbl_tooltip_text\">Выберите департамент, управление, службу, отдел, группу</span></div>",
      type:"foreign_elem", value:'', catalog:"cc_tree_subdivision", mandatory: false, multiple: false, query_qual:"" },

    { name: "subdivision_text", label:"Структурное подразделение (ввести вручную, если не найдено)",
      type:"string", value:'', placeholder:'Введите структурное подразделение', mandatory: false, visibility: false },

    { name: "sheCode", label:"Код ШЕ", type:"string", value:'', placeholder:'Введите код ШЕ',
      validation:"noneempty", error_message:"Заполните код ШЕ. Если вы его не знаете, обратитесь к вашему HR-BP", mandatory: false, visibility: false },

    { name: "function", label:"Функция", type:"select", value:'', validation:"noneempty",
      error_message:"Укажите функцию",
      entries:[{name:"Автолизинг", value:"Автолизинг"},{name:"Корпоративный блок", value:"Корпоративный блок"}],
      mandatory: false, visibility: false },

    { name: "vacancyReason", label:"Причина возникновения вакансии", type:"select", value:'', validation:"noneempty",
      error_message:"Укажите причину возникновения вакансии",
      entries:[
        {name:"Новая штатная единица", value:"Новая штатная единица"},
        {name:"Увольнение сотрудника", value:"Увольнение сотрудника"},
        {name:"Замена неэффективного сотрудника", value:"Замена неэффективного сотрудника"},
        {name:"Внутренний перевод", value:"Внутренний перевод"},
        {name:"Декрет", value:"Декрет"}
      ],
      mandatory: false, visibility: false },

    { name: "workPlace", label:"Место работы", type:"select", value:'', validation:"noneempty",
      error_message:"Укажите место работы",
      entries:[
        {name:"Москва, 2-й Южнопортовый проезд, д.16, стр.1.", value:"Москва, 2-й Южнопортовый проезд, д.16, стр.1."},
        {name:"Москва, Башня на набережной", value:"Москва, Башня на набережной"}
      ],
      mandatory: false, visibility: false },

    { name: "salary", label:"Оклад", type:"real", validation:"number", error_message:"Введите оклад", value:10000, mandatory:false, visibility:false },

    { name: "bonusSystem", label:"Система премирования", type:"select", value:'', validation:"noneempty",
      error_message:"Укажите систему премирования",
      entries:[{name:"Ежемесячная", value:"Ежемесячная"},{name:"Годовая", value:"Годовая"},{name:"Смешанная", value:"Смешанная"}],
      mandatory:false, visibility:false },

    { name: "gasCost", label:"<div class=\"vtbl_tooltip\">ГСМ<span class=\"vtbl_tooltip_text\">Заполните, если должность разъездная (укажите сумму компенсации)</span></div>",
      type:"text", richtext:true, value:'', placeholder:'Введите как оплачиваются ГСМ', mandatory:false, visibility:false },

    { name: "hiringPurpose", label:"<div class=\"vtbl_tooltip\">Обоснование потребности найма<span class=\"vtbl_tooltip_text\">Кратко опишите необходимость в найме этого специалиста</span></div>",
      type:"text", richtext:true, value:'', page:"page_two", mandatory:false, visibility:false },

    { name: "jobSharing", label:"<div class=\"vtbl_tooltip\">Совмещение<span class=\"vtbl_tooltip_text\">Если сотрудник по совместительству</span></div>",
      type:"text", richtext:true, value:'', page:"page_two", mandatory:false, visibility:false },

    { name: "duties", label:"<div class=\"vtbl_tooltip\">Основные обязанности<span class=\"vtbl_tooltip_text\">Опишите задачи и ответственность</span></div>",
      type:"text", richtext:true, value:'', page:"page_two", mandatory:false, visibility:false },

    { name: "requirements", label:"<div class=\"vtbl_tooltip\">Основные требования<span class=\"vtbl_tooltip_text\">Образование, опыт, сертификаты, навыки</span></div>",
      type:"text", richtext:true, value:'', page:"page_two", mandatory:false, visibility:false }
];

// --- Утилита: гарантируем наличия скрытого поля request_id в форме и устанавливаем его текущее значение ---
function ensureRequestIdField(sValue) {
    var f = ArrayOptFind(oForm.form_fields, "This.name == 'request_id'");
    if (f == undefined) {
        oForm.form_fields.push({ name: "request_id", type: "hidden", value: sValue });
    } else {
        f.value = sValue;
    }
}

// --- Определяем initial request id: сначала из object_id параметра, затем из входных form_fields ---
var initialReqID = OptInt(oOptions.object_id, 0);

// Если object_id не передан, пробуем получить из входных form_fields (например, когда форма уже была отправлена)
if (initialReqID == 0) {
    // getFormField использует aFormFields, оно уже есть — безопасно
    initialReqID = OptInt(getFormField("request_id", getFormFieldDefault("request_id", "")), 0);
}

// Если initialReqID > 0 — откроем документ и подставим значения в поля
if (initialReqID > 0) {
    try {
        var oReqDoc_for_fill = tools.open_doc(initialReqID);
        var oReqDocTE_for_fill = oReqDoc_for_fill.TopElem;

        // заполняем видимые поля значениями из документа
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var ff = oForm.form_fields[i];
            switch (ff.name) {
                case "name":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_name").value;
                    break;
                case "subdivision_id":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value;
                    break;
                case "subdivision_text":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value;
                    break;
                case "sheCode":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_cod_he").value;
                    break;
                case "function":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_function").value;
                    break;
                case "vacancyReason":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_cause").value;
                    break;
                case "workPlace":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_place").value;
                    break;
                case "salary":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_salary").value;
                    break;
                case "bonusSystem":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value;
                    break;
                case "gasCost":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value;
                    break;
                case "jobSharing":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value;
                    break;
                case "hiringPurpose":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value;
                    break;
                case "duties":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_candidate_function").value;
                    break;
                case "requirements":
                    ff.value = oReqDocTE_for_fill.custom_elems.ObtainChildByKey("f_candidate_quality").value;
                    break;
                default:
                    // ничего
                    break;
            }
        }
    } catch (_exFill) {
        // если не удалось открыть документ — оставляем поля пустыми, но не ломаем страницу
    }
}

// гарантируем скрытое поле request_id с корректным значением
ensureRequestIdField(initialReqID != 0 ? String(initialReqID) : "");

// --- Кнопки и логика шагов ---
oForm.buttons = [];
oForm.no_buttons = false;

switch (sSubmitType) {

    case "step_0": {
        var aMandatoryFields = [];
        var aVisibleFields = ["rules"];
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "hidden" : oField.type);
            try { oField.validation = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "" : oField.validation); } catch (_e) { }
            oField.mandatory = (ArrayOptFind(aMandatoryFields, "This==oField.name") != undefined);
            oField.value = getFormField(oField.name, oField.value);
        }
        // обновляем скрытое поле request_id на случай, если пришло новое значение в form_fields
        ensureRequestIdField(getFormField("request_id", initialReqID != 0 ? String(initialReqID) : ""));
        oForm.buttons.push({ name: "submit", submit_type: "main_data", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 1 Инструкция по заполнению";
        break;
    }

    case "main_data": {
        var aMandatoryFields = ["name", "sheCode", "function", "vacancyReason", "workPlace"];
        var aVisibleFields = ["name", "subdivision_id", "subdivision_text", "sheCode", "function", "vacancyReason", "workPlace"];
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "hidden" : oField.type);
            try { oField.validation = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "" : oField.validation); } catch (_e) { }
            oField.mandatory = (ArrayOptFind(aMandatoryFields, "This==oField.name") != undefined);
            oField.value = getFormField(oField.name, oField.value);
        }
        ensureRequestIdField(getFormField("request_id", initialReqID != 0 ? String(initialReqID) : ""));
        oForm.buttons.push({ name: "submit", submit_type: "salary", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 2 Основные параметры вакансии";
        break;
    }

    case "salary": {
        var aMandatoryFields = ["salary", "bonusSystem"];
        var aVisibleFields = ["salary", "bonusSystem", "gasCost", "jobSharing"];
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "hidden" : oField.type);
            try { oField.validation = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "" : oField.validation); } catch (_e) { }
            oField.mandatory = (ArrayOptFind(aMandatoryFields, "This==oField.name") != undefined);
            oField.value = getFormField(oField.name, oField.value);
        }
        ensureRequestIdField(getFormField("request_id", initialReqID != 0 ? String(initialReqID) : ""));
        oForm.buttons.push({ name: "submit", submit_type: "main_data", label: "Назад", type: "submit" }, { name: "submit", submit_type: "kupor", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 3 Система оплаты труда и бонусы";
        break;
    }

    case "kupor": {
        var aMandatoryFields = ["hiringPurpose"];
        var aVisibleFields = ["hiringPurpose"];
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "hidden" : oField.type);
            try { oField.validation = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "" : oField.validation); } catch (_e) { }
            oField.mandatory = (ArrayOptFind(aMandatoryFields, "This==oField.name") != undefined);
            oField.value = getFormField(oField.name, oField.value);
        }
        ensureRequestIdField(getFormField("request_id", initialReqID != 0 ? String(initialReqID) : ""));
        oForm.buttons.push({ name: "submit", submit_type: "salary", label: "Назад", type: "submit" }, { name: "submit", submit_type: "another", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 4 Обоснование вакансии для КУПОР";
        break;
    }

    case "another": {
        var aMandatoryFields = [];
        var aVisibleFields = ["duties", "requirements"];
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "hidden" : oField.type);
            try { oField.validation = (ArrayOptFind(aVisibleFields, "This==oField.name") == undefined ? "" : oField.validation); } catch (_e) { }
            oField.mandatory = (ArrayOptFind(aMandatoryFields, "This==oField.name") != undefined);
            oField.value = getFormField(oField.name, oField.value);
        }
        ensureRequestIdField(getFormField("request_id", initialReqID != 0 ? String(initialReqID) : ""));
        oForm.buttons.push({ name: "submit", submit_type: "kupor", label: "Назад", type: "submit" }, { name: "submit", submit_type: "save", label: "Сохранить заявку", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 5 Основные обязанности и требования";
        break;
    }

    case "save": {
        // получаем request_id из текущей отправленной формы (если пользователь редактировал)
        var sReqID = getFormField("request_id", "");
        var oReqDoc;
        // если есть id — открываем существующую заявку и перезаписываем
        if (sReqID != "" && OptInt(sReqID, 0) > 0) {
            try {
                oReqDoc = tools.open_doc(OptInt(sReqID, 0));
            } catch (_eOpen) {
                // если открыть не удалось — создаём новую (защита)
                oReqDoc = tools.new_doc_by_name("request");
                oReqDoc.BindToDb();
            }
        } else {
            // нет id — создаём новую заявку
            oReqDoc = tools.new_doc_by_name("request");
            oReqDoc.BindToDb();
            // запомним новый id в скрытом поле формы (на будущее)
            ensureRequestIdField(oReqDoc.DocID);
        }

        var oReqDocTE = oReqDoc.TopElem;

        // привязка к текущему пользователю
        oReqDocTE.person_id = oOptions.id;

        // общей заполнение (request_type, collaborator)
        tools.common_filling('request_type', oReqDocTE, 7194701844403546882);
        tools.common_filling('collaborator', oReqDocTE, oReqDocTE.person_id);

        // (не меняем состояние workflow здесь — оставлено как в оригинале, при необходимости можно раскомментировать)
        // oReqDocTE.is_group = 0;
        // oReqDocTE.status_in_knowledge_map = "active";
        // oReqDocTE.workflow_state = "stage_manager_accept";
        // oReqDocTE.workflow_state_name = "Утверждение руководителем";
        // oReqDocTE.is_workflow_init = 1;

        // --- Заполняем поля заявки из пришедших значений формы ---
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value = getFormField("subdivision_id", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value = getFormField("subdivision_text", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_function").value = getFormField("function", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value = getFormField("vacancyReason", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_place").value = getFormField("workPlace", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_name").value = getFormField("name", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value = getFormField("salary", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value = getFormField("bonusSystem", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value = getFormField("gasCost", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value = getFormField("jobSharing", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value = getFormField("hiringPurpose", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_function").value = getFormField("duties", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_quality").value = getFormField("requirements", "");
        oReqDocTE.custom_elems.ObtainChildByKey("f_cod_he").value = getFormField("sheCode", "");

        // сохраняем
        oReqDoc.Save();

        // редиректим на просмотр/редактирование сохранённого документа
        oForm = {
            command: "alert",
            msg: "Заявка сохранена",
            confirm_result: {
                command: "redirect",
                url: "/view_doc.html?mode=vtbl_recruitment_request&object_id=" + oReqDoc.DocID
            }
        };
        break;
    }

    default:
        break;
}

// финал
RESULT = oForm;
