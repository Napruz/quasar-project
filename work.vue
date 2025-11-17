// Проверяем, есть ли ID редактируемой заявки
var sReqID = getFormField("request_id", "");

if (sReqID && sReqID != "") {
    var oReqDoc = tools.open_doc(sReqID);
    var oReqDocTE = oReqDoc.TopElem;

    // Подставляем значения в поля формы
    for (var i = 0; i < oForm.form_fields.length; i++) {
        var field = oForm.form_fields[i];
        switch(field.name){
            case "name":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_name").value;
                break;
            case "subdivision_id":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_id").value;
                break;
            case "subdivision_text":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_subdivision_name").value;
                break;
            case "sheCode":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_cod_he").value;
                break;
            case "function":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_function").value;
                break;
            case "vacancyReason":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cause").value;
                break;
            case "workPlace":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_place").value;
                break;
            case "salary":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_salary").value;
                break;
            case "bonusSystem":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_bonus_system").value;
                break;
            case "gasCost":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_gas_cost").value;
                break;
            case "jobSharing":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_job_sharing").value;
                break;
            case "hiringPurpose":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_vacancy_cupor_rationale").value;
                break;
            case "duties":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_function").value;
                break;
            case "requirements":
                field.value = oReqDocTE.custom_elems.ObtainChildByKey("f_candidate_quality").value;
                break;
        }
    }
}
