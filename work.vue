// -----------------------------------------------------
// Редактирование заявки на подбор — фикс: перезапись старой
// -----------------------------------------------------

// лог (по желанию)
var sLogName = 'VelievEditLog';
try{ EnableLog(sLogName, true); }catch(_e){}

// --- Вспомогательные функции (как у тебя были) ---
function getParam(sName, sDefault) {
    var sValue = PARAMETERS.GetOptProperty(sName);
    if (sDefault != undefined && (sValue == undefined || sValue == "")) sValue = sDefault;
    return sValue;
}

function getFormField(sName, sDefault) {
    var sValue = ArrayOptFind(aFormFields, ("This.name == " + XQueryLiteral(sName)));
    sValue = (sValue != undefined ? sValue.value : sValue);
    if (sDefault != undefined && (sValue == undefined || sValue == "")) sValue = sDefault;
    return sValue;
}

function getFormFieldDefault(sName, sDefault) {
    var sValue = ArrayOptFind(aFormFieldsDef, ("This.name == " + XQueryLiteral(sName)));
    sValue = (sValue != undefined ? sValue.value : sValue);
    if (sDefault != undefined && (sValue == undefined || sValue == "")) sValue = sDefault;
    return sValue;
}

// --- Загружаем данные формы (входящие параметры) ---
var aFormFields = ParseJson(getParam("form_fields", "[]"));
var aFormFieldsDef = ParseJson(getParam("form_fields_default", "[]"));
var sSubmitType = getFormField("__submit_type__", getFormFieldDefault("__submit_type__", "step_0"));

// --- Опции текущего пользователя ---
var oOptions = {};
oOptions.id = Request.AuthUserID;
oOptions.name = "Менеджер по подбору";

// --- Построение формы (базовая) ---
var oForm = {};
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
    { name: "name", label: "Наименование вакансии", type: "string", value: '', placeholder: 'Введите название вакансии', validation: "noneempty", error_message: "Заполните наименование вакансии", mandatory: false, visibility: false },
    { name: "subdivision_id", label: "<div class=\"vtbl_tooltip\">Структурное подразделение<span class=\"vtbl_tooltip_text\">Выберите департамент, управление, службу, отдел, группу</span></div>", title: "Выберите структурное подразделение", type: "foreign_elem", value: '', mandatory: false, multiple: false, catalog: "cc_tree_subdivision", query_qual: "" },
    { name: "subdivision_text", label: "Структурное подразделение (ввести вручную, если не найдено)", type: "string", value: '', placeholder: 'Введите структурное подразделение', mandatory: false, visibility: false },
    { name: "sheCode", abel: "Код ШЕ", type: "string", value: '', placeholder: 'Введите код ШЕ', validation: "noneempty", error_message: "Заполните код ШЕ. Если вы его не знаете, обратитесь к вашему HR-BP", mandatory: false, visibility: false },
    { name: "function", label: "Функция", type: "select", value: '', validation: "noneempty", error_message: "Укажите функцию", entries: [{ name: "Автолизинг", value: "Автолизинг" }, { name: "Корпоративный блок", value: "Корпоративный блок" }], mandatory: false, visibility: false },
    { name: "vacancyReason", label: "Причина возникновения вакансии", type: "select", value: '', validation: "noneempty", error_message: "Укажите причину возникновения вакансии", entries: [{ name: "Новая штатная единица", value: "Новая штатная единица" }, { name: "Увольнение сотрудника", value: "Увольнение сотрудника" }, { name: "Замена неэффективного сотрудника", value: "Замена неэффективного сотрудника" }, { name: "Внутренний перевод", value: "Внутренний перевод" }, { name: "Декрет", value: "Декрет" }], mandatory: false, visibility: false },
    { name: "workPlace", label: "Место работы", type: "select", value: '', validation: "noneempty", error_message: "Укажите место работы", entries: [{ name: "Москва, 2-й Южнопортовый проезд, д.16, стр.1.", value: "Москва, 2-й Южнопортовый проезд, д.16, стр.1." }, { name: "Москва, Башня на набережной", value: "Москва, Башня на набережной" }], mandatory: false, visibility: false },
    { name: "salary", label: "Оклад", type: "real", validation: "number", error_message: "Введите оклад", value: 10000, mandatory: false, visibility: false },
    { name: "bonusSystem", label: "Система премирования", type: "select", value: '', validation: "noneempty", error_message: "Укажите систему премирования", entries: [{ name: "Ежемесячная", value: "Ежемесячная" }, { name: "Годовая", value: "Годовая" }, { name: "Смешанная", value: "Смешанная" }], mandatory: false, visibility: false },
    { name: "gasCost", label: "<div class=\"vtbl_tooltip\">ГСМ<span class=\"vtbl_tooltip_text\">Заполните, если должность разъездная (укажите сумму компенсации)</span></div>", type: "text", richtext: true, value: '', placeholder: 'Введите как оплачиваются ГСМ', mandatory: false, visibility: false },
    { name: "hiringPurpose", label: "<div class=\"vtbl_tooltip\">Обоснование потребности найма<span class=\"vtbl_tooltip_text\">Кратко опишите необходимость в найме этого специалиста</span></div>", type: "text", richtext: true, value: '', page: "page_two", mandatory: false, visibility: false },
    { name: "jobSharing", label: "<div class=\"vtbl_tooltip\">Совмещение<span class=\"vtbl_tooltip_text\">Если сотрудник по совместительству</span></div>", type: "text", richtext: true, value: '', page: "page_two", mandatory: false, visibility: false },
    { name: "duties", label: "<div class=\"vtbl_tooltip\">Основные обязанности<span class=\"vtbl_tooltip_text\">Опишите задачи и ответственность</span></div>", type: "text", richtext: true, value: '', page: "page_two", mandatory: false, visibility: false },
    { name: "requirements", label: "<div class=\"vtbl_tooltip\">Основные требования<span class=\"vtbl_tooltip_text\">Образование, опыт, сертификаты, навыки</span></div>", type: "text", richtext: true, value: '', page: "page_two", mandatory: false, visibility: false }
];

// ---------------------------
// Скрытое поле request_id — гарантируем, что оно есть в форме
// ---------------------------
if (!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")) {
    oForm.form_fields.push({ name: "request_id", type: "hidden", value: getFormField("request_id", "") });
}

// ---------------------------
// Подгружаем существующую заявку и подставляем значения в поля формы
// ---------------------------
// Получаем id из входящих параметров (при первом открытии и при шаговой навигации)
var sReqID = getFormField("request_id", getFormFieldDefault("request_id", ""));
sReqID = OptInt(sReqID, 0);

// Если id присутствует — подставляем значения
if (sReqID && sReqID != 0) {
    try {
        var oReqDoc = tools.open_doc(sReqID);
        var oReqDocTE = oReqDoc.TopElem;

        // Проходимся по полям формы и подставляем значения только если поле пустое (чтобы не перезаписывать промежуточный ввод)
        for (var i = 0; i < oForm.form_fields.length; i++) {
            var f = oForm.form_fields[i];
            // Берём текущее значение формы (входящее) — если есть, не затираем
            var cur = getFormField(f.name, f.value);
            if (cur != undefined && String(cur) !== "") {
                // есть уже значение из формы, пропускаем
                f.value = cur;
                continue;
            }
            switch (f.name) {
                case "name": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_name").value; break;
                case "subdivision_id": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value; break;
                case "subdivision_text": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value; break;
                case "sheCode": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_cod_he").value; break;
                case "function": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_function").value; break;
                case "vacancyReason": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value; break;
                case "workPlace": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_place").value; break;
                case "salary": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value; break;
                case "bonusSystem": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value; break;
                case "gasCost": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value; break;
                case "jobSharing": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value; break;
                case "hiringPurpose": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value; break;
                case "duties": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_function").value; break;
                case "requirements": f.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_quality").value; break;
                default: break;
            }
        }
    } catch (_ex) {
        // если не удалось открыть документ — предупредим пользователя
        oForm = { command: "alert", msg: "Ошибка: не удалось загрузить заявку с ID=" + sReqID + ". Проверьте права и корректность ID." };
        RESULT = oForm;
    }
}

// ---------------------------
// Кнопки и логика шагов (сохраняем request_id как скрытое поле на всех шагах)
// ---------------------------
oForm.buttons = [];
oForm.no_buttons = false;

switch (sSubmitType) {
    case "step_0": {
        var aMandatoryFields = [];
        var aVisibleFields = ["rules"];
        for (var j in oForm.form_fields) {
            var ff = oForm.form_fields[j];
            ff.type = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "hidden" : ff.type);
            try { ff.validation = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "" : ff.validation); } catch (_e) { }
            ff.mandatory = (ArrayOptFind(aMandatoryFields, "This == ff.name") != undefined);
            ff.value = getFormField(ff.name, ff.value);
        }
        oForm.buttons.push({ name: "submit", submit_type: "main_data", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 1 Инструкция по заполнению";
        break;
    }
    case "main_data": {
        var aMandatoryFields = ["name", "sheCode", "function", "vacancyReason", "workPlace"];
        var aVisibleFields = ["name", "subdivision_id", "subdivision_text", "sheCode", "function", "vacancyReason", "workPlace"];
        for (var j in oForm.form_fields) {
            var ff = oForm.form_fields[j];
            ff.type = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "hidden" : ff.type);
            try { ff.validation = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "" : ff.validation); } catch (_e) { }
            ff.mandatory = (ArrayOptFind(aMandatoryFields, "This == ff.name") != undefined);
            ff.value = getFormField(ff.name, ff.value);
        }
        oForm.buttons.push({ name: "submit", submit_type: "salary", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 2 Основные параметры вакансии";
        break;
    }
    case "salary": {
        var aMandatoryFields = ["salary", "bonusSystem"];
        var aVisibleFields = ["salary", "bonusSystem", "gasCost", "jobSharing"];
        for (var j in oForm.form_fields) {
            var ff = oForm.form_fields[j];
            ff.type = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "hidden" : ff.type);
            try { ff.validation = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "" : ff.validation); } catch (_e) { }
            ff.mandatory = (ArrayOptFind(aMandatoryFields, "This == ff.name") != undefined);
            ff.value = getFormField(ff.name, ff.value);
        }
        oForm.buttons.push({ name: "submit", submit_type: "main_data", label: "Назад", type: "submit" }, { name: "submit", submit_type: "kupor", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 3 Система оплаты труда и бонусы";
        break;
    }
    case "kupor": {
        var aMandatoryFields = ["hiringPurpose"];
        var aVisibleFields = ["hiringPurpose"];
        for (var j in oForm.form_fields) {
            var ff = oForm.form_fields[j];
            ff.type = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "hidden" : ff.type);
            try { ff.validation = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "" : ff.validation); } catch (_e) { }
            ff.mandatory = (ArrayOptFind(aMandatoryFields, "This == ff.name") != undefined);
            ff.value = getFormField(ff.name, ff.value);
        }
        oForm.buttons.push({ name: "submit", submit_type: "salary", label: "Назад", type: "submit" }, { name: "submit", submit_type: "another", label: "Далее", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 4 Обоснование вакансии для КУПОР";
        break;
    }
    case "another": {
        var aMandatoryFields = [];
        var aVisibleFields = ["duties", "requirements"];
        for (var j in oForm.form_fields) {
            var ff = oForm.form_fields[j];
            ff.type = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "hidden" : ff.type);
            try { ff.validation = ((ArrayOptFind(aVisibleFields, "This == ff.name") == undefined) ? "" : ff.validation); } catch (_e) { }
            ff.mandatory = (ArrayOptFind(aMandatoryFields, "This == ff.name") != undefined);
            ff.value = getFormField(ff.name, ff.value);
        }
        oForm.buttons.push({ name: "submit", submit_type: "kupor", label: "Назад", type: "submit" }, { name: "submit", submit_type: "save", label: "Сохранить заявку", type: "submit" }, { name: "cancel", label: "Отменить", type: "cancel" });
        oForm.title += " Шаг 5 Основные обязанности и требования";
        break;
    }
    case "save": {
        // --- ВНИМАНИЕ: фиксированная логика — только ПЕРЕЗАПИСЬ существующей заявки ---
        var sId = getFormField("request_id", "");
        sId = OptInt(sId, 0);
        if (!sId || sId == 0) {
            // Пользователь не передал request_id — НЕ СОЗДАЕМ новую, показываем ошибку
            oForm = { command: "alert", msg: "Ошибка: request_id не передан. Нельзя создать новую заявку из этой формы. Откройте существующую заявку для редактирования." };
            RESULT = oForm;
            break;
        }

        try {
            var oReq = tools.open_doc(sId);
            var oReqTE = oReq.TopElem;

            // Привязка к текущему пользователю (если нужно)
            oReqTE.person_id = oOptions.id;

            // Заполнение полей
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value = getFormField("subdivision_id", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value = getFormField("subdivision_text", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_function").value = getFormField("function", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value = getFormField("vacancyReason", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_place").value = getFormField("workPlace", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_name").value = getFormField("name", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value = getFormField("salary", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value = getFormField("bonusSystem", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value = getFormField("gasCost", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value = getFormField("jobSharing", "");
            oReqTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value = getFormField("hiringPurpose", "");
            oReqTE.custom_elems.ObtainChildByKey("f_candidate_function").value = getFormField("duties", "");
            oReqTE.custom_elems.ObtainChildByKey("f_candidate_quality").value = getFormField("requirements", "");
            oReqTE.custom_elems.ObtainChildByKey("f_cod_he").value = getFormField("sheCode", "");

            // Сохраняем изменения в существующем документе
            oReq.Save();

            oForm = {
                command: "alert",
                msg: "Заявка сохранена",
                confirm_result: { command: "redirect", url: "/view_doc.html?mode=vtbl_recruitment_request&object_id=" + sId }
            };
        } catch (exSave) {
            oForm = { command: "alert", msg: "Ошибка при сохранении: " + exSave };
        }

        break;
    }
    default: {
        // ничего
        break;
    }
}

// Результат
RESULT = oForm;
