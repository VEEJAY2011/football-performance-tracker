app{padding:24px 20px ">Progress</button><button data-tab="history">History</button>
</nav>
<main>
<section id="tab-today">
<div class="row"><h2 class="section-title">Build today's session</h2><span class="muted tiny" id="todayDate"></span></div>
<div class="field"><label for="trainingDay">Training day</label><select id="trainingDay"><option value="Day 1">Day 1 · Ball mastery + mobility</option><option value="Day 2">Day 2 · Endurance + passing</option><option value="Day 3">Day 3 · Technical quality + flexibility</option><option value="Day 4">Day 4 · Conditioning + mentality</option></select></div>
<div class="grid" id="attributeGrid"></div>
<div class="card" style="margin-top:12px">
<div class="row"><h3 id="selectedTitle" class="section-title">Mobility drills</h3><span class="muted tiny" id="selectedCount">0 of 5 checked</span></div>
<p class="muted tiny">Choose the drills you do today. You do not need to complete all 30 in one session.</p>
<div id="drillList"></div>
</div>
<div class="card" style="margin-top:12px">
<h3 class="section-title">Session record</h3>
<div class="field"><label for="minutes">Total training time (minutes)</label><input id="minutes" type="number" min="0" max="300" value="45"></div>
<div class="field"><label for="energy">How did you feel?</label><select id="energy"><option>Fresh</option><option selected>Good</option><option>Tired</option></select></div>
<div class="field"><label for="notes">Notes: win, lesson, next goal</label><textarea id="notes" placeholder="Example: cleaner weak-foot passes; work on first touch next time."></textarea></div>
<h3 class="section-title">Rate each attribute (1–5)</h3>
<p class="muted tiny">Use the same standards each time. Ratings are your assessment; tests and results make progress easier to verify.</p>
<div id="ratings"></div>
<button class="btn full" id="saveSession">Save session to this phone</button>
<p class="tiny muted">Saved in this browser on this device. Use Export backup regularly.</p>
</div>
</section>
<section id="tab-plan" class="hidden">
<h2 class="section-title">Your four-day weekly plan</h2>
<p class="muted">Example schedule: Monday, Tuesday, Thursday and Saturday. Leave recovery time between demanding sessions and adjust for team training or matches.</p>
<div id="weeklyPlan"></div>
<div class="note">Start each session with a 5–10 minute warm-up. Begin with manageable volume, build gradually, and stop if you feel sharp pain, dizziness or unusual shortness of breath.</div>
<h3 class="section-title" style="margin-top:20px">All 30 exercises</h3><div id="allExercises"></div>
</section>
<section id="tab-progress" class="hidden">
<h2 class="section-title">Progress dashboard</h2>
<div class="card"><h3 class="section-title">Latest attribute ratings</h3><div id="ratingBars"></div></div>
<div class="card" style="margin-top:12px"><h3 class="section-title">Rating trend over time</h3><p class="muted tiny">Each point is the average of the six ratings from a saved session.</p><div class="chart-wrap"><canvas id="chart" aria-label="Training rating trend chart"></canvas></div><p id="chartEmpty" class="muted tiny">Save at least one session to begin your chart.</p></div>
<div class="card" style="margin-top:12px"><h3 class="section-title">Retest every 2–4 weeks</h3><ul><li>Mobility: repeat the same comfortable ankle/hip movement check.</li><li>Flexibility: compare the same controlled stretch, without forcing range.</li><li>Ball control: count clean touches in a fixed-time drill.</li><li>Endurance: compare the same safe route or controlled test.</li><li>Football skills: record accurate passes out of a fixed number.</li><li>Mentality: review consistency, focus and reflection notes.</li></ul></div>
</section>
<section id="tab-history" class="hidden">
<div class="row"><h2 class="section-title">Session history</h2><button class="btn secondary" id="exportBtn">Export backup</button></div>
<p class="muted tiny">Export a JSON backup to keep a copy outside your browser. Import it if you move to another phone/browser.</p>
<div class="field"><label for="importFile">Restore from a backup file</label><input type="file" id="importFile" accept="application/json"></div>
<div id="historyList"></div>
<button class="btn danger full" id="clearBtn" style="margin-top:12px">Delete all saved data</button>
</section>
</main>
<div class="footer">Football Performance Tracker · Personal training data stays in this browser unless you export or share it.</div>
</div>
<script>
const ATTRS = [
{name:"Mobility",icon:"↔",items:[["Ankle rocks","2 × 10 each side"],["90/90 hip rotations","2 × 8 each side"],["World's greatest stretch","2 × 5 each side"],["Leg swings","2 × 10 forward/back and sideways"],["Adductor rock-backs","2 × 10 reps"]]},
{name:"Flexibility",icon:"〰",items:[["Half-kneeling hip-flexor stretch","2 × 25 seconds each side"],["Hamstring stretch","2 × 25 seconds each side"],["Calf wall stretch","2 × 25 seconds each side"],["Butterfly stretch","2 × 25 seconds"],["Glute figure-four stretch","2 × 25 seconds each side"]]},
{name:"Ball control",icon:"⚽",items:[["Inside-inside touches","3 × 30 seconds"],["Sole rolls","3 × 30 seconds"],["Foundation touches","3 × 30 seconds"],["Inside-outside touches","3 × 30 seconds each foot"],["Weak-foot ball mastery","3 × 45 seconds"]]},
{name:"Endurance",icon:"♥",items:[["Easy continuous run","15–25 minutes, conversational pace"],["Run-walk intervals","6 × 2 minutes easy/moderate; 1 minute walk"],["Shuttle runs","4–6 controlled repetitions over 10–15 m"],["Football movement intervals","6 × 1 minute controlled; 1 minute easy"],["Small-sided game","15–25 minutes, breaks as needed"]]},
{name:"Football skills",icon:"◎",items:[["Wall passing","3 × 20 passes per foot"],["First-touch receiving","3 × 10 each side"],["Cone dribbling","4 × 30 seconds"],["Passing to target","3 × 10 passes per foot"],["Finishing practice","3 × 6 controlled shots, if goal available"]]},
{name:"Mentality",icon:"◉",items:[["Pre-session visualization","3 minutes"],["Focused ball practice","5 minutes without distractions"],["Scanning habit","10 repetitions: look around before receiving"],["Pressure decision drill","3 × 2 minutes; choose a target before receiving"],["Training journal","5 minutes: one win, one lesson, one goal"]]}
];
const PLANS=[
{day:"Day 1",title:"Ball mastery + mobility",focus:["Mobility","Ball control"],duration:"45–60 min",detail:"Warm up; choose 2–3 mobility drills and 2–3 ball-control drills; finish with notes."},
{day:"Day 2",title:"Endurance + passing",focus:["Endurance","Football skills"],duration:"45–60 min",detail:"Warm up; choose one appropriate conditioning drill and 2–3 passing/first-touch drills."},
{day:"Day 3",title:"Technical quality + flexibility",focus:["Football skills","Ball control","Flexibility"],duration:"45–60 min",detail:"Prioritize clean technique, weak-foot touches and gentle controlled flexibility work."},
{day:"Day 4",title:"Conditioning + mentality",focus:["Endurance","Mobility","Mentality"],duration:"40–60 min",detail:"Choose controlled conditioning, a short mobility routine and a brief reflection. Avoid maximal efforts when tired."}
];
const STORE="fpt-v1-local";
let db={sessions:[],draft:{day:"Day 1",selected:"Mobility",checked:[],ratings:{},minutes:45,energy:"Good",notes:""}};
ATTRS.forEach(a=>db.draft.ratings[a.name]=3);
function load(){try{const x=JSON.parse(localStorage.getItem(STORE));if(x&&Array.isArray(x.sessions)&&x.draft){db=x;ATTRS.forEach(a=>{if(!Number.isFinite(db.draft.ratings[a.name]))db.draft.ratings[a.name]=3})}}catch(e){}}
function persist(){localStorage.setItem(STORE,JSON.stringify(db))}
const $=id=>document.getElementById(id);
function esc(s){return String(s??"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]))}
function activeAttr(){return ATTRS.find(a=>a.name===db.draft.selected)||ATTRS[0]}
function renderAttrs(){$("attributeGrid").innerHTML=ATTRS.map(a=>`<button class="attr ${a.name===db.draft.selected?"selected":""}" data-attr="${esc(a.name)}"><strong>${a.icon} ${esc(a.name)}</strong><small>5 exercises</small></button>`).join("");document.querySelectorAll("[data-attr]").forEach(b=>b.onclick=()=>{db.draft.selected=b.dataset.attr;persist();renderAttrs();renderDrills()})}
function renderDrills(){const a=activeAttr();$("selectedTitle").textContent=a.name+" drills";$("selectedCount").textContent=a.items.filter((_,i)=>db.draft.checked.includes(a.name+"-"+i)).length+" of 5 checked";$("drillList").innerHTML=a.items.map((it,i)=>{const key=a.name+"-"+i;return `<label class="drill"><input type="checkbox" data-drill="${esc(key)}" ${db.draft.checked.includes(key)?"checked":""}><span><strong>${esc(it[0])}</strong><small>${esc(it[1])}</small></span></label>`}).join("");document.querySelectorAll("[data-drill]").forEach(c=>c.onchange=()=>{const k=c.dataset.drill;if(c.checked&&!db.draft.checked.includes(k))db.draft.checked.push(k);else db.draft.checked=db.draft.checked.filter(x=>x!==k);persist();renderDrills();renderStats()})}
function renderRatings(){$("ratings").innerHTML=ATTRS.map(a=>`<div class="rating"><label for="r-${ATTRS.indexOf(a)}">${esc(a.name)}</label><output id="o-${ATTRS.indexOf(a)}">${db.draft.ratings[a.name]}/5</output><input id="r-${ATTRS.indexOf(a)}" type="range" min="1" max="5" step="1" value="${db.draft.ratings[a.name]}" style="grid-column:1/-1"></div>`).join("");ATTRS.forEach((a,i)=>$("r-"+i).oninput=e=>{db.draft.ratings[a.name]=Number(e.target.value);$("o-"+i).textContent=e.target.value+"/5";persist()})}
function renderPlan(){$("weeklyPlan").innerHTML=PLANS.map(p=>`<div class="day"><div class="row"><strong>${p.day} · ${p.title}</strong><span class="pill" style="background:var(--soft);color:var(--green)">${p.duration}</span></div><p>${p.detail}</p><p>${p.focus.map(esc).join(" · ")}</p></div>`).join("");$("allExercises").innerHTML=ATTRS.map(a=>`<div class="day"><strong>${a.icon} ${esc(a.name)}</strong>${a.items.map((it,i)=>`<p>${i+1}. ${esc(it[0])} — ${esc(it[1])}</p>`).join("")}</div>`).join("")}
function average(s){const vals=ATTRS.map(a=>Number(s.ratings[a.name]||3));return vals.reduce((a,b)=>a+b,0)/vals.length}
function renderStats(){const totalM=db.sessions.reduce((n,s)=>n+(Number(s.minutes)||0),0),totalD=db.sessions.reduce((n,s)=>n+(s.completed||0),0);$("statSessions").textContent=db.sessions.length;$("statMinutes").textContent=totalM;$("statDrills").textContent=totalD;$("statAverage").textContent=db.sessions.length?average(db.sessions[0]).toFixed(1)+"/5":"—"}
function renderProgress(){const latest=db.sessions[0]?.ratings||db.draft.ratings;$("ratingBars").innerHTML=ATTRS.map(a=>{let v=Number(latest[a.name]||3);return `<div class="barrow"><span>${esc(a.name)}</span><div class="bar"><span style="width:${v*20}%"></span></div><strong>${v}/5</strong></div>`}).join("");const canvas=$("chart"),ctx=canvas.getContext("2d"),wrap=canvas.parentElement,ratio=window.devicePixelRatio||1,w=wrap.clientWidth,h=wrap.clientHeight;canvas.width=w*ratio;canvas.height=h*ratio;ctx.scale(ratio,ratio);ctx.clearRect(0,0,w,h);if(!db.sessions.length){$("chartEmpty").classList.remove("hidden");return}$("chartEmpty").classList.add("hidden");const arr=[...db.sessions].reverse().slice(-12),pad={l:30,r:12,t:15,b:30},cw=w-pad.l-pad.r,ch=h-pad.t-pad.b;ctx.strokeStyle="#dfe7df";ctx.lineWidth=1;for(let i=1;i<=5;i++){let y=pad.t+ch-(i/5)*ch;ctx.beginPath();ctx.moveTo(pad.l,y);ctx.lineTo(w-pad.r,y);ctx.stroke();ctx.fillStyle="#66756c";ctx.font="11px system-ui";ctx.fillText(String(i),6,y+4)}ctx.strokeStyle="#176b45";ctx.lineWidth=3;ctx.beginPath();arr.forEach((s,i)=>{let x=pad.l+(arr.length===1?cw/2:i/(arr.length-1)*cw),y=pad.t+ch-(average(s)/5)*ch;if(i===0)ctx.moveTo(x,y);else ctx.lineTo(x,y)});ctx.stroke();arr.forEach((s,i)=>{let x=pad.l+(arr.length===1?cw/2:i/(arr.length-1)*cw),y=pad.t+ch-(average(s)/5)*ch;ctx.fillStyle="#176b45";ctx.beginPath();ctx.arc(x,y,4,0,Math.PI*2);ctx.fill()});ctx.fillStyle="#66756c";ctx.font="10px system-ui";ctx.fillText("Older",pad.l,h-7);ctx.fillText("Latest",w-47,h-7)}
function renderHistory(){$("historyList").innerHTML=db.sessions.length?db.sessions.map(s=>`<div class="history-item"><div class="row"><strong>${esc(s.day)} · ${esc(s.date)}</strong><strong>${average(s).toFixed(1)}/5</strong></div><p class="muted tiny">${s.minutes} minutes · ${s.completed} drills checked · Energy: ${esc(s.energy)}</p><p class="tiny">${ATTRS.map(a=>esc(a.name)+": "+Number(s.ratings[a.name]||3)+"/5").join(" · ")}</p>${s.notes?`<p>${esc(s.notes)}</p>`:""}</div>`).join(""):'<p class="muted">No sessions yet. Save your first session on the Today tab.</p>'}
function renderAll(){renderAttrs();renderDrills();renderRatings();renderPlan();renderStats();renderProgress();renderHistory();$("trainingDay").value=db.draft.day;$("minutes").value=db.draft.minutes;$("energy").value=db.draft.energy;$("notes").value=db.draft.notes}
document.querySelectorAll("[data-tab]").forEach(b=>b.onclick=()=>{document.querySelectorAll("[data-tab]").forEach(x=>x.classList.toggle("active",x===b));["today","plan","progress","history"].forEach(t=>$("tab-"+t).classList.toggle("hidden",t!==b.dataset.tab));if(b.dataset.tab==="progress")renderProgress()});
$("trainingDay").onchange=e=>{db.draft.day=e.target.value;persist()};$("minutes").onchange=e=>{db.draft.minutes=Math.max(0,Math.min(300,Number(e.target.value)||0));persist()};$("energy").onchange=e=>{db.draft.energy=e.target.value;persist()};$("notes").oninput=e=>{db.draft.notes=e.target.value;persist()};
$("saveSession").onclick=()=>{const s={date:new Date().toLocaleDateString(),iso:new Date().toISOString(),day:db.draft.day,minutes:Math.max(0,Math.min(300,Number($("minutes").value)||0)),energy:$("energy").value,notes:$("notes").value.trim(),completed:db.draft.checked.length,checked:[...db.draft.checked],ratings:{...db.draft.ratings}};db.sessions.unshift(s);db.draft.checked=[];db.draft.notes="";persist();renderAll();$("notes").value="";alert("Session saved on this device. Great work—keep showing up!");};
function download(name,blob){const a=document.createElement("a"),url=URL.createObjectURL(blob);a.href=url;a.download=name;a.click();setTimeout(()=>URL.revokeObjectURL(url),1000)}
$("exportBtn").onclick=()=>download("football-tracker-backup-"+new Date().toISOString().slice(0,10)+".json",new Blob([JSON.stringify(db,null,2)],{type:"application/json"}));
$("importFile").onchange=async e=>{const f=e.target.files[0];if(!f)return;try{const x=JSON.parse(await f.text());if(!Array.isArray(x.sessions)||!x.draft)throw Error();db=x;ATTRS.forEach(a=>{if(!Number.isFinite(db.draft.ratings?.[a.name]))db.draft.ratings[a.name]=3});persist();renderAll();alert("Backup restored.");}catch(err){alert("That backup file was not recognized.");}e.target.value=""};
$("clearBtn").onclick=()=>{if(confirm("Delete all sessions and draft data saved by this tracker on this device? Export a backup first if you want to keep it.")){localStorage.removeItem(STORE);db={sessions:[],draft:{day:"Day 1",selected:"Mobility",checked:[],ratings:{},minutes:45,energy:"Good",notes:""}};ATTRS.forEach(a=>db.draft.ratings[a.name]=3);persist();renderAll()}};
function goal(){const target=new Date("2027-03-05T12:00:00");const d=Math.ceil((target-new Date())/86400000);$("daysLeft").textContent=d>0?d+" days to go":d===0?"Target day!":"Target date passed";$("todayDate").textContent=new Date().toLocaleDateString(undefined,{weekday:"short",month:"short",day:"numeric"})}
load();goal();renderAll();window.addEventListener("resize",()=>{if(!$("tab-progress").classList.contains("hidden"))renderProgress()});
if("serviceWorker" in navigator && location.protocol.startsWith("http"))window.addEventListener("load",()=>navigator.serviceWorker.register("./sw.js").catch(()=>{}));
</script>
</body>
</html>
[manifest.json](https://github.com/user-attachments/files/34050651/manifest.json){
  "name": "Football Performance Tracker",
  "short_name": "Football Tracker",
  "description": "Personal football training tracker with offline support and local progress history.",
  "start_url": "./index.html",
  "scope": "./",
  "display": "standalone",
  "orientation": "portrait",
  "background_color": "#f3f6f2",
  "theme_color": "#10251c",
  "icons": [
    {"src":"icon.svg","sizes":"any","type":"image/svg+xml","purpose":"any maskable"}# Football Performance Tracker (Android PWA)

A small installable Progressive Web App for personal football development. It includes:
- Four training days per week
- Five drills for each of six attributes (30 total)
- Session minutes, energy, notes and 1–5 ratings
- Progress summary, rating bars and a rating-trend chart
- Local browser storage, JSON export and restore
- Offline cache after the first successful online load

## Run it locally

Because service workers and PWA installation require a secure context, do not expect full installation/offline behavior from opening `index.html` directly as a file. You can test the tracker in a browser, but publish it over HTTPS for installation.

## Publish free with GitHub Pages

1. Sign in or create an account at https://github.com.
2. Create a new **public** repository, for example `football-performance-tracker`.
3. Upload `index.html`, `manifest.json`, `sw.js`, `icon.svg`, and this README to the repository root.
4. Open repository **Settings → Pages**.
5. Under build/deployment, choose **Deploy from a branch**, select `main` and `/ (root)`, then save.
6. Wait for GitHub Pages to publish the site. The address will look like `https://YOUR-USERNAME.github.io/football-performance-tracker/`.
7. Open that HTTPS address on your Android phone in Chrome. Wait for the page to finish loading once while online.

## Install on Android

1. Open the published HTTPS website in Chrome.
2. Tap Chrome's three-dot menu.
3. Choose **Install app** if shown, or **Add to Home screen**.
4. Confirm the name and tap Install/Add.
5. Open **Football Tracker** from the new home-screen icon.

Chrome menu wording varies by Android version. The web app needs to be served over HTTPS for its service worker and installability behavior.

## Local data and backups

- Session data is stored in `localStorage` in the browser on that device.
- Data is not automatically sent to a server or synced between devices.
- Clearing Chrome site data, uninstalling/clearing app data, or using a different browser/profile can remove or separate records.
- On the History tab, use **Export backup** to download a JSON file regularly.
- Use **Restore from a backup file** to import that JSON backup later.
- Keep backup files somewhere safe. Anyone with access to the unlocked device/browser profile may be able to access the tracker.

## Updating the app

After replacing files in GitHub, increment the cache name in `sw.js` (for example, `football-tracker-v2`) so installed copies refresh to the new version. Revisit the site online to receive the update.

## Notes

This is a static client-side PWA, not a native Play Store APK. It can be installed from the browser after HTTPS hosting, and it works offline after its assets are cached. A Play Store release would require a separate Android packaging and publishing process.

  ]
}
[README.md](https://github.com/user-attachments/files/34050662/README.md)
[sw.js](https://github.com/user-attachments/files/34050667/sw.js)
const CACHE="football-tracker-v1";
const FILES=["./","./index.html","./manifest.json","./icon.svg"];
self.addEventListener("install",event=>event.waitUntil(caches.open(CACHE).then(cache=>cache.addAll(FILES)).then(()=>self.skipWaiting())));
self.addEventListener("activate",event=>event.waitUntil(caches.keys().then(keys=>Promise.all(keys.filter(k=>k!==CACHE).map(k=>caches.delete(k)))).then(()=>self.clients.claim())));
self.addEventListener("fetch",event=>{if(event.request.method!=="GET")return;event.respondWith(caches.match(event.request).then(cached=>cached||fetch(event.request).then(response=>{const copy=response.clone();caches.open(CACHE).then(cache=>cache.put(event.request,copy));return response}).catch(()=>caches.match("./index.html"))))});
