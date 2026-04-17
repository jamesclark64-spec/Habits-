import { useState, useEffect, useRef, useCallback } from "react";

// ── Storage shim ──────────────────────────────────────────────────────────────
const local = {
  get: (k) => { try { return localStorage.getItem(k); } catch { return null; } },
  set: (k, v) => { try { localStorage.setItem(k, v); } catch {} },
};
// Claude artifacts storage (cross-session within Claude)
const claudeStore = {
  async get(k) { try { if(window.storage){const r=await window.storage.get(k);return r?r.value:null;} } catch{} return null; },
  async set(k,v){ try { if(window.storage) await window.storage.set(k,v); } catch{} },
};

// ── JSONBin API ───────────────────────────────────────────────────────────────
const JB_BASE = "https://api.jsonbin.io/v3/b";
const jb = {
  async read(binId, masterKey) {
    const r = await fetch(`${JB_BASE}/${binId}/latest`, {
      headers: { "X-Master-Key": masterKey }
    });
    if (!r.ok) throw new Error(`JSONBin read failed: ${r.status}`);
    const json = await r.json();
    return json.record;
  },
  async write(binId, masterKey, payload) {
    const r = await fetch(`${JB_BASE}/${binId}`, {
      method: "PUT",
      headers: { "Content-Type":"application/json", "X-Master-Key": masterKey },
      body: JSON.stringify(payload),
    });
    if (!r.ok) throw new Error(`JSONBin write failed: ${r.status}`);
    return r.json();
  },
  async create(masterKey, payload) {
    const r = await fetch(JB_BASE, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "X-Master-Key": masterKey,
        "X-Bin-Name": "habits-2026",
        "X-Bin-Private": "true",
      },
      body: JSON.stringify(payload),
    });
    if (!r.ok) throw new Error(`JSONBin create failed: ${r.status}`);
    const json = await r.json();
    return json.metadata.id;
  },
};

// ── Constants ─────────────────────────────────────────────────────────────────
const YEAR      = 2026;
const START     = new Date(YEAR, 3, 16);
const GRACE_END = new Date(YEAR, 4, 15);
const TARGET    = 5;

const DEFAULT_HABITS = [
  { id:"acv",        cat:"Morning Anchor", label:"ACV + Water before breakfast",        bonus:false },
  { id:"logfood",    cat:"Health Basics",  label:"Log food after eating",               bonus:false },
  { id:"addgood",    cat:"Health Basics",  label:"Add 1 good thing to meals",           bonus:false },
  { id:"drinkwater", cat:"Health Basics",  label:"Drink water before meals",            bonus:false },
  { id:"movement",   cat:"Movement",       label:"20 min workout  ·  or  ·  5 min min", bonus:false },
  { id:"social",     cat:"Social",         label:"1 small interaction",                 bonus:false },
  { id:"walk",       cat:"Bonus",          label:"10 min walk",                         bonus:true  },
  { id:"stretch",    cat:"Bonus",          label:"Stretch / mobility",                  bonus:true  },
  { id:"meals",      cat:"Bonus",          label:"Eat mostly default meals",            bonus:true  },
];

const CAT_ORDER    = ["Morning Anchor","Health Basics","Movement","Social","Bonus","Custom"];
const CAT_ICONS    = { "Morning Anchor":"☀️","Health Basics":"🥗","Movement":"⚡","Social":"💬","Bonus":"⭐","Custom":"✦" };
const MONTHS_SHORT = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];

const C = {
  pre:"#0c0c1e", grace:"#6d28d9", green:"#15803d", red:"#b91c1c",
  missed:"#2c0f0f", future:"#13132b",
  bg:"#07080f", surf:"#0d0e1b", surfHi:"#111228",
  border:"#14152a", borderHi:"#1e1f38",
  text:"#dde0f5", dim:"#4a4b72",
};

// ── Helpers ───────────────────────────────────────────────────────────────────
function fmt(d)   { return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,"0")}-${String(d.getDate()).padStart(2,"0")}`; }
function parse(k) { const [y,m,d]=k.split("-").map(Number); return new Date(y,m-1,d); }
function now0()   { const d=new Date(); d.setHours(0,0,0,0); return d; }
function uid()    { return Math.random().toString(36).slice(2,8); }

function dotColor(k, data, coreHabits) {
  const d=parse(k),t=now0();
  if(d<START)  return C.pre;
  if(d>t)      return C.future;
  const day=data[k];
  if(!day||!Object.keys(day).length) return d<=GRACE_END?C.grace:C.missed;
  return coreHabits.filter(h=>day[h.id]).length>=TARGET?C.green:C.red;
}
function buildWeeks() {
  const offset=(new Date(YEAR,0,1).getDay()+6)%7;
  const weeks=[]; let week=Array(offset).fill(null);
  for(let d=new Date(YEAR,0,1); d.getFullYear()===YEAR; d.setDate(d.getDate()+1)){
    week.push(fmt(new Date(d)));
    if(week.length===7){weeks.push([...week]);week=[];}
  }
  if(week.length){while(week.length<7)week.push(null);weeks.push(week);}
  return weeks;
}
function monthPositions(weeks){
  const out=[]; let last=-1;
  weeks.forEach((w,i)=>{const f=w.find(Boolean);if(f){const m=parse(f).getMonth();if(m!==last){out.push({m,i});last=m;}}});
  return out;
}
function currentWeekKeys(){
  const t=now0(),dow=(t.getDay()+6)%7;
  const mon=new Date(t); mon.setDate(t.getDate()-dow);
  return Array.from({length:7},(_,i)=>{const d=new Date(mon);d.setDate(mon.getDate()+i);return fmt(d);});
}
function calcStreak(data,coreHabits){
  let s=0; const d=new Date(now0());
  while(true){const k=fmt(d),day=data[k];if(!day||coreHabits.filter(h=>day[h.id]).length<TARGET)break;s++;d.setDate(d.getDate()-1);}
  return s;
}

// ── Sync Modal ────────────────────────────────────────────────────────────────
function SyncModal({ onClose, onSaved, currentKey, currentBinId, data, habits }) {
  const [masterKey, setMasterKey] = useState(currentKey||"");
  const [binId,     setBinId]     = useState(currentBinId||"");
  const [status,    setStatus]    = useState(""); // idle | creating | testing | ok | error
  const [msg,       setMsg]       = useState("");

  async function handleCreate() {
    if(!masterKey.trim()){setMsg("Enter your Master Key first.");return;}
    setStatus("creating"); setMsg("Creating new bin…");
    try {
      const id = await jb.create(masterKey.trim(), {data, habits});
      setBinId(id);
      setStatus("ok");
      setMsg(`✓ Bin created! ID: ${id}`);
    } catch(e) { setStatus("error"); setMsg(e.message); }
  }

  async function handleTest() {
    if(!masterKey.trim()||!binId.trim()){setMsg("Fill in both fields.");return;}
    setStatus("testing"); setMsg("Connecting…");
    try {
      await jb.read(binId.trim(), masterKey.trim());
      setStatus("ok"); setMsg("✓ Connected successfully!");
    } catch(e) { setStatus("error"); setMsg(e.message); }
  }

  function handleSave() {
    local.set("ah2026_masterkey", masterKey.trim());
    local.set("ah2026_binid",     binId.trim());
    onSaved(masterKey.trim(), binId.trim());
    onClose();
  }

  const inputStyle = {
    width:"100%", background:"#0a0b18", border:`1px solid ${C.borderHi}`,
    borderRadius:7, padding:"10px 12px", color:"#eef0ff",
    fontFamily:"'DM Mono',monospace", fontSize:11, outline:"none",
    letterSpacing:1,
  };
  const labelStyle = {
    fontFamily:"'DM Mono',monospace", fontSize:8, color:C.dim,
    letterSpacing:2, textTransform:"uppercase", marginBottom:5, display:"block",
  };

  return (
    <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.85)",zIndex:200,
      display:"flex",alignItems:"flex-end",justifyContent:"center"}}
      onClick={e=>{if(e.target===e.currentTarget)onClose();}}>
      <div style={{background:"#0a0b18",border:`1px solid ${C.border}`,borderRadius:"18px 18px 0 0",
        width:"100%",maxWidth:480,padding:"20px 20px 32px",display:"flex",flexDirection:"column",gap:16}}>

        {/* Header */}
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"center"}}>
          <div>
            <div style={{fontFamily:"'DM Mono',monospace",fontSize:9,color:C.dim,
              letterSpacing:2,textTransform:"uppercase",marginBottom:3}}>Cross-Device Sync</div>
            <div style={{fontSize:16,fontWeight:700,color:"#eef0ff"}}>JSONBin Setup</div>
          </div>
          <button onClick={onClose} style={{background:C.surf,border:`1px solid ${C.border}`,
            color:C.dim,width:30,height:30,borderRadius:7,fontSize:16,lineHeight:1}}>✕</button>
        </div>

        {/* Instructions */}
        <div style={{background:"#0c0d1e",border:"1px solid #161730",borderRadius:8,padding:"10px 12px",
          fontFamily:"'DM Mono',monospace",fontSize:9,color:"#3a3c60",letterSpacing:1,lineHeight:1.6}}>
          1. Sign up free at jsonbin.io<br/>
          2. Go to API Keys → copy your Master Key<br/>
          3. Paste it below, then tap CREATE BIN (first device)<br/>
          4. On other devices: enter Master Key + Bin ID and tap TEST
        </div>

        {/* Master Key */}
        <div>
          <label style={labelStyle}>Master Key</label>
          <input value={masterKey} onChange={e=>setMasterKey(e.target.value)}
            type="password" placeholder="$2a$10$…" style={inputStyle}/>
        </div>

        {/* Bin ID */}
        <div>
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:5}}>
            <label style={{...labelStyle,marginBottom:0}}>Bin ID</label>
            <button onClick={handleCreate} style={{background:"#0d1a30",border:"1px solid #162a50",
              borderRadius:5,padding:"4px 10px",color:"#5090d0",fontFamily:"'DM Mono',monospace",
              fontSize:8,letterSpacing:1,cursor:"pointer"}}>
              + CREATE BIN
            </button>
          </div>
          <input value={binId} onChange={e=>setBinId(e.target.value)}
            placeholder="64a3f2…" style={inputStyle}/>
        </div>

        {/* Status message */}
        {msg&&(
          <div style={{fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:1,
            color:status==="ok"?"#22c55e":status==="error"?"#ef4444":"#5060a0",
            padding:"8px 10px",background:status==="ok"?"#051408":status==="error"?"#150808":"#090b1a",
            border:`1px solid ${status==="ok"?"#183028":status==="error"?"#2a1010":"#12152a"}`,
            borderRadius:7}}>
            {status==="creating"||status==="testing" ? "⟳  "+msg : msg}
          </div>
        )}

        {/* Buttons */}
        <div style={{display:"flex",gap:8}}>
          <button onClick={handleTest} style={{flex:1,padding:"10px",background:"#0a1020",
            border:"1px solid #141e38",borderRadius:8,color:"#6080c0",
            fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:2,cursor:"pointer"}}>
            TEST CONNECTION
          </button>
          <button onClick={handleSave} style={{flex:1,padding:"10px",
            background:"linear-gradient(135deg,#1e2f6a,#162255)",
            border:"1px solid #2a3a80",borderRadius:8,color:"#a0b0f0",
            fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:2,cursor:"pointer",fontWeight:500}}>
            SAVE & CLOSE
          </button>
        </div>
      </div>
    </div>
  );
}

// ── Manage Habits Modal ───────────────────────────────────────────────────────
function ManageModal({ habits, onClose, onSave }) {
  const [local2, setLocal2] = useState(habits.map(h=>({...h})));
  const [adding, setAdding] = useState(false);
  const [editId, setEditId] = useState(null);
  const [form, setForm]     = useState({label:"",cat:"Custom",bonus:false});
  const inputRef = useRef();

  useEffect(()=>{if(adding||editId)inputRef.current?.focus();},[adding,editId]);

  function startAdd()  {setForm({label:"",cat:"Custom",bonus:false});setAdding(true);setEditId(null);}
  function startEdit(h){setForm({label:h.label,cat:h.cat,bonus:h.bonus});setEditId(h.id);setAdding(false);}
  function cancelForm(){setAdding(false);setEditId(null);}
  function commitAdd() {if(!form.label.trim())return;setLocal2(p=>[...p,{id:uid(),label:form.label.trim(),cat:form.cat,bonus:form.bonus}]);setAdding(false);}
  function commitEdit(){if(!form.label.trim())return;setLocal2(p=>p.map(h=>h.id===editId?{...h,...form}:h));setEditId(null);}
  function remove(id) {setLocal2(p=>p.filter(h=>h.id!==id));}

  const cats=[...new Set([...CAT_ORDER,...local2.map(h=>h.cat)])];

  const iStyle={width:"100%",background:"#0d0e1b",border:`1px solid ${C.borderHi}`,
    borderRadius:6,padding:"8px 10px",color:"#eef0ff",fontFamily:"'Syne',sans-serif",
    fontSize:13,outline:"none",marginBottom:8};

  function FormFields({ref2}) {
    return (
      <>
        <input ref={ref2} value={form.label} onChange={e=>setForm(p=>({...p,label:e.target.value}))}
          onKeyDown={e=>e.key==="Enter"&&(editId?commitEdit():commitAdd())}
          placeholder="Habit label…" style={iStyle}/>
        <div style={{display:"flex",gap:6,marginBottom:8,flexWrap:"wrap"}}>
          {cats.map(c=>(
            <button key={c} onClick={()=>setForm(p=>({...p,cat:c}))} style={{
              padding:"4px 9px",borderRadius:5,cursor:"pointer",
              border:`1px solid ${form.cat===c?"#3d5ce0":C.border}`,
              background:form.cat===c?"#1a1f40":C.surf,
              color:form.cat===c?"#8899ee":C.dim,
              fontFamily:"'DM Mono',monospace",fontSize:8,letterSpacing:1}}>
              {c}
            </button>
          ))}
        </div>
        <div style={{display:"flex",alignItems:"center",gap:8,marginBottom:10}} onClick={()=>setForm(p=>({...p,bonus:!p.bonus}))}>
          <div style={{width:28,height:16,borderRadius:99,cursor:"pointer",transition:"background 0.2s",
            background:form.bonus?"#0d9488":"#1a1a2e",position:"relative",
            border:`1px solid ${form.bonus?"#0d9488":C.border}`}}>
            <div style={{position:"absolute",top:2,left:form.bonus?12:2,width:10,height:10,
              borderRadius:99,background:"#fff",transition:"left 0.2s"}}/>
          </div>
          <span style={{fontFamily:"'DM Mono',monospace",fontSize:9,color:C.dim,letterSpacing:1,cursor:"pointer"}}>
            BONUS (optional)
          </span>
        </div>
      </>
    );
  }

  return (
    <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.82)",zIndex:100,
      display:"flex",alignItems:"flex-end",justifyContent:"center"}}
      onClick={e=>{if(e.target===e.currentTarget)onClose();}}>
      <div style={{background:"#0a0b18",border:`1px solid ${C.border}`,borderRadius:"18px 18px 0 0",
        width:"100%",maxWidth:480,maxHeight:"90vh",display:"flex",flexDirection:"column",overflow:"hidden"}}>

        <div style={{padding:"18px 20px 14px",borderBottom:`1px solid ${C.border}`,
          display:"flex",justifyContent:"space-between",alignItems:"center",flexShrink:0}}>
          <div>
            <div style={{fontFamily:"'DM Mono',monospace",fontSize:9,color:C.dim,
              letterSpacing:2,textTransform:"uppercase",marginBottom:3}}>Configure</div>
            <div style={{fontSize:16,fontWeight:700,color:"#eef0ff"}}>Manage Habits</div>
          </div>
          <button onClick={onClose} style={{background:C.surf,border:`1px solid ${C.border}`,
            color:C.dim,width:30,height:30,borderRadius:7,fontSize:16,lineHeight:1}}>✕</button>
        </div>

        <div style={{overflowY:"auto",flex:1,padding:"14px 20px"}}>
          {local2.map(h=>(
            <div key={h.id}>
              {editId===h.id ? (
                <div style={{background:C.surfHi,border:`1px solid ${C.borderHi}`,
                  borderRadius:9,padding:"12px 13px",marginBottom:6}}>
                  <FormFields ref2={inputRef}/>
                  <div style={{display:"flex",gap:6}}>
                    <button onClick={commitEdit} style={{flex:1,padding:"7px",background:"#162840",
                      border:"1px solid #1e3a5f",borderRadius:6,color:"#60a5fa",
                      fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:1}}>SAVE</button>
                    <button onClick={cancelForm} style={{padding:"7px 12px",background:C.surf,
                      border:`1px solid ${C.border}`,borderRadius:6,color:C.dim,
                      fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:1}}>CANCEL</button>
                  </div>
                </div>
              ) : (
                <div style={{display:"flex",alignItems:"center",gap:8,padding:"10px 12px",
                  background:C.surf,border:`1px solid ${C.border}`,borderRadius:9,marginBottom:5}}>
                  <div style={{flex:1,minWidth:0}}>
                    <div style={{fontSize:12,fontWeight:500,color:"#9098c8",marginBottom:2,
                      overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{h.label}</div>
                    <div style={{display:"flex",gap:5,alignItems:"center"}}>
                      <span style={{fontFamily:"'DM Mono',monospace",fontSize:7,letterSpacing:1,
                        color:C.dim,background:"#0e0f20",padding:"2px 5px",borderRadius:3}}>{h.cat}</span>
                      {h.bonus&&<span style={{fontFamily:"'DM Mono',monospace",fontSize:7,letterSpacing:1,
                        color:"#0d9488",background:"#061a18",padding:"2px 5px",borderRadius:3}}>BONUS</span>}
                    </div>
                  </div>
                  <button onClick={()=>startEdit(h)} style={{background:"#101228",
                    border:`1px solid ${C.border}`,color:"#5060a0",width:28,height:28,
                    borderRadius:6,fontSize:12,cursor:"pointer"}}>✎</button>
                  <button onClick={()=>remove(h.id)} style={{background:"#180e0e",
                    border:"1px solid #2a1010",color:"#6b2020",width:28,height:28,
                    borderRadius:6,fontSize:12,cursor:"pointer"}}>✕</button>
                </div>
              )}
            </div>
          ))}

          {adding ? (
            <div style={{background:C.surfHi,border:`1px solid ${C.borderHi}`,
              borderRadius:9,padding:"12px 13px",marginTop:4}}>
              <FormFields ref2={inputRef}/>
              <div style={{display:"flex",gap:6}}>
                <button onClick={commitAdd} style={{flex:1,padding:"7px",background:"#162840",
                  border:"1px solid #1e3a5f",borderRadius:6,color:"#60a5fa",
                  fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:1}}>ADD HABIT</button>
                <button onClick={cancelForm} style={{padding:"7px 12px",background:C.surf,
                  border:`1px solid ${C.border}`,borderRadius:6,color:C.dim,
                  fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:1}}>CANCEL</button>
              </div>
            </div>
          ) : (
            <button onClick={startAdd} style={{width:"100%",marginTop:4,padding:"10px",
              background:"transparent",border:`1px dashed ${C.border}`,borderRadius:9,
              color:C.dim,fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:2,cursor:"pointer",
              display:"flex",alignItems:"center",justifyContent:"center",gap:6}}>
              <span style={{fontSize:14,lineHeight:1}}>+</span> ADD HABIT
            </button>
          )}
        </div>

        <div style={{padding:"14px 20px",borderTop:`1px solid ${C.border}`,flexShrink:0}}>
          <button onClick={()=>onSave(local2)} style={{width:"100%",padding:"12px",
            background:"linear-gradient(135deg,#1e2f6a,#162255)",
            border:"1px solid #2a3a80",borderRadius:9,color:"#a0b0f0",
            fontFamily:"'DM Mono',monospace",fontSize:10,letterSpacing:2,cursor:"pointer",fontWeight:500}}>
            SAVE CHANGES
          </button>
        </div>
      </div>
    </div>
  );
}

// ── Main App ──────────────────────────────────────────────────────────────────
export default function App() {
  const [tab,      setTab]      = useState("today");
  const [data,     setData]     = useState({});
  const [habits,   setHabits]   = useState(DEFAULT_HABITS);
  const [sel,      setSel]      = useState(fmt(now0()));
  const [loaded,   setLoaded]   = useState(false);
  const [manage,   setManage]   = useState(false);
  const [syncOpen, setSyncOpen] = useState(false);
  const [masterKey,setMasterKey]= useState("");
  const [binId,    setBinId]    = useState("");
  const [syncState,setSyncState]= useState("idle"); // idle | syncing | ok | error
  const debounceRef = useRef(null);

  // ── Push to JSONBin (debounced) ──
  const pushToJB = useCallback(async (d, h, key, id) => {
    if(!key||!id) return;
    setSyncState("syncing");
    try {
      await jb.write(id, key, {data:d, habits:h});
      setSyncState("ok");
      setTimeout(()=>setSyncState("idle"),2000);
    } catch {
      setSyncState("error");
    }
  }, []);

  function schedulePush(d, h, key, id) {
    if(!key||!id) return;
    if(debounceRef.current) clearTimeout(debounceRef.current);
    debounceRef.current = setTimeout(()=>pushToJB(d,h,key,id), 1500);
  }

  // ── Load on mount ──
  useEffect(()=>{
    (async()=>{
      // Load credentials from localStorage
      const mk  = local.get("ah2026_masterkey")||"";
      const bid = local.get("ah2026_binid")||"";
      setMasterKey(mk); setBinId(bid);

      // Try JSONBin first if configured
      if(mk && bid) {
        setSyncState("syncing");
        try {
          const record = await jb.read(bid, mk);
          if(record?.data)   setData(record.data);
          if(record?.habits) setHabits(record.habits);
          setSyncState("ok");
          setTimeout(()=>setSyncState("idle"),2000);
          setLoaded(true);
          return;
        } catch { setSyncState("error"); }
      }

      // Fallback: Claude storage
      try {
        const d = await claudeStore.get("ah2026_data");    if(d) setData(JSON.parse(d));
        const h = await claudeStore.get("ah2026_habits");  if(h) setHabits(JSON.parse(h));
      } catch {}

      // Fallback: localStorage
      try {
        const d = local.get("ah2026_data");    if(d) setData(JSON.parse(d));
        const h = local.get("ah2026_habits");  if(h) setHabits(JSON.parse(h));
      } catch {}

      setLoaded(true);
    })();
  }, []);

  // ── Persist on change ──
  useEffect(()=>{
    if(!loaded) return;
    const ds=JSON.stringify(data), hs=JSON.stringify(habits);
    local.set("ah2026_data",   ds);
    local.set("ah2026_habits", hs);
    claudeStore.set("ah2026_data",   ds);
    claudeStore.set("ah2026_habits", hs);
    schedulePush(data, habits, masterKey, binId);
  }, [data, habits, loaded]);

  function toggle(id){
    setData(prev=>({...prev,[sel]:{...(prev[sel]||{}),[id]:!(prev[sel]?.[id])}}));
  }
  function shift(n){
    const d=parse(sel); d.setDate(d.getDate()+n);
    const k=fmt(d);
    if(k>=fmt(START)&&k<=`${YEAR}-12-31`) setSel(k);
  }
  function saveHabits(updated){setHabits(updated);setManage(false);}
  function onSyncSaved(mk,bid){setMasterKey(mk);setBinId(bid);}

  const coreHabits  = habits.filter(h=>!h.bonus);
  const todayK      = fmt(now0());
  const isToday     = sel===todayK;
  const dayData     = data[sel]||{};
  const coreScore   = coreHabits.filter(h=>dayData[h.id]).length;
  const bonusScore  = habits.filter(h=>h.bonus&&dayData[h.id]).length;
  const strk        = calcStreak(data,coreHabits);
  const wd          = currentWeekKeys();
  const wScore      = wd.filter(k=>{const dd=data[k];return dd&&coreHabits.filter(h=>dd[h.id]).length>=TARGET;}).length;
  const weeks       = buildWeeks();
  const mPos        = monthPositions(weeks);
  const tracked     = Object.keys(data).filter(k=>{const d=parse(k);return d>=START&&d<=now0();});
  const hits        = tracked.filter(k=>coreHabits.filter(h=>data[k][h.id]).length>=TARGET);
  const activeCats  = [...new Set(habits.map(h=>h.cat))].sort((a,b)=>CAT_ORDER.indexOf(a)-CAT_ORDER.indexOf(b));

  const syncColor = syncState==="ok"?"#22c55e":syncState==="error"?"#ef4444":syncState==="syncing"?"#f59e0b":"#2a2b45";
  const syncLabel = syncState==="ok"?"SYNCED":syncState==="error"?"SYNC ERR":syncState==="syncing"?"SYNCING…":binId?"SYNC ON":"NO SYNC";

  if(!loaded) return (
    <div style={{background:C.bg,height:"100vh",display:"flex",alignItems:"center",
      justifyContent:"center",color:C.dim,fontFamily:"monospace",letterSpacing:4,fontSize:11}}>LOADING</div>
  );

  return (
    <>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Mono:wght@300;400;500&display=swap');
        *{box-sizing:border-box;margin:0;padding:0}
        button{cursor:pointer;font-family:inherit}
        ::-webkit-scrollbar{width:3px;height:3px;background:${C.bg}}
        ::-webkit-scrollbar-thumb{background:#1e1f38;border-radius:2px}
        .dot{transition:transform 0.1s}.dot:hover{transform:scale(1.6)!important}
        .hr{transition:filter 0.15s}.hr:hover{filter:brightness(1.15)}
        input::placeholder{color:#252545}
        input:focus{border-color:#252a50!important}
      `}</style>

      {manage   && <ManageModal habits={habits} onClose={()=>setManage(false)} onSave={saveHabits}/>}
      {syncOpen && <SyncModal onClose={()=>setSyncOpen(false)} onSaved={onSyncSaved}
                    currentKey={masterKey} currentBinId={binId} data={data} habits={habits}/>}

      <div style={{background:C.bg,minHeight:"100vh",color:C.text,
        fontFamily:"'Syne',sans-serif",maxWidth:480,margin:"0 auto",paddingBottom:48}}>

        {/* ── HEADER ── */}
        <div style={{padding:"26px 20px 16px",borderBottom:`1px solid ${C.border}`}}>
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
            <div>
              <p style={{fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:3,
                color:C.dim,marginBottom:5,textTransform:"uppercase"}}>2026 · Atomic Habits</p>
              <h1 style={{fontSize:19,fontWeight:800,color:"#eef0ff",letterSpacing:"-0.3px",lineHeight:1.1}}>
                Daily Non-Negotiables
              </h1>
            </div>
            <div style={{display:"flex",gap:6,alignItems:"flex-start",marginTop:2}}>
              {/* Sync status pill */}
              <button onClick={()=>setSyncOpen(true)} title="Sync settings" style={{
                background:C.surf,border:`1px solid ${C.border}`,borderRadius:6,
                padding:"4px 8px",display:"flex",alignItems:"center",gap:5,cursor:"pointer"}}>
                <div style={{width:6,height:6,borderRadius:99,background:syncColor,
                  boxShadow:syncState==="ok"?`0 0 6px ${syncColor}`:"none",
                  animation:syncState==="syncing"?"pulse 1s ease-in-out infinite":"none"}}/>
                <span style={{fontFamily:"'DM Mono',monospace",fontSize:7,color:syncColor,letterSpacing:1}}>
                  {syncLabel}
                </span>
              </button>
              <button onClick={()=>setManage(true)} style={{background:C.surf,
                border:`1px solid ${C.border}`,color:C.dim,
                width:30,height:30,borderRadius:7,fontSize:14,lineHeight:1}}>⚙</button>
              <div style={{textAlign:"right"}}>
                <div style={{fontSize:32,fontWeight:800,fontFamily:"'DM Mono',monospace",
                  color:strk>0?"#22c55e":"#252545",lineHeight:1}}>{strk}</div>
                <div style={{fontSize:8,fontFamily:"'DM Mono',monospace",color:"#32334e",
                  letterSpacing:2,marginTop:3}}>STREAK</div>
              </div>
            </div>
          </div>

          {/* Week strip */}
          <div style={{marginTop:16}}>
            <div style={{display:"flex",gap:3}}>
              {["M","T","W","T","F","S","S"].map((d,i)=>{
                const k=wd[i],dc=dotColor(k,data,coreHabits),isT=k===todayK,dd=data[k];
                const score=dd?coreHabits.filter(h=>dd[h.id]).length:0;
                return (
                  <div key={i} style={{flex:1}}>
                    <div style={{fontSize:8,fontFamily:"'DM Mono',monospace",letterSpacing:1,
                      color:isT?"#6878c0":"#202038",textAlign:"center",marginBottom:3}}>{d}</div>
                    <div onClick={()=>{setSel(k);setTab("today");}} style={{height:26,borderRadius:5,
                      background:dc,cursor:"pointer",border:`1px solid ${isT?"#3555b0":"transparent"}`,
                      display:"flex",alignItems:"center",justifyContent:"center"}}>
                      {dd&&<span style={{fontSize:9,color:"rgba(255,255,255,0.55)",fontFamily:"'DM Mono',monospace"}}>
                        {score>=TARGET?"✓":"×"}
                      </span>}
                    </div>
                  </div>
                );
              })}
            </div>
            <div style={{marginTop:6,fontFamily:"'DM Mono',monospace",fontSize:9,color:"#2a2b45",letterSpacing:1}}>
              THIS WEEK:{" "}
              <span style={{color:wScore>=5?"#22c55e":wScore>=3?"#f59e0b":"#ef4444"}}>
                {wScore>=7?"🔥 ELITE":wScore>=5?"✔ WINNING":`${wScore}/7 days`}
              </span>
              <span style={{color:"#1c1d38",marginLeft:10}}>5+ = WIN · 7 = ELITE</span>
            </div>
          </div>
        </div>

        {/* ── TABS ── */}
        <div style={{display:"flex",borderBottom:`1px solid ${C.border}`,padding:"0 20px"}}>
          {["today","year"].map(t=>(
            <button key={t} onClick={()=>setTab(t)} style={{background:"none",border:"none",
              padding:"10px 16px",fontFamily:"'DM Mono',monospace",fontSize:10,letterSpacing:2,
              textTransform:"uppercase",color:tab===t?"#8899ee":"#252545",
              borderBottom:tab===t?"2px solid #3d5ce0":"2px solid transparent",transition:"color 0.15s"}}>
              {t}
            </button>
          ))}
        </div>

        {/* ── TODAY ── */}
        {tab==="today"&&(
          <div style={{padding:"18px 20px"}}>
            <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:18}}>
              <button onClick={()=>shift(-1)} style={{background:C.surf,border:`1px solid ${C.border}`,
                color:C.dim,width:32,height:32,borderRadius:7,fontSize:18,lineHeight:1}}>‹</button>
              <div style={{textAlign:"center"}}>
                <div style={{fontSize:9,fontFamily:"'DM Mono',monospace",color:C.dim,letterSpacing:2,marginBottom:2}}>
                  {isToday?"TODAY · ":""}{parse(sel).toLocaleDateString("en-GB",{weekday:"long"}).toUpperCase()}
                </div>
                <div style={{fontSize:17,fontWeight:700,color:"#eef0ff"}}>
                  {parse(sel).toLocaleDateString("en-GB",{day:"numeric",month:"long",year:"numeric"})}
                </div>
              </div>
              <button onClick={()=>shift(1)} style={{background:C.surf,border:`1px solid ${C.border}`,
                color:C.dim,width:32,height:32,borderRadius:7,fontSize:18,lineHeight:1}}>›</button>
            </div>

            <div style={{background:C.surf,border:`1px solid ${C.border}`,borderRadius:10,
              padding:"13px 15px",marginBottom:20}}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"baseline",marginBottom:7}}>
                <span style={{fontFamily:"'DM Mono',monospace",fontSize:9,color:C.dim,letterSpacing:2}}>CORE HABITS</span>
                <span style={{fontFamily:"'DM Mono',monospace",fontWeight:500,
                  color:coreScore>=TARGET?"#22c55e":coreScore>=3?"#f59e0b":"#ef4444"}}>
                  <span style={{fontSize:22}}>{coreScore}</span>
                  <span style={{fontSize:11,color:"#252545"}}>/{coreHabits.length}</span>
                </span>
              </div>
              <div style={{height:3,background:"#0e0f20",borderRadius:99,overflow:"hidden"}}>
                <div style={{height:"100%",borderRadius:99,transition:"width 0.35s ease",
                  background:coreScore>=TARGET?"#22c55e":coreScore>=3?"#f59e0b":"#ef4444",
                  width:`${(coreScore/Math.max(coreHabits.length,1))*100}%`}}/>
              </div>
              <div style={{marginTop:7,fontFamily:"'DM Mono',monospace",fontSize:9,letterSpacing:1,
                color:coreScore>=TARGET?"#22c55e":"#2c2d48"}}>
                {coreScore>=TARGET
                  ?`✓ TARGET HIT${bonusScore>0?`  ·  +${bonusScore} bonus win${bonusScore>1?"s":""}`:""}`
                  :`${TARGET-coreScore} more to hit target`}
              </div>
            </div>

            {activeCats.map(cat=>{
              const catHabits=habits.filter(h=>h.cat===cat);
              if(!catHabits.length) return null;
              return (
                <div key={cat} style={{marginBottom:16}}>
                  <div style={{display:"flex",alignItems:"center",gap:6,marginBottom:7}}>
                    <span style={{fontSize:12}}>{CAT_ICONS[cat]||"•"}</span>
                    <span style={{fontFamily:"'DM Mono',monospace",fontSize:10,letterSpacing:2,
                      color:"#8890c8",textTransform:"uppercase",fontWeight:500}}>{cat}</span>
                    {(cat==="Bonus"||catHabits.every(h=>h.bonus))&&
                      <span style={{fontFamily:"'DM Mono',monospace",fontSize:8,color:"#1e1f35",letterSpacing:1}}>
                        OPTIONAL WINS
                      </span>}
                  </div>
                  <div style={{display:"flex",flexDirection:"column",gap:5}}>
                    {catHabits.map(h=>{
                      const on=!!dayData[h.id],cc=h.bonus?"#0d9488":"#16a34a";
                      return (
                        <div key={h.id} className="hr" onClick={()=>toggle(h.id)} style={{
                          display:"flex",alignItems:"center",gap:12,padding:"12px 13px",
                          borderRadius:9,cursor:"pointer",
                          background:on?(h.bonus?"#051412":"#05140e"):C.surf,
                          border:`1px solid ${on?(h.bonus?"#183030":"#18302a"):C.border}`}}>
                          <div style={{width:18,height:18,borderRadius:4,flexShrink:0,
                            background:on?cc:"transparent",border:`1.5px solid ${on?cc:"#242440"}`,
                            display:"flex",alignItems:"center",justifyContent:"center",transition:"all 0.15s"}}>
                            {on&&<span style={{fontSize:10,color:"#fff",fontWeight:700,lineHeight:1}}>✓</span>}
                          </div>
                          <span style={{fontSize:13,fontWeight:500,lineHeight:1.3,
                            color:on?(h.bonus?"#5fbfb8":"#6aaf7a"):"#7880b8"}}>{h.label}</span>
                        </div>
                      );
                    })}
                  </div>
                </div>
              );
            })}

            <div style={{marginTop:8,padding:"9px",background:"#09091a",border:"1px solid #10112a",
              borderRadius:7,fontFamily:"'DM Mono',monospace",fontSize:9,color:"#20213a",
              letterSpacing:2,textAlign:"center",textTransform:"uppercase"}}>
              Miss a day → Don't miss two
            </div>
          </div>
        )}

        {/* ── YEAR ── */}
        {tab==="year"&&(
          <div style={{padding:"18px 20px"}}>
            <div style={{fontFamily:"'DM Mono',monospace",fontSize:9,color:C.dim,
              letterSpacing:2,marginBottom:14,textTransform:"uppercase"}}>2026 · Year at a Glance</div>

            <div style={{display:"flex",gap:10,marginBottom:16,flexWrap:"wrap"}}>
              {[{c:C.green,l:"Hit target"},{c:C.red,l:"Missed"},{c:C.grace,l:"Apr 16 – May 15"},
                {c:C.missed,l:"Not logged"},{c:C.future,l:"Upcoming"},{c:C.pre,l:"Pre-start"}
              ].map(({c,l})=>(
                <div key={l} style={{display:"flex",alignItems:"center",gap:4}}>
                  <div style={{width:9,height:9,borderRadius:2,background:c,flexShrink:0,
                    border:"1px solid rgba(255,255,255,0.07)"}}/>
                  <span style={{fontFamily:"'DM Mono',monospace",fontSize:8,color:C.dim,letterSpacing:0.5}}>{l}</span>
                </div>
              ))}
            </div>

            <div style={{overflowX:"auto",paddingBottom:6}}>
              <div style={{display:"inline-block"}}>
                <div style={{display:"flex",marginLeft:20,marginBottom:4}}>
                  {weeks.map((_,wi)=>{
                    const ml=mPos.find(p=>p.i===wi);
                    return (
                      <div key={wi} style={{width:11,marginRight:2,flexShrink:0}}>
                        {ml&&<span style={{fontFamily:"'DM Mono',monospace",fontSize:7,color:"#28294a",
                          whiteSpace:"nowrap",display:"block"}}>{MONTHS_SHORT[ml.m]}</span>}
                      </div>
                    );
                  })}
                </div>
                <div style={{display:"flex"}}>
                  <div style={{display:"flex",flexDirection:"column",gap:2,width:18,flexShrink:0}}>
                    {["M","","W","","F","","S"].map((d,i)=>(
                      <div key={i} style={{height:11,fontFamily:"'DM Mono',monospace",fontSize:7,
                        color:"#20213a",display:"flex",alignItems:"center"}}>{d}</div>
                    ))}
                  </div>
                  <div style={{display:"flex",gap:2}}>
                    {weeks.map((week,wi)=>(
                      <div key={wi} style={{display:"flex",flexDirection:"column",gap:2}}>
                        {week.map((k,di)=>{
                          if(!k) return <div key={di} style={{width:11,height:11}}/>;
                          const color=dotColor(k,data,coreHabits),isT=k===todayK;
                          const clickable=parse(k)>=START;
                          const ds=data[k]?coreHabits.filter(h=>data[k][h.id]).length:null;
                          return (
                            <div key={di} className={clickable?"dot":undefined}
                              onClick={()=>{if(clickable){setSel(k);setTab("today");}}}
                              title={k+(ds!==null?` · ${ds}/${coreHabits.length} core`:" · no data")}
                              style={{width:11,height:11,borderRadius:2,background:color,flexShrink:0,
                                cursor:clickable?"pointer":"default",
                                outline:isT?"1.5px solid #4466e0":"none",outlineOffset:"1px"}}/>
                          );
                        })}
                      </div>
                    ))}
                  </div>
                </div>
              </div>
            </div>

            <div style={{marginTop:20,display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:8}}>
              {[{label:"Days Tracked",value:tracked.length},{label:"Target Hit",value:hits.length},{label:"Streak",value:strk}].map(s=>(
                <div key={s.label} style={{background:C.surf,border:`1px solid ${C.border}`,
                  borderRadius:9,padding:"12px 8px",textAlign:"center"}}>
                  <div style={{fontSize:22,fontWeight:800,fontFamily:"'DM Mono',monospace",
                    color:"#eef0ff",lineHeight:1}}>{s.value}</div>
                  <div style={{fontSize:8,fontFamily:"'DM Mono',monospace",color:"#28294a",
                    letterSpacing:1,marginTop:4,textTransform:"uppercase"}}>{s.label}</div>
                </div>
              ))}
            </div>

            {tracked.length>0&&(
              <div style={{marginTop:12,background:C.surf,border:`1px solid ${C.border}`,
                borderRadius:9,padding:"12px 15px"}}>
                <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:6}}>
                  <span style={{fontFamily:"'DM Mono',monospace",fontSize:9,color:C.dim,letterSpacing:2}}>HIT RATE</span>
                  <span style={{fontFamily:"'DM Mono',monospace",fontSize:14,fontWeight:500,
                    color:hits.length/tracked.length>=0.7?"#22c55e":"#f59e0b"}}>
                    {Math.round((hits.length/tracked.length)*100)}%
                  </span>
                </div>
                <div style={{height:3,background:"#0e0f20",borderRadius:99,overflow:"hidden"}}>
                  <div style={{height:"100%",borderRadius:99,transition:"width 0.35s",
                    background:hits.length/tracked.length>=0.7?"#22c55e":"#f59e0b",
                    width:`${(hits.length/tracked.length)*100}%`}}/>
                </div>
              </div>
            )}
          </div>
        )}
      </div>
      <style>{`@keyframes pulse{0%,100%{opacity:1}50%{opacity:0.4}}`}</style>
    </>
  );
}
