// --- МАПЫ ---
var correctMap = {};
var questionTextMap = {};
var variantTextMap = {};
var userAnswerMap = {};

// --- 1. Парсим qti_text (вопросы + правильные ответы) ---
try
{
    var items = doc.qti_text.questestinterop.assessment.section.item;

    for (var iItem = 0; iItem < ArrayCount(items); iItem++)
    {
        var item = items[iItem];
        var qid = item.ident;

        // текст вопроса
        var qText = "";
        if (item.presentation.material.mattext != undefined)
            qText = HtmlToPlainText(item.presentation.material.mattext);

        questionTextMap[qid] = qText;

        // добавляем в общий список вопросов (для заголовка)
        if (ArrayOptFind(aQuestions, "This.id == '" + qid + "'") == undefined)
        {
            aQuestions.push({
                id: qid,
                text: qText
            });
        }

        // варианты ответов
        var variants = item.presentation.response_lid.render_choice.response_label;

        for (var v = 0; v < ArrayCount(variants); v++)
        {
            var variant = variants[v];
            var vid = variant.ident;

            var vText = HtmlToPlainText(variant.material.mattext);
            variantTextMap[vid] = vText;

            // правильный ответ
            if (variant.ws_right == "1")
            {
                correctMap[qid] = vid;
            }
        }
    }
}
catch(e){}

// --- 2. Парсим ответы пользователя ---
try
{
    var itemsResult = doc.objects.object.data.items.item;

    for (var iRes = 0; iRes < ArrayCount(itemsResult); iRes++)
    {
        var it = itemsResult[iRes];
        var qid = it.ident;

        var raw = it.attempts.attempt;

        if (raw != undefined && raw != "")
        {
            var selected = StrReplace(raw, "\n", "");
            selected = Trim(selected);

            if (StrContains(selected, "[.]"))
            {
                selected = selected.split("[.]")[1];
                userAnswerMap[qid] = selected;
            }
        }
    }
}
catch(e){}

// --- 3. Собираем вопросы в obj.questions ---
for (var qid in questionTextMap)
{
    var qObj = new Object();

    var userVid = userAnswerMap[qid];
    var correctVid = correctMap[qid];

    qObj.quest_type = ""; // можно доработать при желании

    qObj.answer = (userVid != undefined ? variantTextMap[userVid] : "");
    qObj.correct_answer = (correctVid != undefined ? variantTextMap[correctVid] : "");

    if (userVid == undefined)
        qObj.result = "";
    else
        qObj.result = (userVid == correctVid ? "верно" : "неверно");

    obj.questions[qid] = qObj;
}
