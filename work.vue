function createReportString(aCollaboratorIDArray, aAssessmentIDArray, dStartDate, dFinishDate)
{
    alert("старт функции createReportString");

    var aLearnings = [];
    var aQuestions = [];

    var raw_person_id;

    for (var i = 0; i < ArrayCount(aCollaboratorIDArray); i++)
    {
        raw_person_id = aCollaboratorIDArray[i];

        if (raw_person_id == undefined)
            continue;

        raw_person_id = Trim(raw_person_id);

        if (raw_person_id == "")
            continue;

        var person_id = raw_person_id;

        alert("person_id = " + person_id);

        for (var j = 0; j < ArrayCount(aAssessmentIDArray); j++)
        {
            var raw_ass_id = aAssessmentIDArray[j];

            if (raw_ass_id == undefined)
                continue;

            raw_ass_id = Trim(raw_ass_id);

            if (raw_ass_id == "")
                continue;

            var ass_id = raw_ass_id;

            alert("ass_id = " + ass_id);

            var arr = XQuery(
                "for $l in test_learnings " +
                "where $l/person_id = " + XQueryLiteral(person_id) +
                " and $l/assessment_id = " + XQueryLiteral(ass_id) +
                " and $l/start_usage_date >= date('" + StrDate(dStartDate,false) + "')" +
                " and $l/start_usage_date <= date('" + StrDate(dFinishDate,false) + "')" +
                " return $l"
            );

            alert("ArrayCount(arr) = " + ArrayCount(arr));

            for (var k = 0; k < ArrayCount(arr); k++)
            {
                var l = arr[k];

                alert("l.id = " + l.id);

                var obj = new Object();

                obj.person_fullname = l.person_id.ForeignElem.fullname;
                obj.assessment_name = l.assessment_name;
                obj.person_code = l.person_id.ForeignElem.code;
                obj.org = l.person_id.ForeignElem.org_name;
                obj.subdivision = l.person_id.ForeignElem.position_parent_name;
                obj.position = l.person_id.ForeignElem.position_name;
                obj.date = l.start_usage_date;
                obj.status = l.state_id.ForeignElem.name;
                obj.score = l.score;
                obj.max_score = l.max_score;

                obj.questions = new Object();

                try
                {
                    alert("Открываем документ l.id = " + l.id);

                    var doc = tools.open_doc(l.id).TopElem;

                    var annals = tools.annals_decrypt(doc);

                    if (annals == null ||
                        annals.au == undefined ||
                        annals.au.history == undefined ||
                        annals.au.history.objects == undefined)
                    {
                        alert("annals structure invalid");
                    }
                    else
                    {
                        var objectsArr = annals.au.history.objects.object;

                        if (objectsArr == undefined)
                            continue;

                        for (var oi = 0; oi < ArrayCount(objectsArr); oi++)
                        {
                            var objItem = objectsArr[oi];

                            if (objItem.section == undefined)
                                continue;

                            var sections = objItem.section;

                            for (var si = 0; si < ArrayCount(sections); si++)
                            {
                                var sec = sections[si];

                                if (sec.question == undefined)
                                    continue;

                                var questions = sec.question;

                                for (var qi = 0; qi < ArrayCount(questions); qi++)
                                {
                                    var q = questions[qi];

                                    var qid = q.ident;

                                    alert("question найден: " + qid);

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

                                    obj.questions["" + qid] = qObj;
                                }
                            }
                        }
                    }
                }
                catch (e)
                {
                    alert("ERROR annals block: " + e);
                }

                aLearnings.push(obj);
            }
        }
    }

    alert("aQuestions LENGTH = " + ArrayCount(aQuestions));
    alert("aLearnings LENGTH = " + ArrayCount(aLearnings));

    var html = new Binary();

    html.AppendStr(
        "<html xmlns:o='urn:schemas-microsoft-com:office:office' " +
        "xmlns:x='urn:schemas-microsoft-com:office:excel' " +
        "xmlns='http://www.w3.org/TR/REC-html40'>" +
        "<head>" +
        "<meta http-equiv='Content-Type' content='text/html; charset=utf-8'/>" +
        "</head><body><table border='1'>"
    );

    // HEADER QUESTIONS
    html.AppendStr("<tr><td colspan='9'></td>");

    for (var qh = 0; qh < ArrayCount(aQuestions); qh++)
    {
        html.AppendStr("<td colspan='4'><b>" + aQuestions[qh].text + "</b></td>");
    }

    html.AppendStr("</tr>");

    // HEADER ROW
    html.AppendStr("<tr>");
    html.AppendStr("<td>Пользователь</td>");
    html.AppendStr("<td>Тест</td>");
    html.AppendStr("<td>Код</td>");
    html.AppendStr("<td>Орг</td>");
    html.AppendStr("<td>Подразделение</td>");
    html.AppendStr("<td>Должность</td>");
    html.AppendStr("<td>Дата</td>");
    html.AppendStr("<td>Статус</td>");
    html.AppendStr("<td>Баллы</td>");

    for (var qh2 = 0; qh2 < ArrayCount(aQuestions); qh2++)
    {
        html.AppendStr("<td>Тип</td><td>Результат</td><td>Ответ</td><td>Правильный</td>");
    }

    html.AppendStr("</tr>");

    // DATA
    for (var c = 0; c < ArrayCount(aLearnings); c++)
    {
        var K = aLearnings[c];

        html.AppendStr("<tr>");

        html.AppendStr("<td>" + K.person_fullname + "</td>");
        html.AppendStr("<td>" + K.assessment_name + "</td>");
        html.AppendStr("<td>" + K.person_code + "</td>");
        html.AppendStr("<td>" + K.org + "</td>");
        html.AppendStr("<td>" + K.subdivision + "</td>");
        html.AppendStr("<td>" + K.position + "</td>");
        html.AppendStr("<td>" + StrDate(K.date,false,false) + "</td>");
        html.AppendStr("<td>" + K.status + "</td>");

        var score = "";
        if (K.max_score != null && K.max_score != 0)
            score = K.score + " (" + Math.round(100 * K.score / K.max_score) + "%)";
        else
            score = K.score;

        html.AppendStr("<td>" + score + "</td>");

        for (var qh3 = 0; qh3 < ArrayCount(aQuestions); qh3++)
        {
            var qid2 = aQuestions[qh3].id;
            var qData = K.questions["" + qid2];

            if (qData == undefined)
            {
                html.AppendStr("<td></td><td></td><td></td><td></td>");
            }
            else
            {
                var bg = qData.result == "неверно" ? "#F4CCCC" : "#D9EAD3";

                html.AppendStr("<td>" + qData.quest_type + "</td>");
                html.AppendStr("<td bgcolor='" + bg + "'>" + qData.result + "</td>");
                html.AppendStr("<td>" + qData.answer + "</td>");
                html.AppendStr("<td>" + qData.correct_answer + "</td>");
            }
        }

        html.AppendStr("</tr>");
    }

    html.AppendStr("</table></body></html>");

    alert("конец функции createReportString");

    return html.GetStr();
}
