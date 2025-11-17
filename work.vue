// ---------- ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ ----------
var sReqID, oReqDoc, oReqDocTE;
var aMandatoryFields, aVisibleFields;
var oField;

case "save": {

    // Получаем ID заявки
    sReqID = getFormField("request_id", "");

    // Если ID пуст — ошибка, потому что мы должны редактировать существующую заявку
    if (sReqID == "") {
        oForm = {
            command: "alert",
            msg: "Ошибка: request_id не передан. Невозможно сохранить изменения."
        };
        break;
    }

    // Открываем существующую заявку
    oReqDoc = tools.open_doc(sReqID);
    oReqDocTE = oReqDoc.TopElem;

    // Привязка к текущему пользователю
    oReqDocTE.person_id = oOptions.id;

    // Общие поля
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

    // Возврат alert с редиректом
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
