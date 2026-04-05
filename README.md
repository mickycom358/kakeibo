<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<title>AI家計簿</title>
<style>
:root{--bg:#0a0f1e;--bg2:#111827;--sur:rgba(255,255,255,.04);--bdr:rgba(255,255,255,.08);--acc:#38bdf8;--ac2:#818cf8;--grn:#34d399;--red:#f87171;--txt:#f1f5f9;--mut:#64748b;--r:14px}
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
body{background:var(--bg);background-image:radial-gradient(ellipse 80% 50% at 50% -10%,rgba(56,189,248,.13) 0%,transparent 60%),radial-gradient(ellipse 60% 40% at 80% 80%,rgba(129,140,248,.09) 0%,transparent 50%);font-family:'Hiragino Sans','Yu Gothic','Noto Sans JP',sans-serif;color:var(--txt);min-height:100vh;font-size:15px}
.hdr{position:sticky;top:0;z-index:200;background:rgba(10,15,30,.93);backdrop-filter:blur(16px);border-bottom:1px solid var(--bdr);padding:11px 16px;display:flex;align-items:center;gap:10px}
.logo{width:36px;height:36px;border-radius:10px;flex-shrink:0;background:linear-gradient(135deg,var(--acc),var(--ac2));display:flex;align-items:center;justify-content:center;font-size:18px;box-shadow:0 0 16px rgba(56,189,248,.3)}
.t1{font-weight:800;font-size:16px}.t2{font-size:10px;color:var(--mut)}
.hbtns{margin-left:auto;display:flex;gap:6px}
.hbtn{border:1px solid var(--bdr);border-radius:9px;padding:6px 11px;font-size:11px;font-weight:700;cursor:pointer;white-space:nowrap;background:var(--sur);color:var(--txt)}
.hbtn.p{background:rgba(56,189,248,.15);border-color:rgba(56,189,248,.35);color:var(--acc)}
.stats{display:none;background:var(--bg2);border-bottom:1px solid var(--bdr)}
.stats.show{display:flex}
.stat{flex:1;padding:10px 12px;position:relative}
.stat+.stat::before{content:'';position:absolute;left:0;top:20%;bottom:20%;width:1px;background:var(--bdr)}
.slbl{font-size:9px;color:var(--mut);text-transform:uppercase;letter-spacing:.08em}
.sval{font-weight:800;font-size:14px;margin-top:3px}
.tabs{display:flex;padding:0 16px;background:var(--bg2);border-bottom:1px solid var(--bdr)}
.tab{background:none;border:none;cursor:pointer;padding:12px 14px;font-size:13px;font-weight:700;color:var(--mut);border-bottom:2px solid transparent;transition:all .2s;white-space:nowrap}
.tab.active{color:var(--acc);border-bottom-color:var(--acc)}
.content{padding:16px;max-width:560px;margin:0 auto}
.panel{display:none}.panel.active{display:block}
.scan-zone{border:2px dashed rgba(56,189,248,.3);border-radius:20px;background:linear-gradient(135deg,rgba(56,189,248,.05),rgba(129,140,248,.04));padding:36px 20px 28px;text-align:center;margin-bottom:14px;position:relative;overflow:hidden}
#file-input{position:absolute;inset:0;width:100%;height:100%;opacity:0;cursor:pointer;z-index:10}
.scan-icon{font-size:60px;line-height:1;margin-bottom:14px;display:block}
.scan-title{font-size:18px;font-weight:800;margin-bottom:6px}
.scan-sub{font-size:12px;color:var(--mut);line-height:1.7;margin-bottom:18px}
.scan-pill{display:inline-flex;align-items:center;gap:8px;background:linear-gradient(135deg,var(--acc),var(--ac2));color:#0a0f1e;border:none;border-radius:13px;padding:13px 32px;font-size:15px;font-weight:800;pointer-events:none}
.lov{display:none;position:absolute;inset:0;background:rgba(10,15,30,.92);border-radius:18px;flex-direction:column;align-items:center;justify-content:center;gap:10px;z-index:20}
.lov.show{display:flex}
.spin{font-size:40px;animation:spin 1.2s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}
.prog{position:absolute;bottom:0;left:0;right:0;height:3px;background:linear-gradient(90deg,var(--acc),var(--ac2),var(--acc));background-size:200% 100%;animation:slide 1.4s linear infinite}
@keyframes slide{to{background-position:-200% 0}}
.lstep{font-size:12px;color:var(--mut);margin-top:4px}
.prev{display:none;border-radius:12px;overflow:hidden;border:1px solid var(--bdr);margin-bottom:14px}
.prev.show{display:block}
.plbl{padding:7px 13px;background:var(--sur);font-size:11px;color:var(--mut)}
.prev img{width:100%;max-height:220px;object-fit:contain;background:#000;display:block}
.tips{background:var(--sur);border:1px solid var(--bdr);border-radius:var(--r);padding:13px 15px}
.ttl{font-size:11px;font-weight:700;color:var(--mut);margin-bottom:8px;letter-spacing:.06em}
.tip{display:flex;gap:8px;font-size:12px;color:var(--mut);padding:3px 0}
.tipn{color:var(--acc);font-weight:800;min-width:16px}
.cbar{display:none;background:var(--sur);border:1px solid var(--bdr);border-radius:var(--r);padding:12px 14px;margin-bottom:14px}
.cbar.show{display:block}
.chips{display:flex;flex-wrap:wrap;gap:6px}
.chip{border-radius:8px;padding:4px 10px;font-size:11px;display:flex;gap:5px;align-items:center;font-weight:600}
.cdot{width:6px;height:6px;border-radius:50%;flex-shrink:0}
.elist{display:flex;flex-direction:column;gap:7px}
.ecard{background:var(--sur);border:1px solid var(--bdr);border-radius:var(--r);padding:11px 13px;display:flex;align-items:center;gap:10px}
.edot{width:8px;height:8px;border-radius:50%;flex-shrink:0;margin-top:2px}
.emain{flex:1;min-width:0}
.ename{font-size:14px;font-weight:700;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.emeta{font-size:11px;color:var(--mut);margin-top:2px}
.eamt{font-size:15px;font-weight:800;white-space:nowrap}
.ebtns{display:flex;gap:4px;flex-shrink:0}
.ebtn{border:1px solid var(--bdr);border-radius:7px;padding:5px 8px;font-size:13px;cursor:pointer;background:var(--sur);color:var(--mut)}
.ebtn.del{color:var(--red);border-color:rgba(248,113,113,.25)}
.eform{background:rgba(56,189,248,.07);border:1px solid rgba(56,189,248,.25);border-radius:var(--r);padding:14px;margin-bottom:8px}
.efg{display:flex;flex-direction:column;gap:9px}
.efl{display:block;font-size:10px;color:var(--mut);margin-bottom:3px;font-weight:700;letter-spacing:.05em}
.efi{background:rgba(255,255,255,.07);border:1px solid var(--bdr);border-radius:9px;color:var(--txt);padding:9px 11px;font-size:14px;width:100%;font-family:inherit}
.efi:focus{outline:none;border-color:var(--acc)}
select.efi option{background:#1e293b}
.efbtns{display:flex;gap:8px;margin-top:6px}
.esave{flex:1;background:linear-gradient(135deg,#16a34a,#15803d);color:#fff;border:none;border-radius:9px;padding:11px;font-size:14px;font-weight:800;cursor:pointer}
.ecancel{flex:1;background:var(--sur);color:var(--mut);border:1px solid var(--bdr);border-radius:9px;padding:11px;font-size:14px;cursor:pointer}
.trow{display:none;text-align:right;padding:13px 4px;font-size:16px;font-weight:800;color:var(--acc);border-top:1px solid var(--bdr);margin-top:10px}
.empty{text-align:center;padding:64px 0;color:var(--mut)}
.eico{font-size:52px;margin-bottom:12px}
#toast{position:fixed;bottom:28px;left:50%;z-index:999;transform:translateX(-50%) translateY(16px);background:#1e293b;border:1px solid rgba(255,255,255,.12);border-radius:12px;padding:11px 20px;font-size:13px;font-weight:700;box-shadow:0 8px 28px rgba(0,0,0,.6);white-space:nowrap;opacity:0;transition:all .3s cubic-bezier(.34,1.56,.64,1);pointer-events:none}
#toast.show{opacity:1;transform:translateX(-50%) translateY(0)}
@media print{body{background:#fff!important;color:#000!important}.hdr,.tabs,.ebtns,.scan-zone,.tips,.stats{display:none!important}.panel{display:block!important}.ecard{border:1px solid #ddd!important;background:#fff!important}.ename,.eamt{color:#000!important}.emeta{color:#555!important}}
</style>
</head>
<body>

<div class="hdr">
  <div class="logo">📒</div>
  <div><div class="t1">AI 家計簿</div><div class="t2">レシートを撮るだけで自動記帳</div></div>
  <div class="hbtns">
    <button class="hbtn p" onclick="exportExcel()">📊 Excel</button>
    <button class="hbtn" onclick="window.print()">🖨️ 印刷</button>
  </div>
</div>

<div class="stats" id="stats">
  <div class="stat"><div class="slbl">合計支出</div><div class="sval" id="s-total">¥0</div></div>
  <div class="stat"><div class="slbl">件数</div><div class="sval" id="s-count">0件</div></div>
  <div class="stat"><div class="slbl">トップ</div><div class="sval" id="s-top">—</div></div>
</div>

<div class="tabs">
  <button class="tab active" id="tab-scan" onclick="switchTab('scan')">📷 スキャン</button>
  <button class="tab" id="tab-ledger" onclick="switchTab('ledger')">📋 家計簿</button>
</div>

<div class="content">
  <div class="panel active" id="panel-scan">
    <div class="scan-zone" id="scan-zone">
      <input type="file" id="file-input" accept="image/*" onchange="handleFile(this)">
      <span class="scan-icon">📷</span>
      <div class="scan-title">レシートを読み込む</div>
      <div class="scan-sub">タップしてカメラ撮影 or 写真を選ぶ</div>
      <div class="scan-pill">📸 カメラ / 写真を選ぶ</div>
      <div class="lov" id="lov">
        <span class="spin">⏳</span>
        <span style="font-weight:800;font-size:16px">解析中...</span>
        <span class="lstep" id="lstep">レシートを読み取っています</span>
        <div class="prog"></div>
      </div>
    </div>
    <div class="prev" id="prev">
      <div class="plbl">📄 読み込んだレシート</div>
      <img id="prev-img" src="" alt="">
    </div>
    <div class="tips">
      <div class="ttl">📌 使い方</div>
      <div class="tip"><span class="tipn">1.</span>エリアをタップしてカメラまたは写真を選ぶ</div>
      <div class="tip"><span class="tipn">2.</span>OCRでテキストを読み取り、AIが自動分類</div>
      <div class="tip"><span class="tipn">3.</span>家計簿タブで確認・編集できます</div>
      <div class="tip"><span class="tipn">4.</span>Excelダウンロード・印刷にも対応</div>
    </div>
  </div>

  <div class="panel" id="panel-ledger">
    <div class="cbar" id="cbar"><div class="ttl">カテゴリ別集計</div><div class="chips" id="chips"></div></div>
    <div class="elist" id="elist"></div>
    <div class="trow" id="trow"></div>
    <div class="empty" id="empty">
      <div class="eico">📭</div>
      <div style="font-size:15px;font-weight:700">まだデータがありません</div>
      <div style="font-size:12px;margin-top:6px">スキャンタブでレシートを読み込んでください</div>
    </div>
  </div>
</div>

<div id="toast"></div>

<script>
const OCR_KEY = 'K85378497288957';
const CATS = ["食費","日用品","交通費","外食","医療","娯楽","衣類","その他"];
const COLS = {食費:"#4ade80",日用品:"#38bdf8",交通費:"#fbbf24",外食:"#f87171",医療:"#a78bfa",娯楽:"#fb923c",衣類:"#e879f9",その他:"#94a3b8"};
let entries=[], editId=null;

function handleFile(input){ const f=input.files[0]; input.value=''; if(f) processFile(f); }

async function processFile(file){
  if(!file.type.startsWith('image/')){ showToast('❌ 画像を選択してください'); return; }
  document.getElementById('prev-img').src = URL.createObjectURL(file);
  document.getElementById('prev').className = 'prev show';
  setLoading(true, 'レシートのテキストを読み取り中...');

  try{
    // ── Step1: OCR.space でテキスト抽出 ──
    const fd = new FormData();
    fd.append('file', file);
    fd.append('apikey', OCR_KEY);
    fd.append('language', 'jpn');
    fd.append('isOverlayRequired', 'false');
    fd.append('detectOrientation', 'true');
    fd.append('scale', 'true');
    fd.append('OCREngine', '2');

    const ocrResp = await fetch('https://api.ocr.space/parse/image', {
      method: 'POST', body: fd
    });
    const ocrData = await ocrResp.json();

    if(ocrData.IsErroredOnProcessing){
      throw new Error('OCR読み取り失敗: ' + ocrData.ErrorMessage);
    }

    const ocrText = ocrData.ParsedResults?.[0]?.ParsedText || '';
    if(!ocrText.trim()){
      throw new Error('テキストを読み取れませんでした。明るい場所で撮影してください。');
    }

    setLoading(true, 'AIが内容を解析中...');

    // ── Step2: テキストを解析してJSON生成 ──
    const today = new Date().toISOString().slice(0,10);
    const parsed = parseReceiptText(ocrText, today);

    if(!parsed.items || parsed.items.length === 0){
      throw new Error('商品情報を読み取れませんでした');
    }

    parsed.items.forEach(item=>{
      entries.push({
        id: Date.now()+Math.random(),
        date: parsed.date,
        store: parsed.store,
        name: item.name,
        amount: item.amount,
        category: item.category
      });
    });

    showToast(`✅ ${parsed.items.length}件を取り込みました`);
    renderLedger(); updateStats(); switchTab('ledger');

  }catch(e){
    showToast('❌ ' + e.message);
  }finally{
    setLoading(false);
  }
}

// ── レシートテキスト解析 ──
function parseReceiptText(text, today){
  const lines = text.split('\n').map(l=>l.trim()).filter(l=>l.length>0);

  // 日付抽出
  let date = today;
  for(const line of lines){
    const m = line.match(/(\d{4})[年\/\-](\d{1,2})[月\/\-](\d{1,2})/);
    if(m){ date=`${m[1]}-${m[2].padStart(2,'0')}-${m[3].padStart(2,'0')}`; break; }
    const m2 = line.match(/(\d{2})[\/\-](\d{1,2})[\/\-](\d{1,2})/);
    if(m2){ date=`20${m2[1]}-${m2[2].padStart(2,'0')}-${m2[3].padStart(2,'0')}`; break; }
  }

  // 店名抽出（最初の数行から）
  let store = '不明';
  const skipWords = ['領収証','レシート','領収書','合計','小計','税','お預り','お釣','ありがとう','TEL','FAX','〒'];
  for(const line of lines.slice(0,8)){
    if(line.length >= 2 && !skipWords.some(w=>line.includes(w)) && !/^\d/.test(line)){
      store = line.replace(/[※＊\*]/g,'').trim();
      if(store.length >= 2){ break; }
    }
  }

  // 商品と金額を抽出
  const items = [];
  const pricePattern = /[¥￥\\]?\s*(\d{1,6})\s*$/;
  const skipLineWords = ['合計','小計','税','お預り','お釣','ポイント','割引','値引','軽減','外税','内税','対象','釣銭','預り','change','total','subtotal','消費税'];

  for(const line of lines){
    if(skipLineWords.some(w=>line.toLowerCase().includes(w.toLowerCase()))) continue;
    const m = line.match(/^(.+?)\s+[¥￥\\]?\s*(\d{1,6})\s*$/);
    if(m){
      const name = m[1].replace(/[※＊\*×x]/g,'').trim();
      const amount = parseInt(m[2]);
      if(name.length >= 1 && amount >= 10 && amount <= 100000){
        items.push({
          name: name,
          amount: amount,
          category: guessCategory(name)
        });
      }
    }
  }

  // 金額だけの行パターンも試す
  if(items.length === 0){
    for(const line of lines){
      if(skipLineWords.some(w=>line.toLowerCase().includes(w.toLowerCase()))) continue;
      const m = line.match(/(.{2,20}?)\s*[¥￥]?\s*(\d{2,6})/);
      if(m){
        const name = m[1].replace(/[※＊\*]/g,'').trim();
        const amount = parseInt(m[2]);
        if(name.length >= 1 && amount >= 10 && amount <= 100000 && !/^\d+$/.test(name)){
          items.push({ name, amount, category: guessCategory(name) });
        }
      }
    }
  }

  return { date, store, items };
}

// カテゴリ推定
function guessCategory(name){
  const n = name;
  if(/パン|米|野菜|肉|魚|牛乳|卵|豆腐|納豆|弁当|おにぎり|飲料|ジュース|お茶|水|食品|惣菜|果物|フルーツ|冷凍|ハム|ソーセージ|チーズ|ヨーグルト|アイス|菓子|スナック|チョコ|せんべい/.test(n)) return '食費';
  if(/洗剤|シャンプー|トイレ|ティッシュ|タオル|石鹸|歯ブラシ|洗濯|掃除|電池|ラップ|袋|ポリ/.test(n)) return '日用品';
  if(/電車|バス|タクシー|新幹線|切符|定期|ガソリン|駐車|交通/.test(n)) return '交通費';
  if(/ランチ|ディナー|レストラン|カフェ|コーヒー|ラーメン|寿司|焼肉|居酒屋|食事|外食/.test(n)) return '外食';
  if(/薬|医院|病院|クリニック|調剤|処方|サプリ|ビタミン/.test(n)) return '医療';
  if(/映画|ゲーム|本|雑誌|音楽|スポーツ|ジム|娯楽|趣味/.test(n)) return '娯楽';
  if(/服|シャツ|パンツ|靴|下着|ソックス|ジャケット|コート|衣類|ファッション/.test(n)) return '衣類';
  return 'その他';
}

function setLoading(on, step=''){
  document.getElementById('lov').className='lov'+(on?' show':'');
  if(step) document.getElementById('lstep').textContent=step;
}

function switchTab(t){
  ['scan','ledger'].forEach(x=>{
    document.getElementById('panel-'+x).className='panel'+(x===t?' active':'');
    document.getElementById('tab-'+x).className='tab'+(x===t?' active':'');
  });
  document.getElementById('tab-ledger').textContent=`📋 家計簿${entries.length>0?' ('+entries.length+')':''}`;
}

function renderLedger(){
  const list=document.getElementById('elist'),empty=document.getElementById('empty'),trow=document.getElementById('trow'),cbar=document.getElementById('cbar');
  if(!entries.length){empty.style.display='block';trow.style.display='none';cbar.className='cbar';list.innerHTML='';return;}
  empty.style.display='none';
  const ct={};entries.forEach(e=>{ct[e.category]=(ct[e.category]||0)+e.amount;});
  cbar.className='cbar show';
  document.getElementById('chips').innerHTML=Object.entries(ct).sort((a,b)=>b[1]-a[1]).map(([c,a])=>
    `<div class="chip" style="background:${COLS[c]}18;border:1px solid ${COLS[c]}44"><span class="cdot" style="background:${COLS[c]}"></span>${c} <strong>¥${a.toLocaleString()}</strong></div>`
  ).join('');
  list.innerHTML=[...entries].sort((a,b)=>b.date.localeCompare(a.date)).map(e=>e.id===editId?editForm(e):ecard(e)).join('');
  const total=entries.reduce((s,e)=>s+e.amount,0);
  trow.style.display='block'; trow.textContent=`合計 ¥${total.toLocaleString()}`;
}

function ecard(e){
  return `<div class="ecard"><span class="edot" style="background:${COLS[e.category]}"></span><div class="emain"><div class="ename">${e.name}</div><div class="emeta">${e.date.replace(/-/g,'/')} · ${e.store} · <span style="color:${COLS[e.category]}">${e.category}</span></div></div><div class="eamt">¥${e.amount.toLocaleString()}</div><div class="ebtns"><button class="ebtn" onclick="startEdit(${e.id})">✎</button><button class="ebtn del" onclick="delEntry(${e.id})">🗑</button></div></div>`;
}

function editForm(e){
  return `<div class="eform"><div class="efg">
    <div><label class="efl">日付</label><input class="efi" id="ef-date" type="date" value="${e.date}"></div>
    <div><label class="efl">店舗</label><input class="efi" id="ef-store" value="${e.store}"></div>
    <div><label class="efl">品目</label><input class="efi" id="ef-name" value="${e.name}"></div>
    <div><label class="efl">カテゴリ</label><select class="efi" id="ef-cat">${CATS.map(c=>`<option${c===e.category?' selected':''}>${c}</option>`).join('')}</select></div>
    <div><label class="efl">金額</label><input class="efi" id="ef-amt" type="number" value="${e.amount}"></div>
  </div><div class="efbtns"><button class="esave" onclick="saveEdit(${e.id})">✓ 保存</button><button class="ecancel" onclick="cancelEdit()">キャンセル</button></div></div>`;
}

function delEntry(id){entries=entries.filter(e=>e.id!==id);renderLedger();updateStats();}
function startEdit(id){editId=id;renderLedger();}
function cancelEdit(){editId=null;renderLedger();}
function saveEdit(id){
  const i=entries.findIndex(e=>e.id===id);
  entries[i]={...entries[i],date:document.getElementById('ef-date').value,store:document.getElementById('ef-store').value,name:document.getElementById('ef-name').value,category:document.getElementById('ef-cat').value,amount:Number(document.getElementById('ef-amt').value)};
  editId=null;renderLedger();updateStats();
}

function updateStats(){
  const bar=document.getElementById('stats');
  if(!entries.length){bar.className='stats';return;}
  bar.className='stats show';
  const total=entries.reduce((s,e)=>s+e.amount,0);
  const ct={};entries.forEach(e=>{ct[e.category]=(ct[e.category]||0)+e.amount;});
  const top=Object.entries(ct).sort((a,b)=>b[1]-a[1])[0];
  document.getElementById('s-total').textContent=`¥${total.toLocaleString()}`;
  document.getElementById('s-count').textContent=`${entries.length}件`;
  document.getElementById('s-top').textContent=top?top[0]:'—';
}

function exportExcel(){
  if(!entries.length){showToast('データがありません');return;}
  const total=entries.reduce((s,e)=>s+e.amount,0);
  const ct={};entries.forEach(e=>{ct[e.category]=(ct[e.category]||0)+e.amount;});
  const gr={};entries.forEach(e=>{const ym=e.date.slice(0,7);if(!gr[ym])gr[ym]=[];gr[ym].push(e);});
  let h=`<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:x="urn:schemas-microsoft-com:office:excel" xmlns="http://www.w3.org/TR/REC-html40"><head><meta charset="UTF-8"><style>body{font-family:'Meiryo',sans-serif;font-size:11pt}h2{color:#0f172a;font-size:13pt;margin:14pt 0 3pt}table{border-collapse:collapse;width:100%;margin-bottom:10pt}th{background:#0f172a;color:white;padding:5pt 9pt;text-align:center;border:1px solid #1e293b}td{padding:4pt 9pt;border:1px solid #e2e8f0}tr:nth-child(even) td{background:#f8fafc}.tot td{background:#e0f2fe;font-weight:bold;color:#0369a1}.grand td{background:#dbeafe;font-weight:bold}</style></head><body><h1 style="font-size:17pt;color:#0f172a;border-bottom:3pt solid #38bdf8;padding-bottom:5pt;">📒 家計簿レポート</h1><p style="color:#64748b">出力日: ${new Date().toLocaleDateString('ja-JP')}</p><h2>📊 カテゴリ別集計</h2><table><tr><th>カテゴリ</th><th>合計金額</th><th>割合</th></tr>`;
  Object.entries(ct).sort((a,b)=>b[1]-a[1]).forEach(([c,a])=>{h+=`<tr><td>${c}</td><td align="right">¥${a.toLocaleString()}</td><td align="center">${((a/total)*100).toFixed(1)}%</td></tr>`;});
  h+=`<tr class="grand"><td>合計</td><td align="right">¥${total.toLocaleString()}</td><td align="center">100%</td></tr></table>`;
  Object.entries(gr).sort().forEach(([ym,rows])=>{
    const[y,m]=ym.split('-');const mt=rows.reduce((s,e)=>s+e.amount,0);
    h+=`<h2>📅 ${y}年${parseInt(m)}月</h2><table><tr><th>日付</th><th>店舗</th><th>品目</th><th>カテゴリ</th><th>金額</th></tr>`;
    rows.sort((a,b)=>a.date.localeCompare(b.date)).forEach(e=>{h+=`<tr><td>${e.date.replace(/-/g,'/')}</td><td>${e.store}</td><td>${e.name}</td><td>${e.category}</td><td align="right">¥${e.amount.toLocaleString()}</td></tr>`;});
    h+=`<tr class="tot"><td colspan="4" align="right">月計</td><td align="right">¥${mt.toLocaleString()}</td></tr></table>`;
  });
  h+='</body></html>';
  const a=document.createElement('a');
  a.href=URL.createObjectURL(new Blob(['\uFEFF'+h],{type:'application/vnd.ms-excel;charset=utf-8'}));
  a.download=`家計簿_${new Date().toISOString().slice(0,10)}.xls`;
  a.click();
  showToast('📥 Excelファイルを保存しました');
}

function showToast(msg){
  const t=document.getElementById('toast');t.textContent=msg;t.className='show';
  setTimeout(()=>{t.className=''},3500);
}

renderLedger();
</script>
</body>
</html>
