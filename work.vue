try
{
    alert("Открываем документ l.id = " + l.id);

    doc = tools.open_doc(l.id).TopElem;

    alert("doc открыт");

    var annals = tools.annals_decrypt(doc);

    if (annals == null)
    {
        alert("annals == null");
    }
    else if (annals.au == undefined)
    {
        alert("annals.au undefined");
    }
    else if (annals.au.history == undefined)
    {
        alert("history undefined");
    }
    else if (annals.au.history.objects == undefined)
    {
        alert("objects undefined");
    }
    else if (annals.au.history.objects.object == undefined)
    {
        alert("objects.object undefined");
    }
    else
    {
        var objectsArr = annals.au.history.objects.object;

        alert("objectsArr count = " + ArrayCount(objectsArr));

        for (var i_obj = 0; i_obj < ArrayCount(objectsArr); i_obj++)
        {
            var objItem = objectsArr[i_obj];

            if (objItem.section == undefined)
                continue;

            var sections = objItem.section;

            alert("sections count = " + ArrayCount(sections));

            for (var i_sec = 0; i_sec < ArrayCount(sections); i_sec++)
            {
                var sec = sections[i_sec];

                if (sec.question == undefined)
                    continue;

                var questions = sec.question;

                for (var i_q = 0; i_q < ArrayCount(questions); i_q++)
                {
                    var q = questions[i_q];

                    alert("question найден: " + q.ident);

                    var qid = q.ident;

                    // добавляем вопрос в общий список
                    if (ArrayOptFind(aQuestions, "This.id == '" + qid + "'") == undefined)
                    {
                        aQuestions.push({
                            id: qid,
                            text: q.text
                        });
                    }

                    var qObj = new Object();

                    qObj.quest_type = q.qtype;
                    qObj.result = (q.state == "passed" ? "верно" : "неверно");

                    // ответ пользователя
                    if (q.variant != undefined)
                        qObj.answer = ArrayMerge(q.variant, "This", "; ");
                    else
                        qObj.answer = "";

                    // правильный ответ (в annals его нет)
                    qObj.correct_answer = "";

                    obj.questions[qid] = qObj;
                }
            }
        }
    }
}
catch(e)
{
    alert("ERROR annals block: " + e);
}
