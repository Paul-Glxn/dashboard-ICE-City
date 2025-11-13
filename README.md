<!doctype html>
<html lang="de">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>TeamDashboard → LegendV</title>
<style>
  :root{
    --bg:#0f1724; --card:#0b1220; --muted:#9aa6bd; --accent:#4f97ff; --glass: rgba(255,255,255,0.03);
    --success:#2ecc71; --danger:#ff6b6b;
  }
  *{box-sizing:border-box;font-family:Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
  body{margin:0;background:linear-gradient(180deg,#071026 0%, #061428 100%);color:#e6eef8;min-height:100vh;display:flex;align-items:flex-start;justify-content:center;padding:32px;}
  .container{width:100%;max-width:1000px}
  header{display:flex;align-items:center;gap:16px;margin-bottom:20px}
  h1{margin:0;font-size:22px;letter-spacing:0.6px}
  .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:12px;padding:18px;box-shadow:0 6px 18px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.03)}
  .center{display:flex;align-items:center;justify-content:center}
  .grid{display:grid;grid-template-columns: 1fr 320px;gap:18px}
  .input, textarea, select{width:100%;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:var(--glass);color:inherit;font-size:14px}
  label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
  .btn{display:inline-block;padding:10px 12px;border-radius:10px;border:none;background:var(--accent);color:white;font-weight:600;cursor:pointer}
  .btn.ghost{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted)}
  .small{font-size:13px;color:var(--muted)}
  .section{margin-bottom:14px}
  .entry{padding:12px;border-radius:10px;background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.02));border:1px solid rgba(255,255,255,0.02);margin-bottom:10px}
  .entry-header{display:flex;justify-content:space-between;align-items:center;gap:10px}
  .meta{font-size:12px;color:var(--muted)}
  .danger{color:var(--danger)}
  .success{color:var(--success)}
  .pw-card{max-width:520px;margin:40px auto}
  .right-col {position:sticky; top:32px;}
  .actions{display:flex;gap:8px;align-items:center}
  .hidden{display:none}
  footer{margin-top:18px;text-align:center;color:var(--muted);font-size:13px}
</style>
</head>
<body>
<div class="container">
  <header>
    <div style="flex:1">
      <h1>LegendV Dashboard</h1>
      <div class="small">Verwaltung: Team-Besprechung • Abmeldung</div>
    </div>
    <div style="display:flex;gap:10px;align-items:center">
      <div id="loginState" class="tag">Nicht eingeloggt</div>
      <button id="logoutBtn" class="btn ghost hidden">Abmelden</button>
    </div>
  </header>

  <div id="pwArea" class="card pw-card center">
    <div style="width:100%">
      <label for="password">Passwort eingeben</label>
      <input id="password" class="input" placeholder="Passwort..." type="password" />
      <div style="display:flex;gap:8px;margin-top:10px">
        <button id="pwSubmit" class="btn">Einloggen</button>
      </div>
    </div>
  </div>

  <div id="mainUI" class="grid hidden">
    <div>
      <!-- Team Besprechung + Abmeldung Bereich -->
      <div class="card section">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <h2 style="margin:0">Team-Besprechung / Abmeldung</h2>
            <div class="small">Erfasse Notizen oder Abmeldungen — werden lokal gespeichert und an Discord gesendet.</div>
          </div>
          <div class="actions">
            <button id="newEntryToggle" class="btn ghost">Neuer Eintrag</button>
            <button id="viewEntriesToggle" class="btn">Einträge anzeigen</button>
          </div>
        </div>

        <div id="entryForm" class="section hidden" style="margin-top:12px">
          <label>Typ</label>
          <select id="entryType" class="input">
            <option value="besprechung">TEAMBESPRECHUNG</option>
            <option value="abmeldung">ABMELDUNG</option>
          </select>

          <div style="display:flex;gap:10px;margin-top:10px">
            <div style="flex:1">
              <label for="who">Wer</label>
              <input id="who" class="input" placeholder="Name oder Team"/>
            </div>
            <div style="width:160px">
              <label for="when">Wann (Datum/Uhrzeit)</label>
              <input id="when" class="input" type="datetime-local" />
            </div>
          </div>

          <div style="margin-top:10px">
            <label for="what">Was / Grund / Notiz</label>
            <textarea id="what" class="input" rows="4" placeholder="Beschreibung, Grund, Notizen..."></textarea>
          </div>

          <div style="display:flex;gap:8px;margin-top:10px;align-items:center">
            <button id="saveEntry" class="btn">Absenden & Speichern</button>
            <button id="saveLocalOnly" class="btn ghost">Nur lokal speichern</button>
            <div id="entryMsg" class="small" style="margin-left:8px"></div>
          </div>
        </div>

        <div id="entriesList" class="section hidden" style="margin-top:12px"></div>
      </div>
    </div>

    <aside class="right-col">
      <div class="card section">
        <h3 style="margin:0">Aktionen</h3>
        <div style="display:flex;flex-direction:column;gap:8px;margin-top:10px">
          <button id="clearLocal" class="btn ghost">Alle lokale Einträge löschen</button>
          <button id="exportJSON" class="btn">Exportiere JSON</button>
        </div>
      </div>
    </aside>
  </div>

  <footer class="small">Made with ♥ für LegendV</footer>
</div>

<script>
/* --- Webhooks --- */
const WEBHOOK_TEAMBESPRECHUNG = "https://discordapp.com/api/webhooks/1438561448396587094/sxm3T1ojv5Kd4QQbFcmZWl3AGGRF0DLZJLJaeM4ZtU2FTI8SP4r3lu8A-1jfyM4hhqkA";
const WEBHOOK_ABMELDUNG = "https://discordapp.com/api/webhooks/1438561236760399992/gU_8yyqb3AxD500yaobTXpq8Hk4mgi06GqvWCzzk3laxtaxu-gikkDj4WXW3432YlaUh";

/* --- LocalStorage keys --- */
const KEY_TEAM = "legendv_team";
const KEY_ABMELDUNG = "legendv_abmeldung";

/* --- State --- */
let loggedIn = false;

/* --- Utilities --- */
const $ = id => document.getElementById(id);
const uid = ()=> Date.now().toString(36) + Math.random().toString(36).slice(2,8);
function loadList(key){ try { const raw = localStorage.getItem(key); return raw ? JSON.parse(raw) : []; } catch(e){ return []; } }
function saveList(key, arr){ localStorage.setItem(key, JSON.stringify(arr)); }
function pushEntry(key, entry){ const arr = loadList(key); arr.unshift(entry); saveList(key, arr); }

/* --- Elements --- */
const pwArea = $("pwArea"), mainUI = $("mainUI"), pwInput = $("password"), pwSubmit = $("pwSubmit");
const loginState = $("loginState"), logoutBtn = $("logoutBtn");
const newEntryToggle = $("newEntryToggle"), viewEntriesToggle = $("viewEntriesToggle");
const entryForm = $("entryForm"), entryType = $("entryType"), who = $("who"), whenInput = $("when"), what = $("what");
const saveEntry = $("saveEntry"), saveLocalOnly = $("saveLocalOnly"), entryMsg = $("entryMsg");
const entriesList = $("entriesList");
const clearLocal = $("clearLocal"), exportJSON = $("exportJSON");

/* --- Login --- */
pwSubmit.addEventListener("click", ()=> handleLogin(pwInput.value.trim()));
pwInput.addEventListener("keydown", e=> { if(e.key === "Enter") handleLogin(pwInput.value.trim()); });

function handleLogin(pw){
  if(pw === "0000"){
    loggedIn = true;
    pwArea.classList.add("hidden");
    mainUI.classList.remove("hidden");
    loginState.textContent = "Eingeloggt";
    logoutBtn.classList.remove("hidden");
    renderEntries();
  } else {
    alert("Falsches Passwort.");
  }
  pwInput.value = "";
}

logoutBtn.addEventListener("click", ()=>{
  loggedIn = false;
  pwArea.classList.remove("hidden");
  mainUI.classList.add("hidden");
  loginState.textContent = "Nicht eingeloggt";
  logoutBtn.classList.add("hidden");
});

/* --- Form Toggles --- */
newEntryToggle.addEventListener("click", ()=> entryForm.classList.toggle("hidden"));
viewEntriesToggle.addEventListener("click", ()=> { entriesList.classList.toggle("hidden"); renderEntries(); });

/* --- Save Entry --- */
saveEntry.addEventListener("click", ()=> submitEntry(true));
saveLocalOnly.addEventListener("click", ()=> submitEntry(false));

function submitEntry(send){
  const type = entryType.value;
  const obj = { id: uid(), type, who: who.value.trim() || "(unbekannt)", when: whenInput.value || new Date().toISOString(), what: what.value.trim() || "(keine Notiz)", createdAt: new Date().toISOString() };
  if(type === "besprechung") pushEntry(KEY_TEAM, obj); else pushEntry(KEY_ABMELDUNG, obj);
  entryMsg.textContent = "Gespeichert (lokal)";
  setTimeout(()=> entryMsg.textContent = "", 2500);
  renderEntries();
  if(send){
    let webhook = (type === "besprechung") ? WEBHOOK_TEAMBESPRECHUNG : WEBHOOK_ABMELDUNG;
    let username = (type === "besprechung") ? "TEAMBESPRECHUNG" : "ABMELDUNG";
    const content = `**${username}**\nWer: ${obj.who}\nWann: ${obj.when}\nInhalt: ${obj.what}\n(gespeichert: ${obj.createdAt})`;
    postToWebhook(webhook, content, username);
  }
  who.value = ""; whenInput.value = ""; what.value = "";
}

/* --- Render Entries --- */
function renderEntries(){
  const teams = loadList(KEY_TEAM);
  const abmeld = loadList(KEY_ABMELDUNG);
  entriesList.innerHTML = "";
  if(teams.length === 0 && abmeld.length === 0){
    entriesList.innerHTML = '<div class="small muted-note">Keine Einträge vorhanden.</div>';
    return;
  }
  if(abmeld.length > 0){
    const h = document.createElement("div");
    h.innerHTML = "<h3 style='margin:0 0 8px 0'>Abmeldungen</h3>";
    entriesList.appendChild(h);
    abmeld.forEach(e=> entriesList.appendChild(renderEntryCard(e)));
  }
  if(teams.length > 0){
    const h2 = document.createElement("div");
    h2.innerHTML = "<h3 style='margin:14px 0 8px 0'>Besprechungen</h3>";
    entriesList.appendChild(h2);
    teams.forEach(e=> entriesList.appendChild(renderEntryCard(e)));
  }
}

function renderEntryCard(e){
  const el = document.createElement("div");
  el.className = "entry";
  el.innerHTML = `<div class="entry-header"><div><strong>${e.type.toUpperCase()}</strong> · <span class="meta">${new Date(e.createdAt).toLocaleString()}</span></div>
    <button class="btn ghost" onclick="removeEntry('${e.id}','${e.type}')">Löschen</button></div>
    <div style="margin-top:8px"><strong>Wer:</strong> ${escapeHtml(e.who)}<br>
    <strong>Wann:</strong> ${escapeHtml(e.when)}<br>
    <div style="margin-top:6px"><strong>Notiz:</strong><div style="margin-top:6px">${escapeHtml(e.what)}</div></div></div>`;
  return el;
}

function removeEntry(id, type){
  const key = (type === "besprechung") ? KEY_TEAM : KEY_ABMELDUNG;
  const arr = loadList(key).filter(x=>x.id!==id);
  saveList(key, arr);
  renderEntries();
}

/* --- Helpers --- */
async function postToWebhook(url, content, username){
  try {
    await fetch(url, { method:"POST", headers:{"Content-Type":"application/json"}, body:JSON.stringify({ username, content }) });
  } catch (err){ console.warn("Webhook send error", err); }
}
function escapeHtml(s){ return (""+s).replace(/[&<>"']/g, c=>({ '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;' })[c]); }

/* --- Extras --- */
clearLocal.addEventListener("click", ()=>{
  if(confirm("Alle lokalen Einträge löschen?")){ localStorage.removeItem(KEY_TEAM); localStorage.removeItem(KEY_ABMELDUNG); renderEntries(); }
});
exportJSON.addEventListener("click", ()=>{
  const data = { team:loadList(KEY_TEAM), abmeldung:loadList(KEY_ABMELDUNG) };
  const blob = new Blob([JSON.stringify(data,null,2)],{type:"application/json"});
  const a = document.createElement("a"); a.href = URL.createObjectURL(blob); a.download="legendv_export.json"; a.click();
});
</script>
</body>
</html>
