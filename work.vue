function createReportString(aCollaboratorIDArray, aAsessmentIDArray, dStartDate, dFinishDate){

    var reportStr = new Binary();

    reportStr.AppendStr("<html><head><meta http-equiv='Content-Type' content='text/html; charset=utf-8'/></head><body><table border='1'>");

    var aLearnings = [];
    var aQuestions = [];

    // --- СБОР ДАННЫХ ---
    for (i in aCollaboratorIDArray)
    {
        var person_id = aCollaboratorIDArray[i];

        for (j in aAsessmentIDArray)
        {
            var ass_id = aAsessmentIDArray[j];

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
                var learningDoc = OpenDoc(UrlFromDocID(l.id)).TopElem;

                var obj = new Object();

                obj.person_fullname = l.person_id.ForeignElem.fullname;
                obj.person_code = l.person_id.ForeignElem.code;
                obj.org = l.person_id.ForeignElem.org_name;
                obj.subdivision = l.person_id.ForeignElem.position_parent_name;
                obj.position = l.person_id.ForeignElem.position_name;
                obj.test_name = l.assessment_id.ForeignElem.name;
                obj.score = l.score;
                obj.max_score = l.max_score;
                obj.start_date = l.start_usage_date;
                obj.status = l.state.ForeignElem.name;

                obj.questions = {};

                var fldAnnals = tools.annals_decrypt(learningDoc);

                if (fldAnnals != null)
                {
                    var history = fldAnnals.au.history;

                    if (ArrayCount(history.objects) > 0)
                    {
                        var sections = history.objects[0].section;

                        for (s in sections)
                        {
                            for (q in s.question)
                            {
                                if (ArrayOptFind(aQuestions, "This.id == " + q.id) == undefined)
                                {
                                    aQuestions.push({
                                        id: q.id,
                                        text: HtmlToPlainText(q.text)
                                    });
                                }

                                var curQ = new Object();

                                curQ.type = (q.qtype.OptForeignElem != undefined ? q.qtype.ForeignElem.name : "");
                                curQ.result = tools_web.is_correct_question(q) ? "верно" : "неверно";
                                curQ.correct = ArrayMerge(q.variant, "HtmlToPlainText(cor_value)", "; ");
                                curQ.answer = ArrayMerge(q.variant, "HtmlToPlainText(value)", "; ");

                                obj.questions[q.id] = curQ;
                            }
                        }
                    }
                }

                aLearnings.push(obj);
            }
        }
    }

    // --- ГРУППИРОВКА ПО ТЕСТАМ ---
    var tests = ArraySelectDistinct(aLearnings, "test_name");

    for (t in tests)
    {
        var testName = tests[t];
        var rows = ArraySelect(aLearnings, "This.test_name == testName");

        // --- HEADER 1 (вопросы) ---
        reportStr.AppendStr("<tr>");
        for (i = 0; i < 9; i++) reportStr.AppendStr("<td></td>");

        for (q in aQuestions)
        {
            reportStr.AppendStr("<td colspan='4'><b>" + aQuestions[q].text + "</b></td>");
        }

        reportStr.AppendStr("</tr>");

        // --- HEADER 2 ---
        reportStr.AppendStr("<tr>");

        reportStr.AppendStr("<td><b>Пользователь</b></td>");
        reportStr.AppendStr("<td><b>Название теста</b></td>");
        reportStr.AppendStr("<td><b>Код</b></td>");
        reportStr.AppendStr("<td><b>Организация</b></td>");
        reportStr.AppendStr("<td><b>Подразделение</b></td>");
        reportStr.AppendStr("<td><b>Должность</b></td>");
        reportStr.AppendStr("<td><b>Дата</b></td>");
        reportStr.AppendStr("<td><b>Статус</b></td>");
        reportStr.AppendStr("<td><b>Баллы</b></td>");

        for (q in aQuestions)
        {
            reportStr.AppendStr("<td><b>Тип</b></td>");
            reportStr.AppendStr("<td><b>Результат</b></td>");
            reportStr.AppendStr("<td><b>Полученный ответ</b></td>");
            reportStr.AppendStr("<td><b>Правильный ответ</b></td>");
        }

        reportStr.AppendStr("</tr>");

        // --- ДАННЫЕ ---
        for (r in rows)
        {
            var l = rows[r];

            var percent = (l.max_score > 0) ? Math.round(l.score / l.max_score * 100) : 0;

            reportStr.AppendStr("<tr>");

            reportStr.AppendStr("<td>" + l.person_fullname + "</td>");
            reportStr.AppendStr("<td>" + l.test_name + "</td>");
            reportStr.AppendStr("<td>" + l.person_code + "</td>");
            reportStr.AppendStr("<td>" + l.org + "</td>");
            reportStr.AppendStr("<td>" + l.subdivision + "</td>");
            reportStr.AppendStr("<td>" + l.position + "</td>");
            reportStr.AppendStr("<td>" + StrDate(l.start_date, true, false) + "</td>");
            reportStr.AppendStr("<td>" + l.status + "</td>");
            reportStr.AppendStr("<td>" + l.score + " (" + percent + "%)</td>");

            for (q in aQuestions)
            {
                var qid = aQuestions[q].id;
                var curQ = l.questions[qid];

                if (curQ == undefined)
                {
                    reportStr.AppendStr("<td></td><td></td><td></td><td></td>");
                }
                else
                {
                    reportStr.AppendStr("<td>" + curQ.type + "</td>");
                    reportStr.AppendStr("<td>" + curQ.result + "</td>");
                    reportStr.AppendStr("<td>" + curQ.answer + "</td>");
                    reportStr.AppendStr("<td>" + curQ.correct + "</td>");
                }
            }

            reportStr.AppendStr("</tr>");
        }

        // --- ПУСТАЯ СТРОКА (как в эталоне) ---
        reportStr.AppendStr("<tr><td colspan='100'></td></tr>");
    }

    reportStr.AppendStr("</table></body></html>");

    return reportStr.GetStr();
}
