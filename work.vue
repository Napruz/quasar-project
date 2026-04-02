for (var li = 0; li < learnings.length; li++) {
    var l = learnings[li];
    var percent = (l.max_score > 0) ? StrReal((l.score / l.max_score) * 100, 1) : "0";
    var scoreDisplay = l.score + (l.max_score ? " (" + percent + "%)" : "");

    reportStr.AppendStr("<tr valign='top'>");
    // ... остальные ячейки ...
}
