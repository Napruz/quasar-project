switch(sSubmitType){

    function ensureRequestID() {
        // Проверяем, есть ли поле request_id в форме
        var rid = getFormField("request_id","");
        if(!ArrayOptFind(oForm.form_fields, "This.name=='request_id'")){
            oForm.form_fields.push({name:"request_id", type:"hidden", value:rid});
        } else {
            // Обновляем значение, если уже есть
            for(f of oForm.form_fields){
                if(f.name=="request_id"){ f.value = rid; break; }
            }
        }
        return rid;
    }

    case "step_0": {
        aMandatoryFields = [];
        aVisibleFields = ["rules"];
        for(oField in oForm.form_fields){
            oField.type = (ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "hidden": oField.type);
            try{oField.validation=(ArrayOptFind(aVisibleFields,"This==oField.name")==undefined ? "": oField.validation);}catch(_e){}
            oField.mandatory = (ArrayOptFind(aMandatoryFields,"This==oField.name")!=undefined);
            oField.value = getFormField(oField.name,oField.value);
        }
        ensureRequestID();
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
        ensureRequestID();
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
        ensureRequestID();
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
        ensureRequestID();
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
        ensureRequestID();
        oForm.buttons.push(
            {name:"submit", submit_type:"kupor", label:"Назад", type:"submit"},
            {name:"submit", submit_type:"save", label:"Сохранить заявку", type:"submit"},
            {name:"cancel", label:"Отменить", type:"cancel"}
        );
        oForm.title += " Шаг 5 Основные обязанности и требования";
        break;
    }

    case "save": {
        var sReqID = ensureRequestID();
        var oReqDoc;

        if(sReqID != "") {
            oReqDoc = tools.open_doc(sReqID);
        } else {
            oReqDoc = tools.new_doc_by_name("request");
            oReqDoc.BindToDb();
            // Записываем новый ID в скрытое поле
            for(f of oForm.form_fields){
                if(f.name=="request_id"){ f.value = oReqDoc.DocID; break; }
            }
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
        oReqDocTE.custom_elems.ObtainChi_
