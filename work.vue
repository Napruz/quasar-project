switch(sSubmitType){

    case "step_0": {
        aMandatoryFields = [];
        aVisibleFields = ["rules"];
        for(oField in oForm.form_fields){
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type);
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){}
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined);
            oField.value = getFormField(oField.name,oField.value);
        }

        // Добавляем скрытое поле request_id, если его нет
        if(!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")){
            oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id","")});
        }

        oForm.buttons.push(
            {name:"submit", submit_type:"main_data", label:"Далее", type:"submit"},
            {name:"cancel", label:"Отменить", type:"cancel"}
        );
        oForm.title += " Шаг 1 Инструкция по заполнению";
        break;
    }

    case "main_data": {
        aMandatoryFields = ["name","sheCode","function","vacancyReason","workPlace"];
        aVisibleFields = ["name","subdivision_id","subdivision_text","sheCode","function","vacancyReason","workPlace"];
        for(oField in oForm.form_fields){
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type);
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){}
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined);
            oField.value = getFormField(oField.name,oField.value);
        }
        if(!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")){
            oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id","")});
        }
        oForm.buttons.push(
            {name:"submit", submit_type:"salary", label:"Далее", type:"submit"},
            {name:"cancel", label:"Отменить", type:"cancel"}
        );
        oForm.title += " Шаг 2 Основные параметры вакансии";
        break;
    }

    case "salary": {
        aMandatoryFields = ["salary","bonusSystem"];
        aVisibleFields = ["salary","bonusSystem","gasCost","jobSharing"];
        for(oField in oForm.form_fields){
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type);
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){}
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined);
            oField.value = getFormField(oField.name,oField.value);
        }
        if(!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")){
            oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id","")});
        }
        oForm.buttons.push(
            {name:"submit", submit_type:"main_data", label:"Назад", type:"submit"},
            {name:"submit", submit_type:"kupor", label:"Далее", type:"submit"},
            {name:"cancel", label:"Отменить", type:"cancel"}
        );
        oForm.title += " Шаг 3 Система оплаты труда и бонусы";
        break;
    }

    case "kupor": {
        aMandatoryFields = ["hiringPurpose"];
        aVisibleFields = ["hiringPurpose"];
        for(oField in oForm.form_fields){
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type);
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){}
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined);
            oField.value = getFormField(oField.name,oField.value);
        }
        if(!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")){
            oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id","")});
        }
        oForm.buttons.push(
            {name:"submit", submit_type:"salary", label:"Назад", type:"submit"},
            {name:"submit", submit_type:"another", label:"Далее", type:"submit"},
            {name:"cancel", label:"Отменить", type:"cancel"}
        );
        oForm.title += " Шаг 4 Обоснование вакансии для КУПОР";
        break;
    }

    case "another": {
        aMandatoryFields = [];
        aVisibleFields = ["duties","requirements"];
        for(oField in oForm.form_fields){
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type);
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){}
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined);
            oField.value = getFormField(oField.name,oField.value);
        }
        if(!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")){
            oForm.form_fields.push({name:"request_id", type:"hidden", value:getFormField("request_id","")});
        }
        oForm.buttons.push(
            {name:"submit", submit_type:"kupor", label:"Назад", type:"submit"},
            {name:"submit", submit_type:"save", label:"Сохранить заявку", type:"submit"},
            {name:"cancel", label:"Отменить", type:"cancel"}
        );
        oForm.title += " Шаг 5 Основные обязанности и требования";
        break;
    }

    case "save": {
        // Получаем ID заявки
        var sReqID = getFormField("request_id","");
        var oReqDoc;

        if(sReqID != "") {
            // Открываем существующую заявку
            oReqDoc = tools.open_doc(sReqID);
        } else {
            // Создаем новую заявку
            oReqDoc = tools.new_doc_by_name("request");
            oReqDoc.BindToDb();
            // Сохраняем ID новой заявки в форму
            oForm.form_fields.push({name:"request_id", type:"hidden", value:oReqDoc.DocID});
        }

        var oReqDocTE = oReqDoc.TopElem;

        // Привязка к текущему пользователю
        oReqDocTE.person_id = oOptions.id;
        tools.common_filling('request_type', oReqDocTE, 7194701844403546882);
        tools.common_filling('collaborator', oReqDocTE, oReqDocTE.person_id);

        // Заполняем поля заявки
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

    default:
        break;
}
