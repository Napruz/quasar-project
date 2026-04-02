function createReportString(){

    var reportStr = new Binary();

    reportStr.AppendStr("<html><meta http-equiv='Content-Type' content='text/html; charset=utf-8'/><body>");
    reportStr.AppendStr("<table border='1' cellpadding='2' cellspacing='0'>");

    // --- HEADER 1 ---
    reportStr.AppendStr("<tr>");
    reportStr.AppendStr("<td colspan='8'></td>");

    for (q in Ps.questions)
    {
        var fldQuestionElem = Ps.questions[q];
        reportStr.AppendStr("<td align='center' colspan='4' bgcolor='#FF4433'><b>" + fldQuestionElem.text + "</b></td>");
    }

    reportStr.AppendStr("</tr>");

    // --- HEADER 2 ---
    reportStr.AppendStr("<tr>");

    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>ФИО</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Код</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Орг</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Подразделение</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Должность</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Дата</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Статус</b></td>");
    reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Баллы</b></td>");

    for (q in Ps.questions)
    {
        reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Тип</b></td>");
        reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Результат</b></td>");
        reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Ответ</b></td>");
        reportStr.AppendStr("<td bgcolor='#FFCC99'><b>Правильный</b></td>");
    }

    reportStr.AppendStr("</tr>");

    // --- DATA ---
    for (l in Ps.learnings)
    {
        try
        {
            var fldLearningElem = Ps.learnings[l];

            reportStr.AppendStr("<tr valign='top'>");

            reportStr.AppendStr("<td width='300'>" + fldLearningElem.person_fullname + "</td>");
            reportStr.AppendStr("<td width='150'>" + fldLearningElem.person_code + "</td>");
            reportStr.AppendStr("<td width='200'>" + fldLearningElem.person_id.ForeignElem.org_name + "</td>");

            var subd = TopElem.disp_person_list_staff
                ? fldLearningElem.person_list_staff
                : fldLearningElem.person_subdivision_name;

            reportStr.AppendStr("<td width='200'>" + subd + "</td>");
            reportStr.AppendStr("<td width='200'>" + fldLearningElem.person_position_name + "</td>");
            reportStr.AppendStr("<td width='120' align='center'>" + StrDate(fldLearningElem.start_usage_date, true, false) + "</td>");
            reportStr.AppendStr("<td width='100' align='center'>" + fldLearningElem.state_id.ForeignElem.name + "</td>");

            var scoreStr = "";
            try {
                if (fldLearningElem.max_score != null)
                {
                    var percent = (100 * fldLearningElem.score) / fldLearningElem.max_score;
                    scoreStr = fldLearningElem.score + " (" + percent + "%)";
                }
                else
                {
                    scoreStr = fldLearningElem.score;
                }
            } catch(e){}

            reportStr.AppendStr("<td width='100' align='center'>" + scoreStr + "</td>");

            // --- QUESTIONS ---
            for (q in Ps.questions)
            {
                var fldQuestionElem = Ps.questions[q];
                var fldQuestionChild = fldLearningElem.questions.GetOptChildByKey(fldQuestionElem.PrimaryKey);

                if (fldQuestionChild == undefined)
                {
                    reportStr.AppendStr("<td></td><td></td><td></td><td></td>");
                }
                else
                {
                    var bg = "";

                    if (fldQuestionChild.result == "неверно")
                        bg = "#FFCCCC";
                    else if (fldQuestionChild.result == "верно")
                        bg = "#CCFFCC";

                    reportStr.AppendStr("<td width='200' align='center'>" + fldQuestionChild.quest_type + "</td>");
                    reportStr.AppendStr("<td width='80' align='center' bgcolor='" + bg + "'>" + fldQuestionChild.result + "</td>");
                    reportStr.AppendStr("<td width='300'>" + fldQuestionChild.answer + "</td>");

                    var correct = fldQuestionChild.correct_answer;
                    if (StrBegins(correct, "="))
                        correct = StrRightRangePos(correct, 1);

                    reportStr.AppendStr("<td width='300'>" + correct + "</td>");
                }
            }

            reportStr.AppendStr("</tr>");
        }
        catch(e)
        {
            alert(e);
        }
    }

    reportStr.AppendStr("</table></body></html>");

    return reportStr.GetStr();
}
