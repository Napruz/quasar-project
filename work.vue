for (l in arr)
{
    var obj = new Object();

    obj.person_fullname = l.person_id.ForeignElem.fullname;
    obj.person_code = l.person_id.ForeignElem.code;
    obj.org = l.person_id.ForeignElem.org_name;
    obj.subdivision = l.person_id.ForeignElem.position_parent_name;
    obj.position = l.person_id.ForeignElem.position_name;
    obj.date = l.start_usage_date;
    obj.status = l.state_id.ForeignElem.name;
    obj.score = l.score;
    obj.max_score = l.max_score;
    obj.assessment_name = l.assessment_id.ForeignElem.name;

    obj.questions = {};

    try
    {
        var doc = OpenDoc(UrlFromDocID(l.id)).TopElem;
        var annals = tools.annals_decrypt(doc);

        if (annals != null && annals.au != undefined && annals.au.history != undefined && ArrayCount(annals.au.history.objects) > 0)
        {
            var sections = annals.au.history.objects[0].section;

            for (s in sections)
            {
                for (q in sections[s].question)
                {
                    var qid = q.PrimaryKey;

                    if (ArrayOptFind(aQuestions, "This.id == " + XQueryLiteral(qid)) == undefined)
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
    }
    catch(e)
    {
        alert(e);
    }

    aLearnings.push(obj);
}
