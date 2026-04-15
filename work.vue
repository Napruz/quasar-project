var _cur_history = annals.au.history;

if (_cur_history == undefined)
{
    alert("history undefined");
}
else if (ArrayCount(_cur_history.objects) == 0)
{
    alert("objects пустой");
}
else
{
    alert("objects НЕ пустой");

    var firstObj = ArrayFirstElem(_cur_history.objects);
    alert("firstObj = " + firstObj);

    var sections = firstObj.section;
    alert("sections = " + sections);

    for (s in sections)
    {
        alert("section = " + sections[s]);

        for (q in sections[s].question)
        {
            alert("question найден");

            var qid = q.id;

            if (ArrayOptFind(aQuestions, "This.id == " + qid) == undefined)
            {
                aQuestions.push({
                    id: qid,
                    text: HtmlToPlainText(q.text)
                });
            }

            var qObj = new Object();

            qObj.quest_type = (q.qtype.OptForeignElem != undefined ? q.qtype.ForeignElem.name : "");
            qObj.result = tools_web.is_correct_question(q) ? "верно" : "неверно";
            qObj.answer = ArrayMerge(q.variant, "HtmlToPlainText(value)", "; ");
            qObj.correct_answer = ArrayMerge(q.variant, "HtmlToPlainText(cor_value)", "; ");

            obj.questions[qid] = qObj;
        }
    }
}
