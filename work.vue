function createReportString(aCollaboratorIDArray, aAssessmentIDArray, dStartDate, dFinishDate)
{
    alert("старт функции createReportString");

    var aLearnings = [];
    var aQuestions = [];

    var raw_person_id;

    for (var i = 0; i < ArrayCount(aCollaboratorIDArray); i++) {

        raw_person_id = aCollaboratorIDArray[i];

        if (raw_person_id == undefined)
            continue;

        raw_person_id = Trim(raw_person_id);

        if (raw_person_id == "")
            continue;

        var person_id = raw_person_id;
        alert("person_id = " + person_id);

        var raw_ass_id;
        var ass_id;
        var arr;

        for (var j = 0; j < ArrayCount(aAssessmentIDArray); j++)
        {
            alert("Массив IDs assesments = " + aAssessmentIDArray);

            raw_ass_id = aAssessmentIDArray[j];

            if (raw_ass_id == undefined)
                continue;

            raw_ass_id = Trim(raw_ass_id);

            if (raw_ass_id == "")
                continue;

            ass_id = raw_ass_id;
            alert("ass_id = " + ass_id);

            arr = XQuery(
                "for $l in test_learnings " +
                "where $l/person_id = " + XQueryLiteral(person_id) +
                " and $l/assessment_id = " + XQueryLiteral(ass_id) +
                " and $l/start_usage_date >= date('" + StrDate(dStartDate,false) + "')" +
                " and $l/start_usage_date <= date('" + StrDate(dFinishDate,false) + "')" +
                " return $l"
            );

            alert("Array (arr) = " + arr);
            alert("ArrayCount(arr) = " + ArrayCount(arr));

            for (l in arr)
            {
                alert("LOOP l = " + l);

                obj = new Object();

                alert("l.id = " + l.id);

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

                obj.questions = {};

                try
                {
                    alert("Открываем документ l.id = " + l.id);

                    var doc = OpenDoc(UrlFromDocID(l.id)).TopElem;

                    alert("doc открыт");

                    var annals = tools.annals_decrypt(doc);

                    alert("annals = " + annals);

                    if (annals != null)
                    {
                        alert("annals НЕ null");

                        var sections = annals.au.history.objects[0].section;

                        alert("sections = " + sections);

                        for (s in sections)
                        {
                            alert("section = " + s);

                            for (q in sections[s].question)
                            {
                                alert("question object = " + q);

                                var qid = String(q.id);
                                alert("qid = " + qid);

                                if (ArrayOptFind(aQuestions, "This.id == " + XQueryLiteral(qid)) == undefined)
                                {
                                    alert("Добавляем вопрос в aQuestions");

                                    aQuestions.push({
                                        id: qid,
                                        text: HtmlToPlainText(q.text)
                                    });
                                }

                                var qObj = new Object();

                                qObj.quest_type = (q.qtype.OptForeignElem != undefined ? q.qtype.ForeignElem.name : "");
                                qObj.result = tools_web.is_correct_question(q) ? "верно" : "неверно";

                                alert("variants = " + q.variant);

                                qObj.answer = ArrayMerge(q.variant, "HtmlToPlainText(value)", "; ");
                                qObj.correct_answer = ArrayMerge(q.variant, "HtmlToPlainText(cor_value)", "; ");

                                obj.questions[qid] = qObj;
                            }
                        }
                    }
                    else
                    {
                        alert("annals == NULL !!!");
                    }
                }
                catch(e)
                {
                    alert("ERROR annals block: " + e);
                }

                alert("obj.questions = " + obj.questions);

                aLearnings.push(obj);
            }
        }
    }

    alert("aQuestions LENGTH = " + ArrayCount(aQuestions));
    alert("aLearnings LENGTH = " + ArrayCount(aLearnings));

    var html = new Binary();

    html.AppendStr("<html><body><table border='1'>");

    html.AppendStr("<tr><td colspan='9'></td>");
    for (q in aQuestions)
    {
        html.AppendStr("<td colspan='4'><b>" + aQuestions[q].text + "</b></td>");
    }
    html.AppendStr("</tr>");

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

    for (q in aQuestions)
    {
        html.AppendStr("<td>Тип</td>");
        html.AppendStr("<td>Результат</td>");
        html.AppendStr("<td>Ответ</td>");
        html.AppendStr("<td>Правильный</td>");
    }
    html.AppendStr("</tr>");

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
        html.AppendStr("<td>" + StrDate(K.date, false, false) + "</td>");
        html.AppendStr("<td>" + K.status + "</td>");
        html.AppendStr("<td>" + K.score + "</td>");

        for (q in aQuestions)
        {
            var qid = aQuestions[q].id;
            var qData = K.questions[qid];

            if (qData == undefined)
            {
                html.AppendStr("<td></td><td></td><td></td><td></td>");
            }
            else
            {
                html.AppendStr("<td>" + qData.quest_type + "</td>");
                html.AppendStr("<td>" + qData.result + "</td>");
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
