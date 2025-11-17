var sReqID = getFormField("request_id","");
var oReqDoc;

if (sReqID != "") {
    oReqDoc = tools.open_doc(sReqID); // открываем существующую заявку
} else {
    oReqDoc = tools.new_doc_by_name("request"); // только если реально нет ID
    oReqDoc.BindToDb();
}

var oReqDocTE = oReqDoc.TopElem;
oReqDocTE.person_id = oOptions.id;

// далее заполняем поля, как раньше

