case "save": {
    if (sReqID == 0) {
        throw "Не найден ID редактируемой заявки!";
    }

    // Берем существующую заявку
    var oReqDoc = tools.open_doc(sReqID);
    var oReqDocTE = oReqDoc.TopElem;
    oReqDocTE.person_id = oOptions.id;

    // --- Заполняем поля из формы ---
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

    // --- Сохраняем заявку ---
    oReqDoc.BindToDb(DefaultDb);
    oReqDoc.Save();

    oForm = {
        command:"alert",
        msg:"Заявка обновлена",
        confirm_result:{command:"redirect", url:"/view_doc.html?mode=vtbl_recruitment_request&object_id=" + sReqID}
    };
    break;
}
