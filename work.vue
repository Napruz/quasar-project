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

        if (_cur_history == undefined)
        {
            alert("history undefined");
        }
        else if (_cur_history.objects == undefined)
        {
            alert("objects undefined");
        }
        else if (_cur_history.objects.object == undefined)
        {
            alert("objects.object undefined");
        }
        else
        {
            var objectsArr = _cur_history.objects.object;

            alert("objectsArr count = " + ArrayCount(objectsArr));

            var firstObj = ArrayFirstElem(objectsArr);

            if (firstObj.section == undefined)
            {
                alert("section undefined");
            }
            else
            {
                var sections = firstObj.section;

                alert("sections count = " + ArrayCount(sections));

                for (var s = 0; s < ArrayCount(sections); s++)
                {
                    var section = ArrayGetElem(sections, s);

                    alert("section найден");

                    if (section.question == undefined)
                    {
                        alert("question undefined");
                        continue;
                    }

                    var questions = section.question;

                    alert("questions count = " + ArrayCount(questions));

                    for (var qi = 0; qi < ArrayCount(questions); qi++)
                    {
                        var q = ArrayGetElem(questions, qi);

                        alert("question найден: " + q.ident);

                        var qid = q.ident;

                        // --- добавляем в общий список вопросов ---
                        if (ArrayOptFind(aQuestions, "This.id == " + XQueryLiteral(qid)) == undefined)
                        {
                            aQuestions.push({
                                id: qid,
                                text: HtmlToPlainText(q.text)
                            });
                        }

                        // --- формируем объект ответа ---
                        var qObj = new Object();

                        qObj.quest_type = q.qtype;

                        // результат
                        qObj.result = (q.state == "passed") ? "верно" : "неверно";

                        // выбранные варианты (пока сырые)
                        if (q.variant != undefined)
                        {
                            qObj.answer = ArrayMerge(q.variant, "This", "; ");
                        }
                        else
                        {
                            qObj.answer = "";
                        }

                        // правильный ответ (пока заглушка)
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
