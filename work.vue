var sections = firstObj.section;

alert("sections COUNT = " + ArrayCount(sections));

for (var si = 0; si < ArrayCount(sections); si++)
{
    var section = sections[si];
    alert("section = " + section);

    if (section.question == undefined)
    {
        alert("section.question == undefined");
        continue;
    }

    alert("questions COUNT = " + ArrayCount(section.question));

    for (var qi = 0; qi < ArrayCount(section.question); qi++)
    {
        var q = section.question[qi];

        alert("question найден, id = " + q.id);

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
