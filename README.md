<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>ダバオ養鶏 損益シミュレーター / Davao Poultry Profit Simulator</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Zen+Kaku+Gothic+New:wght@400;500;700;900&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
:root{
  --paper:#E9ECE6; --card:#F8F9F6; --ink:#16211C; --ink-soft:#5C6862;
  --line:#CBD2C8; --line-soft:#DDE2D9;
  --yolk:#F0A81E; --profit:#2E7D5B; --loss:#B03A2A;
  --bar-h:82px;
}
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
html{-webkit-text-size-adjust:100%}
body{
  background:var(--paper); color:var(--ink);
  font-family:'Zen Kaku Gothic New',system-ui,sans-serif;
  font-size:15px; line-height:1.7;
  padding-bottom:calc(var(--bar-h) + env(safe-area-inset-bottom) + 28px);
}
.num{font-family:'IBM Plex Mono',ui-monospace,monospace;font-variant-numeric:tabular-nums}
.wrap{max-width:660px;margin:0 auto;padding:0 16px}

.hero{background:var(--ink);color:var(--paper);padding:24px 0 22px;margin-bottom:18px}
.hero-top{display:flex;align-items:flex-start;justify-content:space-between;gap:14px}
.eyebrow{font-family:'IBM Plex Mono',monospace;font-size:10.5px;letter-spacing:.22em;color:var(--yolk);text-transform:uppercase;margin-bottom:8px}
.hero h1{font-size:23px;font-weight:900;line-height:1.35;letter-spacing:-.01em}
.hero p{font-size:12.5px;color:#A8B4AC;margin-top:9px;line-height:1.6;max-width:46em}
.lang{display:flex;flex-shrink:0;border:1.5px solid #3B4A42;border-radius:8px;overflow:hidden;margin-top:2px}
.lang button{padding:6px 11px;background:transparent;border:none;cursor:pointer;font-family:'IBM Plex Mono',monospace;font-size:11px;font-weight:600;letter-spacing:.06em;color:#8C9A92}
.lang button[aria-pressed="true"]{background:var(--yolk);color:var(--ink)}

/* 一生の流れ */
.life{display:flex;align-items:stretch;gap:0;margin-bottom:22px;border:1.5px solid var(--line);border-radius:11px;overflow:hidden;background:var(--card)}
.life-seg{padding:10px 11px;display:flex;flex-direction:column;justify-content:center;min-width:0;transition:flex-grow .3s ease}
.life-seg + .life-seg{border-left:1.5px solid var(--line)}
.life-k{font-family:'IBM Plex Mono',monospace;font-size:9px;letter-spacing:.12em;color:var(--ink-soft)}
.life-v{font-size:12.5px;font-weight:700;line-height:1.35;margin-top:2px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.life-seg.rear{background:#E3E7DF}
.life-seg.lay{background:#FEF6E5}
.life-seg.sell{background:var(--ink);color:var(--paper);flex:0 0 auto}
.life-seg.sell .life-k{color:var(--yolk)}

.sec{margin-bottom:22px}
.sec-title{display:flex;align-items:baseline;gap:9px;padding-bottom:7px;margin-bottom:14px;border-bottom:1.5px solid var(--ink)}
.sec-title h2{font-size:14px;font-weight:900;letter-spacing:.02em}
.sec-title span{font-family:'IBM Plex Mono',monospace;font-size:10px;color:var(--ink-soft);letter-spacing:.1em;margin-left:auto;white-space:nowrap}

.field{background:var(--card);border:1px solid var(--line-soft);border-radius:11px;padding:12px 13px 11px;margin-bottom:9px}
.field.key{border:1.5px solid var(--yolk);background:#FEF6E5}
.f-head{display:flex;align-items:center;justify-content:space-between;gap:10px}
.f-label{font-size:13.5px;font-weight:700;line-height:1.4}
.f-in{display:flex;align-items:baseline;gap:4px;flex-shrink:0}
.f-in input[type=text]{width:88px;padding:6px 8px;text-align:right;border:1.5px solid var(--line);border-radius:7px;background:#fff;color:var(--ink);font-family:'IBM Plex Mono',monospace;font-size:16px;font-weight:600}
.f-in input[type=text]:focus{outline:none;border-color:var(--yolk);box-shadow:0 0 0 3px rgba(240,168,30,.22)}
.f-unit{font-size:11.5px;color:var(--ink-soft);font-weight:500;min-width:30px}
.f-hint{font-size:11px;color:var(--ink-soft);margin-top:7px;line-height:1.55}
input[type=range]{-webkit-appearance:none;appearance:none;width:100%;height:22px;margin-top:9px;background:transparent}
input[type=range]::-webkit-slider-runnable-track{height:3px;background:var(--line);border-radius:2px}
input[type=range]::-moz-range-track{height:3px;background:var(--line);border-radius:2px}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:22px;height:22px;border-radius:50%;background:var(--ink);border:4px solid var(--yolk);margin-top:-9.5px;cursor:pointer}
input[type=range]::-moz-range-thumb{width:22px;height:22px;border-radius:50%;background:var(--ink);border:4px solid var(--yolk);cursor:pointer}
input[type=range]:focus-visible::-webkit-slider-thumb{box-shadow:0 0 0 4px rgba(240,168,30,.35)}

.tbl{background:var(--card);border:1px solid var(--line-soft);border-radius:11px;overflow:hidden}
.row{display:flex;justify-content:space-between;align-items:center;gap:12px;padding:11px 14px;border-bottom:1px solid var(--line-soft)}
.row:last-child{border-bottom:none}
.row-l{font-size:13px;line-height:1.4}
.row-l em{display:block;font-style:normal;font-size:10.5px;color:var(--ink-soft);margin-top:1px}
.row-v{font-family:'IBM Plex Mono',monospace;font-size:14.5px;font-weight:600;white-space:nowrap}
.row.plus .row-v{color:var(--profit)}
.row.minus .row-v{color:var(--loss)}
.row.head{background:#E3E7DF;padding:7px 14px}
.row.head .row-l{font-size:10.5px;font-weight:900;letter-spacing:.09em;color:var(--ink-soft)}
.row.sub{background:#EFF2EC}
.row.sub .row-l{font-weight:700}
.row.sub .row-v{font-size:15.5px}
.row.total{background:var(--ink);border:none}
.row.total .row-l{color:var(--paper);font-weight:700;font-size:13.5px}
.row.total .row-v{font-size:19px;font-weight:600}

.chart-box{background:var(--card);border:1px solid var(--line-soft);border-radius:11px;padding:14px 12px 10px}
.chart-box svg{display:block;width:100%;height:auto}
.chart-legend{display:flex;flex-wrap:wrap;gap:14px;margin-top:8px;padding:0 3px;font-size:10.5px;color:var(--ink-soft)}
.chart-legend b{display:inline-block;width:9px;height:9px;border-radius:50%;margin-right:5px;vertical-align:middle}
.apply{margin-top:11px;width:100%;padding:11px;border:1.5px solid var(--ink);background:var(--ink);color:var(--paper);border-radius:10px;font-family:inherit;font-size:13px;font-weight:700;cursor:pointer}
.apply:disabled{opacity:.35;cursor:default}

.cards{display:grid;grid-template-columns:1fr 1fr;gap:9px}
.card{background:var(--card);border:1px solid var(--line-soft);border-radius:11px;padding:13px}
.card-k{font-size:11px;color:var(--ink-soft);font-weight:500;line-height:1.45;min-height:32px}
.card-v{font-family:'IBM Plex Mono',monospace;font-size:21px;font-weight:600;margin-top:5px;letter-spacing:-.02em}
.card-s{font-size:10.5px;color:var(--ink-soft);margin-top:2px;line-height:1.5}
.card.wide{grid-column:1/-1}
.card.flag{border-color:var(--yolk);background:#FEF6E5}
.note{font-size:11.5px;color:var(--ink-soft);line-height:1.65;margin-top:12px;padding-left:11px;border-left:2.5px solid var(--yolk)}
.reset{display:block;width:100%;margin:22px 0 8px;padding:12px;background:transparent;border:1.5px solid var(--line);border-radius:10px;color:var(--ink-soft);font-family:inherit;font-size:13px;font-weight:700;cursor:pointer}
.reset:active{background:var(--line-soft)}

.meter{position:fixed;left:0;right:0;bottom:0;z-index:50;background:var(--ink);color:var(--paper);padding:11px 16px calc(11px + env(safe-area-inset-bottom));box-shadow:0 -6px 24px rgba(22,33,28,.28)}
.meter-in{max-width:660px;margin:0 auto}
.meter-top{display:flex;align-items:baseline;justify-content:space-between;gap:12px}
.meter-k{font-family:'IBM Plex Mono',monospace;font-size:10px;letter-spacing:.16em;color:#9DAAA2}
.meter-v{font-family:'IBM Plex Mono',monospace;font-size:27px;font-weight:600;letter-spacing:-.02em;line-height:1.15}
.meter-y{font-family:'IBM Plex Mono',monospace;font-size:11.5px;color:#9DAAA2;text-align:right;line-height:1.45}
.gauge{display:flex;height:7px;border-radius:4px;overflow:hidden;background:#2B3A33;margin-top:9px}
.gauge i{display:block;height:100%;transition:width .3s ease}
.legend{display:flex;gap:12px;margin-top:6px;font-size:9.5px;color:#9DAAA2;font-family:'IBM Plex Mono',monospace}
.legend b{display:inline-block;width:7px;height:7px;border-radius:2px;margin-right:4px;vertical-align:middle}
@media (prefers-reduced-motion:reduce){*{transition:none!important;animation:none!important}}
</style>
</head>
<body>

<header class="hero">
  <div class="wrap">
    <div class="hero-top">
      <div>
        <div class="eyebrow">Davao · Philippines</div>
        <h1 id="hTitle"></h1>
      </div>
      <div class="lang">
        <button id="btn-ja" aria-pressed="true" onclick="setLang('ja')">JA</button>
        <button id="btn-en" aria-pressed="false" onclick="setLang('en')">EN</button>
      </div>
    </div>
    <p id="hSub"></p>
  </div>
</header>

<main class="wrap">
  <div class="life" id="life"></div>
  <div id="inputs"></div>

  <section class="sec">
    <div class="sec-title"><h2 id="tChart"></h2><span>OPTIMUM</span></div>
    <div class="chart-box">
      <div id="chart"></div>
      <div class="chart-legend">
        <span><b style="background:var(--yolk)"></b><span id="cl1"></span></span>
        <span><b style="background:var(--profit)"></b><span id="cl2"></span></span>
      </div>
      <button class="apply" id="btnApply" onclick="applyBest()"></button>
    </div>
  </section>

  <section class="sec">
    <div class="sec-title"><h2 id="tBreak"></h2><span id="periodLabel"></span></div>
    <div class="tbl" id="breakdown"></div>
  </section>

  <section class="sec">
    <div class="sec-title"><h2 id="tKey"></h2><span>KEY FIGURES</span></div>
    <div class="cards" id="cards"></div>
    <p class="note" id="verdict"></p>
  </section>

  <button class="reset" id="btnReset" onclick="resetAll()"></button>
</main>

<div class="meter">
  <div class="meter-in">
    <div class="meter-top">
      <div>
        <div class="meter-k" id="mLabel"></div>
        <div class="meter-v num" id="mVal">₱0</div>
      </div>
      <div class="meter-y num" id="mYen"></div>
    </div>
    <div class="gauge">
      <i id="gCost"  style="width:0%;background:var(--loss)"></i>
      <i id="gLabor" style="width:0%;background:var(--yolk)"></i>
      <i id="gGain"  style="width:0%;background:var(--profit)"></i>
    </div>
    <div class="legend">
      <span><b style="background:var(--loss)"></b><span id="lg1"></span></span>
      <span><b style="background:var(--yolk)"></b><span id="lg2"></span></span>
      <span><b style="background:var(--profit)"></b><span id="lg3"></span></span>
    </div>
  </div>
</div>

<script>
/* ================= 入力項目 ================= */
const ITEMS = {
  birds:  {ja:['羽数（1群れ）','羽','一度に導入して、まとめて育てる鶏の数です。'],
           en:['Flock size','birds','Birds brought in and raised together as one batch.'],
           d:200,min:0,max:5000,step:50},
  rear:   {ja:['育成期間（産み始めるまで）','ヶ月','ひなから育てるなら4〜5ヶ月。産卵直前の若鶏を買うなら0。'],
           en:['Rearing period (before first egg)','months','4–5 months from day-old chicks. Set 0 if you buy point-of-lay pullets.'],
           d:1,min:0,max:8,step:1},
  lay:    {ja:['産卵期間（産み続ける月数）','ヶ月','この月数が過ぎたら食肉として販売します。分からなければ下のグラフで最適値を探せます。'],
           en:['Laying period (months of egg production)','months','After this, the hen is sold for meat. Unsure? The chart below finds the best value.'],
           d:18,min:1,max:40,step:1, key:true},
  mort:   {ja:['死亡率（1周期あたり）','%','導入から出荷までに失う割合。5〜12%が現実的です。'],
           en:['Mortality per cycle','%','Share lost between intake and sale. 5–12% is realistic.'],
           d:8,min:0,max:40,step:1},

  workers:{ja:['作業員の人数','人','目安：1人で1,000〜2,000羽まで管理できます。'],
           en:['Number of workers','ppl','A rule of thumb: one worker handles 1,000–2,000 birds.'],
           d:2,min:0,max:10,step:1},
  wage:   {ja:['1人あたりの月給','ペソ','ダバオの農業最低賃金は月12,000ペソ前後です。'],
           en:['Monthly wage per worker','PHP','Davao agricultural minimum wage runs around ₱12,000 a month.'],
           d:8000,min:0,max:30000,step:500},

  rate0:  {ja:['産卵率（産み始めの時期）','%','ピーク時の産卵率。健康なレイヤーで85〜92%。'],
           en:['Lay rate at peak','%','Peak laying rate. Healthy layers reach 85–92%.'],
           d:90,min:20,max:98,step:1},
  drop:   {ja:['産卵率の低下（1ヶ月あたり）','pt','鶏は歳をとると産まなくなります。月1〜2ptの低下が一般的。'],
           en:['Lay rate decline per month','pt','Hens slow down with age. A 1–2 pt monthly drop is typical.'],
           d:1.5,min:0,max:5,step:0.1},
  egg:    {ja:['たまご1個の売値','ペソ','市場出荷なら5〜7ペソ。放し飼いブランド卵の直販なら10〜18ペソ。'],
           en:['Selling price per egg','PHP','₱5–7 at farmgate. ₱10–18 for branded free-range sold direct.'],
           d:7,min:2,max:25,step:0.5},

  weight: {ja:['出荷時の体重','kg','産卵を終えた鶏は1.4〜1.8kg程度になります。'],
           en:['Live weight at sale','kg','A hen at the end of lay weighs about 1.4–1.8 kg.'],
           d:1.5,min:0.5,max:4,step:0.1},
  kgPrice:{ja:['生体1kgの売値','ペソ','廃鶏は120〜180ペソ／kg。地鶏系なら200ペソ超も。'],
           en:['Live weight price per kg','PHP','Spent hens fetch ₱120–180/kg; native breeds can exceed ₱200.'],
           d:150,min:40,max:500,step:10},

  buy:    {ja:['導入時の1羽あたり仕入価格','ペソ','ひなは40〜60ペソ、産卵直前の若鶏は350〜420ペソ。'],
           en:['Purchase price per bird','PHP','Day-old chicks ₱40–60; point-of-lay pullets ₱350–420.'],
           d:380,min:10,max:1000,step:10},
  feedR:  {ja:['えさの量（育成中・1羽1日）','g','ひな〜若鶏は成長につれ40〜90g。平均で60g前後。'],
           en:['Feed per bird per day, rearing','g','Chicks to pullets eat 40–90 g; around 60 g on average.'],
           d:60,min:10,max:150,step:5},
  feedL:  {ja:['えさの量（産卵中・1羽1日）','g','レイヤーは1日105〜120g。放し飼いなら20〜30%減。'],
           en:['Feed per bird per day, laying','g','Layers eat 105–120 g. Grazing cuts this 20–30%.'],
           d:110,min:30,max:200,step:5},
  sack:   {ja:['えさ50kg袋の価格','ペソ','レイヤーマッシュの相場は1,450〜1,600ペソ／袋。'],
           en:['Feed price per 50 kg sack','PHP','Layer mash typically runs ₱1,450–1,600 a sack.'],
           d:1600,min:600,max:2800,step:50},
  misc:   {ja:['諸経費（1羽1ヶ月）','ペソ','薬・ワクチン・電気水道・もみ殻など。'],
           en:['Overhead per bird per month','PHP','Medication, vaccines, power, water, bedding.'],
           d:15,min:0,max:100,step:1},

  fx:     {ja:['両替レート（1ペソ）','円','円に換算して表示するためのレートです。'],
           en:['Conversion rate (per ₱1)','JPY','Used only for the secondary figure shown in yen.'],
           d:2.6,min:1,max:5,step:0.05}
};

const LAYOUT = [
  {ja:'群れと周期',en:'Flock and cycle',code:'CYCLE',keys:['birds','rear','lay','mort']},
  {ja:'人件費',en:'Labour',code:'LABOUR',keys:['workers','wage']},
  {ja:'たまごの売上',en:'Egg revenue',code:'EGGS',keys:['rate0','drop','egg']},
  {ja:'食肉としての売上',en:'Meat revenue',code:'MEAT',keys:['weight','kgPrice']},
  {ja:'仕入・えさ・諸経費',en:'Stock, feed and overhead',code:'COST',keys:['buy','feedR','feedL','sack','misc']},
  {ja:'円への換算',en:'Currency',code:'FX',keys:['fx']}
];

/* ================= 文言 ================= */
const T = {
ja:{
  title:'養鶏場 損益シミュレーター',
  sub:'ひなを入れて、たまごを採り、産まなくなったら食肉として売る──この一連の流れをまとめて計算します。数字はすべて書き換えられます。',
  chart:'産卵期間と利益の関係', breakdown:'収支の内訳', key:'判断に使う数字',
  reset:'すべて初期値に戻す', apply:(n)=>`産卵期間を ${n}ヶ月 にする`, applied:'いまが最適な産卵期間です',
  meterLabel:'月あたりの損益', period:'PER MONTH',
  lg:['えさ・諸経費','人件費','残る利益'], approx:'約', annual:'年間',
  cl:['いまの設定','いちばん儲かる産卵期間'],
  life:{rearK:'REARING',rearV:(n)=>`育成 ${n}ヶ月`,layK:'LAYING',layV:(n)=>`産卵 ${n}ヶ月`,sellK:'SELL',sellV:'食肉出荷'},
  r:{head1:'1周期（%s）の収入', head2:'1周期の支出', head3:'まとめ',
     egg:'たまごの売上', meat:'食肉としての売上', buy:'鶏の仕入', feed:'えさ代',
     misc:'諸経費', labor:'人件費', cycleTotal:'1周期の損益', monthly:'月あたりに直すと'},
  c:{be:'人件費を回収できる最低の羽数（損益分岐点）',
     beNone:'達成できません', beNoneS:'1羽あたりの利益がマイナスです。売値かえさ代を見直してください。',
     beShort:(n,g)=>`いまの ${n}羽 では ${g}羽 足りません。`,
     beOk:(n)=>`いまの ${n}羽 は分岐点を超えています。`,
     best:'いちばん儲かる産卵期間', bestS:(p)=>`そのときの月間損益は ${p}`,
     avgRate:'期間中の平均産卵率', avgRateS:(a,b)=>`${a}% から ${b}% まで下がる想定`,
     eggShare:'たまごの売上が<br>収入に占める割合', meatShare:'食肉の売上が<br>収入に占める割合',
     shareS:'収入の内訳です',
     perBird:'鶏1羽が1ヶ月に生む利益<br>（人件費を引く前）', perBirdS:'この数字 × 羽数 が人件費を上回れば黒字',
     eggsTotal:'1周期で採れる<br>たまごの総数', eggsTotalS:(n)=>`1羽あたり ${n}個`,
     annual:'年間の損益', daily:'1日あたりの損益'},
  v:{good:(p,y)=>`黒字です。月 ${p}（${y}）が手元に残る計算になります。`,
     thin:' ただし分岐点ギリギリなので、卵価の下落や病気で簡単に赤字へ転びます。羽数か単価に余裕を持たせてください。',
     bad:(p,y)=>`赤字です。月 ${p}（${y}）の持ち出しになります。`,
     fix:(n)=>` 黒字にするには、羽数を ${n}羽以上にするか、作業員を減らすか、売値を上げる必要があります。`,
     fixG:' 黒字にするには、作業員を減らすか、売値を上げるか、羽数を増やしてください。'},
  u:{birds:'羽',eggs:'個',sacks:'袋',mo:'ヶ月'}
},
en:{
  title:'Poultry Farm Profit Simulator',
  sub:'Bring in birds, collect eggs, then sell each hen for meat once she stops laying. This works out the whole cycle. Every figure can be changed.',
  chart:'Laying period vs profit', breakdown:'Breakdown', key:'Figures that decide it',
  reset:'Reset everything to defaults', apply:(n)=>`Set laying period to ${n} months`, applied:'Your laying period is already the best one',
  meterLabel:'PROFIT PER MONTH', period:'PER MONTH',
  lg:['Feed & overhead','Labour','Profit left'], approx:'approx.', annual:'per year',
  cl:['Your current setting','Most profitable laying period'],
  life:{rearK:'REARING',rearV:(n)=>`Rearing ${n} mo`,layK:'LAYING',layV:(n)=>`Laying ${n} mo`,sellK:'SELL',sellV:'Sold for meat'},
  r:{head1:'REVENUE PER CYCLE (%s)', head2:'COSTS PER CYCLE', head3:'RESULT',
     egg:'Egg sales', meat:'Meat sales', buy:'Bird purchases', feed:'Feed',
     misc:'Overhead', labor:'Labour', cycleTotal:'Profit per cycle', monthly:'Expressed per month'},
  c:{be:'Birds needed to cover labour (break-even)',
     beNone:'Not reachable', beNoneS:'Profit per bird is negative. Revisit your selling price or feed cost.',
     beShort:(n,g)=>`At ${n} birds you are ${g} short.`,
     beOk:(n)=>`Your ${n} birds clear the break-even point.`,
     best:'Most profitable laying period', bestS:(p)=>`Monthly profit there would be ${p}`,
     avgRate:'Average lay rate over the period', avgRateS:(a,b)=>`Falling from ${a}% to ${b}%`,
     eggShare:'Eggs as a share<br>of total revenue', meatShare:'Meat as a share<br>of total revenue',
     shareS:'Where the money comes from',
     perBird:'Profit per bird per month<br>(before labour)', perBirdS:'Multiply by flock size and beat the wage bill',
     eggsTotal:'Total eggs<br>per cycle', eggsTotalS:(n)=>`${n} per hen`,
     annual:'Profit per year', daily:'Profit per day'},
  v:{good:(p,y)=>`Profitable. You keep about ${p} (${y}) a month.`,
     thin:' The margin is thin, though — a dip in egg prices or a disease outbreak would push it negative. Build in some headroom.',
     bad:(p,y)=>`Loss-making. You are ${p} (${y}) out of pocket each month.`,
     fix:(n)=>` To turn this around, raise the flock to at least ${n} birds, cut a worker, or lift your selling price.`,
     fixG:' To turn this around, cut a worker, lift your selling price, or raise the flock size.'},
  u:{birds:'birds',eggs:'eggs',sacks:'sacks',mo:'mo'}
}};

/* ================= 状態 ================= */
let lang = 'ja';
const S = {};
function seed(){ for(const k in ITEMS) S[k] = String(ITEMS[k].d); }
seed();

const v  = k => { const n = parseFloat(S[k]); return isFinite(n) ? n : 0; };
const nf = n => Math.round(n).toLocaleString('en-US');
const peso = n => (n < 0 ? '−₱' : '₱') + nf(Math.abs(n));
const yen  = n => (n < 0 ? '−¥' : '¥') + nf(Math.abs(n));

/* ================= 中核の計算 ================= */
/* 1つの群れの一生（導入 → 育成 → 産卵 → 食肉出荷）をまとめて計算する */
function core(layOverride){
  const N     = v('birds');
  const rear  = v('rear');
  const lay   = Math.max(layOverride !== undefined ? layOverride : v('lay'), 0);
  const total = rear + lay;
  const mort  = v('mort') / 100;

  // 死亡は期間中に少しずつ起きるので、平均の飼育羽数は半分だけ減らして扱う
  const avgN   = N * (1 - mort / 2);
  const finalN = N * (1 - mort);

  // 産卵率は毎月落ちていく。期間中の平均をとる
  const rEnd = Math.max(v('rate0') - v('drop') * Math.max(lay - 1, 0), 0);
  const rAvg = lay > 0 ? Math.max((v('rate0') + rEnd) / 2, 0) : 0;

  const eggs    = avgN * (rAvg / 100) * 30 * lay;
  const eggRev  = eggs * v('egg');
  const meatRev = finalN * v('weight') * v('kgPrice');

  const buy     = N * v('buy');
  const feedKg  = avgN * (v('feedR')/1000) * 30 * rear + avgN * (v('feedL')/1000) * 30 * lay;
  const feed    = feedKg / 50 * v('sack');
  const misc    = avgN * v('misc') * total;
  const labor   = v('workers') * v('wage') * total;

  const revenue   = eggRev + meatRev;
  const costNoLab = buy + feed + misc;
  const profit    = revenue - costNoLab - labor;
  const monthly   = total > 0 ? profit / total : 0;

  return {N, rear, lay, total, avgN, finalN, rAvg, rEnd, eggs, eggRev, meatRev,
          buy, feedKg, feed, misc, labor, revenue, costNoLab, profit, monthly,
          laborMonthly: v('workers') * v('wage'),
          perBirdMonth: (N > 0 && total > 0) ? (revenue - costNoLab) / N / total : 0};
}

/* いちばん儲かる産卵期間を探す（1〜40ヶ月を総当たり） */
function findBest(){
  let best = {lay:1, monthly:-Infinity};
  for(let L = 1; L <= 40; L++){
    const m = core(L).monthly;
    if(m > best.monthly) best = {lay:L, monthly:m};
  }
  return best;
}

/* ================= 描画 ================= */
function fieldHTML(k){
  const it = ITEMS[k], [label, unit, hint] = it[lang];
  return `<div class="field${it.key ? ' key' : ''}" data-k="${k}">
    <div class="f-head">
      <span class="f-label">${label}</span>
      <span class="f-in">
        <input type="text" inputmode="decimal" value="${S[k]}" aria-label="${label}"
          oninput="setVal('${k}',this.value,false)" onblur="normalize('${k}')">
        <span class="f-unit">${unit}</span>
      </span>
    </div>
    <input type="range" min="${it.min}" max="${it.max}" step="${it.step}" value="${S[k]}" aria-label="${label}"
      oninput="setVal('${k}',this.value,true)">
    <p class="f-hint">${hint}</p>
  </div>`;
}
function renderInputs(){
  document.getElementById('inputs').innerHTML = LAYOUT.map(g =>
    `<section class="sec"><div class="sec-title"><h2>${g[lang]}</h2><span>${g.code}</span></div>` +
    g.keys.map(fieldHTML).join('') + `</section>`).join('');
}

function renderLife(){
  const t = T[lang].life, r = v('rear'), l = v('lay');
  const segs = [];
  if(r > 0) segs.push(`<div class="life-seg rear" style="flex:${r} 1 0"><span class="life-k">${t.rearK}</span><span class="life-v">${t.rearV(r)}</span></div>`);
  segs.push(`<div class="life-seg lay" style="flex:${Math.max(l,1)} 1 0"><span class="life-k">${t.layK}</span><span class="life-v">${t.layV(l)}</span></div>`);
  segs.push(`<div class="life-seg sell"><span class="life-k">${t.sellK}</span><span class="life-v">${t.sellV}</span></div>`);
  document.getElementById('life').innerHTML = segs.join('');
}

function renderChart(){
  const t = T[lang], best = findBest(), cur = v('lay');
  const W = 320, H = 148, PL = 34, PR = 8, PT = 10, PB = 24;
  const MAXL = 40;
  const pts = [];
  for(let L = 1; L <= MAXL; L++) pts.push({L, m: core(L).monthly});
  const ys = pts.map(p => p.m);
  let lo = Math.min(...ys), hi = Math.max(...ys);
  if(hi === lo){ hi = lo + 1; }
  const pad = (hi - lo) * 0.12; lo -= pad; hi += pad;
  if(lo > 0) lo = 0; if(hi < 0) hi = 0;

  const X = L => PL + (L - 1) / (MAXL - 1) * (W - PL - PR);
  const Y = m => PT + (hi - m) / (hi - lo) * (H - PT - PB);

  const line = pts.map((p, i) => `${i ? 'L' : 'M'}${X(p.L).toFixed(1)},${Y(p.m).toFixed(1)}`).join(' ');
  const area = `${line} L${X(MAXL).toFixed(1)},${Y(Math.max(lo,0)).toFixed(1)} L${X(1).toFixed(1)},${Y(Math.max(lo,0)).toFixed(1)} Z`;

  let ticks = '';
  [6,12,18,24,30,36].forEach(L => {
    ticks += `<text x="${X(L)}" y="${H-7}" font-size="9" fill="#5C6862" text-anchor="middle" font-family="'IBM Plex Mono',monospace">${L}</text>`;
  });
  const zeroY = Y(0);
  const showZero = (0 >= lo && 0 <= hi);

  const curM = core(cur).monthly;
  const inRange = cur >= 1 && cur <= MAXL;

  document.getElementById('chart').innerHTML = `
  <svg viewBox="0 0 ${W} ${H}" role="img" aria-label="${t.chart}">
    <path d="${area}" fill="rgba(46,125,91,.10)"/>
    ${showZero ? `<line x1="${PL}" y1="${zeroY}" x2="${W-PR}" y2="${zeroY}" stroke="#B03A2A" stroke-width="1" stroke-dasharray="3 3"/>
      <text x="${PL-5}" y="${zeroY+3}" font-size="9" fill="#B03A2A" text-anchor="end" font-family="'IBM Plex Mono',monospace">0</text>` : ''}
    <text x="${PL-5}" y="${Y(hi)+8}" font-size="9" fill="#5C6862" text-anchor="end" font-family="'IBM Plex Mono',monospace">${nf(hi/1000)}k</text>
    <path d="${line}" fill="none" stroke="#2E7D5B" stroke-width="2" stroke-linejoin="round" stroke-linecap="round"/>
    <circle cx="${X(best.lay)}" cy="${Y(best.monthly)}" r="5" fill="#2E7D5B" stroke="#F8F9F6" stroke-width="2"/>
    ${inRange ? `<circle cx="${X(cur)}" cy="${Y(curM)}" r="5" fill="#F0A81E" stroke="#16211C" stroke-width="2"/>` : ''}
    ${ticks}
    <text x="${W-PR}" y="${H-7}" font-size="8.5" fill="#5C6862" text-anchor="end" font-family="'IBM Plex Mono',monospace">${t.u.mo}</text>
  </svg>`;

  document.getElementById('cl1').textContent = t.cl[0] + '（' .replace('（', lang==='ja'?'：':': ') + v('lay') + t.u.mo + '）'.replace('）','');
  document.getElementById('cl1').textContent = `${t.cl[0]}: ${v('lay')} ${t.u.mo}`;
  document.getElementById('cl2').textContent = `${t.cl[1]}: ${best.lay} ${t.u.mo}`;

  const btn = document.getElementById('btnApply');
  const same = Math.round(cur) === best.lay;
  btn.disabled = same;
  btn.textContent = same ? t.applied : t.apply(best.lay);
  btn.dataset.best = best.lay;
}

function renderOut(){
  const t = T[lang], u = t.u, r = core(), fx = v('fx');
  const cycleTag = `${r.total} ${u.mo}`;
  document.getElementById('periodLabel').textContent = t.period;

  const rows = [
    {l:t.r.head1.replace('%s', cycleTag), val:null, s:'head'},
    {l:t.r.egg,  e:`${nf(r.eggs)} ${u.eggs} × ₱${v('egg')}`, val:r.eggRev, s:'plus'},
    {l:t.r.meat, e:`${nf(r.finalN)} ${u.birds} × ${v('weight')} kg × ₱${v('kgPrice')}`, val:r.meatRev, s:'plus'},
    {l:t.r.head2, val:null, s:'head'},
    {l:t.r.buy,  e:`${nf(r.N)} ${u.birds} × ₱${v('buy')}`, val:-r.buy, s:'minus'},
    {l:t.r.feed, e:`${nf(r.feedKg)} kg = ${nf(r.feedKg/50)} ${u.sacks}`, val:-r.feed, s:'minus'},
    {l:t.r.misc, e:'', val:-r.misc, s:'minus'},
    {l:t.r.labor,e:`${v('workers')} × ₱${nf(v('wage'))} × ${cycleTag}`, val:-r.labor, s:'minus'},
    {l:t.r.head3, val:null, s:'head'},
    {l:t.r.cycleTotal, e:cycleTag, val:r.profit, s:'sub'},
    {l:t.r.monthly, e:'', val:r.monthly, s:'total'}
  ];
  document.getElementById('breakdown').innerHTML = rows.map(x =>
    `<div class="row ${x.s}">
      <span class="row-l">${x.l}${x.e ? `<em>${x.e}</em>` : ''}</span>
      ${x.val === null ? '' : `<span class="row-v num">${peso(x.val)}</span>`}
     </div>`).join('');

  const be = r.perBirdMonth > 0 ? Math.ceil(r.laborMonthly / r.perBirdMonth) : null;
  const birds = r.N, unitB = lang === 'ja' ? ' 羽' : ' birds';
  const best = findBest();

  let cards = `<div class="card wide ${(be === null || be > birds) ? 'flag' : ''}">
    <div class="card-k">${t.c.be}</div>
    <div class="card-v">${be === null ? t.c.beNone : nf(be) + unitB}</div>
    <div class="card-s">${be === null ? t.c.beNoneS
      : (be > birds ? t.c.beShort(nf(birds), nf(be - birds)) : t.c.beOk(nf(birds)))}</div></div>`;

  cards += `<div class="card"><div class="card-k">${t.c.best}</div><div class="card-v">${best.lay} ${u.mo}</div><div class="card-s">${t.c.bestS(peso(best.monthly))}</div></div>`;
  cards += `<div class="card"><div class="card-k">${t.c.avgRate}</div><div class="card-v">${r.rAvg.toFixed(0)}%</div><div class="card-s">${t.c.avgRateS(v('rate0'), r.rEnd.toFixed(0))}</div></div>`;

  const eggPct = r.revenue > 0 ? r.eggRev / r.revenue * 100 : 0;
  cards += `<div class="card"><div class="card-k">${t.c.eggShare}</div><div class="card-v">${eggPct.toFixed(0)}%</div><div class="card-s">${t.c.shareS}</div></div>`;
  cards += `<div class="card"><div class="card-k">${t.c.meatShare}</div><div class="card-v">${(100-eggPct).toFixed(0)}%</div><div class="card-s">${t.c.shareS}</div></div>`;
  cards += `<div class="card"><div class="card-k">${t.c.perBird}</div><div class="card-v">${peso(r.perBirdMonth)}</div><div class="card-s">${t.c.perBirdS}</div></div>`;
  cards += `<div class="card"><div class="card-k">${t.c.eggsTotal}</div><div class="card-v">${nf(r.eggs)}</div><div class="card-s">${t.c.eggsTotalS(nf(r.N > 0 ? r.eggs / r.N : 0))}</div></div>`;

  const col = r.monthly >= 0 ? 'var(--profit)' : 'var(--loss)';
  cards += `<div class="card"><div class="card-k">${t.c.annual}</div><div class="card-v" style="color:${col}">${peso(r.monthly*12)}</div><div class="card-s">${yen(r.monthly*12*fx)}</div></div>`;
  cards += `<div class="card"><div class="card-k">${t.c.daily}</div><div class="card-v" style="color:${col}">${peso(r.monthly/30)}</div><div class="card-s">${yen(r.monthly/30*fx)}</div></div>`;
  document.getElementById('cards').innerHTML = cards;

  let msg;
  if(r.monthly >= 0){
    msg = t.v.good(peso(r.monthly), yen(r.monthly*fx));
    if(be !== null && birds < be * 1.5) msg += t.v.thin;
  } else {
    msg = t.v.bad(peso(Math.abs(r.monthly)), yen(Math.abs(r.monthly)*fx));
    msg += (be !== null && be > 0) ? t.v.fix(nf(be)) : t.v.fixG;
  }
  document.getElementById('verdict').textContent = msg;

  document.getElementById('mVal').textContent = peso(r.monthly);
  document.getElementById('mVal').style.color = r.monthly >= 0 ? '#7BD1A6' : '#F09A8A';
  document.getElementById('mYen').innerHTML = `${t.approx} ${yen(r.monthly*fx)}<br>${t.annual} ${peso(r.monthly*12)}`;

  const mCost = r.total > 0 ? r.costNoLab / r.total : 0;
  const gain  = Math.max(r.monthly, 0);
  const base  = Math.max(r.total > 0 ? r.revenue / r.total : 0, mCost + r.laborMonthly + gain, 1);
  const pc = x => Math.min(100, Math.max(0, x / base * 100));
  document.getElementById('gCost').style.width  = pc(mCost) + '%';
  document.getElementById('gLabor').style.width = pc(r.laborMonthly) + '%';
  document.getElementById('gGain').style.width  = pc(gain) + '%';
}

function renderChrome(){
  const t = T[lang];
  document.documentElement.lang = lang;
  document.getElementById('hTitle').textContent = t.title;
  document.getElementById('hSub').textContent   = t.sub;
  document.getElementById('tChart').textContent = t.chart;
  document.getElementById('tBreak').textContent = t.breakdown;
  document.getElementById('tKey').textContent   = t.key;
  document.getElementById('btnReset').textContent = t.reset;
  document.getElementById('mLabel').textContent = t.meterLabel;
  ['lg1','lg2','lg3'].forEach((id,i) => document.getElementById(id).textContent = t.lg[i]);
  document.getElementById('btn-ja').setAttribute('aria-pressed', lang === 'ja');
  document.getElementById('btn-en').setAttribute('aria-pressed', lang === 'en');
}

function renderAll(){ renderLife(); renderChart(); renderOut(); }

/* ================= 操作 ================= */
function setVal(k, val, fromSlider){
  S[k] = val;
  const f = document.querySelector(`.field[data-k="${k}"]`);
  if(f){
    const txt = f.querySelector('input[type=text]'), sl = f.querySelector('input[type=range]');
    if(fromSlider) txt.value = val; else sl.value = val;
  }
  renderAll();
}
function normalize(k){
  const n = parseFloat(S[k]);
  S[k] = isFinite(n) ? String(n) : String(ITEMS[k].d);
  const f = document.querySelector(`.field[data-k="${k}"]`);
  if(f){
    f.querySelector('input[type=text]').value = S[k];
    f.querySelector('input[type=range]').value = S[k];
  }
  renderAll();
}
function applyBest(){
  const b = document.getElementById('btnApply').dataset.best;
  if(b) { S.lay = String(b); renderInputs(); renderAll(); }
}
function setLang(l){ lang = l; renderChrome(); renderInputs(); renderAll(); }
function resetAll(){ seed(); renderInputs(); renderAll(); }

renderChrome(); renderInputs(); renderAll();
</script>
</body>
</html>
