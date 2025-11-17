sLogName = 'VelievEditLog'; 
EnableLog(sLogName, true); 

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

// --- ВАЖНО: Правильное получение request_id ---
// Сначала пробуем получить из параметров URL, потом из полей формы
var sReqID = OptInt(getParam("request_id", getFormField("request_id", getFormFieldDefault("request_id", 0))), 0);

LogAction("DEBUG: request_id = " + sReqID + ", submit_type = " + sSubmitType);

// --- Опции текущего пользователя --- 
var oOptions = {}; 
oOptions.id = Request.AuthUserID; 
oOptions.name = "Менеджер по подбору"; 

// --- Форма --- 
oForm = {}; 
oForm.command = "display_form"; 
oForm.height = 320; 
oForm.title = 'Редактирование заявки на подбор.'; 
oForm.message = ""; 

oForm.form_fields = [ 
    { 
        name: "rules", 
        type: "paragraph", 
        value: "<iframe id=\"rule_frame\" src=\"/vtbleasing/2025_recruitment/recruitment_application/rules.html\" style=\"width:100%;border:0;height:1200px\"></iframe>", 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "name", 
        label: "Наименование вакансии", 
        type: "string", 
        value:'', 
        placeholder:'Введите название вакансии', 
        validation:"noneempty", 
        error_message:"Заполните наименование вакансии", 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "subdivision_id", 
        label: "<div class=\"vtbl_tooltip\">Структурное подразделение<span class=\"vtbl_tooltip_text\">Выберите департамент, управление, службу, отдел, группу</span></div>", 
        type:"foreign_elem", 
        value:'', 
        catalog:"cc_tree_subdivision", 
        mandatory: false, 
        multiple: false, 
        query_qual:"" 
    }, 
    { 
        name: "subdivision_text", 
        label:"Структурное подразделение (ввести вручную, если не найдено)", 
        type:"string", 
        value:'', 
        placeholder:'Введите структурное подразделение', 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "sheCode", 
        label:"Код ШЕ", 
        type:"string", 
        value:'', 
        placeholder:'Введите код ШЕ', 
        validation:"noneempty", 
        error_message:"Заполните код ШЕ. Если вы его не знаете, обратитесь к вашему HR-BP", 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "function", 
        label:"Функция", 
        type:"select", 
        value:'', 
        validation:"noneempty", 
        error_message:"Укажите функцию", 
        entries:[{name:"Автолизинг", value:"Автолизинг"},{name:"Корпоративный блок", value:"Корпоративный блок"}], 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "vacancyReason", 
        label:"Причина возникновения вакансии", 
        type:"select", 
        value:'', 
        validation:"noneempty", 
        error_message:"Укажите причину возникновения вакансии", 
        entries:[{name:"Новая штатная единица", value:"Новая штатная единица"},{name:"Увольнение сотрудника", value:"Увольнение сотрудника"},{name:"Замена неэффективного сотрудника", value:"Замена неэффективного сотрудника"},{name:"Внутренний перевод", value:"Внутренний перевод"},{name:"Декрет", value:"Декрет"}], 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "workPlace", 
        label:"Место работы", 
        type:"select", 
        value:'', 
        validation:"noneempty", 
        error_message:"Укажите место работы", 
        entries:[{name:"Москва, 2-й Южнопортовый проезд, д.16, стр.1.", value:"Москва, 2-й Южнопортовый проезд, д.16, стр.1."},{name:"Москва, Башня на набережной", value:"Москва, Башня на набережной"}], 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "salary", 
        label:"Оклад", 
        type:"real", 
        validation:"number", 
        error_message:"Введите оклад", 
        value: 10000, 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "bonusSystem", 
        label:"Система премирования", 
        type:"select", 
        value:'', 
        validation:"noneempty", 
        error_message:"Укажите систему премирования", 
        entries:[{name:"Ежемесячная", value:"Ежемесячная"},{name:"Годовая", value:"Годовая"},{name:"Смешанная", value:"Смешанная"}], 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "gasCost", 
        label:"<div class=\"vtbl_tooltip\">ГСМ<span class=\"vtbl_tooltip_text\">Заполните, если должность разъездная (укажите сумму компенсации)</span></div>", 
        type:"text", 
        richtext: true, 
        value:'', 
        placeholder:'Введите как оплачиваются ГСМ', 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "hiringPurpose", 
        label:"<div class=\"vtbl_tooltip\">Обоснование потребности найма<span class=\"vtbl_tooltip_text\">Кратко опишите необходимость в найме этого специалиста</span></div>", 
        type:"text", 
        richtext: true, 
        value:'', 
        page:"page_two", 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "jobSharing", 
        label:"<div class=\"vtbl_tooltip\">Совмещение<span class=\"vtbl_tooltip_text\">Если сотрудник по совместительству</span></div>", 
        type:"text", 
        richtext: true, 
        value:'', 
        page:"page_two", 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "duties", 
        label:"<div class=\"vtbl_tooltip\">Основные обязанности<span class=\"vtbl_tooltip_text\">Опишите задачи и ответственность</span></div>", 
        type:"text", 
        richtext: true, 
        value:'', 
        page: "page_two", 
        mandatory: false, 
        visibility: false 
    }, 
    { 
        name: "requirements", 
        label:"<div class=\"vtbl_tooltip\">Основные требования<span class=\"vtbl_tooltip_text\">Образование, опыт, сертификаты, навыки</span></div>", 
        type:"text", 
        richtext: true, 
        value:'', 
        page:"page_two", 
        mandatory: false, 
        visibility: false 
    } 
]; 

// Загружаем данные существующей заявки если есть ID
if (sReqID != 0) { 
    try {
        LogAction("Загружаем данные заявки ID: " + sReqID);
        var oReqDoc = tools.open_doc(sReqID); 
        var oReqDocTE = oReqDoc.TopElem; 
        
        // Подставляем значения в поля формы 
        for (var i = 0; i < oForm.form_fields.length; i++) { 
            var fField = oForm.form_fields[i];
            switch(fField.name){ 
                case "name": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_name").value; 
                    break; 
                case "subdivision_id": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value; 
                    break; 
                case "subdivision_text": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value; 
                    break; 
                case "sheCode": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_cod_he").value; 
                    break; 
                case "function": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_function").value; 
                    break; 
                case "vacancyReason": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value; 
                    break; 
                case "workPlace": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_place").value; 
                    break; 
                case "salary": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value; 
                    break; 
                case "bonusSystem": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value; 
                    break; 
                case "gasCost": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value; 
                    break; 
                case "jobSharing": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value; 
                    break; 
                case "hiringPurpose": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value; 
                    break; 
                case "duties": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_function").value; 
                    break; 
                case "requirements": 
                    fField.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_quality").value; 
                    break; 
                default: 
                    break; 
            } 
        }
        LogAction("Данные заявки успешно загружены");
    } catch (e) {
        LogAction("Ошибка при загрузке заявки: " + e.message);
    }
} 

// --- Кнопки и логика шагов --- 
oForm.buttons = []; 
oForm.no_buttons = false; 

switch(sSubmitType){ 
    case "step_0": { 
        aMandatoryFields = []; 
        aVisibleFields = ["rules"]; 
        for(var i = 0; i < oForm.form_fields.length; i++){ 
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type); 
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){} 
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined); 
            oField.value = getFormField(oField.name,oField.value); 
        } 
        
        // Всегда добавляем request_id
        oForm.form_fields.push({name:"request_id", type:"hidden", value:sReqID}); 
        
        oForm.buttons.push({name:"submit", submit_type:"main_data", label:"Далее", type:"submit"},{name:"cancel", label:"Отменить", type:"cancel"}); 
        oForm.title += " Шаг 1 Инструкция по заполнению"; 
        break; 
    } 
    case "main_data": { 
        aMandatoryFields = ["name","sheCode","function","vacancyReason","workPlace"]; 
        aVisibleFields = ["name","subdivision_id","subdivision_text","sheCode","function","vacancyReason","workPlace"]; 
        for(var i = 0; i < oForm.form_fields.length; i++){ 
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type); 
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){} 
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined); 
            oField.value = getFormField(oField.name,oField.value); 
        } 
        
        // Всегда добавляем request_id
        oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id", sReqID)}); 
        
        oForm.buttons.push({name:"submit", submit_type:"salary", label:"Далее", type:"submit"},{name:"cancel", label:"Отменить", type:"cancel"}); 
        oForm.title += " Шаг 2 Основные параметры вакансии"; 
        break; 
    } 
    case "salary": { 
        aMandatoryFields = ["salary","bonusSystem"]; 
        aVisibleFields = ["salary","bonusSystem","gasCost","jobSharing"]; 
        for(var i = 0; i < oForm.form_fields.length; i++){ 
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type); 
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){} 
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined); 
            oField.value = getFormField(oField.name,oField.value); 
        } 
        
        // Всегда добавляем request_id
        oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id", sReqID)}); 
        
        oForm.buttons.push({name:"submit", submit_type:"main_data", label:"Назад", type:"submit"},{name:"submit", submit_type:"kupor", label:"Далее", type:"submit"},{name:"cancel", label:"Отменить", type:"cancel"}); 
        oForm.title += " Шаг 3 Система оплаты труда и бонусы"; 
        break; 
    } 
    case "kupor": { 
        aMandatoryFields = ["hiringPurpose"]; 
        aVisibleFields = ["hiringPurpose"]; 
        for(var i = 0; i < oForm.form_fields.length; i++){ 
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type); 
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){} 
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined); 
            oField.value = getFormField(oField.name,oField.value); 
        } 
        
        // Всегда добавляем request_id
        oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id", sReqID)}); 
        
        oForm.buttons.push({name:"submit", submit_type:"salary", label:"Назад", type:"submit"},{name:"submit", submit_type:"another", label:"Далее", type:"submit"},{name:"cancel", label:"Отменить", type:"cancel"}); 
        oForm.title += " Шаг 4 Обоснование вакансии для КУПОР"; 
        break; 
    } 
    case "another": { 
        aMandatoryFields = []; 
        aVisibleFields = ["duties","requirements"]; 
        for(var i = 0; i < oForm.form_fields.length; i++){ 
            var oField = oForm.form_fields[i];
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type); 
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){} 
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined); 
            oField.value = getFormField(oField.name,oField.value); 
        } 
        
        // Всегда добавляем request_id
        oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id", sReqID)}); 
        
        oForm.buttons.push({name:"submit", submit_type:"kupor", label:"Назад", type:"submit"},{name:"submit", submit_type:"save", label:"Сохранить заявку", type:"submit"},{name:"cancel", label:"Отменить", type:"cancel"}); 
        oForm.title += " Шаг 5 Основные обязанности и требования"; 
        break; 
    } 
    case "save": { 
        // Получаем ID заявки из ВСЕХ возможных источников
        var sCurrentReqID = getFormField("request_id", sReqID);
        LogAction("DEBUG save: request_id = " + sCurrentReqID);
        
        var oReqDoc; 
        
        if(sCurrentReqID && sCurrentReqID != "" && sCurrentReqID != 0) { 
            // Открываем существующую заявку
            try {
                oReqDoc = tools.open_doc(sCurrentReqID); 
                LogAction("Открыта существующая заявка: " + sCurrentReqID);
            } catch (e) {
                LogAction("Ошибка при открытии заявки " + sCurrentReqID + ": " + e.message);
                // Если не удалось открыть - создаем новую
                oReqDoc = tools.new_doc_by_name("request"); 
                oReqDoc.BindToDb(); 
                sCurrentReqID = oReqDoc.DocID;
                LogAction("Создана новая заявка: " + sCurrentReqID);
            }
        } else { 
            // Создаем новую заявку 
            oReqDoc = tools.new_doc_by_name("request"); 
            oReqDoc.BindToDb(); 
            sCurrentReqID = oReqDoc.DocID; 
            LogAction("Создана новая заявка: " + sCurrentReqID);
        } 
        
        var oReqDocTE = oReqDoc.TopElem; 
        
        // Привязка к текущему пользователю 
        oReqDocTE.person_id = oOptions.id; 
        tools.common_filling('request_type', oReqDocTE, 7194701844403546882); 
        tools.common_filling('collaborator', oReqDocTE, oReqDocTE.person_id); 
        
        // --- Заполняем поля заявки --- 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value = getFormField("subdivision_id",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value = getFormField("subdivision_text",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_function").value = getFormField("function",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value = getFormField("vacancyReason",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_place").value = getFormField("workPlace",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_name").value = getFormField("name",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value = getFormField("salary",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value = getFormField("bonusSystem",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value = getFormField("gasCost",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value = getFormField("jobSharing",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value = getFormField("hiringPurpose",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_function").value = getFormField("duties",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_quality").value = getFormField("requirements",""); 
        oReqDocTE.custom_elems.ObtainChildByKey("f_cod_he").value = getFormField("sheCode",""); 
        
        // Сохраняем документ 
        oReqDoc.Save(); 
        LogAction("Заявка сохранена: " + oReqDoc.DocID);
        
        // Возврат alert с редиректом на редактируемую заявку 
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

RESULT = oForm;
