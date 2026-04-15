try
{
    alert("Открываем документ l.id = " + l.id);

    var doc = tools.open_doc(l.id).TopElem;

    alert("doc открыт");

    var annals = tools.annals_decrypt(doc);

    alert("annals = " + tools.object_to_text(annals, 'json'));

    if (annals == null)
    {
        alert("annals == NULL !!!");
    }
    else
    {
        var _cur_history = annals.au.history;

        if (!_cur_history)
        {
            alert("history undefined");
        }
        else if (!_cur_history.objects || !_cur_history.objects.object)
        {
            alert("objects undefined");
        }
        else
        {
            var objectsArr = _cur_history.objects.object;

            alert("objectsArr count = " + objectsArr.length);

            var firstObj = objectsArr[0];

            if (!firstObj.section)
            {
                alert("section undefined");
            }
            else
            {
                var sections = firstObj.section;

                alert("sections count = " + sections.length);

                for (var s = 0; s < sections.length; s++)
                {
                    var section = sections[s];

                    alert("section найден");

                    if (!section.question)
                    {
                        alert("question undefined");
                        continue;
                    }

                    var questions = section.question;

                    alert("questions count = " + questions.length);

                    for (var qi = 0; qi < questions.length; qi++)
                    {
                        var q = questions[qi];

                        alert("question найден: " + q.ident);

                        var qid = q.ident;

                        // --- добавляем вопрос ---
                        if (ArrayOptFind(aQuestions, "This.id == " + XQueryLiteral(qid)) == undefined)
                        {
                            aQuestions.push({
                                id: qid,
                                text: HtmlToPlainText(q.text)
                            });
                        }

                        var qObj = new Object();

                        qObj.quest_type = q.qtype;
                        qObj.result = (q.state == "passed") ? "верно" : "неверно";

                        // выбранные ответы (пока 0/1)
                        if (q.variant)
                        {
                            qObj.answer = q.variant.join("; ");
                        }
                        else
                        {
                            qObj.answer = "";
                        }

                        qObj.correct_answer = "";

                        obj.questions[qid] = qObj;
                    }
                }
            }
        }
    }
}
catch(e)
{
    alert("ERROR annals block: " + e);
}
