var html = new Binary();

html.AppendStr("<html xmlns:o=\"urn:schemas-microsoft-com:office:office\" xmlns:x=\"urn:schemas-microsoft-com:office:excel\" xmlns=\"http://www.w3.org/TR/REC-html40\">" +
"<head>" +
"<meta http-equiv=\"Content-Type\" content=\"text/html; charset=utf-8\"/>" +
"<meta name=\"ProgId\" content=\"Excel.Sheet\"/>" +
"<meta name=\"Generator\" content=\"Microsoft Excel 11\"/>" +
"<style>td.two-decimals {mso-number-format: \"0\\.00\"}</style>" +
"</head><body><table border=\"1\" cellpadding=\"2\" cellspacing=\"0\">");

// --- Заголовок вопросов ---
html.AppendStr("<tr><td colspan='9'></td>");
for (q in aQuestions)
{
    html.AppendStr("<td colspan='4' bgcolor='#EA9999'><b>" + q.text + "</b></td>");
}
html.AppendStr("</tr>");

// --- Шапка ---
html.AppendStr("<tr>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Пользователь</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Название теста</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Код</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Организация</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Подразделение</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Должность</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Дата</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Статус</b></td>");
html.AppendStr("<td bgcolor='#FCE5CD'><b>Баллы</b></td>");

for (q in aQuestions)
{
    html.AppendStr("<td bgcolor='#FCE5CD'><b>Тип</b></td>");
    html.AppendStr("<td bgcolor='#FCE5CD'><b>Результат</b></td>");
    html.AppendStr("<td bgcolor='#FCE5CD'><b>Полученный ответ</b></td>");
    html.AppendStr("<td bgcolor='#FCE5CD'><b>Правильный ответ</b></td>");
}
html.AppendStr("</tr>");

// --- Данные ---
var K, score, percent;
  
for (var c = 0; c < ArrayCount(aLearnings); c++)
{
    K = aLearnings[c];

    html.AppendStr("<tr valign='top'>");

    html.AppendStr("<td width='250'>" + K.person_fullname + "</td>");
    html.AppendStr("<td width='200'>" + K.assessment_name + "</td>");
    html.AppendStr("<td width='100'>" + K.person_code + "</td>");
    html.AppendStr("<td width='150'>" + K.org + "</td>");
    html.AppendStr("<td width='150'>" + K.subdivision + "</td>");
    html.AppendStr("<td width='150'>" + K.position + "</td>");
    html.AppendStr("<td width='110' align='center'>" + StrDate(K.date, false, false) + "</td>");
    html.AppendStr("<td width='100' align='center'>" + K.status + "</td>");

     score = "";
    if (K.max_score != null && K.max_score != 0)
    {
         percent = Math.round(100 * K.score / K.max_score);
        score = K.score + " (" + percent + "%)";
    }
    else
    {
        score = K.score;
    }

    html.AppendStr("<td width='100' align='center'>" + score + "</td>");

    for (q in aQuestions)
    {
        qid = q.id;
        qData = K.questions[qid];

        if (qData == undefined)
        {
            html.AppendStr("<td></td><td></td><td></td><td></td>");
        }
        else
        {
            bg = qData.result == "неверно" ? "#F4CCCC" : "#D9EAD3";

            html.AppendStr("<td width='150'>" + qData.quest_type + "</td>");
            html.AppendStr("<td width='80' align='center' bgcolor='" + bg + "'>" + qData.result + "</td>");
            html.AppendStr("<td width='250'>" + qData.answer + "</td>");
            html.AppendStr("<td width='250'>" + qData.correct_answer + "</td>");
        }
    }

    html.AppendStr("</tr>");
}

html.AppendStr("</table></body></html>");
