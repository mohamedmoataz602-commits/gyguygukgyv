<!DOCTYPE html><html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>EduPath – AI Compare & Semantic Search (v3)</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css" />
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@300;400;600;700;800&display=swap" rel="stylesheet">
  <style>
    :root{
      --primary:#ffffff;--secondary:#0d6efd;--secondary-600:#0b5ed7;--accent:#ffc107;--text:#1f2937;--muted:#6b7280;--bg:#f3f4f6;--card:#ffffff;--border:#e5e7eb;--ok:#22c55e;--warn:#f59e0b;--error:#ef4444
    }
    *{box-sizing:border-box}
    body{font-family:'Cairo',system-ui,-apple-system,Segoe UI,Roboto,Arial,sans-serif;margin:0;background:var(--bg);color:var(--text)}
    a{color:inherit}
    header{position:sticky;top:0;background:var(--primary);z-index:30;border-bottom:1px solid var(--border)}
    header .wrap{max-width:1200px;margin:auto;display:flex;gap:16px;align-items:center;justify-content:space-between;padding:12px 16px}
    .brand{font-weight:800;color:var(--secondary);text-decoration:none;font-size:22px}
    main{max-width:1200px;margin:0 auto;padding:16px}/* hero */
.hero{background:linear-gradient( to left, rgba(13,110,253,.07), rgba(13,110,253,.03) ), url('https://placehold.co/1200x360/0d6efd/ffffff?text=EduPath+AI') center/cover no-repeat;border-radius:18px;padding:36px 18px;color:#fff;overflow:hidden;position:relative}
.hero h1{margin:0 0 6px;font-size:36px}
.hero p{margin:0;opacity:.95}

/* controls */
.controls{margin-top:18px;background:var(--card);border:1px solid var(--border);border-radius:14px;padding:14px;display:grid;gap:12px}
.chip{display:inline-flex;align-items:center;gap:8px;padding:8px 12px;border-radius:999px;border:1px solid var(--border);background:#fff}

.row{display:flex;flex-wrap:wrap;gap:10px}
input[type="text"], select{padding:10px 12px;border-radius:10px;border:1px solid var(--border);background:#fff;min-width:220px}
.btn{appearance:none;border:1px solid var(--secondary);background:var(--secondary);color:#fff;border-radius:10px;padding:10px 14px;font-weight:700;cursor:pointer}
.btn.secondary{background:#fff;color:var(--secondary)}
.btn:disabled{opacity:.5;cursor:not-allowed}
.hint{color:var(--muted);font-size:12px}

/* results */
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:14px;margin-top:16px}
.card{background:var(--card);border:1px solid var(--border);border-radius:16px;overflow:hidden;display:flex;flex-direction:column;box-shadow:0 2px 8px rgba(0,0,0,.04)}
.card img{width:100%;height:160px;object-fit:cover}
.card .pad{padding:12px}
.card h3{margin:2px 0 6px;font-size:18px}
.meta{display:flex;gap:10px;flex-wrap:wrap;color:var(--muted);font-size:13px}
.badge{display:inline-flex;align-items:center;gap:6px;padding:4px 8px;border-radius:8px;background:#f9fafb;border:1px solid var(--border);font-size:12px}
.justify{margin-top:10px;color:#374151}
.actions{display:flex;gap:8px;margin-top:auto;padding:12px;border-top:1px solid var(--border)}

/* compare drawer */
.drawer{position:sticky;bottom:12px;background:var(--card);border:1px solid var(--border);border-radius:16px;padding:12px;box-shadow:0 6px 24px rgba(0,0,0,.07);display:flex;flex-direction:column;gap:10px}
.drawer h4{margin:0}
.sel{display:flex;gap:10px;align-items:stretch;overflow:auto}
.sel .pill{min-width:190px;background:#f8fafc;border:1px dashed var(--border);padding:10px;border-radius:12px;display:flex;flex-direction:column;gap:6px}
.pill strong{font-size:14px}

/* compare table */
.table{width:100%;border-collapse:separate;border-spacing:0 8px}
.table th,.table td{background:#fff;border:1px solid var(--border);padding:10px;border-radius:8px;text-align:center}
.table th{background:#f8fafc}

/* toast */
.toast{position:fixed;left:50%;transform:translateX(-50%);bottom:18px;background:#111827;color:#fff;padding:10px 14px;border-radius:10px;opacity:0;pointer-events:none;transition:opacity .25s}
.toast.show{opacity:1}

.sr{display:none}

  </style>
</head>
<body>
  <header>
    <div class="wrap">
      <a class="brand" href="#">EduPath</a>
      <nav class="meta" style="gap:14px">
        <span class="badge"><i class="fa-solid fa-wand-magic-sparkles"></i> بحث ذكي</span>
        <span class="badge"><i class="fa-solid fa-scale-balanced"></i> مقارنة جامعات</span>
      </nav>
    </div>
  </header>  <main>
    <section class="hero">
      <h1>ابحث بالذكاء الاصطناعي و قارِن بين الجامعات</h1>
      <p>اكتب وصفًا طبيعيًا مثل: "هندسة كمبيوتر بميزانية 100 ألف في القاهرة" وسنقترح أفضل الخيارات مع مقارنة تلقائية.</p>
    </section><!-- Controls -->
<section class="controls" aria-label="AI search and filters">
  <div class="row">
    <input id="q" type="text" placeholder="اكتب سؤالك.. مثال: صيدلة خاصة أقل من 120 ألف في اسكندرية" />
    <select id="typeFilter">
      <option value="">الكل (نوع الجامعة)</option>
      <option value="Public">حكومية</option>
      <option value="Private">خاصة</option>
      <option value="National">أهلية/وطنية</option>
    </select>
    <select id="facultyFilter">
      <option value="">كل الكليات</option>
    </select>
    <button class="btn" id="runAi"><i class="fa-solid fa-magnifying-glass"></i> بحث AI</button>
    <button class="btn secondary" id="clear">إعادة ضبط</button>
  </div>
  <small class="hint">نستخرج تلقائيًا الكلمات المفتاحية (التخصص/المدينة/الميزانية) ونستخدم تشابه دلالي (TF‑IDF + cosine) لترتيب النتائج.</small>
</section>

<!-- Results -->
<section>
  <div id="stats" class="meta" style="margin:10px 2px"></div>
  <div id="results" class="grid" aria-live="polite"></div>
</section>

<!-- Compare Drawer -->
<section class="drawer" aria-label="Compare universities">
  <h4><i class="fa-solid fa-scale-balanced"></i> المقارنة المختارة (<span id="cmpCount">0</span>/4)</h4>
  <div id="selected" class="sel"></div>
  <div class="row">
    <button id="genCompare" class="btn" disabled><i class="fa-solid fa-wand-magic-sparkles"></i> توليد المقارنة</button>
    <button id="resetCompare" class="btn secondary" disabled>مسح</button>
  </div>
  <div id="compareWrap" class="pad"></div>
</section>

  </main>  <div id="toast" class="toast" role="status" aria-live="polite"></div>  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>  <script>
  /******
   * Demo Data (Subset)
   * NOTE: Replace this array with your full 100+ universities dataset from your existing app.
   ******/
  const universities = [
    { id:1, name:'Cairo University', location:'Giza', description:"إحدى أعرق الجامعات المصرية..", imageUrl:'https://placehold.co/400x200/536878/FFF?text=Cairo+University', coordinates:[30.0275,31.2096], type:'Public', colleges:[{name:'Engineering', totalScorePercent:'95%', tuition:'EGP 10000 / year'},{name:'Medicine', totalScorePercent:'98%', tuition:'EGP 15000 / year'},{name:'Commerce', totalScorePercent:'80%', tuition:'EGP 5000 / year'}]},
    { id:2, name:'Ain Shams University', location:'Cairo', description:"جامعة حكومية مرموقة..", imageUrl:'https://placehold.co/400x200/A9A9A9/FFF?text=Ain+Shams', coordinates:[30.0754,31.2858], type:'Public', colleges:[{name:'Medicine', totalScorePercent:'97%', tuition:'EGP 12000 / year'},{name:'Engineering', totalScorePercent:'94%', tuition:'EGP 11000 / year'},{name:'Pharmacy', totalScorePercent:'96%', tuition:'EGP 13000 / year'}]},
    { id:5, name:'American University in Cairo (AUC)', location:'New Cairo', description:"جامعة خاصة باللغة الإنجليزية..", imageUrl:'https://placehold.co/400x200/87CEEB/FFF?text=AUC', coordinates:[29.9880,31.4397], type:'Private', colleges:[{name:'Business', totalScorePercent:'90%', tuition:'EGP 250000 / year'},{name:'Engineering', totalScorePercent:'92%', tuition:'EGP 280000 / year'}]},
    { id:6, name:'The German University in Cairo (GUC)', location:'New Cairo', description:"جامعة ألمانية خاصة..", imageUrl:'https://placehold.co/400x200/5F9EA0/FFF?text=GUC', coordinates:[29.9703,31.4239], type:'Private', colleges:[{name:'Engineering', totalScorePercent:'93%', tuition:'EGP 150000 / year'},{name:'Pharmacy', totalScorePercent:'94%', tuition:'EGP 160000 / year'}]},
    { id:8, name:'British University in Egypt (BUE)', location:'El Sherouk', description:"جامعة بنظام بريطاني..", imageUrl:'https://placehold.co/400x200/87CEEB/FFF?text=BUE', coordinates:[30.1332,31.5979], type:'Private', colleges:[{name:'Engineering', totalScorePercent:'90%', tuition:'EGP 140000 / year'},{name:'Pharmacy', totalScorePercent:'89%', tuition:'EGP 135000 / year'}]},
    { id:9, name:'Mansoura University', location:'Mansoura', description:"جامعة دلتا قوية في الطب..", imageUrl:'https://placehold.co/400x200/5F9EA0/FFF?text=Mansoura', coordinates:[31.0410,31.3802], type:'Public', colleges:[{name:'Medicine', totalScorePercent:'97%', tuition:'EGP 10000 / year'},{name:'Dentistry', totalScorePercent:'96%', tuition:'EGP 11000 / year'}]},
    { id:30, name:'Nile University', location:'Sheikh Zayed City', description:"جامعة خاصة غير هادفة للربح..", imageUrl:'https://placehold.co/400x200/DDA0DD/FFF?text=Nile+University', coordinates:[30.0163,30.9324], type:'Private', colleges:[{name:'Engineering', totalScorePercent:'91%', tuition:'EGP 130000 / year'},{name:'Business Administration', totalScorePercent:'86%', tuition:'EGP 110000 / year'}]}
  ];

  // Build faculty list for filter
  (function fillFacultyFilter(){
    const s = new Set();
    universities.forEach(u=>u.colleges.forEach(c=>s.add(c.name)));
    const sel = document.getElementById('facultyFilter');
    [...s].sort().forEach(f=>{
      const o=document.createElement('option');o.value=f;o.textContent=f;sel.appendChild(o);
    });
  })();

  /*** AI Utilities: tokenization, TF-IDF, cosine ***/
  const tokenize = (t)=> (t||'').toString().toLowerCase().replace(/[^\p{L}\p{N}\s]/gu,' ').split(/\s+/).filter(Boolean);
  const uniq = (arr)=>[...new Set(arr)];
  const docs = universities.map(u=>({
    id:u.id,
    text:[u.name,u.location,u.type,u.description, ...(u.colleges||[]).map(c=>${c.name} ${c.totalScorePercent} ${c.tuition})].join(' ')
  }));
  const corpusTokens = docs.map(d=>tokenize(d.text));
  const vocab = uniq(corpusTokens.flat());
  const df = Object.fromEntries(vocab.map(w=>[w,0]));
  corpusTokens.forEach(tokens=>uniq(tokens).forEach(w=>df[w]++));
  const idf = Object.fromEntries(Object.entries(df).map(([w,dfv])=>[w, Math.log( (1+docs.length) / (1+dfv) ) + 1]));
  function vecize(tokens){
    const tf = {};
    tokens.forEach(w=>tf[w]=(tf[w]||0)+1);
    const v = {};
    Object.keys(tf).forEach(w=>{
      if(!idf[w]) return; v[w]=tf[w]*idf[w];
    });
    return v;
  }
  function cosine(a,b){
    let dot=0,na=0,nb=0; const keys = new Set([...Object.keys(a),...Object.keys(b)]);
    keys.forEach(k=>{const va=a[k]||0,vb=b[k]||0;dot+=va*vb;na+=va*va;nb+=vb*vb});
    return dot/(Math.sqrt(na)*Math.sqrt(nb) || 1);
  }

  // Constraint extractor (very light NLU)
  function extract(query){
    const q = (query||'').toLowerCase();
    const faculty = [...new Set(universities.flatMap(u=>u.colleges.map(c=>c.name.toLowerCase())))].find(f=>q.includes(f.toLowerCase())) || '';
    const city = [...new Set(universities.map(u=>u.location.toLowerCase()))].find(loc=>q.includes(loc));
    const type = q.match(/\b(حكوم|حكومية|public)\b/) ? 'Public' : q.match(/\b(خاصة|private)\b/) ? 'Private' : q.match(/\b(أهلية|وطنية|national)\b/) ? 'National' : '';
    // Budget: numbers like 120 ألف / 120k / 120000
    let budget = null;
    const m = q.match(/(\d{2,6})\s*(?:k|ألف|الف|,|\.)?/);
    if(m){ budget = parseInt(m[1],10); if(/k|ألف|الف/.test(q)) budget*=1000; }
    return {faculty, city, type, budget};
  }

  // Helper: parse EGP value from tuition string
  function egp(str){
    const m = (str||'').replace(/,/g,'').match(/(\d{3,6})/); return m? parseInt(m[1],10): null;
  }

  // Rank results using cosine + soft boosts for constraints
  function aiSearch(query,{type,faculty,city,budget}){
    const qVec = vecize(tokenize(query + ' ' + [type,faculty,city,budget?'EGP '+budget:''].filter(Boolean).join(' ')));
    const scored = universities.map(u=>{
      const d = docs.find(d=>d.id===u.id);
      const base = cosine(qVec, vecize(tokenize(d.text)));
      let boost = 0;
      if(type && u.type===type) boost += 0.15;
      if(city && u.location.toLowerCase()===city.toLowerCase()) boost += 0.12;
      if(faculty && u.colleges.some(c=>c.name.toLowerCase()===faculty.toLowerCase())) boost += 0.18;
      if(budget!=null){
        // prefer universities with any program tuition <= budget
        const cheapest = Math.min(...u.colleges.map(c=>egp(c.tuition)||1e9));
        if(cheapest<=budget) boost += 0.15; else boost -= 0.05;
      }
      return {u, score: +(base+boost).toFixed(4)};
    }).sort((a,b)=>b.score-a.score);
    return scored;
  }

  // Renderers
  const resultsEl = document.getElementById('results');
  const statsEl = document.getElementById('stats');
  const selected = new Map();

  function showToast(msg){ const t=document.getElementById('toast'); t.textContent=msg; t.classList.add('show'); setTimeout(()=>t.classList.remove('show'),1600); }

  function card(u,score){
    const minTuition = Math.min(...u.colleges.map(c=>egp(c.tuition)||1e9));
    const maxTuition = Math.max(...u.colleges.map(c=>egp(c.tuition)||0));
    const top = [...u.colleges].sort((a,b)=>parseInt(b.totalScorePercent)-parseInt(a.totalScorePercent))[0];
    const div = document.createElement('article');
    div.className='card';
    div.innerHTML = `
      <img src="${u.imageUrl}" alt="${u.name}">
      <div class="pad">
        <h3>${u.name}</h3>
        <div class="meta">
          <span class="badge"><i class="fa-solid fa-location-dot"></i> ${u.location}</span>
          <span class="badge"><i class="fa-solid fa-university"></i> ${u.type}</span>
          ${score!=null?<span class="badge" title="درجة التشابه"><i class="fa-solid fa-wand-magic-sparkles"></i> ${Math.round(score*100)}%</span>:''}
        </div>
        <p class="justify">أبرز البرامج: <strong>${top?.name||'-'}</strong> (${top?.totalScorePercent||'-'}) – مصاريف تقريبية من <strong>${minTuition.toLocaleString('en-EG')}</strong> إلى <strong>${maxTuition.toLocaleString('en-EG')}</strong> جنيه/سنة.</p>
      </div>
      <div class="actions">
        <button class="btn secondary" aria-label="إضافة للمقارنة">أضِف للمقارنة</button>
        <button class="btn" aria-label="التفاصيل">تفاصيل</button>
      </div>`;
    const [btnAdd, btnMore] = div.querySelectorAll('button');
    btnAdd.onclick=()=>{
      if(!selected.has(u.id)){
        if(selected.size>=4){ showToast('يمكن المقارنة حتى 4 جامعات فقط'); return; }
        selected.set(u.id,u); refreshSelected(); showToast('تمت الإضافة للمقارنة');
      }
    };
    btnMore.onclick=()=>{
      alert(${u.name}\n\n${u.description}\n\nالكليات المتاحة: \n- ${u.colleges.map(c=>${c.name} (${c.totalScorePercent}) – ${c.tuition}).join('\n- ')});
    };
    return div;
  }

  function render(list){
    resultsEl.innerHTML='';
    list.forEach(({u,score})=>resultsEl.appendChild(card(u,score)));
    statsEl.textContent = عدد النتائج: ${list.length};
  }

  // Initial render (no ranking)
  render(universities.map(u=>({u,score:null})));

  // Selected (drawer)
  const selEl = document.getElementById('selected');
  const cmpCount = document.getElementById('cmpCount');
  const genBtn = document.getElementById('genCompare');
  const resetBtn = document.getElementById('resetCompare');
  const compareWrap = document.getElementById('compareWrap');

  function refreshSelected(){
    selEl.innerHTML='';
    selected.forEach(u=>{
      const minTuition = Math.min(...u.colleges.map(c=>egp(c.tuition)||1e9));
      const p = document.createElement('div'); p.className='pill';
      p.innerHTML = `<strong>${u.name}</strong>
        <span class="meta"><span class="badge"><i class='fa-solid fa-location-dot'></i> ${u.location}</span>
        <span class="badge"><i class='fa-solid fa-university'></i> ${u.type}</span>
        <span class="badge"><i class='fa-solid fa-money-bill-1-wave'></i> من ${minTuition.toLocaleString('en-EG')} ج</span></span>
        <button class="btn secondary" style="width:max-content"><i class="fa-solid fa-xmark"></i> إزالة</button>`;
      p.querySelector('button').onclick=()=>{selected.delete(u.id); refreshSelected();};
      selEl.appendChild(p);
    });
    cmpCount.textContent = selected.size;
    genBtn.disabled = resetBtn.disabled = selected.size<2;
    if(selected.size<2) compareWrap.innerHTML='';
  }

  function summarizeComparison(items){
    // Build a small insight text comparing tuition ranges and top programs
    const lines = [];
    const names = items.map(u=>u.name).join(' × ');
    const ranges = items.map(u=>{
      const t = u.colleges.map(c=>egp(c.tuition)).filter(Boolean);
      return {name:u.name, min:Math.min(...t), max:Math.max(...t)}
    });
    const cheapest = [...ranges].sort((a,b)=>a.min-b.min)[0];
    const priciest = [...ranges].sort((a,b)=>b.max-a.max)[0];
    lines.push(من حيث التكلفة: الأرخص بدايةً هو ${cheapest.name}، بينما الأعلى سقفًا هو ${priciest.name}.);
    // Count faculty overlaps
    const allFac = items.map(u=>new Set(u.colleges.map(c=>c.name)));
    const common = [...allFac.reduce((a,s)=> new Set([...a].filter(x=>s.has(x))))];
    if(common.length) lines.push(القواسم المشتركة في التخصصات: ${common.join('، ')}.);
    // Best program per uni
    items.forEach(u=>{
      const top = [...u.colleges].sort((a,b)=>parseInt(b.totalScorePercent)-parseInt(a.totalScorePercent))[0];
      if(top) lines.push(${u.name}: أقوى برنامج ${top.name} (${top.totalScorePercent}).);
    });
    return lines.join('\n');
  }

  genBtn.onclick = ()=>{
    const items = [...selected.values()];
    // table
    const rows = [
      ['الجامعة', ...items.map(u=>u.name)],
      ['المدينة', ...items.map(u=>u.location)],
      ['النوع', ...items.map(u=>u.type)],
      ['أقل مصروفات', ...items.map(u=>{
        const t = u.colleges.map(c=>egp(c.tuition)).filter(Boolean); return Math.min(...t).toLocaleString('en-EG')+' ج';})],
      ['أعلى مصروفات', ...items.map(u=>{
        const t = u.colleges.map(c=>egp(c.tuition)).filter(Boolean); return Math.max(...t).toLocaleString('en-EG')+' ج';})],
      ['أقوى برنامج', ...items.map(u=>{
        const top = [...u.colleges].sort((a,b)=>parseInt(b.totalScorePercent)-parseInt(a.totalScorePercent))[0];
        return top? ${top.name} (${top.totalScorePercent}) : '-';
      })]
    ];

    let html = '<div class="pad">';
    html += '<table class="table">'+ rows.map(r=>'<tr>'+r.map((c,i)=> i?<td>${c}</td>:<th>${c}</th>).join('') +'</tr>').join('') +'</table>';
    html += '<h4>ملخص ذكي</h4><pre style="white-space:pre-wrap;background:#f8fafc;padding:10px;border:1px solid var(--border);border-radius:10px">'+ summarizeComparison(items) +'</pre>';html += '</div>';
compareWrap.innerHTML = html;

}; resetBtn.onclick = ()=>{ selected.clear(); refreshSelected(); showToast('تم مسح قائمة المقارنة'); };

// AI search actions document.getElementById('runAi').onclick = ()=>{ const q = document.getElementById('q').value.trim(); const cons = extract(q); // apply explicit dropdown filters too const typeFilter = document.getElementById('typeFilter').value || cons.type; const facultyFilter = document.getElementById('facultyFilter').value || cons.faculty; const ranked = aiSearch(q, {...cons, type:typeFilter, faculty:facultyFilter}); // optional: post-filter by selected filters const filtered = ranked.filter(({u})=>{ if(typeFilter && u.type!==typeFilter) return false; if(facultyFilter && !u.colleges.some(c=>c.name===facultyFilter)) return false; return true; }); render(filtered); co
