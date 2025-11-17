// Проверяем, есть ли ID редактируемой заявки
var sReqID = OptInt(getFormField("request_id", getFormFieldDefault("request_id", "step_0")), 0);

if (sReqID != 0) {
    var oReqDoc = tools.open_doc(sReqID);
    var oReqDocTE = oReqDoc.TopElem;

    // Подставляем значения в поля формы
    var fField; // объявляем один раз перед циклом
    for (var i = 0; i < oForm.form_fields.length; i++) {
        fField = oForm.form_fields[i]; // присваиваем без var
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
        }
    }
}
