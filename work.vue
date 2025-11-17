case "save": {

    // Получаем ID заявки
    var sReqID = getFormField("request_id", "");
    var oReqDoc;

    // Если ID есть — открываем существующую заявку, иначе создаем новую
    if (sReqID != "") {
        oReqDoc = tools.open_doc(sReqID);
    } else {
        oReqDoc = tools.new_doc_by_name("request");
        oReqDoc.BindToDb();
    }

    var oReqDocTE = oReqDoc.TopElem;

    // Привязка к текущему пользователю
    oReqDocTE.person_id = oOptions.id;

    // Общие поля (аналог common_filling)
    tools.common_filling('request_type', oReqDocTE, 7194701844403546882);
    tools.common_filling('collaborator', oReqDocTE, oReqDocTE.person_id);

    oReqDocTE.is_group = 0;
    oReqDocTE.status_in_knowledge_map = "active";
    oReqDocTE.workflow_state = "stage_manager_accept";
    oReqDocTE.workflow_state_name = "Утверждение руководителем";
    oReqDocTE.is_workflow_init = 1;

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
