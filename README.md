<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <title>生命密碼：星空專業分析系統</title>
    <style>
        :root { 
            --main: #4834d4; 
            --accent: #eb4d4b; 
            --warn: #ffdadb; 
        }
        
        body { 
            font-family: "Microsoft JhengHei", sans-serif; 
            /* 深藍色星空漸變背景 */
            background: radial-gradient(circle at center, #1b2735 0%, #090a0f 100%);
            background-attachment: fixed;
            color: #fff; /* 全域文字改為白色以對應深色背景 */
            padding: 20px; 
            display: flex; 
            justify-content: center;
            min-height: 100vh;
            margin: 0;
        }

        /* 增加星星點綴感 */
        body::before {
            content: "";
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-image: radial-gradient(white, rgba(255,255,255,.2) 1px, transparent 20px);
            background-size: 100px 100px;
            opacity: 0.2;
            z-index: -1;
        }

        .card { 
            background: rgba(255, 255, 255, 0.1); /* 半透明背景 */
            backdrop-filter: blur(15px); /* 磨砂玻璃效果 */
            padding: 30px; 
            border-radius: 20px; 
            box-shadow: 0 20px 50px rgba(0,0,0,0.5); 
            width: 100%; 
            max-width: 900px; 
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .input-group { 
            display: flex; 
            gap: 15px; 
            justify-content: center; 
            margin-bottom: 25px; 
            align-items: flex-end; 
            background: rgba(255, 255, 255, 0.1); 
            padding: 15px; 
            border-radius: 12px; 
        }
        .input-item { display: flex; flex-direction: column; gap: 5px; font-size: 0.9rem; font-weight: bold; }
        input { padding: 10px; border-radius: 8px; border: 1px solid rgba(255,255,255,0.3); width: 150px; background: rgba(0,0,0,0.2); color: white; }
        
        button { background: #5f27cd; color: white; border: none; padding: 10px 25px; border-radius: 8px; cursor: pointer; font-weight: bold; height: 40px; transition: 0.3s; }
        button:hover { background: #341f97; transform: translateY(-2px); box-shadow: 0 5px 15px rgba(95, 39, 205, 0.4); }
        
        .people-grid { display: grid; grid-template-columns: 1fr; gap: 30px; }
        .people-grid.dual { grid-template-columns: 1fr 1fr; }
        .person-box { border: 1px solid rgba(255, 255, 255, 0.1); padding: 20px; border-radius: 15px; background: rgba(255, 255, 255, 0.05); }
        .name-tag { display: inline-block; padding: 4px 12px; background: #5f27cd; color: white; border-radius: 20px; font-size: 0.8rem; margin-bottom: 15px; }

        /* 沙漏主盤 */
        .map { display: flex; flex-direction: column; align-items: center; margin-bottom: 20px; }
        .row { display: flex; gap: 6px; margin: 3px 0; }
        .box { width: 40px; height: 40px; border: 1.5px solid rgba(255,255,255,0.5); display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 1.1rem; background: rgba(0,0,0,0.2); }
        .main-num { border: 3px solid #ff4757; color: #ff4757; font-size: 1.3rem; }

        /* 伴侶位等 */
        .extra-bits { display: flex; justify-content: space-between; align-items: flex-start; margin-top: 10px; }
        .bit-group { text-align: center; width: 100px; }
        .bit-title { font-size: 0.75rem; color: #ccc; margin-top: 5px; }
        .mini-row { display: flex; gap: 3px; justify-content: center; }
        .mini-box { width: 30px; height: 30px; border: 1px solid rgba(255,255,255,0.4); display: flex; align-items: center; justify-content: center; font-size: 0.9rem; font-weight: bold; background: rgba(0,0,0,0.2); }

        /* 五行表 */
        .el-table { width: 100%; border-collapse: collapse; margin-top: 20px; text-align: center; }
        .el-table th { background: rgba(255,255,255,0.1); padding: 8px; border: 1px solid rgba(255,255,255,0.2); font-size: 0.8rem; }
        .el-table td { border: 1px solid rgba(255,255,255,0.2); padding: 10px; font-weight: bold; font-size: 1.1rem; }
        .low-score { background-color: rgba(255, 71, 87, 0.3) !important; color: #ff6b81; } /* 少於3分淡紅 */

        .crystal-info { margin-top: 15px; padding: 12px; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(255, 255, 255, 0.2); border-radius: 8px; font-size: 0.85rem; color: #f1f2f6; }
        .per-desc { margin-top: 10px; line-height: 1.6; color: #dfe4ea; font-size: 0.85rem; border-top: 1px solid rgba(255,255,255,0.1); padding-top: 10px; }

        .match-section { background: rgba(95, 39, 205, 0.2); padding: 25px; border-radius: 15px; text-align: center; border: 2px dashed rgba(255,255,255,0.3); margin-top: 25px; }
        .match-score { font-size: 2.8rem; font-weight: bold; color: #a29bfe; margin: 5px 0; }
    </style>
</head>
<body>

<div class="card">
    <h2 style="text-align:center; margin-top:0;">✨ 生命密碼星空分析 ✨</h2>
    <div class="input-group">
        <div class="input-item">生日 A (必填)<input type="date" id="date1"></div>
        <div class="input-item">生日 B (選填)<input type="date" id="date2"></div>
        <button onclick="startAnalysis()">啟動完整分析</button>
    </div>

    <div id="resultsArea" style="display:none;">
        <div id="pGrid" class="people-grid">
            <div class="person-box" id="p1Box">
                <div class="name-tag">分析對象 A</div>
                <div id="map1" class="map"></div>
                <div class="extra-bits">
                    <div class="bit-group"><div id="partner1" class="mini-row"></div><div class="bit-title">伴侶位 (左下)</div></div>
                    <div class="bit-group"><div id="child1" class="mini-row"></div><div class="bit-title">子女位 (中)</div></div>
                    <div class="bit-group"><div id="future1" class="mini-row"></div><div class="bit-title">晚年位 (右下)</div></div>
                </div>
                <table id="table1" class="el-table"></table>
                <div id="cry1" class="crystal-info"></div>
                <div id="desc1" class="per-desc"></div>
            </div>

            <div class="person-box" id="p2Box" style="display:none;">
                <div class="name-tag">分析對象 B</div>
                <div id="map2" class="map"></div>
                <div class="extra-bits">
                    <div class="bit-group"><div id="partner2" class="mini-row"></div><div class="bit-title">伴侶位 (左下)</div></div>
                    <div class="bit-group"><div id="child2" class="mini-row"></div><div class="bit-title">子女位 (中)</div></div>
                    <div class="bit-group"><div id="future2" class="mini-row"></div><div class="bit-title">晚年位 (右下)</div></div>
                </div>
                <table id="table2" class="el-table"></table>
                <div id="cry2" class="crystal-info"></div>
                <div id="desc2" class="per-desc"></div>
            </div>
        </div>

        <div id="matchCard" class="match-section" style="display:none;">
            <h3 style="margin:0;">💖 雙人契合度分析</h3>
            <div id="scoreVal" class="match-score">0分</div>
            <div id="matchMsg" style="font-size: 0.95rem; color: #dfe4ea; padding: 0 15px;"></div>
        </div>
    </div>
</div>

<script>
// 核心邏輯保持不變
const CRYSTAL_MAP = { '金': '白水晶、鈦晶', '木': '綠幽靈、天河石', '水': '黑曜石、海藍寶', '火': '紫水晶、粉晶', '土': '黃水晶、茶晶' };
const PERSONALITY = {
    1: "【1號領袖】獨立具開創性。天生領導者但易自我，需學習團隊合作與傾聽。",
    2: "【2號溝通】溫和具同理心。擅長協調但易優柔寡斷，需建立自信與果斷力。",
    3: "【3號創意】熱情極具活力。行動派但情緒起伏大，需培養耐性與穩定心態。",
    4: "【4號穩定】務實守規、謹慎。極佳執行者但易固執，需嘗試打破框框接受改變。",
    5: "【5號冒險】嚮往自由、口才佳。適應力強但欠缺專注，需建立規律與毅力。",
    6: "【6號責任】愛心、追求完美。責任感強但易給人壓力，需學會放鬆與包容缺憾。",
    7: "【7號博學】愛鑽研、洞察力。智慧型但易顯孤僻，需開放心胸與外界交流。",
    8: "【8號成就】野心家、具威信。追求成功但掌控欲強，需平衡物質與心靈成長。",
    9: "【9號夢想】博愛、樂觀大方。理想家但易脫離現實，需加強務實執行力。"
};

function getS(n) {
    let s = String(n).split('').reduce((a,b)=>Number(a)+Number(b),0);
    return s > 9 ? getS(s) : s;
}

function solve(dateStr) {
    const [y, m, d] = dateStr.split('-');
    let n1=getS(d), n2=getS(m), n3=getS(y.slice(0,2)), n4=getS(y.slice(2,4));
    let n5=getS(n1+n2), n6=getS(n3+n4), n7=getS(n5+n6);
    let n8=getS(n6+n7), n9=getS(n5+n7), n10=getS(n8+n9);
    let p3=getS(n1+n5), p2=getS(n2+n5), p1=getS(p2+p3);
    let e1=getS(n3+n6), e2=getS(n4+n6), e3=getS(e1+e2);

    let list = [n1,n2,n3,n4,n5,n6,n7,n8,n9,n10,p1,p2,p3,e1,e2,e3];
    let sc = {"金":0,"木":0,"水":0,"火":0,"土":0};
    const ref = {'1':'金','6':'金','4':'木','9':'木','2':'水','7':'水','3':'火','8':'火','5':'土','0':'土'};
    list.forEach(x => { if(ref[x]) sc[ref[x]]++; });

    const bx = (v, c='') => `<div class="box ${c}">${v}</div>`;
    const mbx = (v) => `<div class="mini-box">${v}</div>`;

    return {
        n7, sc,
        mainUI: `<div class="row">${bx(n1)+bx(n2)+bx(n3)+bx(n4)}</div><div class="row">${bx(n5)+bx(n6)}</div><div class="row">${bx(n7,'main-num')}</div><div class="row">${bx(n8)+bx(n9)}</div>`,
        childUI: bx(n10),
        partnerUI: mbx(p1)+mbx(p2)+mbx(p3),
        futureUI: mbx(e1)+mbx(e2)+mbx(e3)
    };
}

function startAnalysis() {
    const d1 = document.getElementById('date1').value;
    const d2 = document.getElementById('date2').value;
    if(!d1) { alert("請輸入生日 A"); return; }
    const res1 = solve(d1);
    render(1, res1);
    if(d2) {
        const res2 = solve(d2);
        render(2, res2);
        document.getElementById('pGrid').classList.add('dual');
        document.getElementById('p2Box').style.display = 'block';
        document.getElementById('matchCard').style.display = 'block';
        const diff = Math.abs(res1.n7 - res2.n7);
        let score = 100 - (diff * 8);
        if(res1.n7 === res2.n7) score = 96;
        document.getElementById('scoreVal').innerText = score + "分";
        document.getElementById('matchMsg').innerText = `在這片星空下，${res1.n7}號與${res2.n7}號相遇。這是一段契合度極高的緣分，透過理解彼此能量盈缺，可以達成持久的和諧共處。`;
    } else {
        document.getElementById('pGrid').classList.remove('dual');
        document.getElementById('p2Box').style.display = 'none';
        document.getElementById('matchCard').style.display = 'none';
    }
    document.getElementById('resultsArea').style.display = 'block';
}

function render(id, res) {
    document.getElementById(`map${id}`).innerHTML = res.mainUI;
    document.getElementById(`child${id}`).innerHTML = res.childUI;
    document.getElementById(`partner${id}`).innerHTML = res.partnerUI;
    document.getElementById(`future${id}`).innerHTML = res.futureUI;
    document.getElementById(`desc${id}`).innerText = PERSONALITY[res.n7];
    let low = [];
    let table = `<tr><th>金(1,6)</th><th>木(4,9)</th><th>水(2,7)</th><th>火(3,8)</th><th>土(5,0)</th></tr><tr>`;
    ["金","木","水","火","土"].forEach(k => {
        let v = res.sc[k];
        let cls = v < 3 ? 'low-score' : '';
        if(v < 3) low.push(k);
        table += `<td class="${cls}">${v}</td>`;
    });
    document.getElementById(`table${id}`).innerHTML = table + `</tr>`;
    document.getElementById(`cry${id}`).innerHTML = low.length > 0 ? `💎 <b>建議補充：</b>${low.map(k => `${k}(${CRYSTAL_MAP[k]})`).join('、')}` : "✨ 五行能量平衡穩定。";
}
</script>
</body>
</html>
