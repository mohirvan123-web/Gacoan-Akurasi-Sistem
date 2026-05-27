<!DOCTYPE html>
<html lang="id">
<head>

<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover"/>

<title>Gacoan ACC System</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<style>
html, body {
  height: 100dvh;
  background: #090d16;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  -webkit-tap-highlight-color: transparent;
  position: fixed;
  width: 100%;
  overflow: hidden;
}

.hidden { display: none !important; }
.no-scrollbar::-webkit-scrollbar { display: none; }
button { user-select: none; }

/* Feedback sentuhan premium yang halus */
.tap-btn {
  transition: all 0.1s cubic-bezier(0.4, 0, 0.2, 1);
}
.tap-btn:active {
  transform: scale(0.95);
  opacity: 0.85;
}

.scroll-smooth-touch {
  -webkit-overflow-scrolling: touch;
}

/* Safe Area untuk poni iOS / lubang kamera Android */
.safe-p {
  padding-top: max(12px, env(safe-area-inset-top));
  padding-bottom: max(12px, env(safe-area-inset-bottom));
  padding-left: max(12px, env(safe-area-inset-left));
  padding-right: max(12px, env(safe-area-inset-right));
}
</style>

</head>
<body class="text-slate-100 selection:bg-orange-500/30">

<div id="role-screen" class="h-full flex flex-col justify-center items-center gap-4 px-6 bg-[#090d16]">
  <div class="text-center mb-6">
    <h1 class="text-4xl font-black tracking-tighter bg-gradient-to-r from-orange-500 to-amber-500 bg-clip-text text-transparent">
      GACOAN ACC
    </h1>
    <p class="text-slate-500 text-xs font-semibold tracking-widest mt-1 uppercase">
      Packing Accuracy System
    </p>
  </div>

  <button onclick="openPacker()" class="tap-btn w-full max-w-xs bg-orange-500 hover:bg-orange-600 py-4 rounded-2xl font-black text-lg shadow-lg shadow-orange-500/10 tracking-wide text-white">
    📦 INTERFAS PACKER
  </button>

  <button onclick="openPresenter()" class="tap-btn w-full max-w-xs bg-blue-600 hover:bg-blue-700 py-4 rounded-2xl font-black text-lg shadow-lg shadow-blue-600/10 tracking-wide text-white">
    🖥 MONITOR MONITOR QC
  </button>
</div>


<div id="packer-screen" class="hidden h-full flex flex-col bg-[#090d16] safe-p gap-3 overflow-hidden">
  
  <div class="bg-slate-900/80 backdrop-blur-md rounded-2xl p-3 flex flex-col gap-2 shrink-0 border border-slate-800/60 shadow-xl">
    <div class="flex gap-2 w-full">
      <input id="customer-name" type="text" placeholder="Nama meja / customer..." 
        class="flex-1 bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-sm outline-none text-white focus:border-orange-500 transition font-medium placeholder:text-slate-600"/>
      <button onclick="resetOrder()" class="tap-btn px-4 bg-slate-800 hover:bg-slate-700 text-slate-300 rounded-xl text-xs font-bold tracking-wider shrink-0">
        RESET
      </button>
    </div>
    
    <div class="flex justify-between items-center pt-1 border-t border-slate-800/50">
      <div class="flex items-center gap-2">
        <span class="text-slate-500 text-xs font-bold tracking-wider">TOTAL ITEM:</span>
        <span id="total-item" class="text-2xl font-black text-orange-400">0</span>
      </div>
      <button id="submit-btn" onclick="submitOrder()" class="tap-btn bg-emerald-500 hover:bg-emerald-600 text-white px-6 py-2 rounded-xl text-sm font-black tracking-wide shadow-md shadow-emerald-500/10">
        DONE
      </button>
    </div>
  </div>

  <div id="menu-grid" class="flex-1 overflow-y-auto no-scrollbar scroll-smooth-touch grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2.5 pb-4">
    </div>

</div>


<div id="presenter-screen" class="hidden h-full bg-[#090d16] safe-p overflow-hidden">
  <div class="h-full flex flex-col gap-3">

    <div class="flex justify-between items-center shrink-0 bg-slate-900/40 p-2 rounded-xl border border-slate-800/30">
      <h1 class="text-xl font-black tracking-tight flex items-center gap-2">
        <span>📋</span> MONITOR QC
      </h1>
      <div id="order-count" class="bg-blue-500/10 text-blue-400 px-3 py-1 rounded-lg text-xs font-black tracking-wider">
        0 ORDER
      </div>
    </div>

    <div id="presenter-list" class="flex-1 overflow-y-auto no-scrollbar scroll-smooth-touch space-y-2.5 pb-4">
      <div class="text-center text-slate-600 pt-40 text-sm font-medium tracking-wide">
        Belum ada antrian pesanan.
      </div>
    </div>

  </div>
</div>


<script>
/* =========================
FIREBASE CONFIGURATION
========================= */
const firebaseConfig = {
  apiKey: "AIzaSyCbfeWmArHKHXWxhr5p9c756vl5KrJ9pUE",
  authDomain: "akurasi-sistem.firebaseapp.com",
  databaseURL: "https://akurasi-sistem-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "akurasi-sistem",
  storageBucket: "akurasi-sistem.firebasestorage.app",
  messagingSenderId: "526061479850",
  appId: "1:526061479850:web:90462c55e0f86ab3366dd3",
  measurementId: "G-2NYP4V3E85"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();
const ordersRef = db.ref("gacoan_orders");

/* =========================
DATA MASTER MENU
========================= */
const menuList = [
  { id: "udang-keju", nama: "Udang Keju", color: "bg-orange-500" },
  { id: "udang-rambutan", nama: "Udang Rambutan", color: "bg-red-500" },
  { id: "siomay", nama: "Siomay", color: "bg-amber-500" },
  { id: "lumpia-udang", nama: "Lumpia Udang", color: "bg-blue-500" }
];

/* =========================
APPLICATION STATE
========================= */
let items = {};
let submitting = false;

/* =========================
NAVIGATION CONTROLLER
========================= */
function openPacker(){
  document.getElementById("role-screen").classList.add("hidden");
  document.getElementById("packer-screen").classList.remove("hidden");
  renderMenu();
}

function openPresenter(){
  document.getElementById("role-screen").classList.add("hidden");
  document.getElementById("presenter-screen").classList.remove("hidden");
  listenOrders();
}

/* =========================
AUDIO FEEDBACK
========================= */
function tapSound(){
  const audio = new Audio("https://cdn.pixabay.com/download/audio/2022/03/15/audio_c8c8a73467.mp3?filename=click-124467.mp3");
  audio.volume = 0.12;
  audio.play().catch(err => console.log("Audio play blocked/error"));
}

/* =========================
RENDER PACKER ITEMS
========================= */
function renderMenu(){
  const container = document.getElementById("menu-grid");
  let html = "";

  menuList.forEach(menu => {
    const qty = items[menu.id]?.qty || 0;
    const hasQty = qty > 0;

    html += `
      <div class="bg-slate-900 rounded-2xl p-3.5 flex flex-col justify-between border ${hasQty ? 'border-orange-500/40 shadow-lg' : 'border-slate-800/70'} transition duration-150 min-h-[145px]">
        <div>
          <div class="flex items-center gap-1.5 mb-1.5">
            <span class="w-1.5 h-1.5 rounded-full ${menu.color}"></span>
            <span class="text-[10px] uppercase font-black text-slate-500 tracking-wider">DIMSUM</span>
          </div>
          <h3 class="font-black text-lg leading-snug text-slate-100 tracking-tight">
            ${menu.nama}
          </h3>
        </div>

        <div class="flex items-center justify-between gap-2 mt-2">
          <button onclick="minusQty('${menu.id}')" 
            class="tap-btn w-10 h-10 rounded-xl bg-slate-800 text-xl font-bold flex items-center justify-center text-rose-400 active:bg-rose-500/10">
            −
          </button>
          
          <div class="text-2xl font-black ${hasQty ? 'text-orange-400 scale-105' : 'text-slate-600'} transition-all min-w-[32px] text-center">
            ${qty}
          </div>
          
          <button onclick="plusQty('${menu.id}','${menu.nama}')" 
            class="tap-btn w-10 h-10 rounded-xl bg-slate-800 text-xl font-bold flex items-center justify-center text-emerald-400 active:bg-emerald-500/10">
            +
          </button>
        </div>
      </div>
    `;
  });

  container.innerHTML = html;
  updateTotal();
}

function plusQty(id, nama){
  tapSound();
  if(items[id]) { items[id].qty += 1; } 
  else { items[id] = { id, nama, qty: 1 }; }
  renderMenu();
}

function minusQty(id){
  tapSound();
  if(!items[id]) return;
  items[id].qty -= 1;
  if(items[id].qty <= 0) { delete items[id]; }
  renderMenu();
}

function updateTotal(){
  let total = 0;
  Object.values(items).forEach(item => { total += item.qty; });
  document.getElementById("total-item").innerText = total;
}

function resetOrder(){
  tapSound();
  items = {};
  document.getElementById("customer-name").value = "";
  renderMenu();
}

/* =========================
SUBMIT TO DATABASE
========================= */
async function submitOrder(){
  if(submitting) return;
  const customer = document.getElementById("customer-name").value.trim();

  if(!customer){ alert("Masukkan nama customer / nomor meja!"); return; }
  if(Object.keys(items).length === 0){ alert("Belum ada item yang diinput!"); return; }

  tapSound();
  submitting = true;
  
  const btn = document.getElementById("submit-btn");
  btn.disabled = true;
  btn.innerText = "SAVING...";

  const orderId = Date.now();

  try {
    await ordersRef.child(orderId).set({
      customer,
      timestamp: Date.now(),
      status: "waiting",
      items
    });
    resetOrder();
  } catch(err) {
    console.error(err);
    alert("Koneksi gagal, coba lagi!");
  }

  btn.disabled = false;
  btn.innerText = "DONE";
  submitting = false;
}

/* =========================
LIVE DATABASE MONITOR (PRESENTER)
========================= */
function listenOrders(){
  ordersRef.on("value", (snapshot) => {
    const container = document.getElementById("presenter-list");

    if(!snapshot.exists()){
      container.innerHTML = `
        <div class="text-center text-slate-600 pt-40 text-sm font-medium tracking-wide">
          Belum ada antrian pesanan.
        </div>
      `;
      document.getElementById("order-count").innerText = "0 ORDER";
      return;
    }

    const data = snapshot.val();
    const keys = Object.keys(data).reverse();
    document.getElementById("order-count").innerText = `${keys.length} ORDER`;

    let html = "";

    keys.forEach(orderId => {
      const order = data[orderId];
      let itemHtml = "";

      Object.values(order.items).forEach(item => {
        itemHtml += `
          <div class="bg-slate-950/60 rounded-xl p-2.5 flex justify-between items-center border border-slate-900">
            <span class="font-bold text-base text-slate-200 tracking-tight">${item.nama}</span>
            <span class="text-xl font-black text-orange-400">x${item.qty}</span>
          </div>
        `;
      });

      html += `
        <div class="bg-slate-900 rounded-2xl p-4 border border-slate-800/80 shadow-md">
          <div class="flex justify-between items-start mb-3">
            <div>
              <h2 class="font-black text-lg text-white tracking-tight">👤 ${order.customer}</h2>
              <p class="text-[11px] text-slate-500 font-medium mt-0.5">
                Jam: ${new Date(order.timestamp).toLocaleTimeString('id-ID', {hour: '2-digit', minute:'2-digit'})}
              </p>
            </div>
            <button onclick="finishOrder('${orderId}')"
              class="tap-btn bg-emerald-500 hover:bg-emerald-600 text-white px-4 py-2 rounded-xl text-xs font-black tracking-wider">
              CLEAR
            </button>
          </div>
          <div class="grid grid-cols-1 gap-1.5">
            ${itemHtml}
          </div>
        </div>
      `;
    });

    container.innerHTML = html;
  });
}

function finishOrder(orderId){
  tapSound();
  ordersRef.child(orderId).remove();
}
</script>

</body>
</html>
