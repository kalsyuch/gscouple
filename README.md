<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Bias/Preference Sorter</title>
  <link rel="icon" href="data:," />
  <style>
    :root {
      --accent: #e097d9;
      --border: #000;
      --text: #000;
      --bg: #fff;
    }
    html, body {
      margin: 0;
      padding: 0;
      background: var(--bg);
      color: var(--text);
      font-family: "Times New Roman", serif;
    }
    .wrap {
      max-width: 640px;
      margin: 0 auto;
      padding: 24px 16px 48px;
      text-align: center;
    }
    h1 {
      margin: 0 0 8px;
      font-size: 28px;
      line-height: 1.2;
    }
    p.notice {
      margin: 0 0 20px;
      font-size: 14px;
      color: #444;
    }

    #mainTable{
      font-size: 19px;
      text-align: center;
      vertical-align: middle;
      width: 410px;
      margin: 0 auto;
      border-collapse: separate;
      border-spacing: 10px 5px;
    }
    #battleNumber{
      padding-bottom: 10px;
    }
    #leftField, #rightField{
      width: 150px;
      height: 150px;
      border: 1px solid var(--border);
      cursor: pointer;
      vertical-align: middle;
      padding: 8px;
      word-wrap: break-word;
    }

    #resultField table{
      width: 260px;
      font-size: 18px;
      line-height: 120%;
      margin: 12px auto 0;
      border: 1px solid var(--border);
      border-collapse: collapse;
    }
    #resultField th{
      color: #fff;
      background: var(--accent);
      text-align: center;
      border: 1px solid var(--border);
      padding: 6px 4px;
    }
    #resultField td{
      border: 1px solid var(--border);
      padding: 6px 4px;
    }

    .controls {
      margin-top: 16px;
      display: grid;
      gap: 8px;
      grid-template-columns: 1fr 1fr;
      max-width: 410px;
      margin-left: auto;
      margin-right: auto;
    }
    button{
      font-size: 16px;
      padding: 10px 12px;
      border: 1px solid var(--border);
      background: #f6f6f6;
      cursor: pointer;
    }
    button:active { transform: translateY(1px); }
  </style>
</head>
<body>
  <div class="wrap">
    <h1 id="sorterTitle">SORTER NAME</h1>
    <p class="notice">
      Pick who you like better in each battle to get an accurate ranked list.
    </p>

    <table id="mainTable" role="presentation" aria-live="polite">
      <tr>
        <td id="battleNumber" colspan="2"><b>battle #1<br>0% sorted.</b></td>
      </tr>
      <tr>
        <td id="leftField" onclick="if(finishFlag===0) sortList(-1);"></td>
        <td id="rightField" onclick="if(finishFlag===0) sortList(1);"></td>
      </tr>
    </table>

    <div class="controls">
      <button onclick="if(finishFlag===0) sortList(-1)">← Left is better</button>
      <button onclick="if(finishFlag===0) sortList(1)">Right is better →</button>
    </div>

    <div id="resultField" style="text-align:center;"></div>
  </div>

  <script>
  // =============== Customize this list =================
  var namMember = [
    "option 1",
    "option 2",
    "option 3",
    "option 4",
    "option 5"
  ];
  document.getElementById("sorterTitle").textContent = "Bias/Preference Sorter";
  // =====================================================

  var lstMember = [];
  var parent = [];
  var equal = [];
  var rec = [];
  var cmp1, cmp2;
  var head1, head2;
  var nrec;

  var numQuestion;
  var totalSize;
  var finishSize;
  var finishFlag;

  function initList(){
    var n = 0;
    var mid;
    var i;

    lstMember[n] = [];
    for (i=0; i<namMember.length; i++) lstMember[n][i] = i;
    parent[n] = -1;
    totalSize = 0;
    n++;

    for (i=0; i<lstMember.length; i++) {
      if (lstMember[i].length >= 2) {
        mid = Math.ceil(lstMember[i].length/2);

        lstMember[n] = lstMember[i].slice(0, mid);
        totalSize += lstMember[n].length;
        parent[n] = i; n++;

        lstMember[n] = lstMember[i].slice(mid);
        totalSize += lstMember[n].length;
        parent[n] = i; n++;
      }
    }

    for (i=0; i<namMember.length; i++) rec[i] = 0;
    nrec = 0;

    for (i=0; i<=namMember.length; i++) equal[i] = -1;

    cmp1 = lstMember.length - 2;
    cmp2 = lstMember.length - 1;
    head1 = 0;
    head2 = 0;
    numQuestion = 1;
    finishSize = 0;
    finishFlag = 0;
  }

  // only -1 (left) or 1 (right)
  function sortList(flag){
    var i, str;
    if (flag < 0) {
      rec[nrec] = lstMember[cmp1][head1]; head1++; nrec++; finishSize++;
    } else if (flag > 0) {
      rec[nrec] = lstMember[cmp2][head2]; head2++; nrec++; finishSize++;
    }

    if (head1 < lstMember[cmp1].length && head2 === lstMember[cmp2].length) {
      while (head1 < lstMember[cmp1].length){
        rec[nrec] = lstMember[cmp1][head1]; head1++; nrec++; finishSize++;
      }
    } else if (head1 === lstMember[cmp1].length && head2 < lstMember[cmp2].length) {
      while (head2 < lstMember[cmp2].length){
        rec[nrec] = lstMember[cmp2][head2]; head2++; nrec++; finishSize++;
      }
    }

    if (head1 === lstMember[cmp1].length && head2 === lstMember[cmp2].length) {
      for (i=0; i<lstMember[cmp1].length + lstMember[cmp2].length; i++) {
        lstMember[parent[cmp1]][i] = rec[i];
      }
      lstMember.pop();
      lstMember.pop();
      cmp1 -= 2;
      cmp2 -= 2;
      head1 = 0;
      head2 = 0;

      if (head1 === 0 && head2 === 0) {
        for (i=0; i<namMember.length; i++) rec[i] = 0;
        nrec = 0;
      }
    }

    if (cmp1 < 0) {
      str = "battle #" + (numQuestion-1) + "<br>" + Math.floor(finishSize*100/totalSize) + "% sorted.";
      document.getElementById("battleNumber").innerHTML = str;
      showResult();
      finishFlag = 1;
    } else {
      showImage();
    }
  }

  function showResult() {
    var ranking = 1;
    var sameRank = 1;
    var str = "";
    var i;

    str += '<table aria-label="Results">';
    str += '<tr><th>rank</th><th>option</th></tr>';

    for (i=0; i<namMember.length; i++) {
      str += '<tr><td style="text-align:center;">' + ranking + '</td><td>' + escapeHTML(namMember[lstMember[0][i]]) + '</td></tr>';
      if (i < namMember.length-1) {
        ranking++;
      }
    }
    str += '</table>';
    document.getElementById("resultField").innerHTML = str;
  }

  function showImage() {
    var str0 = "battle #" + numQuestion + "<br>" + Math.floor(finishSize*100/totalSize) + "% sorted.";
    var str1 = toNameFace(lstMember[cmp1][head1]);
    var str2 = toNameFace(lstMember[cmp2][head2]);
    document.getElementById("battleNumber").innerHTML = str0;
    document.getElementById("leftField").innerHTML = str1;
    document.getElementById("rightField").innerHTML = str2;
    numQuestion++;
  }

  function toNameFace(n){
    return escapeHTML(namMember[n]);
  }

  function escapeHTML(s){
    return String(s)
      .replace(/&/g,'&amp;')
      .replace(/</g,'&lt;')
      .replace(/>/g,'&gt;')
      .replace(/"/g,'&quot;')
      .replace(/'/g,'&#39;');
  }

  initList();
  showImage();
  </script>
</body>
</html>
