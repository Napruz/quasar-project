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

// --- Загружаем данные формы ---
aFormFields = ParseJson(getParam("form_fields", "[]"));
aFormFieldsDef = ParseJson(getParam("form_fields_default", "[]"));
sSubmitType = getFormField("__submit_type__", getFormFieldDefault("__submit_type__", "step_0"));

// --- Опции текущего пользователя ---
var oOptions = {};
oOptions.id = Request.AuthUserID;
oOptions.name = "Менеджер по подбору";

// --- Форма ---
oForm = {};
oForm.command = "display_form";
oForm.height = 320;
oForm.title = 'Редактирование заявки на подбор';
oForm.message = "";
oForm.form_fields = [
    { name: "rules", type: "paragraph", value: "<iframe id=\"rule_frame\" src=\"/vtbleasing/2025_recruitment/recruitment_application/rules.html\" style=\"width:100%;border:0;height:1200px\"></iframe>", mandatory: false, visibility: false },
    { name: "name", label: "Наименование вакансии", type: "string", value:'', placeholder:'Введите название вакансии', validation:"noneempty", error_message:"Заполните наименование вакансии", mandatory: false, visibility: false },
    { name: "subdivision_id", label: "<div class=\"vtbl_tooltip\">Структурное подразделение<span class=\"vtbl_tooltip_text\">Выберите департамент, управление, службу, отдел, группу</span></div>", type:"foreign_elem", value:'', catalog:"cc_tree_subdivision", mandatory: false, multiple: false, query_qual:"" },
    { name: "subdivision_text", label:"Структурное подразделение (ввести вручную, если не найдено)", type:"string", value:'', placeholder:'Введите структурное подразделение', mandatory: false, visibility: false },
    { name: "sheCode", label:"Код ШЕ", type:"string", value:'', placeholder:'Введите код ШЕ', validation:"noneempty", error_message:"Заполните код ШЕ. Если вы его не знаете, обратитесь к вашему HR-BP", mandatory: false, visibility: false },
    { name: "function", label:"Функция", type:"select", value:'', validation:"noneempty", error_message:"Укажите функцию", entries:[{name:"Автолизинг", value:"Автолизинг"},{name:"Корпоративный блок", value:"Корпоративный блок"}], mandatory: false, visibility: false },
    { name: "vacancyReason", label:"Причина возникновения вакансии", type:"select", value:'', validation:"noneempty", error_message:"Укажите причину возникновения вакансии", entries:[{name:"Новая штатная единица", value:"Новая штатная единица"},{name:"Увольнение сотрудника", value:"Увольнение сотрудника"},{name:"Замена неэффективного сотрудника", value:"Замена неэффективного сотрудника"},{name:"Внутренний перевод", value:"Внутренний перевод"},{name:"Декрет", value:"Декрет"}], mandatory: false, visibility: false },
    { name: "workPlace", label:"Место работы", type:"select", value:'', validation:"noneempty", error_message:"Укажите место работы", entries:[{name:"Москва, 2-й Южнопортовый проезд, д.16, стр.1.", value:"Москва, 2-й Южнопортовый проезд, д.16, стр.1."},{name:"Москва, Башня на набережной", value:"Москва, Башня на набережной"}], mandatory: false, visibility: false },
    { name: "salary", label:"Оклад", type:"real", validation:"number", error_message:"Введите оклад", value: 10000, mandatory: false, visibility: false },
    { name: "bonusSystem", label:"Система премирования", type:"select", value:'', validation:"noneempty", error_message:"Укажите систему премирования", entries:[{name:"Ежемесячная", value:"Ежемесячная"},{name:"Годовая", value:"Годовая"},{name:"Смешанная", value:"Смешанная"}], mandatory: false, visibility: false },
    { name: "gasCost", label:"<div class=\"vtbl_tooltip\">ГСМ<span class=\"vtbl_tooltip_text\">Заполните, если должность разъездная (укажите сумму компенсации)</span></div>", type:"text", richtext: true, value:'', placeholder:'Введите как оплачиваются ГСМ', mandatory: false, visibility: false },
    { name: "hiringPurpose", label:"<div class=\"vtbl_tooltip\">Обоснование потребности найма<span class=\"vtbl_tooltip_text\">Кратко опишите необходимость в найме этого специалиста</span></div>", type:"text", richtext: true, value:'', page:"page_two", mandatory: false, visibility: false },
    { name: "jobSharing", label:"<div class=\"vtbl_tooltip\">Совмещение<span class=\"vtbl_tooltip_text\">Если сотрудник по совместительству</span></div>", type:"text", richtext: true, value:'', page:"page_two", mandatory: false, visibility: false },
    { name: "duties", label:"<div class=\"vtbl_tooltip\">Основные обязанности<span class=\"vtbl_tooltip_text\">Опишите задачи и ответственность</span></div>", type:"text", richtext: true, value:'', page: "page_two", mandatory: false, visibility: false },
    { name: "requirements", label:"<div class=\"vtbl_tooltip\">Основные требования<span class=\"vtbl_tooltip_text\">Образование, опыт, сертификаты, навыки</span></div>", type:"text", richtext: true, value:'', page:"page_two", mandatory: false, visibility: false }
];

// --- Загружаем существующую заявку ---
var sReqID = OptInt(getFormField("request_id", getFormFieldDefault("request_id", "step_0")), 0);
if (sReqID == 0) {
    throw "Не указан ID редактируемой заявки!";
}

var oReqDoc = tools.open_doc(sReqID);
var oReqDocTE = oReqDoc.TopElem;
oReqDocTE.person_id = oOptions.id;

// --- Подставляем значения из заявки в форму ---
for (var i = 0; i < oForm.form_fields.length; i++) {
    var fField = oForm.form_fields[i];
    switch(fField.name){
        case "name": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_name").value; break;
        case "subdivision_id": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value; break;
        case "subdivision_text": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value; break;
        case "sheCode": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_cod_he").value; break;
        case "function": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_function").value; break;
        case "vacancyReason": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value; break;
        case "workPlace": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_place").value; break;
        case "salary": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value; break;
        case "bonusSystem": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value; break;
        case "gasCost": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value; break;
        case "jobSharing": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value; break;
        case "hiringPurpose": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value; break;
        case "duties": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_function").value; break;
        case "requirements": fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_quality").value; break;
    }
}
