<!doctype html>
<html lang="de">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>TeamDashboard-ICE V</title>
<style>
  :root{
    --bg:#0f1724; --card:#0b1220; --muted:#9aa6bd; --accent:#4f97ff; --glass: rgba(255,255,255,0.03);
    --success:#2ecc71; --danger:#ff6b6b;
  }
  *{box-sizing:border-box;font-family:Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
  body{margin:0;background:linear-gradient(180deg,#071026 0%, #061428 100%);color:#e6eef8;min-height:100vh;display:flex;align-items:flex-start;justify-content:center;padding:32px;}
  .container{width:100%;max-width:1100px}
  header{display:flex;align-items:center;gap:16px;margin-bottom:20px}
  h1{margin:0;font-size:20px;letter-spacing:0.6px}
  .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:12px;padding:18px;box-shadow:0 6px 18px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.03)}
  .center{display:flex;align-items:center;justify-content:center}
  .grid{display:grid;grid-template-columns: 1fr 380px;gap:18px}
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
  .muted-note{font-size:13px;color:var(--muted);margin-top:8px}
  .right-col {position:sticky; top:32px;}
  .actions{display:flex;gap:8px;align-items:center}
  .hidden{display:none}
  footer{margin-top:18px;text-align:center;color:var(--muted);font-size:13px}
  .tag{display:inline-block;padding:6px 8px;border-radius:999px;background:rgba(255,255,255,0.02);font-size:12px;color:var(--muted)}
</style>
</head>
<body>
<div class="container">
  <header>
    <div style="flex:1">
      <h1>TeamDashboard-ICE V</h1>
      <div class="small">Verwaltung: Team-Besprechung • Abmeldung • KummerKasten</div>
    </div>
    <div style="display:flex;gap:10px;align-items:center">
      <div id="loginState" class="tag">Nicht eingeloggt</div>
      <button id="logoutBtn" class="btn ghost hidden">Abmelden</button>
    </div>
  </header>

  <div id="pwArea" class="card pw-card center">
    <div style="width:100%">
      <label for="password">Passwort eingeben</label>
      <input id="password" class="input" placeholder="Passwort..." />
      <div style="display:flex;gap:8px;margin-top:10px">
        <button id="pwSubmit" class="btn">Einloggen</button>
        <button id="pwDemo" class="btn ghost">Demo-Daten laden</button>
      </div>
      <div class="muted-note">Passwörter: <strong>0911</strong> = Vollzugriff, <strong>1111</strong> = Nur hinzufügen.</div>
    </div>
  </div>

  <div id="mainUI" class="grid hidden">
    <div>
      <!-- Team Besprechung + Abmeldung Bereich -->
      <div class="card section">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <h2 style="margin:0">Team-Besprechung / Abmeldung</h2>
            <div class="small">Erfasse Besprechungsnotizen oder Abmeldungen — werden lokal gespeichert und an Discord gesendet.</div>
          </div>
          <div class="actions">
            <button id="newEntryToggle" class="btn ghost">Neuer Eintrag</button>
            <button id="viewEntriesToggle" class="btn">Einträge anzeigen</button>
          </div>
        </div>

        <div id="entryForm" class="section hidden" style="margin-top:12px">
          <label>Typ</label>
          <select id="entryType" class="input">
            <option value="besprechung">TEAMBESPRECHUNG (Notiz)</option>
            <option value="abmeldung">ABMELDUNG (Abmeldung/Fehlt)</option>
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

      <!-- KummerKasten anonym -->
      <div class="card section" style="margin-top:18px">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <h2 style="margin:0">KummerKasten (anonym)</h2>
            <div class="small">Anonyme Nachrichten — werden lokal gespeichert und an den KummerKasten-Webhook gesendet.</div>
          </div>
          <div class="actions">
            <button id="kToggle" class="btn">Schreiben</button>
            <button id="kView" class="btn ghost">Gespeicherte (lokal)</button>
          </div>
        </div>

        <div id="kForm" class="section hidden" style="margin-top:12px">
          <label for="kText">Deine Nachricht (anonym)</label>
          <textarea id="kText" class="input" rows="4" placeholder="Teile Sorgen, Lob, Ideen..."></textarea>
          <div style="display:flex;gap:8px;margin-top:10px">
            <button id="kSend" class="btn">Absenden (Anonym)</button>
            <button id="kLocal" class="btn ghost">Nur lokal speichern</button>
            <div id="kMsg" class="small" style="margin-left:8px"></div>
          </div>
        </div>

        <div id="kList" class="section hidden" style="margin-top:12px"></div>
      </div>
    </div>

    <aside class="right-col">
      <div class="card section">
        <h3 style="margin:0">Schnell-Info</h3>
        <div class="small" style="margin-top:8px">
          <ul style="padding-left:18px;margin:8px 0">
            <li>0911 = Vollzugriff (anzeigen, bearbeiten, löschen)</li>
            <li>1111 = Nur hinzufügen</li>
          </ul>
          <div style="margin-top:6px">LocalStorage-Schlüssel: <code>team_entries</code>, <code>abmeldung_entries</code>, <code>kummer_entries</code></div>
        </div>
      </div>

      <div class="card section" style="margin-top:12px">
        <h4 style="margin:0">Aktionen</h4>
        <div style="display:flex;flex-direction:column;gap:8px;margin-top:10px">
          <button id="clearLocal" class="btn ghost">Alle lokale Einträge löschen</button>
          <button id="exportJSON" class="btn">Exportiere JSON</button>
        </div>
      </div>
      <div class="card section" style="margin-top:12px">
        <div class="small">Technisches:</div>
        <div class="small muted-note" id="noteCors">Hinweis: Discord-Webhook-Anfragen können im Browser durch CORS blockiert werden.</div>
      </div>
    </aside>
  </div>

  <footer class="small">Made with ♥ für TeamDashboard-ICE V</footer>
</div>

<script>
/* --- Konfiguration: Webhook-URLs (einfach ersetzen wenn nötig) --- */
const WEBHOOK_TEAMBESPRECHUNG = "https://discordapp.com/api/webhooks/1437888844518391808/rtLdCUZoL-kjq-ofHzFbcP33PCE1JYAg_Dex40YL4WAI_awOjAu062BzrX3Q81EtPtDW";
const WEBHOOK_ABMELDUNG = "https://discordapp.com/api/webhooks/1437889767655346317/KyXV0seuIsJ3qGEvqNITtx9oHwwlJKefaHVN2QtaUGTPtcyOHUwZ0SA7NyQk_ejjjv_S";
const WEBHOOK_KUMMER = "https://discordapp.com/api/webhooks/1437890318853734402/z1At4bdeW1jeGOoyBZD6wiu8HMDxZYICiuRi0fjG_ZfSYudgB0Ptp4CEo1owBwCItBk7";

/* --- LocalStorage keys --- */
const KEY_TEAM = "team_entries";
const KEY_ABMELDUNG = "abmeldung_entries";
const KEY_KUMMER = "kummer_entries";

/* --- State --- */
let mode = null; // 'full' or 'add' or null
let currentUser = null;

/* --- Utilities --- */
const $ = id => document.getElementById(id);
const uid = ()=> Date.now().toString(36) + Math.random().toString(36).slice(2,8);

function loadList(key){ try { const raw = localStorage.getItem(key); return raw ? JSON.parse(raw) : []; } catch(e){ return []; } }
function saveList(key, arr){ localStorage.setItem(key, JSON.stringify(arr)); }
function pushEntry(key, entry){ const arr = loadList(key); arr.unshift(entry); saveList(key, arr); }

/* --- Init UI Elements --- */
const pwArea = $("pwArea"), mainUI = $("mainUI"), loginState = $("loginState"), logoutBtn = $("logoutBtn");
const pwInput = $("password"), pwSubmit = $("pwSubmit"), pwDemo = $("pwDemo");
const newEntryToggle = $("newEntryToggle"), viewEntriesToggle = $("viewEntriesToggle");
const entryForm = $("entryForm"), entryType = $("entryType"), who = $("who"), whenInput = $("when"), what = $("what");
const saveEntry = $("saveEntry"), saveLocalOnly = $("saveLocalOnly"), entryMsg = $("entryMsg");
const entriesList = $("entriesList");

const kToggle = $("kToggle"), kView = $("kView"), kForm = $("kForm"), kText = $("kText"), kSend = $("kSend"), kLocal = $("kLocal"), kMsg = $("kMsg"), kList = $("kList");
const newBtns = [newEntryToggle, viewEntriesToggle];

const logout = $("logoutBtn");

const clearLocal = $("clearLocal"), exportJSON = $("exportJSON");

/* --- Password / Login --- */
pwSubmit.addEventListener("click", ()=> handleLogin(pwInput.value.trim()));
pwInput.addEventListener("keydown", e=> { if(e.key === "Enter") handleLogin(pwInput.value.trim()); });
pwDemo.addEventListener("click", ()=> loadDemoData());

function handleLogin(pw){
  if(!pw) return alert("Bitte Passwort eingeben.");
  if(pw === "0911"){ mode = "full"; currentUser = "Vollzugriff"; onLogin(); }
  else if(pw === "1111"){ mode = "add"; currentUser = "Nur-Hinzufügen"; onLogin(); }
  else { alert("Falsches Passwort."); }
  pwInput.value = "";
}

function onLogin(){
  pwArea.classList.add("hidden");
  mainUI.classList.remove("hidden");
  loginState.textContent = mode === "full" ? "Eingeloggt: Vollzugriff" : "Eingeloggt: Nur hinzufügen";
  logout.classList.remove("hidden");
  renderEntries();
  renderKummerList();
}

logout.addEventListener("click", ()=>{
  mode = null; currentUser = null;
  pwArea.classList.remove("hidden");
  mainUI.classList.add("hidden");
  loginState.textContent = "Nicht eingeloggt";
  logout.classList.add("hidden");
});

/* --- Entry Form toggles --- */
newEntryToggle.addEventListener("click", ()=> {
  entryForm.classList.toggle("hidden");
});
viewEntriesToggle.addEventListener("click", ()=> {
  entriesList.classList.toggle("hidden");
  renderEntries();
});

/* --- Save Entry --- */
saveEntry.addEventListener("click", ()=> submitEntry(true));
saveLocalOnly.addEventListener("click", ()=> submitEntry(false));

function submitEntry(sendToDiscord){
  const type = entryType.value;
  const obj = {
    id: uid(),
    type,
    who: who.value.trim() || "(unbekannt)",
    when: whenInput.value || new Date().toISOString(),
    what: what.value.trim() || "(keine Notiz)",
    createdAt: new Date().toISOString()
  };
  // Save locally & push to lists
  if(type === "besprechung"){
    pushEntry(KEY_TEAM, obj);
  } else {
    pushEntry(KEY_ABMELDUNG, obj);
  }
  entryMsg.textContent = "Gespeichert lokal.";
  setTimeout(()=> entryMsg.textContent = "", 2500);
  renderEntries();
  // send to webhook if requested
  if(sendToDiscord){
    let webhook = (type === "besprechung") ? WEBHOOK_TEAMBESPRECHUNG : WEBHOOK_ABMELDUNG;
    let username = (type === "besprechung") ? "TEAMBESPRECHUNG" : "ABMELDUNG";
    const content = `**${username}**\nWer: ${obj.who}\nWann: ${obj.when}\nInhalt: ${obj.what}\n(gespeichert: ${obj.createdAt})`;
    postToWebhook(webhook, content, username).then(ok=>{
      if(ok) entryMsg.textContent = "An Discord gesendet.";
      else entryMsg.textContent = "Senden fehlgeschlagen (evtl. CORS).";
      setTimeout(()=> entryMsg.textContent = "", 3000);
    });
  }
  // clear form if add-only (keep fields for editing in full)
  if(mode === "add"){
    who.value = ""; whenInput.value = ""; what.value = "";
  }
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
    h2.innerHTML = "<h3 style='margin:14px 0 8px 0'>Besprechungen / Notizen</h3>";
    entriesList.appendChild(h2);
    teams.forEach(e=> entriesList.appendChild(renderEntryCard(e)));
  }
}

function renderEntryCard(e){
  const el = document.createElement("div");
  el.className = "entry";
  const header = document.createElement("div");
  header.className = "entry-header";
  header.innerHTML = `<div><strong>${e.type === "besprechung" ? "TEAMBESPRECHUNG" : "ABMELDUNG"}</strong> · <span class="meta">${new Date(e.createdAt).toLocaleString()}</span></div>`;
  const actions = document.createElement("div");
  actions.className = "actions";
  if(mode === "full"){
    const edit = document.createElement("button"); edit.textContent = "Bearbeiten"; edit.className="btn ghost";
    edit.onclick = ()=> openEditModal(e);
    const del = document.createElement("button"); del.textContent="Löschen"; del.className="btn"; del.style.background="var(--danger)";
    del.onclick = ()=> { if(confirm("Eintrag löschen?")) { removeEntry(e); } }
    actions.appendChild(edit); actions.appendChild(del);
  } else {
    const addOnly = document.createElement("span"); addOnly.className="small"; addOnly.textContent="Nur-Lesen (keine Änderungen)";
    actions.appendChild(addOnly);
  }
  header.appendChild(actions);
  el.appendChild(header);

  const body = document.createElement("div");
  body.style.marginTop = "8px";
  body.innerHTML = `<div><strong>Wer:</strong> ${escapeHtml(e.who)}</div>
                    <div><strong>Wann:</strong> ${escapeHtml(e.when)}</div>
                    <div style="margin-top:6px"><strong>Notiz:</strong><div style="margin-top:6px">${escapeHtml(e.what)}</div></div>`;
  el.appendChild(body);
  return el;
}

function removeEntry(e){
  const key = e.type === "besprechung" ? KEY_TEAM : KEY_ABMELDUNG;
  let arr = loadList(key).filter(x=> x.id !== e.id);
  saveList(key, arr);
  renderEntries();
}

/* --- Edit Modal (inline simple) --- */
function openEditModal(e){
  // populate form with this entry for editing
  entryForm.classList.remove("hidden");
  entryType.value = e.type;
  who.value = e.who;
  whenInput.value = e.when ? (e.when.includes("T") ? e.when : e.when) : "";
  what.value = e.what;
  // change save behavior to update instead of push
  saveEntry.onclick = function updateExisting(){
    e.who = who.value.trim() || e.who;
    e.when = whenInput.value || e.when;
    e.what = what.value.trim() || e.what;
    e.updatedAt = new Date().toISOString();
    const key = e.type === "besprechung" ? KEY_TEAM : KEY_ABMELDUNG;
    let arr = loadList(key).map(x=> x.id === e.id ? e : x);
    saveList(key, arr);
    entryMsg.textContent = "Eintrag aktualisiert (lokal).";
    setTimeout(()=> entryMsg.textContent = "", 2500);
    saveEntry.onclick = ()=> submitEntry(true); // restore
    renderEntries();
  };
}

/* --- KummerKasten --- */
kToggle.addEventListener("click", ()=> kForm.classList.toggle("hidden"));
kView.addEventListener("click", ()=> kList.classList.toggle("hidden"));

kSend.addEventListener("click", ()=> submitKummer(true));
kLocal.addEventListener("click", ()=> submitKummer(false));

function submitKummer(send){
  const text = kText.value.trim();
  if(!text) return alert("Nachricht schreiben oder abbrechen.");
  const obj = { id: uid(), text, createdAt: new Date().toISOString() };
  pushEntry(KEY_KUMMER, obj);
  kMsg.textContent = "Gespeichert lokal.";
  setTimeout(()=> kMsg.textContent = "", 2500);
  renderKummerList();
  if(send){
    const content = `**KummerKasten**\n${text}\n(gespeichert: ${obj.createdAt})`;
    postToWebhook(WEBHOOK_KUMMER, content, "KummerKasten").then(ok=>{
      if(ok) kMsg.textContent = "An Discord gesendet.";
      else kMsg.textContent = "Senden fehlgeschlagen (evtl. CORS).";
      setTimeout(()=> kMsg.textContent = "", 3000);
    });
  }
  kText.value = "";
}

function renderKummerList(){
  const arr = loadList(KEY_KUMMER);
  kList.innerHTML = "";
  if(arr.length === 0){
    kList.innerHTML = '<div class="small muted-note">Keine anonymen Nachrichten gespeichert.</div>';
    return;
  }
  arr.forEach(e=>{
    const d = document.createElement("div"); d.className="entry";
    d.innerHTML = `<div class="meta" style="margin-bottom:6px">${new Date(e.createdAt).toLocaleString()}</div><div>${escapeHtml(e.text)}</div>`;
    // allow delete only in full mode
    if(mode === "full"){
      const del = document.createElement("button"); del.className="btn ghost"; del.textContent="Löschen";
      del.style.marginTop="8px"; del.onclick = ()=>{ if(confirm("Löschen?")){ saveList(KEY_KUMMER, loadList(KEY_KUMMER).filter(x=> x.id !== e.id)); renderKummerList(); } };
      d.appendChild(del);
    }
    kList.appendChild(d);
  });
}

/* --- Posting to webhook --- */
async function postToWebhook(url, content, username){
  try {
    const body = { username: username || "TeamDashboard", content };
    const res = await fetch(url, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(body),
      mode: "cors"
    });
    // Discord often returns 204 No Content on success
    if(res.ok) return true;
    console.warn("Webhook response not ok:", res.status, res.statusText);
    return false;
  } catch (err){
    console.warn("Webhook send error:", err);
    return false;
  }
}

/* --- Helpers --- */
function escapeHtml(s){
  return (""+s).replace(/[&<>"']/g, c=>({ '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;' })[c]);
}

/* --- Extras: Demo data, export, clear --- */
function loadDemoData(){
  const demo1 = { id: uid(), type: "besprechung", who: "Team Alpha", when: new Date().toISOString(), what: "Sprint Review: Themen A, B, C", createdAt: new Date().toISOString() };
  const demo2 = { id: uid(), type: "abmeldung", who: "Max Mustermann", when: new Date().toISOString(), what: "Krankheit, 2 Tage", createdAt: new Date().toISOString() };
  saveList(KEY_TEAM, [demo1]);
  saveList(KEY_ABMELDUNG, [demo2]);
  saveList(KEY_KUMMER, [{id:uid(), text:"Ich wünsche mir bessere Kaffeemaschine.", createdAt:new Date().toISOString()}]);
  alert("Demo-Daten geladen.");
  renderEntries();
  renderKummerList();
}

clearLocal.addEventListener("click", ()=>{
  if(!confirm("Alle lokalen Einträge löschen? (Kann nicht rückgängig gemacht werden)")) return;
  localStorage.removeItem(KEY_TEAM); localStorage.removeItem(KEY_ABMELDUNG); localStorage.removeItem(KEY_KUMMER);
  renderEntries(); renderKummerList();
  alert("Lokale Einträge gelöscht.");
});

exportJSON.addEventListener("click", ()=>{
  const packageAll = { team: loadList(KEY_TEAM), abmeldung: loadList(KEY_ABMELDUNG), kummer: loadList(KEY_KUMMER) };
  const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(packageAll, null, 2));
  const a = document.createElement("a"); a.href = dataStr; a.download = "teamdashboard_export.json"; a.click();
});

/* --- On load: nothing visible until login --- */
// nothing to do

</script>
</body>
</html>

