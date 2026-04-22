import { useState, useEffect } from "react";
const buildLink = (source, brand, model) => {
const q = encodeURIComponent(`${brand} ${model}`);
if (source === "LeBonCoin") return `https://www.leboncoin.fr/recherche?category=2&text=${q}
if (source === "AutoScout24") return `https://www.autoscout24.fr/lst?fullTextSearch=${q}`;
if (source === "La Centrale") return `https://www.lacentrale.fr/listing?makesModelsCommerci
if (source === "ParuVendu") return `https://www.paruvendu.fr/auto-moto-bateau/voiture/?q=${
return "#";
};
const CARS = [
{ id: 1, brand: "Peugeot", model: "308", year: 2023, price: 28900, mileage: 8000, fuel: "Es
{ id: 2, brand: "Renault", model: "Clio", year: 2022, price: 18500, mileage: 22000, fuel: "
{ id: 3, brand: "BMW", model: "Série 3", year: 2021, price: 45000, mileage: 35000, fuel: "D
{ id: 4, brand: "Tesla", model: "Model 3", year: 2023, price: 52000, mileage: 5000, fuel: "
{ id: 5, brand: "Volkswagen", model: "Golf", year: 2022, price: 31000, mileage: 18000, fuel
{ id: 6, brand: "Ford", model: "Mustang", year: 2023, price: 68000, mileage: 2000, fuel: "E
{ id: 7, brand: "Toyota", model: "Yaris", year: 2021, price: 16900, mileage: 40000, fuel: "
{ id: 8, brand: "Mercedes", model: "Classe A", year: 2022, price: 42000, mileage: 12000, fu
{ id: 9, brand: "Citroën", model: "C3", year: 2023, price: 19500, mileage: 6000, fuel: "Ess
{ id: 10, brand: "Audi", model: "A4", year: 2021, price: 38000, mileage: 45000, fuel: "Dies
{ id: 11, brand: "Hyundai", model: "Tucson", year: 2022, price: 34000, mileage: 20000, fuel
{ id: 12, brand: "Dacia", model: "Sandero", year: 2023, price: 13500, mileage: 3000, fuel:
{ id: 13, brand: "Porsche", model: "Cayenne", year: 2022, price: 95000, mileage: 15000, fue
{ id: 14, brand: "Seat", model: "Ibiza", year: 2021, price: 17500, mileage: 30000, fuel: "E
{ id: 15, brand: "Kia", model: "EV6", year: 2023, price: 48000, mileage: 8000, fuel: "Élect
{ id: 16, brand: "Peugeot", model: "3008", year: 2022, price: 39500, mileage: 25000, fuel:
];
const FILTER_DEFINITIONS = [
{ key: "brand", label: "Marque", icon: " ", type: "select", options: [...new Set(CARS.map(
{ key: "fuel", label: "Carburant", icon: " { key: "category", label: "Catégorie", icon: " ", type: "select", options: ["Essence", "Diesel
", type: "select", options: ["Citadine", "B
{ key: "transmission", label: "Boîte de vitesse", icon: " ", type: "select", options: ["Ma
{ key: "priceMax", label: "Prix max", icon: " { key: "yearMin", label: "Année min", icon: " { key: "mileageMax", label: "Kilométrage max", icon: " ", type: "range", max: 100000, step: 1000 },
", type: "range", max: 2023, min: 2015, step
", type: "range", max: 150000, step
{ key: "source", label: "Site source", icon: " ", type: "select", options: ["LeBonCoin", "
];
const SOURCES = [
{ name: "LeBonCoin", color: "#F56400", url: "https://www.leboncoin.fr/c/voitures" },
{ name: "AutoScout24", color: "#003399", url: "https://www.autoscout24.fr" },
{ name: "La Centrale", color: "#E30016", url: "https://www.lacentrale.fr" },
{ name: "ParuVendu", color: "#FF6900", url: "https://www.paruvendu.fr/auto-moto-bateau/voit
];
function ApplePayButton({ onClick }) {
const [hov, setHov] = useState(false);
return (
<button onClick={onClick}
onMouseOver={() => setHov(true)} onMouseOut={() => setHov(false)}
style={{ width: "100%", background: "#000", color: "#fff", border: "none", borderRadius
<svg width="20" height="20" viewBox="0 0 24 24" fill="white">
<path d="M18.71 19.5c-.83 1.24-1.71 2.45-3.05 2.47-1.34.03-1.77-.79-3.29-.79-1.53 0-2
</svg>
Pay
</button>
);
}
function PayPalButton({ onClick }) {
const [hov, setHov] = useState(false);
return (
<button onClick={onClick}
onMouseOver={() => setHov(true)} onMouseOut={() => setHov(false)}
style={{ width: "100%", background: "#FFC439", color: "#003087", border: "none", <svg width="22" height="22" viewBox="0 0 24 24" fill="#003087">
<path d="M7.076 21.337H2.47a.641.641 0 0 1-.633-.74L4.944.901C5.026.382 5.474 0 5.998
</svg>
Payer avec PayPal
</button>
border
);
}
export default function AutoFind() {
const [unlockedFilters, setUnlockedFilters] = useState(new Set());
const [filterValues, setFilterValues] = useState({});
const [credits, setCredits] = useState(5);
const [payModal, setPayModal] = useState(null);
const [payStep, setPayStep] = useState("choose");
const [view, setView] = useState("grid");
const [sortBy, setSortBy] = useState("price");
const [selectedCar, setSelectedCar] = useState(null);
const [searchText, setSearchText] = useState("");
const [toast, setToast] = useState(null);
const [animIn, setAnimIn] = useState(false);
const [rechargeQty, setRechargeQty] = useState(5);
useEffect(() => { setTimeout(() => setAnimIn(true), 100); }, []);
const showToast = (msg, type = "success") => {
setToast({ msg, type });
setTimeout(() => setToast(null), 3000);
};
const simulatePay = (method) => {
setPayStep(method);
setTimeout(() => {
setPayStep("success");
setTimeout(() => {
if (payModal?.filter) {
setUnlockedFilters(prev => new Set([...prev, payModal.filter.key]));
setCredits(c => c - 1);
showToast(`✓ Filtre "${payModal.filter.label}" débloqué !`);
} else {
setCredits(c => c + rechargeQty);
showToast(`✓ ${rechargeQty} crédits ajoutés !`);
}
setPayModal(null);
setPayStep("choose");
}, 1200);
}, 1900);
};
const filteredCars = CARS
.filter(car => {
if (searchText) {
const q = searchText.toLowerCase();
if (!`${car.brand} ${car.model} ${car.category}`.toLowerCase().includes(q)) return fa
}
for (const [key, val] of Object.entries(filterValues)) {
if (!val || !unlockedFilters.has(key)) continue;
if (key === "priceMax" && car.price > val) return false;
if (key === "yearMin" && car.year < val) return false;
if (key === "mileageMax" && car.mileage > val) return false;
if (["brand", "fuel", "category", "transmission", "source"].includes(key) && car[key]
}
return true;
})
.sort((a, b) => sortBy === "price" ? a.price - b.price : sortBy === "year" ? b.year - a.y
const fmt = (p) => p.toLocaleString("fr-FR") + " €";
const fmtKm = (m) => m.toLocaleString("fr-FR") + " km";
return (
<div style={{ minHeight: "100vh", background: "#0a0a0f", color: "#f0f0f5", fontFamily: "'
<style>{`
@import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;700;900&fa
*{box-sizing:border-box;margin:0;padding:0}
::-webkit-scrollbar{width:4px}::-webkit-scrollbar-thumb{background:#f97316;border-rad
.car-card{background:#111118;border:1px solid #1e1e2e;border-radius:16px;padding:20px
.car-card::before{content:'';position:absolute;top:0;left:0;right:0;height:2px;backgr
.car-card:hover{transform:translateY(-4px);border-color:#f97316;box-shadow:0 20px 60p
.car-card:hover::before{transform:scaleX(1)}
.filter-pill{padding:10px 18px;border-radius:50px;border:1px solid #2a2a3a;background
.filter-pill.unlocked{border-color:#f97316;color:#f97316;background:rgba(249,115,22,.
.filter-pill:hover{border-color:#f97316;color:#fff}
.btn-orange{background:linear-gradient(135deg,#f97316,#ef4444);border:none;padding:12
.btn-orange:hover{transform:scale(1.04);box-shadow:0 8px 30px rgba(249,115,22,.4)}
.modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,.9);display:flex;align-it
.modal-box{background:#12121c;border:1px solid #2a2a3a;border-radius:24px;padding:36p
@keyframes fi{from{opacity:0}to{opacity:1}}
@keyframes su{from{transform:translateY(40px);opacity:0}to{transform:translateY(0);op
@keyframes spin{to{transform:rotate(360deg)}}
@keyframes pop{0%,100%{transform:scale(1)}50%{transform:scale(1.12)}}
@keyframes fadein{from{opacity:0;transform:translateY(12px)}to{opacity:1;transform:tr
.toast{position:fixed;bottom:28px;right:28px;padding:13px 22px;border-radius:12px;fon
.toast.ok{background:#1a2a1a;border:1px solid #22c55e;color:#22c55e}
.toast.err{background:#2a1a1a;border:1px solid #ef4444;color:#ef4444}
input[type=range]{width:100%;accent-color:#f97316}
select{background:#1a1a2a;border:1px solid #2a2a3a;color:#f0f0f5;padding:10px 14px;bo
.fi{animation:fadein .4s ease both}
.spinner{width:34px;height:34px;border:3px solid #2a2a3a;border-top-color:#f97316;bor
.divider{display:flex;align-items:center;gap:12px;color:#444;font-size:12px;margin:14
.divider::before,.divider::after{content:'';flex:1;height:1px;background:#2a2a3a}
.src-badge{font-size:11px;padding:3px 10px;border-radius:20px;font-weight:600;backgro
.qty-btn{flex:1;padding:12px 8px;border-radius:12px;cursor:pointer;font-weight:700;fo
`}</style>
{/* BG glow */}
<div style={{ position:"fixed",inset:0,zIndex:0,pointerEvents:"none" }}>
<div style={{ position:"absolute",top:"-20%",left:"-10%",width:600,height:600,borderR
<div style={{ position:"absolute",bottom:"-20%",right:"-10%",width:500,height:500,bor
</div>
<div style={{ position:"relative",zIndex:1 }}>
{/* HEADER */}
<header style={{ borderBottom:"1px solid #1e1e2e",padding:"14px 40px",display:"flex",
<div style={{ display:"flex",alignItems:"center",gap:12 }}>
<span style={{ fontSize:26 }}> </span>
<div>
<div style={{ fontFamily:"'Space Grotesk',sans-serif",fontWeight:700,fontSize:2
<div style={{ fontSize:11,color:"#555" }}>Agrégateur de voitures</div>
</div>
</div>
<div style={{ display:"flex",alignItems:"center",gap:12 }}>
{SOURCES.map(s => (
<a key={s.name} href={s.url} target="_blank" rel="noopener noreferrer"
style={{ textDecoration:"none",display:"flex",alignItems:"center",gap:6,paddi
onMouseOver={e=>e.currentTarget.style.borderColor=s.color}
onMouseOut={e=>e.currentTarget.style.borderColor="#2a2a3a"}>
<div style={{ width:6,height:6,borderRadius:"50%",background:s.color,boxShado
{s.name}
</a>
))}
<div style={{ background:"linear-gradient(135deg,#f97316,#ef4444)",padding:"6px 1
{credits} crédit{credits!==1?"s":""}
</div>
<button className="btn-orange" onClick={()=>{setPayModal({recharge:true});setPayS
+ Recharger
</button>
</div>
</header>
{/* HERO */}
<div style={{ padding:"54px 40px 34px",textAlign:"center",opacity:animIn?1:0,transfor
<div style={{ fontSize:11,letterSpacing:3,color:"#f97316",fontWeight:700,marginBott
<h1 style={{ fontSize:50,fontWeight:900,lineHeight:1.1,marginBottom:12,fontFamily:"
Trouvez votre<br/>
<span style={{ background:"linear-gradient(135deg,#f97316,#ef4444)",WebkitBackgro
</h1>
<p style={{ color:"#666",fontSize:15,marginBottom:34 }}>
{CARS.length} annonces depuis LeBonCoin, AutoScout24, La Centrale &amp; ParuVendu
Filtres avancés à <strong style={{ color:"#f97316" }}>1€ / filtre</strong> </p>
<div style={{ maxWidth:500,margin:"0 auto",position:"relative" }}>
<span style={{ position:"absolute",left:20,top:"50%",transform:"translateY(-50%)"
<input
style={{ background:"#111118",border:"1px solid #2a2a3a",borderRadius:50,paddin
placeholder="Rechercher une marque, un modèle..."
value={searchText} onChange={e=>setSearchText(e.target.value)}
onFocus={e=>e.target.style.borderColor="#f97316"}
onBlur={e=>e.target.style.borderColor="#2a2a3a"}
— <str
/>
</div>
</div>
{/* FILTERS */}
<div style={{ padding:"0 40px 26px" }}>
<div style={{ background:"#0e0e18",border:"1px solid #1e1e2e",borderRadius:20,paddi
<div style={{ display:"flex",justifyContent:"space-between",alignItems:"center",m
<div>
<div style={{ fontWeight:700,fontSize:15 }}>Filtres avancés</div>
<div style={{ fontSize:12,color:"#555",marginTop:2 }}>{unlockedFilters.size}
</div>
{unlockedFilters.size>0 && (
<button onClick={()=>{setUnlockedFilters(new Set());setFilterValues({});}} st
)}
</div>
<div style={{ display:"flex",flexWrap:"wrap",gap:10 }}>
{FILTER_DEFINITIONS.map(filter=>{
const on=unlockedFilters.has(filter.key);
return (
<div key={filter.key}>
<div className={`filter-pill${on?" unlocked":""}`} onClick={()=>!on&&(set
<span>{filter.icon}</span><span>{filter.label}</span>
{!on&&<span style={{ fontSize:11,background:"#1a1a2a",padding:"2px 8px"
{on&&<span style={{ fontSize:11,color:"#22c55e" }}>✓</span>}
</div>
{on&&(
<div style={{ marginTop:8,padding:14,background:"#0a0a14",borderRadius:
{filter.type==="select"&&(
<select value={filterValues[filter.key]||""} onChange={e=>setFilter
<option value="">Tous</option>
{filter.options.map(o=><option key={o} value={o}>{o}</option>)}
</select>
)}
{filter.type==="range"&&(
<div>
<div style={{ fontSize:12,color:"#888",marginBottom:8 }}>
{filter.key==="priceMax"&&`Max: ${fmt(filterValues[filter.key]|
{filter.key==="mileageMax"&&`Max: ${fmtKm(filterValues[filter.k
{filter.key==="yearMin"&&`Min: ${filterValues[filter.key]||filt
</div>
<input type="range" min={filter.min||0} max={filter.max} step={fi
value={filterValues[filter.key]||filter.max}
onChange={e=>setFilterValues(p=>({...p,[filter.key]:Number(e.ta
</div>
)}
</div>
)}
</div>
);
})}
</div>
</div>
</div>
{/* RESULTS BAR */}
<div style={{ padding:"0 40px 16px",display:"flex",justifyContent:"space-between",ali
<div style={{ color:"#888",fontSize:14 }}>
<span style={{ color:"#f97316",fontWeight:700,fontSize:22 }}>{filteredCars.length
<span style={{ marginLeft:8 }}>voiture{filteredCars.length>1?"s":""} trouvée{filt
</div>
<div style={{ display:"flex",gap:10,alignItems:"center" }}>
<select value={sortBy} onChange={e=>setSortBy(e.target.value)} style={{ width:"au
<option value="price">Prix croissant</option>
<option value="year">Plus récent</option>
<option value="mileage">Moins de km</option>
</select>
<div style={{ display:"flex",gap:4,background:"#111118",border:"1px solid #2a2a3a
{["grid","list"].map(v=>(
<button key={v} onClick={()=>setView(v)} style={{ background:view===v?"#f9731
{v==="grid"?"⊞":"☰"}
</button>
))}
</div>
</div>
</div>
{/* CARS GRID */}
<div style={{ padding:"0 40px 60px",display:view==="grid"?"grid":"flex",gridTemplateC
{filteredCars.map((car,i)=>(
<div key={car.id} className="car-card fi" style={{ animationDelay:`${i*.04}s`,dis
<div style={{ fontSize:view==="grid"?60:46,textAlign:"center",padding:view==="g
{car.img}
</div>
<div style={{ flex:1 }}>
<div style={{ display:"flex",justifyContent:"space-between",alignItems:"flex-
<div>
<div style={{ fontWeight:800,fontSize:17,fontFamily:"'Space Grotesk',sans
<div style={{ color:"#888",fontSize:13 }}>{car.model} · {car.year}</div>
</div>
<a className="src-badge" href={buildLink(car.source,car.brand,car.model)} t
{car.source} ↗
</a>
</div>
<div style={{ display:"flex",gap:6,flexWrap:"wrap",marginBottom:14 }}>
{[{i:" ",v:car.fuel},{i:" ",v:car.transmission},{i:" ",v:fmtKm(car.mile
<span key={t.v} style={{ fontSize:12,padding:"3px 9px",background:"#1a1a2
))}
</div>
<div style={{ display:"flex",justifyContent:"space-between",alignItems:"cente
<div style={{ fontSize:22,fontWeight:900,color:"#f97316",fontFamily:"'Space
<button className="btn-orange" style={{ padding:"7px 16px",fontSize:13 }} o
</div>
</div>
</div>
))}
</div>
{filteredCars.length===0&&(
<div style={{ textAlign:"center",padding:"80px 40px",color:"#444" }}>
<div style={{ fontSize:60,marginBottom:14 }}> </div>
<div style={{ fontSize:20,fontWeight:700,marginBottom:8 }}>Aucune voiture trouvée
<div style={{ fontSize:14 }}>Essayez d'ajuster vos filtres</div>
</div>
)}
</div>
{/* ── MODAL PAIEMENT ── */}
{payModal&&(
<div className="modal-overlay" onClick={()=>{setPayModal(null);setPayStep("choose");}
<div className="modal-box" onClick={e=>e.stopPropagation()}>
{payStep==="choose"&&(
<>
<div style={{ textAlign:"center",marginBottom:24 }}>
<div style={{ fontSize:46,marginBottom:10 }}>{payModal.recharge?" ":payMod
<div style={{ fontFamily:"'Space Grotesk',sans-serif",fontWeight:800,fontSi
{payModal.recharge?"Recharger des crédits":"Débloquer le filtre"}
</div>
<div style={{ color:"#777",fontSize:14 }}>
{payModal.recharge?"Choisissez la quantité de crédits à ajouter":<>Filtre
</div>
</div>
{payModal.recharge&&(
<div style={{ display:"flex",gap:8,marginBottom:20 }}>
{[5,10,20].map(n=>(
<button key={n} className="qty-btn" onClick={()=>setRechargeQty(n)} sty
{n}<br/><span style={{ fontSize:11,fontWeight:400,opacity:.8 }}>crédi
</button>
))}
</div>
)}
<div style={{ background:"#0a0a14",borderRadius:14,padding:"16px 18px",margin
<div style={{ display:"flex",justifyContent:"space-between",marginBottom:8
<span style={{ color:"#777",fontSize:13 }}>Crédits disponibles</span>
<span style={{ fontWeight:700,color:credits>0?"#22c55e":"#ef4444" }}>{cre
</div>
<div style={{ height:1,background:"#222",margin:"8px 0" }} />
<div style={{ display:"flex",justifyContent:"space-between" }}>
<span style={{ fontWeight:700 }}>Total à payer</span>
<span style={{ fontWeight:900,fontSize:22,color:"#f97316" }}>{payModal.re
</div>
</div>
<ApplePayButton onClick={()=>simulatePay("applepay")} />
<div className="divider">ou</div>
<PayPalButton onClick={()=>simulatePay("paypal")} />
<button onClick={()=>{setPayModal(null);setPayStep("choose");}} style={{ widt
Annuler
</button>
</>
)}
{payStep==="applepay"&&(
<div style={{ textAlign:"center",padding:"16px 0" }}>
<div style={{ fontSize:54,marginBottom:14 }}> </div>
<div style={{ fontWeight:700,fontSize:19,marginBottom:6 }}>Apple Pay</div>
<div style={{ color:"#666",fontSize:14,marginBottom:28 }}>Confirmez avec Face
<div className="spinner" />
<div style={{ color:"#555",fontSize:13 }}>En attente de confirmation…</div>
</div>
)}
{payStep==="paypal"&&(
<div style={{ textAlign:"center",padding:"16px 0" }}>
<div style={{ fontSize:54,marginBottom:14 }}> </div>
<div style={{ fontWeight:700,fontSize:19,marginBottom:6 }}>Connexion PayPal</
<div style={{ color:"#666",fontSize:14,marginBottom:28 }}>Redirection vers le
<div className="spinner" />
<div style={{ color:"#555",fontSize:13 }}>Traitement du paiement…</div>
</div>
)}
{payStep==="success"&&(
<div style={{ textAlign:"center",padding:"20px 0" }}>
<div style={{ fontSize:70,marginBottom:14,animation:"pop .5s ease" }}> </div
<div style={{ fontWeight:800,fontSize:22,color:"#22c55e",marginBottom:8 }}>Pa
<div style={{ color:"#666",fontSize:14 }}>
{payModal.recharge?`${rechargeQty} crédits ajoutés à votre compte.`:`Filtre
</div>
</div>
)}
</div>
</div>
)}
{/* ── MODAL DÉTAIL VOITURE ── */}
{selectedCar&&(
<div className="modal-overlay" onClick={()=>setSelectedCar(null)}>
<div className="modal-box" style={{ maxWidth:540 }} onClick={e=>e.stopPropagation()
<div style={{ textAlign:"center",fontSize:74,marginBottom:16,background:"#0a0a14"
<div style={{ display:"flex",justifyContent:"space-between",alignItems:"flex-star
<div>
<h2 style={{ fontFamily:"'Space Grotesk',sans-serif",fontWeight:800,fontSize:
<div style={{ color:"#888" }}>{selectedCar.year} · {selectedCar.location}</di
</div>
<a className="src-badge" href={buildLink(selectedCar.source,selectedCar.brand,s
{selectedCar.source} ↗
</a>
</div>
<div style={{ display:"grid",gridTemplateColumns:"1fr 1fr",gap:10,marginBottom:20
{[
{l:"Carburant",v:selectedCar.fuel,i:" "},
{l:"Boîte",v:selectedCar.transmission,i:" "},
{l:"Kilométrage",v:fmtKm(selectedCar.mileage),i:" "},
{l:"Puissance",v:`${selectedCar.power} ch`,i:" "},
{l:"Portes",v:selectedCar.doors,i:" "},
{l:"Couleur",v:selectedCar.color,i:" "},
{l:"Catégorie",v:selectedCar.category,i:" "},
{l:"Année",v:selectedCar.year,i:" "},
].map(x=>(
<div key={x.l} style={{ background:"#0a0a14",borderRadius:10,padding:"10px 14
<div style={{ fontSize:11,color:"#555",marginBottom:3 }}>{x.i} {x.l}</div>
<div style={{ fontWeight:700 }}>{x.v}</div>
</div>
))}
</div>
<div style={{ fontSize:30,fontWeight:900,color:"#f97316",fontFamily:"'Space Grote
<a href={buildLink(selectedCar.source,selectedCar.brand,selectedCar.model)} targe
<button className="btn-orange" style={{ width:"100%",padding:"15px",fontSize:15
Voir l'annonce sur {selectedCar.source} →
</button>
</a>
</div>
</div>
<button onClick={()=>setSelectedCar(null)} style={{ width:"100%",background:"none
)}
{toast&&<div className={`toast ${toast.type==="success"?"ok":"err"}`}>{toast.msg}</div>
</div>
}
);
