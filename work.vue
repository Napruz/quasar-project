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

        // 🔥 ВАЖНО: только for in
        for (var objItem in objectsArr)
        {
            if (objItem.section == undefined)
                continue;

            var sections = objItem.section;

            alert("sections count = " + ArrayCount(sections));

            for (var sec in sections)
            {
                if (sec.question == undefined)
                    continue;

                var questions = sec.question;

                for (var q in questions)
                {
                    alert("question найден: " + q.ident);

                    var qid = q.ident;

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

                    if (q.variant != undefined)
                        qObj.answer = ArrayMerge(q.variant, "This", "; ");
                    else
                        qObj.answer = "";

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
