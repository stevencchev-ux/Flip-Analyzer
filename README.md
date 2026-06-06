<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Deal Analyzer</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0f0f0f;
    --surface: #1a1a1a;
    --surface2: #222222;
    --border: #2a2a2a;
    --border2: #333333;
    --text: #f0ede8;
    --muted: #888880;
    --hint: #555550;
    --accent: #c8f060;
    --accent-dim: #8aaa30;
    --danger: #f06060;
    --warn: #f0a840;
    --info: #60a8f0;
    --grade-a-bg: #1a2a0a; --grade-a: #a8e040;
    --grade-b-bg: #0a1a2a; --grade-b: #60a8f0;
    --grade-c-bg: #2a1a0a; --grade-c: #f0a840;
    --grade-d-bg: #2a0a0a; --grade-d: #f06060;
    --font-mono: 'DM Mono', monospace;
    --font-sans: 'DM Sans', sans-serif;
  }
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--font-sans);
    font-size: 14px;
    line-height: 1.6;
    min-height: 100vh;
  }

  header {
    padding: 2rem 2rem 1.5rem;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: baseline;
    gap: 1.5rem;
  }
  header h1 {
    font-family: var(--font-mono);
    font-size: 13px;
    font-weight: 500;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--accent);
  }
  header span {
    font-size: 12px;
    color: var(--hint);
    font-family: var(--font-mono);
  }

  .container { max-width: 960px; margin: 0 auto; padding: 2rem; }

  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; }
  .grid3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 1rem; }
  .grid4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 1rem; }

  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1.25rem 1.5rem;
    margin-bottom: 1rem;
  }
  .card-title {
    font-family: var(--font-mono);
    font-size: 10px;
    font-weight: 500;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--hint);
    margin-bottom: 1.25rem;
  }

  .field { display: flex; flex-direction: column; gap: 5px; }
  .field label {
    font-family: var(--font-mono);
    font-size: 10px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
  }
  .field input, .field select {
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: 3px;
    color: var(--text);
    font-family: var(--font-mono);
    font-size: 13px;
    padding: 7px 10px;
    width: 100%;
    outline: none;
    transition: border-color 0.15s;
    -webkit-appearance: none;
  }
  .field input:focus, .field select:focus { border-color: var(--accent-dim); }
  .field select { cursor: pointer; }

  .toggle-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 0; }
  .toggle-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 8px 0;
    border-bottom: 1px solid var(--border);
    gap: 12px;
  }
  .toggle-item:nth-last-child(-n+2) { border-bottom: none; }
  .toggle-item span { font-size: 13px; color: var(--text); }
  .toggle-item input[type=checkbox] {
    width: 16px; height: 16px;
    accent-color: var(--accent);
    cursor: pointer;
    flex-shrink: 0;
  }
  .toggle-col { padding: 0 0; }
  .toggle-col:first-child .toggle-item { padding-right: 1.5rem; }
  .toggle-col:last-child .toggle-item { padding-left: 1.5rem; border-left: 1px solid var(--border); }

  .run-btn {
    width: 100%;
    padding: 14px;
    background: var(--accent);
    color: #0f0f0f;
    border: none;
    border-radius: 4px;
    font-family: var(--font-mono);
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    cursor: pointer;
    margin-bottom: 2rem;
    transition: background 0.15s, transform 0.1s;
  }
  .run-btn:hover { background: #d8ff70; }
  .run-btn:active { transform: scale(0.99); }

  #results { display: none; }

  .grade-strip {
    display: flex;
    align-items: center;
    gap: 1.5rem;
    padding: 1.25rem 1.5rem;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    margin-bottom: 1rem;
    flex-wrap: wrap;
  }
  .grade-badge {
    font-family: var(--font-mono);
    font-size: 28px;
    font-weight: 500;
    width: 52px; height: 52px;
    display: flex; align-items: center; justify-content: center;
    border-radius: 4px;
    flex-shrink: 0;
  }
  .grade-A { background: var(--grade-a-bg); color: var(--grade-a); border: 1px solid var(--grade-a); }
  .grade-B { background: var(--grade-b-bg); color: var(--grade-b); border: 1px solid var(--grade-b); }
  .grade-C { background: var(--grade-c-bg); color: var(--grade-c); border: 1px solid var(--grade-c); }
  .grade-D { background: var(--grade-d-bg); color: var(--grade-d); border: 1px solid var(--grade-d); }
  .grade-basis { font-size: 13px; color: var(--text); flex: 1; min-width: 160px; }
  .grade-basis small { display: block; font-size: 11px; color: var(--muted); margin-top: 2px; font-family: var(--font-mono); }
  .flags { display: flex; flex-wrap: wrap; gap: 6px; }
  .flag {
    font-family: var(--font-mono);
    font-size: 10px;
    letter-spacing: 0.05em;
    padding: 3px 8px;
    border-radius: 2px;
    border: 1px solid;
  }
  .flag-warn { background: #2a1a0a; color: var(--warn); border-color: var(--warn); }
  .flag-bad { background: #2a0a0a; color: var(--danger); border-color: var(--danger); }
  .flag-good { background: #1a2a0a; color: var(--accent); border-color: var(--accent); }

  .summary-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 1rem; }
  .summary-col {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1.25rem 1.5rem;
  }
  .summary-col-title {
    font-family: var(--font-mono);
    font-size: 10px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--hint);
    margin-bottom: 1rem;
  }
  .row {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    padding: 5px 0;
    border-bottom: 1px solid var(--border);
    font-size: 13px;
    gap: 8px;
  }
  .row:last-child { border-bottom: none; }
  .row .n { color: var(--muted); white-space: nowrap; }
  .row .v { font-family: var(--font-mono); font-size: 12px; color: var(--text); text-align: right; }
  .row.sub-row { opacity: 0.6; }
  .row.sub-row .n { font-size: 11px; }
  .row.sub-row .v { font-size: 11px; color: var(--muted); }
  .row.total-row { margin-top: 4px; padding-top: 10px; border-top: 1px solid var(--border2); border-bottom: none; }
  .row.total-row .n { color: var(--text); font-weight: 500; }
  .row.profit-row { margin-top: 6px; border-bottom: none; }
  .row.profit-row .n { font-size: 15px; font-weight: 500; color: var(--text); }
  .row.profit-row .v { font-size: 18px; font-family: var(--font-mono); }
  .profit-pos { color: var(--accent) !important; }
  .profit-neg { color: var(--danger) !important; }
  .divider { border: none; border-top: 1px solid var(--border); margin: 8px 0; }

  .glance-grid { display: grid; grid-template-columns: repeat(4,1fr); gap: 1rem; margin-bottom: 2rem; }
  .glance-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1rem 1.25rem;
    text-align: center;
  }
  .glance-card .gl { font-family: var(--font-mono); font-size: 10px; letter-spacing: 0.1em; text-transform: uppercase; color: var(--hint); margin-bottom: 6px; }
  .glance-card .gv { font-family: var(--font-mono); font-size: 20px; font-weight: 500; color: var(--text); }
  .glance-card .gs { font-size: 11px; color: var(--hint); margin-top: 3px; }

  hr.section-div { border: none; border-top: 1px solid var(--border); margin: 6px 0 8px; }

  @media (max-width: 680px) {
    .container { padding: 1rem; }
    .grid2, .grid3, .grid4, .summary-grid, .glance-grid { grid-template-columns: 1fr; }
    .toggle-grid { grid-template-columns: 1fr; }
    .toggle-col:last-child .toggle-item { border-left: none; padding-left: 0; }
    .toggle-item:nth-last-child(-n+2) { border-bottom: 1px solid var(--border); }
    .toggle-item:last-child { border-bottom: none; }
    header { flex-direction: column; gap: 4px; }
  }
</style>
</head>
<body>

<header>
  <h1>Deal Analyzer</h1>
  <span>SoCal Fix &amp; Flip · Block 1 Engine</span>
</header>

<div class="container">

  <div class="card">
    <div class="card-title">Deal inputs</div>
    <div class="grid3" style="margin-bottom:1rem">
      <div class="field"><label>Purchase price ($)</label><input type="number" id="purchase" value="550000" step="1000"></div>
      <div class="field"><label>Wholesaler ARV ($)</label><input type="number" id="arv" value="850000" step="1000"></div>
      <div class="field"><label>Sqft</label><input type="number" id="sqft" value="1000" step="50"></div>
    </div>
    <div class="grid3">
      <div class="field"><label>Bedrooms</label><select id="beds"><option>2</option><option selected>3</option><option>4</option></select></div>
      <div class="field"><label>Bathrooms</label><select id="baths"><option>1</option><option selected>2</option><option>3</option></select></div>
      <div class="field"><label>Market</label><select id="market"><option>Long Beach</option><option>Torrance</option><option>Orange County</option></select></div>
    </div>
  </div>

  <div class="card">
    <div class="card-title">Base scope</div>
    <div class="grid3">
      <div class="field"><label>Kitchen cabinets (qty)</label><input type="number" id="cab_qty" value="10"></div>
      <div class="field"><label>Windows (qty)</label><input type="number" id="win_qty" value="6"></div>
      <div class="field"><label>Recessed lights (qty)</label><input type="number" id="light_qty" value="16"></div>
    </div>
  </div>

  <div class="card">
    <div class="card-title">Optional scope</div>
    <div class="toggle-grid">
      <div class="toggle-col">
        <div class="toggle-item"><span>Roof replacement</span><input type="checkbox" id="opt_roof"></div>
        <div class="toggle-item"><span>HVAC + ducts</span><input type="checkbox" id="opt_hvac"></div>
        <div class="toggle-item"><span>Water heater</span><input type="checkbox" id="opt_wh"></div>
        <div class="toggle-item"><span>Panel upgrade</span><input type="checkbox" id="opt_panel"></div>
        <div class="toggle-item"><span>Bathroom exhaust fans</span><input type="checkbox" id="opt_fans"></div>
      </div>
      <div class="toggle-col">
        <div class="toggle-item"><span>Front door</span><input type="checkbox" id="opt_fdoor"></div>
        <div class="toggle-item"><span>Garage door + motor</span><input type="checkbox" id="opt_garage"></div>
        <div class="toggle-item"><span>Blinds</span><input type="checkbox" id="opt_blinds"></div>
        <div class="toggle-item"><span>Primer</span><input type="checkbox" id="opt_primer"></div>
        <div class="toggle-item"><span>Dishwasher</span><input type="checkbox" id="opt_dw"></div>
      </div>
    </div>
  </div>

  <button class="run-btn" onclick="analyze()">Run Deal Analysis</button>

  <div id="results">

    <div class="grade-strip">
      <div id="grade_badge" class="grade-badge grade-A">A</div>
      <div class="grade-basis">
        <span id="grade_basis">—</span>
        <small id="hold_note">—</small>
      </div>
      <div class="flags" id="flags_container"></div>
    </div>

    <div class="glance-grid">
      <div class="glance-card"><div class="gl">Best case profit</div><div class="gv" id="g_best">—</div><div class="gs">Base scope</div></div>
      <div class="glance-card"><div class="gl">Worst case profit</div><div class="gv" id="g_worst">—</div><div class="gs">Full scope</div></div>
      <div class="glance-card"><div class="gl">Total invested</div><div class="gv" id="g_invested">—</div><div class="gs">All-in base scope</div></div>
      <div class="glance-card"><div class="gl">Best case ROI</div><div class="gv" id="g_roi">—</div><div class="gs">Net profit / invested</div></div>
    </div>

    <div class="summary-grid">
      <div class="summary-col">
        <div class="summary-col-title">Base scope</div>
        <div class="row"><span class="n">Purchase price</span><span class="v" id="b_purchase">—</span></div>
        <div class="row"><span class="n">Closing costs (buy 1.5%)</span><span class="v" id="b_closing_buy">—</span></div>
        <div class="row sub-row"><span class="n">subtotal — acquisition</span><span class="v" id="b_acq_sub">—</span></div>
        <hr class="section-div">
        <div class="row"><span class="n">Materials (incl. 10.5% tax)</span><span class="v" id="b_mat">—</span></div>
        <div class="row"><span class="n">Labor</span><span class="v" id="b_lab">—</span></div>
        <div class="row"><span class="n">Disposal</span><span class="v">$1,500</span></div>
        <div class="row"><span class="n">Contingency (12%)</span><span class="v" id="b_contingency">—</span></div>
        <div class="row sub-row"><span class="n">subtotal — renovation</span><span class="v" id="b_reno_sub">—</span></div>
        <hr class="section-div">
        <div class="row"><span class="n">Carry costs</span><span class="v" id="b_carry">—</span></div>
        <div class="row"><span class="n">Selling costs (2.5% ARV)</span><span class="v" id="b_closing_sell">—</span></div>
        <hr class="section-div">
        <div class="row total-row"><span class="n">Total out of pocket</span><span class="v" id="b_total_out">—</span></div>
        <div class="row profit-row"><span class="n">Net profit</span><span class="v" id="b_profit">—</span></div>
      </div>

      <div class="summary-col">
        <div class="summary-col-title">Full scope — worst case</div>
        <div class="row"><span class="n">Purchase price</span><span class="v" id="f_purchase">—</span></div>
        <div class="row"><span class="n">Closing costs (buy 1.5%)</span><span class="v" id="f_closing_buy">—</span></div>
        <div class="row sub-row"><span class="n">subtotal — acquisition</span><span class="v" id="f_acq_sub">—</span></div>
        <hr class="section-div">
        <div class="row"><span class="n">Materials (incl. 10.5% tax)</span><span class="v" id="f_mat">—</span></div>
        <div class="row"><span class="n">Labor</span><span class="v" id="f_lab">—</span></div>
        <div class="row"><span class="n">Sub-contracted (flat)</span><span class="v" id="f_subs">—</span></div>
        <div class="row"><span class="n">Disposal</span><span class="v">$1,500</span></div>
        <div class="row"><span class="n">Contingency (15%)</span><span class="v" id="f_contingency">—</span></div>
        <div class="row sub-row"><span class="n">subtotal — renovation</span><span class="v" id="f_reno_sub">—</span></div>
        <hr class="section-div">
        <div class="row"><span class="n">Carry costs</span><span class="v" id="f_carry">—</span></div>
        <div class="row"><span class="n">Selling costs (2.5% ARV)</span><span class="v" id="f_closing_sell">—</span></div>
        <hr class="section-div">
        <div class="row total-row"><span class="n">Total out of pocket</span><span class="v" id="f_total_out">—</span></div>
        <div class="row profit-row"><span class="n">Net profit</span><span class="v" id="f_profit">—</span></div>
      </div>
    </div>

  </div>
</div>

<script>
const TAX=1.105,DISPOSAL=1500,SELL_PCT=0.025;
const rA=400,rB=300,rC=300,rD=260,rE=160;
function fmt(n){return'$'+Math.round(n).toLocaleString()}
function pct(n){return(Math.round(n*10)/10)+'%'}

function calcMaterials(sqft,beds,baths,cab_qty,win_qty,light_qty){
  const doors=beds+baths+1,m={};
  m.lvp=sqft*1.79;
  m.baseboards=Math.ceil((sqft*0.5)/16)*8.25;
  m.casings=doors*2*7.90;
  m.cabinets=cab_qty*425;
  m.countertop=2000;m.sink=350;m.sink_faucet=150;
  m.disposal_mat=175+35;m.range=900;m.range_hood=250;m.fridge=1200;
  m.vanities=baths*425;m.toilets=baths*165;m.tubs=baths*550;
  m.shower_tile_wall=baths*90*3.75;m.bath_floor_tile=baths*45*3.75;
  m.schluter=baths*25;m.thinset=baths*4*15;m.grout=baths*25;
  m.spacers=baths*50;m.waterproofing=baths*80;
  m.backer_board=baths*Math.ceil(90/32)*17.50;
  m.shower_doors=baths*300;m.vanity_lights=baths*40;
  m.shower_trim=baths*50;m.shower_valves=baths*100;
  m.ptrap=baths*40;m.bath_faucet=baths*30;m.towel_bar=baths*25;
  const rr=Math.ceil(light_qty/8);
  m.romex=rr*85;m.lights=Math.ceil(light_qty/16)*73;
  m.gfci=Math.ceil((baths+2)/4)*56;
  m.outlets=Math.ceil((sqft/100)*0.5)*20;
  m.switches=Math.ceil(doors*0.6)*22;
  m.faceplates=Math.ceil((baths*2+doors)*1.5/10)*8;
  m.smoke=(beds+baths+2)*54;
  m.paint=Math.ceil(sqft/1000)*168;
  m.doors=doors*100;
  m.door_hw=Math.ceil(doors/30)*25;
  m.cab_hw=Math.ceil(cab_qty*2/30)*25;
  m.consumables=sqft*0.175;
  m.supply=(baths+1)*25;
  return Object.values(m).reduce((a,b)=>a+b,0)*TAX;
}

function calcLabor(sqft,beds,baths,cab_qty,win_qty,light_qty){
  const doors=beds+baths+1,rr=Math.ceil(light_qty/8);
  let l=0;
  l+=rB+rC+rD+rE;
  l+=(rB+rC)*3+250;
  l+=rB*0.5;
  l+=(doors*20/60/8)*rB;
  l+=(rB+rC)*1.5;
  l+=(win_qty*0.5/8)*rB;
  l+=(baths*0.5/8)*rB;
  l+=(baths*10/60/8)*rB;
  l+=(baths*2/8)*rB;
  l+=baths*rA*2;
  l+=baths*rA*0.5;
  l+=baths*150;
  l+=(baths*3.5/8)*rB;
  l+=(baths*0.75/8)*rB;
  l+=(rr*2/8)*rB;
  l+=(2.5/8)*rB;
  l+=(doors*1.25/8)*rB;
  l+=(rB+rC)*2;
  return l;
}

function calcOpt(beds,baths,sqft,win_qty){
  let mt=0,lab=0,sub=0;
  if(document.getElementById('opt_roof').checked) sub+=13000;
  if(document.getElementById('opt_hvac').checked) sub+=11900;
  if(document.getElementById('opt_panel').checked) sub+=7000;
  if(document.getElementById('opt_garage').checked) sub+=2500;
  if(document.getElementById('opt_wh').checked){mt+=900;lab+=400;}
  if(document.getElementById('opt_fdoor').checked){mt+=500;lab+=rB;}
  if(document.getElementById('opt_fans').checked){mt+=baths*65;lab+=baths*(2/8)*rB;}
  if(document.getElementById('opt_blinds').checked){mt+=win_qty*60;lab+=(win_qty*0.5/8)*rB;}
  if(document.getElementById('opt_primer').checked){mt+=Math.ceil(sqft/1000)*80;lab+=(rB+rC);}
  if(document.getElementById('opt_dw').checked){mt+=600;lab+=rB*0.5;}
  return{mat:mt*TAX,lab,sub};
}

function analyze(){
  const purchase=+document.getElementById('purchase').value;
  const arv=+document.getElementById('arv').value;
  const sqft=+document.getElementById('sqft').value;
  const beds=+document.getElementById('beds').value;
  const baths=+document.getElementById('baths').value;
  const cab_qty=+document.getElementById('cab_qty').value;
  const win_qty=+document.getElementById('win_qty').value;
  const light_qty=+document.getElementById('light_qty').value;
  const holdWeeks=sqft<=1100?6:8;
  const holdMonths=holdWeeks/4.33;

  const mb=calcMaterials(sqft,beds,baths,cab_qty,win_qty,light_qty);
  const lb=calcLabor(sqft,beds,baths,cab_qty,win_qty,light_qty);
  const base_pre=mb+lb+DISPOSAL;
  const bc=base_pre*0.12;
  const base_reno=base_pre+bc;

  const opt=calcOpt(beds,baths,sqft,win_qty);
  const full_pre=base_pre+opt.mat+opt.lab+opt.sub;
  const fc=full_pre*0.15;
  const full_reno=full_pre+fc;

  const carry=(purchase*0.012/12)*holdMonths+200*holdMonths+175*holdMonths;
  const cb=purchase*0.015;
  const cs=arv*SELL_PCT;

  const b_out=purchase+cb+base_reno+carry+cs;
  const f_out=purchase+cb+full_reno+carry+cs;
  const bp=arv-b_out;
  const fp=arv-f_out;
  const inv=purchase+cb+base_reno+carry;
  const roi=(bp/inv)*100;

  let grade,basis;
  if(fp>=70000){grade='A';basis='Worst case clears $70K — strong buy candidate';}
  else if(fp>=50000){grade='B';basis='Worst case $50K–$69K — worth a walkthrough';}
  else if(bp>=50000){grade='C';basis='Only pencils on base scope — inspect before pursuing';}
  else{grade='D';basis='Does not hit $50K under any scenario';}

  const flags=[];
  if(arv/sqft>900)flags.push({t:'ARV >$900/sqft',cls:'flag-warn'});
  if(bp-fp>60000)flags.push({t:'Wide scope spread',cls:'flag-warn'});
  if(fp<0)flags.push({t:'Full scope underwater',cls:'flag-bad'});
  if(roi>20)flags.push({t:'Strong ROI',cls:'flag-good'});
  if(holdWeeks<=6)flags.push({t:'Fast-flip profile',cls:'flag-good'});

  const el=id=>document.getElementById(id);
  el('results').style.display='block';
  const badge=el('grade_badge');
  badge.textContent=grade;badge.className='grade-badge grade-'+grade;
  el('grade_basis').textContent=basis;
  el('hold_note').textContent=holdWeeks+'-week hold · '+Math.round(carry).toLocaleString()+' carry';
  el('flags_container').innerHTML=flags.map(f=>`<span class="flag ${f.cls}">${f.t}</span>`).join('');

  el('g_best').textContent=fmt(bp);
  el('g_best').style.color=bp>=0?'var(--accent)':'var(--danger)';
  el('g_worst').textContent=fmt(fp);
  el('g_worst').style.color=fp>=0?'var(--accent)':'var(--danger)';
  el('g_invested').textContent=fmt(inv);
  el('g_roi').textContent=pct(roi);

  el('b_purchase').textContent=fmt(purchase);
  el('b_closing_buy').textContent=fmt(cb);
  el('b_acq_sub').textContent=fmt(purchase+cb);
  el('b_mat').textContent=fmt(mb);
  el('b_lab').textContent=fmt(lb);
  el('b_contingency').textContent=fmt(bc);
  el('b_reno_sub').textContent=fmt(base_reno);
  el('b_carry').textContent=fmt(carry);
  el('b_closing_sell').textContent=fmt(cs);
  el('b_total_out').textContent=fmt(b_out);
  const bpEl=el('b_profit');
  bpEl.textContent=fmt(bp);
  bpEl.className='v '+(bp>=0?'profit-pos':'profit-neg');

  el('f_purchase').textContent=fmt(purchase);
  el('f_closing_buy').textContent=fmt(cb);
  el('f_acq_sub').textContent=fmt(purchase+cb);
  el('f_mat').textContent=fmt(mb+opt.mat);
  el('f_lab').textContent=fmt(lb+opt.lab);
  el('f_subs').textContent=fmt(opt.sub);
  el('f_contingency').textContent=fmt(fc);
  el('f_reno_sub').textContent=fmt(full_reno);
  el('f_carry').textContent=fmt(carry);
  el('f_closing_sell').textContent=fmt(cs);
  el('f_total_out').textContent=fmt(f_out);
  const fpEl=el('f_profit');
  fpEl.textContent=fmt(fp);
  fpEl.className='v '+(fp>=0?'profit-pos':'profit-neg');

  el('results').scrollIntoView({behavior:'smooth',block:'start'});
}
</script>
</body>
</html>
