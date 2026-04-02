function createReportString(aCollaboratorIDArray, aAssessmentIDArray, dStartDate, dFinishDate)
{
    var aLearnings = [];
    var aQuestions = [];

    // --- СБОР ДАННЫХ ---
    for (i in aCollaboratorIDArray)
    {
        var person_id = aCollaboratorIDArray[i];

        for (j in aAssessmentIDArray)
        {
            var ass_id = aAssessmentIDArray[j];

            var arr = XQuery(
                "for $l in test_learnings " +
                "where $l/person_id = " + person_id +
                " and $l/assessment_id = " + ass_id +
                " and $l/start_usage_date >= date('" + StrDate(dStartDate,false) + "')" +
                " and $l/start_usage_date <= date('" + StrDate(dFinishDate,false) + "')" +
                " return $l"
            );

            for (k in arr)
            {
                var l = arr[k];

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

                obj.questions = {};

                try
                {
                    var doc = OpenDoc(UrlFromDocID(l.id)).TopElem;
                    var annals = tools.annals_decrypt(doc);

                    if (annals != null)
                    {
                        var sections = annals.au.history.objects[0].section;

                        for (s in sections)
                        {
                            for (q in sections[s].question)
                            {
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
                }
                catch(e){}

                aLearnings.push(obj);
            }
        }
    }

    // --- HTML ---
    var html = new Binary();

    html.AppendStr("<html><meta http-equiv='Content-Type' content='text/html; charset=utf-8'/><body>");
    html.AppendStr("<table border='1' cellpadding='2' cellspacing='0'>");

    // HEADER 1
    html.AppendStr("<tr><td colspan='8'></td>");
    for (q in aQuestions)
    {
        html.AppendStr("<td colspan='4' bgcolor='#FF4433'><b>" + aQuestions[q].text + "</b></td>");
    }
    html.AppendStr("</tr>");

    // HEADER 2
    html.AppendStr("<tr>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>ФИО</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Код</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Орг</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Подразделение</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Должность</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Дата</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Статус</b></td>");
    html.AppendStr("<td bgcolor='#FFCC99'><b>Баллы</b></td>");

    for (q in aQuestions)
    {
        html.AppendStr("<td bgcolor='#FFCC99'><b>Тип</b></td>");
        html.AppendStr("<td bgcolor='#FFCC99'><b>Результат</b></td>");
        html.AppendStr("<td bgcolor='#FFCC99'><b>Ответ</b></td>");
        html.AppendStr("<td bgcolor='#FFCC99'><b>Правильный</b></td>");
    }
    html.AppendStr("</tr>");

    // DATA
    for (i in aLearnings)
    {
        var l = aLearnings[i];

        html.AppendStr("<tr valign='top'>");

        html.AppendStr("<td width='300'>" + l.person_fullname + "</td>");
        html.AppendStr("<td width='150'>" + l.person_code + "</td>");
        html.AppendStr("<td width='200'>" + l.org + "</td>");
        html.AppendStr("<td width='200'>" + l.subdivision + "</td>");
        html.AppendStr("<td width='200'>" + l.position + "</td>");
        html.AppendStr("<td width='120' align='center'>" + StrDate(l.date, true, false) + "</td>");
        html.AppendStr("<td width='100' align='center'>" + l.status + "</td>");

        var score = "";
        if (l.max_score != null)
        {
            var percent = Math.round(100 * l.score / l.max_score);
            score = l.score + " (" + percent + "%)";
        }
        else
        {
            score = l.score;
        }

        html.AppendStr("<td width='100' align='center'>" + score + "</td>");

        for (q in aQuestions)
        {
            var qid = aQuestions[q].id;
            var qData = l.questions[qid];

            if (qData == undefined)
            {
                html.AppendStr("<td></td><td></td><td></td><td></td>");
            }
            else
            {
                var bg = qData.result == "неверно" ? "#FFCCCC" : "#CCFFCC";

                html.AppendStr("<td width='200' align='center'>" + qData.quest_type + "</td>");
                html.AppendStr("<td width='80' align='center' bgcolor='" + bg + "'>" + qData.result + "</td>");
                html.AppendStr("<td width='300'>" + qData.answer + "</td>");
                html.AppendStr("<td width='300'>" + qData.correct_answer + "</td>");
            }
        }

        html.AppendStr("</tr>");
    }

    html.AppendStr("</table></body></html>");

    return html.GetStr();
}
