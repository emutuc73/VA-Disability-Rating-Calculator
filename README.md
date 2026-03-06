import { useState, useCallback, useMemo } from "react";

const CONDITIONS = {
  "Autoimmune & Blood Diseases": [
    { name: "AL Amyloidosis", min: 10, max: 100, bl: false },
    { name: "Anemia", min: 0, max: 100, bl: false },
    { name: "Chronic Fatigue Syndrome", min: 10, max: 100, bl: false },
    { name: "Fibromyalgia", min: 10, max: 40, bl: false },
    { name: "Gulf War Syndrome", min: 0, max: 100, bl: false },
    { name: "HIV / AIDS", min: 0, max: 100, bl: false },
    { name: "Lupus", min: 10, max: 100, bl: false },
  ],
  "Cardiovascular": [
    { name: "Coronary Artery Disease", min: 10, max: 100, bl: false },
    { name: "Heart Disease", min: 10, max: 100, bl: false },
    { name: "Heart Palpitations", min: 10, max: 100, bl: false },
    { name: "Hypertension", min: 10, max: 60, bl: false },
    { name: "Ischemic Heart Disease", min: 10, max: 100, bl: false },
    { name: "Varicose Veins", min: 0, max: 100, bl: true },
  ],
  "Digestive Issues": [
    { name: "Crohn's Disease", min: 10, max: 60, bl: false },
    { name: "GERD", min: 10, max: 60, bl: false },
    { name: "Hemorrhoids", min: 0, max: 20, bl: false },
    { name: "Hepatitis C", min: 0, max: 100, bl: false },
    { name: "Hiatal Hernia", min: 10, max: 60, bl: false },
    { name: "IBS", min: 10, max: 30, bl: false },
    { name: "Ulcerative Colitis", min: 10, max: 60, bl: false },
  ],
  "Ear Conditions": [
    { name: "Hearing Loss", min: 0, max: 100, bl: true },
    { name: "Meniere's Disease", min: 30, max: 100, bl: true },
    { name: "Tinnitus", min: 10, max: 10, bl: false },
    { name: "Vertigo", min: 10, max: 100, bl: false },
  ],
  "Endocrine": [
    { name: "Diabetes", min: 10, max: 100, bl: false },
    { name: "Thyroid Disabilities", min: 10, max: 100, bl: false },
  ],
  "Eye Conditions": [
    { name: "Blindness", min: 30, max: 100, bl: true },
    { name: "Cataracts", min: 10, max: 100, bl: true },
    { name: "Dry Eye Syndrome", min: 10, max: 30, bl: true },
    { name: "Glaucoma", min: 10, max: 100, bl: true },
    { name: "Retinopathy", min: 10, max: 100, bl: true },
  ],
  "Genitourinary & Reproductive": [
    { name: "Erectile Dysfunction", min: 0, max: 20, bl: false },
    { name: "Kidney Disease", min: 0, max: 100, bl: false },
    { name: "Kidney Stones", min: 10, max: 30, bl: false },
    { name: "Prostate Cancer", min: 0, max: 100, bl: false },
    { name: "Urinary Incontinence", min: 20, max: 60, bl: false },
  ],
  "Musculoskeletal": [
    { name: "Ankle Pain", min: 10, max: 40, bl: true },
    { name: "Arthritis", min: 10, max: 100, bl: true },
    { name: "Back Pain", min: 10, max: 100, bl: false },
    { name: "Carpal Tunnel", min: 10, max: 70, bl: true },
    { name: "Degenerative Disc Disease", min: 10, max: 100, bl: false },
    { name: "Flat Feet", min: 0, max: 50, bl: true },
    { name: "Foot Pain", min: 10, max: 30, bl: true },
    { name: "Gout", min: 20, max: 60, bl: true },
    { name: "Hip Pain", min: 10, max: 90, bl: true },
    { name: "Knee Pain", min: 10, max: 60, bl: true },
    { name: "Lumbar Strain", min: 10, max: 50, bl: false },
    { name: "Neck Pain", min: 10, max: 100, bl: false },
    { name: "Plantar Fasciitis", min: 10, max: 30, bl: true },
    { name: "Rotator Cuff / Bursitis", min: 10, max: 50, bl: true },
    { name: "Shoulder Disabilities", min: 10, max: 50, bl: true },
    { name: "Spinal Fusion", min: 20, max: 100, bl: false },
    { name: "Spinal Stenosis", min: 10, max: 100, bl: false },
    { name: "Tennis Elbow", min: 10, max: 50, bl: true },
    { name: "TMJ", min: 10, max: 50, bl: false },
    { name: "Wrist Pain", min: 10, max: 30, bl: true },
  ],
  "Neurological": [
    { name: "Epilepsy", min: 10, max: 100, bl: false },
    { name: "Headaches", min: 0, max: 50, bl: false },
    { name: "Migraines", min: 0, max: 50, bl: false },
    { name: "Multiple Sclerosis", min: 30, max: 100, bl: false },
    { name: "Nerve Damage", min: 10, max: 80, bl: true },
    { name: "Peripheral Neuropathy", min: 10, max: 80, bl: true },
    { name: "Radiculopathy", min: 10, max: 60, bl: true },
    { name: "Sciatic Nerve", min: 10, max: 80, bl: true },
    { name: "TBI", min: 10, max: 100, bl: false },
  ],
  "Respiratory": [
    { name: "Asthma", min: 10, max: 100, bl: false },
    { name: "COPD", min: 10, max: 100, bl: false },
    { name: "Rhinitis", min: 0, max: 30, bl: false },
    { name: "Sinusitis", min: 0, max: 50, bl: false },
    { name: "Sleep Apnea", min: 0, max: 100, bl: false },
  ],
  "Skin Conditions": [
    { name: "Scars", min: 0, max: 80, bl: false },
    { name: "Skin Cancer", min: 0, max: 100, bl: false },
    { name: "Other Skin Conditions", min: 0, max: 60, bl: false },
  ],
  "Mental Health": [
    { name: "Adjustment Disorder", min: 0, max: 100, bl: false },
    { name: "Anxiety Disorders", min: 0, max: 100, bl: false },
    { name: "Bipolar Disorder", min: 10, max: 100, bl: false },
    { name: "Depression", min: 0, max: 100, bl: false },
    { name: "Eating Disorders", min: 0, max: 100, bl: false },
    { name: "Insomnia", min: 0, max: 70, bl: false },
    { name: "OCD", min: 10, max: 100, bl: false },
    { name: "Panic Disorder", min: 10, max: 100, bl: false },
    { name: "PTSD", min: 0, max: 100, bl: false },
    { name: "Schizophrenia", min: 10, max: 100, bl: false },
  ],
};

const VALID_RATINGS = [0,10,20,30,40,50,60,70,80,90,100];
const dm = "'DM Sans', sans-serif";
const card = { background: "#141d2f", border: "1px solid #253049", borderRadius: "12px" };
const inp = { width: "100%", padding: "10px 14px", background: "#0c1220", border: "1px solid #253049", borderRadius: "8px", color: "#e2e8f0", fontSize: "14px", fontFamily: dm, outline: "none", boxSizing: "border-box" };

function combineRatings(ratings) {
  if (!ratings.length) return 0;
  let rem = 100;
  ratings.forEach(r => { rem -= Math.round(rem * r / 100); });
  return 100 - rem;
}

function calcVARating(selected) {
  if (!selected.length) return { combined: 0, rounded: 0, steps: [], bilateralGroups: [] };
  const bU = [], bL = [], indiv = [];
  selected.forEach(item => {
    if (item.bl && item.side && item.side !== "none" && item.ext) {
      if (item.ext === "upper") bU.push(item); else bL.push(item);
    } else indiv.push(item);
  });
  const bGroups = [];
  [{ items: bU, label: "Upper Extremity Bilateral" }, { items: bL, label: "Lower Extremity Bilateral" }].forEach(({ items, label }) => {
    if (!items.length) return;
    const hasL = items.some(i => i.side === "left" || i.side === "both");
    const hasR = items.some(i => i.side === "right" || i.side === "both");
    if (!hasL || !hasR) { indiv.push(...items); return; }
    const rs = [], ns = [];
    items.forEach(i => {
      if (i.side === "both") { rs.push(i.rating, i.rating); ns.push(i.name + " (L)", i.name + " (R)"); }
      else { rs.push(i.rating); ns.push(i.name + (i.side === "left" ? " (L)" : " (R)")); }
    });
    const sorted = rs.map((r, i) => ({ r, n: ns[i] })).sort((a, b) => b.r - a.r);
    const cv = combineRatings(sorted.map(s => s.r));
    const bf = Math.round(cv * 0.1 * 10) / 10;
    bGroups.push({ label, items: sorted, combinedVal: cv, bilateralFactor: bf, bilateralRounded: Math.round(cv + bf) });
  });
  const all = [];
  bGroups.forEach(g => all.push({ name: g.label + " (incl. BF)", rating: g.bilateralRounded, isBG: true, group: g }));
  indiv.forEach(item => all.push({ name: item.name, rating: item.rating }));
  all.sort((a, b) => b.rating - a.rating);
  let rem = 100; const steps = [];
  all.forEach(item => { const ap = Math.round(rem * item.rating / 100); steps.push({ ...item, remaining: rem, applied: ap }); rem -= ap; });
  const cWith = 100 - rem;
  let cWithout = null;
  if (bGroups.length > 0) {
    const flat = []; selected.forEach(i => { if (i.bl && i.side === "both" && i.ext) flat.push(i.rating, i.rating); else flat.push(i.rating); });
    flat.sort((a, b) => b - a); cWithout = combineRatings(flat);
  }
  const eff = cWithout !== null ? Math.max(cWith, cWithout) : cWith;
  const rounded = eff % 10 >= 5 ? Math.ceil(eff / 10) * 10 : Math.floor(eff / 10) * 10;
  return { combined: eff, rounded: Math.min(rounded, 100), steps, bilateralGroups: bGroups, usedBilateral: cWithout !== null ? cWith >= cWithout : true };
}

const SMC_LEVELS = [
  { level: "SMC-K", rate: "$139.87/mo", desc: "Added to your base pay for anatomical loss or loss of use of one hand, one foot, both buttocks, one or more creative organs, or breast tissue. You can receive up to 3 SMC-K awards simultaneously.", color: "#60a5fa" },
  { level: "SMC-L", rate: "$4,858.29/mo", desc: "Loss of use of both feet, one hand and one foot, blindness in both eyes, or being permanently bedridden. Also awarded when you need regular Aid & Attendance.", color: "#60a5fa" },
  { level: "SMC-M", rate: "$5,366.97/mo", desc: "Loss of use of both hands, or conditions at the SMC-L level combined with additional loss of use or blindness.", color: "#60a5fa" },
  { level: "SMC-N", rate: "$5,861.97/mo", desc: "Loss of use of both arms above the elbow, or conditions combining blindness, amputation, and severe loss of use.", color: "#60a5fa" },
  { level: "SMC-O/P", rate: "$6,519.93/mo", desc: "Highest anatomical loss combinations. Multiple qualifying conditions from levels L–N combined.", color: "#60a5fa" },
  { level: "SMC-R.1", rate: "$9,555.44/mo", desc: "Need for regular Aid & Attendance at a higher level — requiring daily personal healthcare services from another person.", color: "#22c55e" },
  { level: "SMC-R.2/T", rate: "$11,271.67/mo", desc: "R.2: Higher level of care under supervision of a licensed healthcare professional. T: Severe TBI requiring ongoing supervision for safety.", color: "#22c55e" },
  { level: "SMC-S", rate: "$4,408.53/mo", desc: "Housebound benefit. Two paths: (1) One condition rated 100% PLUS others combining to 60%+ (\"100 plus 60 rule\"), or (2) substantially confined to home due to SC disabilities.", color: "#fbbf24" },
];

export default function VADisabilityRatingCalculator() {
  const [selected, setSelected] = useState([]);
  const [openCat, setOpenCat] = useState(null);
  const [search, setSearch] = useState("");
  const [showBD, setShowBD] = useState(false);

  const add = useCallback(c => {
    setSelected(p => { if (p.some(s => s.name === c.name)) return p; return [...p, { ...c, rating: c.min || 10, side: c.bl ? "both" : "none", ext: null, id: Date.now() }]; });
  }, []);
  const remove = useCallback(id => { setSelected(p => p.filter(s => s.id !== id)); }, []);
  const update = useCallback((id, f, v) => { setSelected(p => p.map(s => s.id === id ? { ...s, [f]: v } : s)); }, []);
  const result = useMemo(() => calcVARating(selected), [selected]);

  const filtered = search.trim()
    ? Object.entries(CONDITIONS).reduce((a, [cat, items]) => { const f = items.filter(c => c.name.toLowerCase().includes(search.toLowerCase())); if (f.length) a[cat] = f; return a; }, {})
    : CONDITIONS;

  const rc = result.rounded >= 70 ? "#dc2626" : result.rounded >= 40 ? "#d97706" : result.rounded > 0 ? "#2563eb" : "#6b7280";

  return (
    <div style={{ minHeight: "100vh", background: "#0c1220", color: "#e2e8f0", fontFamily: "'Crimson Text', Georgia, serif" }}>
      <link href="https://fonts.googleapis.com/css2?family=Crimson+Text:wght@400;600;700&family=DM+Sans:wght@400;500;600;700&display=swap" rel="stylesheet" />

      {/* Header */}
      <div style={{ background: "linear-gradient(135deg, #1a2744 0%, #0f1b30 100%)", borderBottom: "2px solid #2a3a5c", padding: "24px 32px", textAlign: "center" }}>
        <div style={{ display: "flex", alignItems: "center", justifyContent: "center", gap: "14px", marginBottom: "4px" }}>
          <div style={{ width: "40px", height: "40px", borderRadius: "50%", background: "linear-gradient(135deg, #3b82f6, #1d4ed8)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: "20px", fontWeight: "bold", color: "white", fontFamily: dm, boxShadow: "0 0 20px rgba(59,130,246,0.3)" }}>VA</div>
          <h1 style={{ fontSize: "26px", fontWeight: 700, margin: 0, letterSpacing: "-0.5px", color: "#f1f5f9" }}>VA Disability Rating Calculator</h1>
        </div>
        <p style={{ fontFamily: dm, fontSize: "13px", color: "#94a3b8", margin: 0 }}>Combined rating with VA "whole person" math, bilateral factor & SMC guide</p>
      </div>

      <div style={{ maxWidth: "960px", margin: "0 auto", padding: "24px 20px" }}>
        {/* Calculator Grid */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1.15fr", gap: "20px" }}>
          {/* LEFT: Condition Picker */}
          <div style={{ ...card, padding: "14px", overflow: "hidden", display: "flex", flexDirection: "column" }}>
            <div style={{ fontFamily: dm, fontSize: "13px", fontWeight: 600, textTransform: "uppercase", letterSpacing: "1.5px", color: "#64748b", marginBottom: "8px" }}>Add Conditions</div>
            <input type="text" placeholder="Search conditions..." value={search}
              onChange={e => { setSearch(e.target.value); if (e.target.value) setOpenCat("__s__"); else setOpenCat(null); }}
              style={{ ...inp, marginBottom: "10px", fontSize: "13px" }} />
            <div style={{ flex: 1, overflowY: "auto", maxHeight: "420px" }}>
              {Object.entries(filtered).map(([cat, items]) => {
                const isO = openCat === cat || (search.trim() && openCat === "__s__");
                return (
                  <div key={cat} style={{ marginBottom: "2px" }}>
                    <button onClick={() => setOpenCat(isO && !search ? null : cat)}
                      style={{ width: "100%", padding: "9px 12px", background: isO ? "#1a2744" : "transparent", border: "none", borderRadius: "6px", color: isO ? "#93c5fd" : "#94a3b8", fontFamily: dm, fontSize: "13px", fontWeight: 600, cursor: "pointer", textAlign: "left", display: "flex", justifyContent: "space-between" }}>
                      {cat}
                      <span style={{ transform: isO ? "rotate(180deg)" : "rotate(0deg)", transition: "transform 0.2s", fontSize: "10px" }}>▼</span>
                    </button>
                    {isO && <div style={{ padding: "4px 0 4px 8px" }}>
                      {items.map(c => {
                        const added = selected.some(s => s.name === c.name);
                        return (
                          <button key={c.name} onClick={() => !added && add(c)}
                            style={{ display: "flex", justifyContent: "space-between", alignItems: "center", width: "100%", padding: "6px 10px", background: added ? "rgba(59,130,246,0.1)" : "transparent", border: "none", borderRadius: "5px", color: added ? "#60a5fa" : "#cbd5e1", fontFamily: dm, fontSize: "12px", cursor: added ? "default" : "pointer", textAlign: "left", opacity: added ? 0.6 : 1 }}>
                            <span>{c.bl && <span style={{ display: "inline-block", background: "rgba(168,85,247,0.2)", color: "#c084fc", fontSize: "9px", fontWeight: 700, padding: "1px 5px", borderRadius: "3px", marginRight: "6px" }}>BL</span>}{c.name}</span>
                            <span style={{ fontSize: "10px", color: "#64748b", flexShrink: 0, marginLeft: "8px" }}>{added ? "✓" : `${c.min}–${c.max}%`}</span>
                          </button>
                        );
                      })}
                    </div>}
                  </div>
                );
              })}
            </div>
          </div>

          {/* RIGHT: Result + Selected */}
          <div style={{ display: "flex", flexDirection: "column", gap: "16px" }}>
            {/* Result */}
            <div style={{ ...card, padding: "24px", textAlign: "center", background: "linear-gradient(135deg, #141d2f, #192640)" }}>
              <div style={{ fontSize: "72px", fontWeight: 700, lineHeight: 1, color: rc, textShadow: `0 0 40px ${rc}44` }}>{result.rounded}%</div>
              <div style={{ fontFamily: dm, fontSize: "12px", color: "#64748b", marginTop: "6px" }}>
                Combined VA Rating{result.combined !== result.rounded && <span style={{ color: "#475569" }}> (exact: {result.combined}%, rounded to nearest 10)</span>}
              </div>
              {result.bilateralGroups?.length > 0 && (
                <div style={{ marginTop: "8px", display: "inline-block", background: "rgba(168,85,247,0.12)", border: "1px solid rgba(168,85,247,0.25)", borderRadius: "20px", padding: "4px 14px", fontFamily: dm, fontSize: "11px", color: "#c084fc" }}>
                  {result.usedBilateral ? `Bilateral factor applied (+${result.bilateralGroups.map(g => g.bilateralFactor.toFixed(1)).join(", +")}%)` : "Bilateral factor skipped (higher rating without it — 2023 rule)"}
                </div>
              )}
              {selected.length > 0 && (
                <div><button onClick={() => setShowBD(!showBD)} style={{ marginTop: "12px", padding: "6px 16px", background: "rgba(59,130,246,0.15)", border: "1px solid rgba(59,130,246,0.3)", borderRadius: "20px", color: "#60a5fa", fontFamily: dm, fontSize: "12px", fontWeight: 500, cursor: "pointer" }}>{showBD ? "Hide" : "Show"} VA Math Breakdown</button></div>
              )}
              {showBD && result.steps.length > 0 && (
                <div style={{ marginTop: "14px", textAlign: "left", background: "#0c1220", borderRadius: "8px", padding: "14px", fontFamily: dm, fontSize: "11.5px", lineHeight: 1.9, color: "#94a3b8" }}>
                  {result.bilateralGroups?.map((g, i) => (
                    <div key={i} style={{ marginBottom: "10px", padding: "8px", background: "rgba(168,85,247,0.06)", borderRadius: "6px", border: "1px solid rgba(168,85,247,0.15)" }}>
                      <div style={{ color: "#c084fc", fontWeight: 600, marginBottom: "3px" }}>{g.label} Group:</div>
                      {g.items.map((it, j) => <div key={j} style={{ paddingLeft: "8px" }}>{it.n}: {it.r}%</div>)}
                      <div style={{ marginTop: "3px" }}>Combined = {g.combinedVal}% + 10% BF ({g.bilateralFactor.toFixed(1)}%) = <span style={{ color: "#c084fc" }}>{g.bilateralRounded}%</span></div>
                    </div>
                  ))}
                  <div style={{ color: "#64748b", marginBottom: "4px" }}>Final combination (whole person):</div>
                  {result.steps.map((s, i) => (
                    <div key={i}><span style={{ color: s.isBG ? "#c084fc" : "#60a5fa" }}>{s.name}</span> → {s.rating}% of {s.remaining}% = <span style={{ color: "#e2e8f0" }}>{s.applied}%</span><span style={{ color: "#475569" }}> (remaining: {s.remaining - s.applied}%)</span></div>
                  ))}
                  <div style={{ borderTop: "1px solid #253049", marginTop: "8px", paddingTop: "8px", color: "#e2e8f0" }}>
                    Exact combined = <strong>{result.combined}%</strong> → rounded to <strong style={{ color: rc }}>{result.rounded}%</strong>
                  </div>
                </div>
              )}
            </div>

            {/* Selected Conditions */}
            <div style={{ ...card, padding: "14px", flex: 1 }}>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: "10px" }}>
                <span style={{ fontFamily: dm, fontSize: "13px", fontWeight: 600, textTransform: "uppercase", letterSpacing: "1.5px", color: "#64748b" }}>Your Conditions ({selected.length})</span>
                {selected.length > 0 && <button onClick={() => setSelected([])} style={{ background: "none", border: "none", color: "#ef4444", fontFamily: dm, fontSize: "11px", cursor: "pointer" }}>Clear All</button>}
              </div>
              {!selected.length ? (
                <div style={{ textAlign: "center", padding: "30px 20px", color: "#475569", fontFamily: dm, fontSize: "13px", lineHeight: 1.6 }}>
                  Select conditions from the left panel.<br />
                  <span style={{ color: "#c084fc", fontSize: "11px" }}>BL</span> = bilateral-eligible (specify side & extremity)
                </div>
              ) : (
                <div style={{ display: "flex", flexDirection: "column", gap: "5px", maxHeight: "300px", overflowY: "auto" }}>
                  {selected.map(item => {
                    const opts = VALID_RATINGS.filter(r => r >= item.min && r <= item.max);
                    return (
                      <div key={item.id} style={{ background: "#0f1825", borderRadius: "8px", padding: "9px 11px", border: "1px solid #1e2d47" }}>
                        <div style={{ display: "flex", alignItems: "center", gap: "8px" }}>
                          <div style={{ flex: 1, fontFamily: dm, fontSize: "13px", color: "#cbd5e1", overflow: "hidden", textOverflow: "ellipsis", whiteSpace: "nowrap" }}>{item.name}</div>
                          <select value={item.rating} onChange={e => update(item.id, "rating", +e.target.value)}
                            style={{ padding: "4px 6px", background: "#1a2744", border: "1px solid #2a3a5c", borderRadius: "6px", color: "#93c5fd", fontFamily: dm, fontSize: "13px", fontWeight: 600, cursor: "pointer", minWidth: "62px", textAlign: "center" }}>
                            {opts.map(r => <option key={r} value={r}>{r}%</option>)}
                          </select>
                          <button onClick={() => remove(item.id)} style={{ background: "none", border: "none", color: "#475569", cursor: "pointer", fontSize: "16px", padding: "0 2px" }}>×</button>
                        </div>
                        {item.bl && (
                          <div style={{ display: "flex", gap: "5px", marginTop: "7px", flexWrap: "wrap", alignItems: "center" }}>
                            <span style={{ fontFamily: dm, fontSize: "10px", color: "#64748b", textTransform: "uppercase" }}>Side:</span>
                            {["left", "right", "both"].map(s => (
                              <button key={s} onClick={() => update(item.id, "side", s)}
                                style={{ padding: "3px 9px", borderRadius: "4px", border: item.side === s ? "1px solid rgba(168,85,247,0.5)" : "1px solid #253049", background: item.side === s ? "rgba(168,85,247,0.15)" : "transparent", color: item.side === s ? "#c084fc" : "#64748b", fontFamily: dm, fontSize: "11px", cursor: "pointer", textTransform: "capitalize" }}>
                                {s === "both" ? "Both" : s === "left" ? "Left" : "Right"}
                              </button>
                            ))}
                            <span style={{ fontFamily: dm, fontSize: "10px", color: "#64748b", textTransform: "uppercase", marginLeft: "8px" }}>Ext:</span>
                            {["upper", "lower"].map(e => (
                              <button key={e} onClick={() => update(item.id, "ext", e)}
                                style={{ padding: "3px 9px", borderRadius: "4px", border: item.ext === e ? "1px solid rgba(168,85,247,0.5)" : "1px solid #253049", background: item.ext === e ? "rgba(168,85,247,0.15)" : "transparent", color: item.ext === e ? "#c084fc" : "#64748b", fontFamily: dm, fontSize: "11px", cursor: "pointer", textTransform: "capitalize" }}>
                                {e === "upper" ? "Upper" : "Lower"}
                              </button>
                            ))}
                          </div>
                        )}
                      </div>
                    );
                  })}
                </div>
              )}
            </div>
          </div>
        </div>

        {/* ── SMC Section ── */}
        <div style={{ ...card, padding: "20px", marginTop: "20px" }}>
          <h3 style={{ fontFamily: dm, fontSize: "16px", fontWeight: 700, color: "#f1f5f9", margin: "0 0 10px", display: "flex", alignItems: "center", gap: "8px" }}>
            <span style={{ fontSize: "20px" }}>⭐</span> Special Monthly Compensation (SMC)
          </h3>
          <p style={{ fontFamily: dm, fontSize: "13px", color: "#94a3b8", lineHeight: 1.7, margin: "0 0 16px" }}>
            If your service-connected disabilities are severe enough, you may qualify for SMC — additional tax-free compensation <em>above</em> the standard 100% rate. SMC is defined under 38 U.S.C. § 1114 and uses letter designations (K through T). The VA should award SMC automatically when evidence meets the criteria, but it is frequently overlooked.
          </p>

          <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: "10px", marginBottom: "16px" }}>
            {SMC_LEVELS.map(s => (
              <div key={s.level} style={{ background: "#0f1825", borderRadius: "8px", padding: "12px 14px", border: "1px solid #1e2d47" }}>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: "5px" }}>
                  <span style={{ fontFamily: dm, fontSize: "14px", fontWeight: 700, color: s.color }}>{s.level}</span>
                  <span style={{ fontFamily: dm, fontSize: "12px", fontWeight: 600, color: "#22c55e" }}>{s.rate}</span>
                </div>
                <div style={{ fontFamily: dm, fontSize: "11.5px", color: "#94a3b8", lineHeight: 1.55 }}>{s.desc}</div>
              </div>
            ))}
          </div>

          <div style={{ background: "#0c1220", borderRadius: "8px", padding: "14px 16px", fontFamily: dm, fontSize: "12px", lineHeight: 1.75, color: "#64748b" }}>
            <strong style={{ color: "#fbbf24" }}>Key things to know about SMC:</strong><br />
            • <strong style={{ color: "#e2e8f0" }}>SMC-K is additive</strong> — added on top of your regular compensation or other SMC levels. All other SMC levels (L–T) <em>replace</em> your standard disability pay with a higher amount.<br />
            • <strong style={{ color: "#e2e8f0" }}>SMC-S (Housebound)</strong> is the most commonly missed benefit. If you have one condition at 100% and others combining to 60%+, you may automatically qualify — even if you're not literally housebound.<br />
            • <strong style={{ color: "#e2e8f0" }}>SMC should be automatic</strong> — the VA is required to consider it whenever evidence supports it. However, many veterans must specifically request it or appeal. Review your rating decision letter for SMC language.<br />
            • <strong style={{ color: "#e2e8f0" }}>Retroactive pay</strong> — if the VA later determines you qualified earlier, you can receive back pay to your original eligibility date.<br />
            • All rates shown are <strong style={{ color: "#e2e8f0" }}>2026 rates</strong> (single veteran, no dependents). Rates increase with dependents and adjust annually with COLA.
          </div>

          <div style={{ marginTop: "10px", fontFamily: dm, fontSize: "12px" }}>
            <a href="https://www.va.gov/disability/compensation-rates/special-monthly-compensation-rates/" target="_blank" rel="noopener noreferrer" style={{ color: "#60a5fa", textDecoration: "none" }}>View official 2026 SMC rates at VA.gov →</a>
          </div>
        </div>

        {/* Footer */}
        <div style={{ ...card, padding: "18px 20px", marginTop: "16px", fontFamily: dm, fontSize: "12px", lineHeight: 1.7, color: "#64748b" }}>
          <strong style={{ color: "#94a3b8" }}>How VA Math Works:</strong> The VA uses a "whole person" concept — each rating is applied to the remaining healthy percentage. Two 50% ratings = 75%, rounded to 80%.
          <br /><strong style={{ color: "#c084fc" }}>Bilateral Factor:</strong> When disabilities affect both sides (both arms, both legs, or paired muscles), ratings are combined first, then 10% of that value is added before merging with other conditions. Per the 2023 rule, if the bilateral factor lowers the rating, it is excluded.
          <br /><span style={{ fontSize: "11px", color: "#475569", marginTop: "4px", display: "inline-block" }}>This tool is for estimation only and does not constitute official VA guidance.</span>
        </div>
      </div>
    </div>
  );
}
