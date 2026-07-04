<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FD Maturity Calculator — Passbook</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Newsreader:ital,opsz,wght@0,6..72,400;0,6..72,500;0,6..72,600;1,6..72,500&family=IBM+Plex+Sans:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --ink:#12213B;
    --ink-soft:#3A4A66;
    --paper:#EDEFF2;
    --paper-line:#C7CCD6;
    --gold:#B08A2E;
    --gold-deep:#8C6C1F;
    --stamp-red:#8C2F2F;
    --white:#FBFBFA;
  }
  *{box-sizing:border-box;}
  html{scroll-behavior:smooth;}
  body{
    margin:0;
    background:var(--paper);
    color:var(--ink);
    font-family:'IBM Plex Sans', sans-serif;
    -webkit-font-smoothing:antialiased;
  }
  @media (prefers-reduced-motion: reduce){
    *{animation-duration:0.001ms !important; animation-iteration-count:1 !important; transition-duration:0.001ms !important;}
  }

  a{color:inherit;}
  .wrap{
    max-width:1040px;
    margin:0 auto;
    padding:0 24px;
  }

  /* ---- Ledger ruled background ---- */
  .ledger-bg{
    background-image: repeating-linear-gradient(
      to bottom,
      transparent 0px,
      transparent 43px,
      var(--paper-line) 43px,
      var(--paper-line) 44px
    );
  }

  header.top{
    padding:28px 0 18px;
    border-bottom:2px solid var(--ink);
  }
  header.top .wrap{
    display:flex;
    align-items:baseline;
    justify-content:space-between;
    flex-wrap:wrap;
    gap:8px;
  }
  .brand{
    font-family:'Newsreader', serif;
    font-weight:600;
    font-size:1.3rem;
    letter-spacing:0.01em;
  }
  .brand small{
    display:block;
    font-family:'IBM Plex Mono', monospace;
    font-size:0.62rem;
    letter-spacing:0.18em;
    text-transform:uppercase;
    color:var(--ink-soft);
    margin-top:2px;
  }
  .acct-no{
    font-family:'IBM Plex Mono', monospace;
    font-size:0.75rem;
    color:var(--ink-soft);
    letter-spacing:0.05em;
  }

  /* ---- Hero: passbook entry stamp ---- */
  .hero{
    padding:56px 0 40px;
  }
  .hero-grid{
    display:grid;
    grid-template-columns: 1.1fr 1fr;
    gap:48px;
    align-items:start;
  }
  @media (max-width: 860px){
    .hero-grid{grid-template-columns:1fr;}
  }

  .eyebrow{
    font-family:'IBM Plex Mono', monospace;
    font-size:0.7rem;
    letter-spacing:0.18em;
    text-transform:uppercase;
    color:var(--gold-deep);
    margin:0 0 10px;
  }
  h1{
    font-family:'Newsreader', serif;
    font-weight:500;
    font-size:clamp(2rem, 4vw, 2.7rem);
    line-height:1.15;
    margin:0 0 14px;
  }
  .lede{
    color:var(--ink-soft);
    font-size:1.02rem;
    line-height:1.6;
    max-width:44ch;
    margin:0;
  }

  /* controls */
  .controls{
    margin-top:34px;
    display:flex;
    flex-direction:column;
    gap:22px;
  }
  .field{
    display:flex;
    flex-direction:column;
    gap:8px;
  }
  .field-head{
    display:flex;
    justify-content:space-between;
    align-items:baseline;
    font-family:'IBM Plex Mono', monospace;
  }
  .field-head label{
    font-size:0.72rem;
    letter-spacing:0.1em;
    text-transform:uppercase;
    color:var(--ink-soft);
  }
  .field-head output{
    font-size:1rem;
    font-weight:600;
    color:var(--ink);
  }
  input[type="range"]{
    -webkit-appearance:none;
    width:100%;
    height:2px;
    background:var(--ink);
    outline:none;
    margin:6px 0;
  }
  input[type="range"]::-webkit-slider-thumb{
    -webkit-appearance:none;
    width:16px;
    height:16px;
    border-radius:50%;
    background:var(--gold);
    border:2px solid var(--ink);
    cursor:pointer;
    margin-top:-7px;
  }
  input[type="range"]::-moz-range-thumb{
    width:14px;
    height:14px;
    border-radius:50%;
    background:var(--gold);
    border:2px solid var(--ink);
    cursor:pointer;
  }
  input[type="range"]:focus-visible::-webkit-slider-thumb{
    outline:3px solid var(--gold-deep);
    outline-offset:2px;
  }
  input[type="range"]::-webkit-slider-runnable-track{
    height:2px;
    background:var(--ink);
  }

  select.compound-select{
    font-family:'IBM Plex Sans', sans-serif;
    font-size:0.9rem;
    padding:9px 10px;
    border:1.5px solid var(--ink);
    background:var(--white);
    color:var(--ink);
    border-radius:2px;
  }
  select.compound-select:focus-visible{
    outline:3px solid var(--gold-deep);
    outline-offset:1px;
  }

  /* ---- Passbook stamp card ---- */
  .passbook{
    background:var(--white);
    border:1.5px solid var(--ink);
    position:relative;
    padding:30px 28px 26px;
    box-shadow: 6px 6px 0 rgba(18,33,59,0.08);
  }
  .passbook::before{
    content:"";
    position:absolute;
    inset:8px;
    border:1px dashed var(--paper-line);
    pointer-events:none;
  }
  .passbook-row{
    display:flex;
    justify-content:space-between;
    font-family:'IBM Plex Mono', monospace;
    font-size:0.72rem;
    letter-spacing:0.06em;
    text-transform:uppercase;
    color:var(--ink-soft);
    margin-bottom:18px;
  }
  .maturity-label{
    font-family:'IBM Plex Mono', monospace;
    font-size:0.72rem;
    letter-spacing:0.14em;
    text-transform:uppercase;
    color:var(--ink-soft);
    margin:0 0 6px;
  }
  .maturity-amount{
    font-family:'Newsreader', serif;
    font-weight:600;
    font-size:clamp(2.1rem, 5vw, 2.9rem);
    line-height:1.05;
    margin:0 0 4px;
    font-variant-numeric: tabular-nums;
    transition: opacity 0.15s ease;
  }
  .maturity-sub{
    font-size:0.85rem;
    color:var(--ink-soft);
    margin:0 0 20px;
  }
  .interest-earned{
    color:var(--gold-deep);
    font-weight:600;
  }

  .stamp{
    position:absolute;
    top:24px;
    right:26px;
    width:82px;
    height:82px;
    border:2.5px solid var(--stamp-red);
    border-radius:50%;
    color:var(--stamp-red);
    display:flex;
    align-items:center;
    justify-content:center;
    text-align:center;
    font-family:'IBM Plex Mono', monospace;
    font-size:0.58rem;
    letter-spacing:0.06em;
    text-transform:uppercase;
    line-height:1.15;
    transform:rotate(-12deg);
    opacity:0.85;
    padding:6px;
  }
  @media (max-width:520px){
    .stamp{width:64px;height:64px;font-size:0.48rem;top:18px;right:18px;}
  }

  .breakdown-mini{
    display:flex;
    gap:22px;
    border-top:1px solid var(--paper-line);
    padding-top:16px;
    margin-top:4px;
  }
  .breakdown-mini div{flex:1;}
  .breakdown-mini .k{
    font-family:'IBM Plex Mono', monospace;
    font-size:0.65rem;
    letter-spacing:0.08em;
    text-transform:uppercase;
    color:var(--ink-soft);
    margin-bottom:4px;
  }
  .breakdown-mini .v{
    font-weight:600;
    font-variant-numeric: tabular-nums;
  }

  /* ---- Ledger table of yearly entries ---- */
  section.ledger-section{
    padding:10px 0 70px;
  }
  .section-head{
    display:flex;
    align-items:baseline;
    justify-content:space-between;
    border-bottom:2px solid var(--ink);
    padding-bottom:10px;
    margin-bottom:4px;
  }
  .section-head h2{
    font-family:'Newsreader', serif;
    font-weight:500;
    font-size:1.4rem;
    margin:0;
  }
  .section-head .note{
    font-family:'IBM Plex Mono', monospace;
    font-size:0.68rem;
    color:var(--ink-soft);
    letter-spacing:0.06em;
    text-transform:uppercase;
  }

  table.entries{
    width:100%;
    border-collapse:collapse;
    font-variant-numeric: tabular-nums;
  }
  table.entries th{
    text-align:right;
    font-family:'IBM Plex Mono', monospace;
    font-size:0.65rem;
    letter-spacing:0.08em;
    text-transform:uppercase;
    color:var(--ink-soft);
    font-weight:500;
    padding:14px 0 10px;
    border-bottom:1px solid var(--ink);
  }
  table.entries th:first-child, table.entries td:first-child{text-align:left;}
  table.entries td{
    text-align:right;
    padding:12px 0;
    border-bottom:1px solid var(--paper-line);
    font-size:0.92rem;
  }
  table.entries tr:last-child td{
    font-weight:600;
    border-bottom:2px solid var(--ink);
  }
  .row-year{
    font-family:'IBM Plex Mono', monospace;
    color:var(--ink-soft);
  }

  footer.foot{
    border-top:2px solid var(--ink);
    padding:26px 0 40px;
  }
  footer.foot p{
    font-family:'IBM Plex Mono', monospace;
    font-size:0.68rem;
    color:var(--ink-soft);
    letter-spacing:0.04em;
    line-height:1.6;
    max-width:60ch;
    margin:0;
  }

  .visually-hidden{
    position:absolute;
    width:1px;height:1px;
    overflow:hidden;
    clip:rect(0 0 0 0);
    white-space:nowrap;
  }
</style>
</head>
<body>

<header class="top">
  <div class="wrap">
    <div class="brand">Fixed Deposit Ledger<small>Maturity calculator</small></div>
    <div class="acct-no">No. FD-<span id="acctRandom">04072026</span></div>
  </div>
</header>

<section class="hero ledger-bg">
  <div class="wrap">
    <div class="hero-grid">
      <div>
        <p class="eyebrow">Entry no. 001</p>
        <h1>Work out what your deposit will be worth at maturity.</h1>
        <p class="lede">Set your principal, rate and tenure below. Every entry below updates like a fresh line in the passbook — no submit button, no waiting.</p>

        <div class="controls">
          <div class="field">
            <div class="field-head">
              <label for="principal">Principal amount</label>
              <output id="principalOut" for="principal">₹1,00,000</output>
            </div>
            <input type="range" id="principal" min="1000" max="5000000" step="1000" value="100000">
          </div>

          <div class="field">
            <div class="field-head">
              <label for="rate">Annual interest rate</label>
              <output id="rateOut" for="rate">7.00%</output>
            </div>
            <input type="range" id="rate" min="1" max="12" step="0.05" value="7">
          </div>

          <div class="field">
            <div class="field-head">
              <label for="tenure">Tenure (years)</label>
              <output id="tenureOut" for="tenure">5 years</output>
            </div>
            <input type="range" id="tenure" min="1" max="15" step="1" value="5">
          </div>

          <div class="field">
            <div class="field-head">
              <label for="compound">Compounding frequency</label>
            </div>
            <select id="compound" class="compound-select">
              <option value="4" selected>Quarterly</option>
              <option value="1">Annually</option>
              <option value="2">Half-yearly</option>
              <option value="12">Monthly</option>
            </select>
          </div>
        </div>
      </div>

      <div class="passbook">
        <div class="stamp">Maturity<br>Value<br>Certified</div>
        <div class="passbook-row">
          <span>Deposit entry</span>
          <span id="todayDate">04 Jul 2026</span>
        </div>
        <p class="maturity-label">Amount payable on maturity</p>
        <p class="maturity-amount" id="maturityAmount">₹1,41,478</p>
        <p class="maturity-sub">Principal <span id="subPrincipal">₹1,00,000</span> + interest <span class="interest-earned" id="subInterest">₹41,478</span></p>

        <div class="breakdown-mini">
          <div>
            <div class="k">Effective yield</div>
            <div class="v" id="effYield">7.19%</div>
          </div>
          <div>
            <div class="k">Tenure</div>
            <div class="v" id="tenureMini">5 yrs</div>
          </div>
          <div>
            <div class="k">Compounding</div>
            <div class="v" id="compMini">Quarterly</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<section class="ledger-section">
  <div class="wrap">
    <div class="section-head">
      <h2>Year-by-year entries</h2>
      <span class="note">Balance carried forward</span>
    </div>
    <table class="entries" id="entriesTable">
      <thead>
        <tr>
          <th>Year</th>
          <th>Opening balance</th>
          <th>Interest credited</th>
          <th>Closing balance</th>
        </tr>
      </thead>
      <tbody id="entriesBody">
      </tbody>
    </table>
  </div>
</section>

<footer class="foot">
  <div class="wrap">
    <p>Figures are illustrative, calculated by compound interest, and assume the deposit is held to full term without early withdrawal. Actual bank terms, penalties and tax deductions may change the amount you receive. This is not financial advice — check the current rate sheet with your bank before opening a deposit.</p>
  </div>
</footer>

<script>
  const principalEl = document.getElementById('principal');
  const rateEl = document.getElementById('rate');
  const tenureEl = document.getElementById('tenure');
  const compoundEl = document.getElementById('compound');

  const principalOut = document.getElementById('principalOut');
  const rateOut = document.getElementById('rateOut');
  const tenureOut = document.getElementById('tenureOut');

  const maturityAmountEl = document.getElementById('maturityAmount');
  const subPrincipalEl = document.getElementById('subPrincipal');
  const subInterestEl = document.getElementById('subInterest');
  const effYieldEl = document.getElementById('effYield');
  const tenureMiniEl = document.getElementById('tenureMini');
  const compMiniEl = document.getElementById('compMini');
  const entriesBody = document.getElementById('entriesBody');

  const inr = (n) => '₹' + Math.round(n).toLocaleString('en-IN');

  const compoundLabels = {1:'Annually', 2:'Half-yearly', 4:'Quarterly', 12:'Monthly'};

  function todayLabel(){
    const d = new Date();
    const months = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
    return String(d.getDate()).padStart(2,'0') + ' ' + months[d.getMonth()] + ' ' + d.getFullYear();
  }
  document.getElementById('todayDate').textContent = todayLabel();

  function calculate(){
    const P = parseFloat(principalEl.value);
    const rPct = parseFloat(rateEl.value);
    const years = parseInt(tenureEl.value, 10);
    const n = parseInt(compoundEl.value, 10);
    const r = rPct / 100;

    principalOut.textContent = inr(P);
    rateOut.textContent = rPct.toFixed(2) + '%';
    tenureOut.textContent = years + (years === 1 ? ' year' : ' years');

    const maturity = P * Math.pow(1 + r / n, n * years);
    const interest = maturity - P;
    const effectiveYield = (Math.pow(1 + r / n, n) - 1) * 100;

    maturityAmountEl.textContent = inr(maturity);
    subPrincipalEl.textContent = inr(P);
    subInterestEl.textContent = inr(interest);
    effYieldEl.textContent = effectiveYield.toFixed(2) + '%';
    tenureMiniEl.textContent = years + (years === 1 ? ' yr' : ' yrs');
    compMiniEl.textContent = compoundLabels[n];

    // Year by year ledger entries
    entriesBody.innerHTML = '';
    let opening = P;
    for(let y = 1; y <= years; y++){
      const closing = P * Math.pow(1 + r / n, n * y);
      const interestCredited = closing - opening;
      const tr = document.createElement('tr');
      tr.innerHTML =
        '<td class="row-year">Year ' + y + '</td>' +
        '<td>' + inr(opening) + '</td>' +
        '<td>' + inr(interestCredited) + '</td>' +
        '<td>' + inr(closing) + '</td>';
      entriesBody.appendChild(tr);
      opening = closing;
    }
  }

  [principalEl, rateEl, tenureEl, compoundEl].forEach(el => {
    el.addEventListener('input', calculate);
  });

  calculate();
</script>

</body>
</html>
