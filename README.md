<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover"/>
<title>Gacoan ACC System</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<style>
  /* ---- Reset & base ---- */
  *, *::before, *::after { box-sizing: border-box; }

  :root {
    --orange: #f97316;
    --amber:  #f59e0b;
    --emerald:#10b981;
    --blue:   #3b82f6;
    --bg:     #090d16;
    --surface:#111827;
    --border: #1f2937;
    --text:   #f1f5f9;
    --muted:  #64748b;
    --safe-top:    env(safe-area-inset-top,    0px);
    --safe-bottom: env(safe-area-inset-bottom, 0px);
    --safe-left:   env(safe-area-inset-left,   0px);
    --safe-right:  env(safe-area-inset-right,  0px);
  }

  html {
    /* Penting: pakai height 100% bukan 100dvh di root, biar iOS tidak kacau */
    height: 100%;
    background: var(--bg);
  }

  body {
    margin: 0;
    min-height: 100%;
    /* TIDAK menggunakan position:fixed — ini yang bikin scroll mati */
    background: var(--bg);
    color: var(--text);
    font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI", Roboto, sans-serif;
    -webkit-tap-highlight-color: transparent;
    -webkit-text-size-adjust: 100%;
    overscroll-behavior: none;
  }

  /* ---- Screen management ---- */
  .screen {
    display: none;
    min-height: 100dvh;              /* dynamic viewport height — iOS 15.4+ */
    min-height: -webkit-fill-available; /* fallback older Safari */
  }
  .screen.active { display: flex; flex-direction: column; }

  /* ---- Safe-area padding utility ---- */
  .pt-safe { padding-top: max(16px, env(safe-area-inset-top)); }
  .pb-safe { padding-bottom: max(16px, env(safe-area-inset-bottom)); }
  .px-safe {
    padding-left:  max(16px, env(safe-area-inset-left));
    padding-right: max(16px, env(safe-area-inset-right));
  }

  /* ---- Scrollable container — kunci utama fix scroll ---- */
  .scroll-area {
    flex: 1 1 0;          /* ambil sisa ruang */
    overflow-y: auto;
    -webkit-overflow-scrolling: touch; /* momentum scroll iOS */
    overscroll-behavior-y: contain;
  }

  /* ---- Tap feedback ---- */
  .tap {
    -webkit-user-select: none; user-select: none;
    cursor: pointer;
    transition: transform .1s ease, opacity .1s ease;
  }
  .tap:active { transform: scale(.94); opacity: .82; }

  /* ---- No scrollbar ---- */
  .scroll-area::-webkit-scrollbar { display: none; }
  .scroll-area { scrollbar-width: none; }

  /* ---- Menu card ---- */
  .menu-card {
    background: var(--surface);
    border: 1.5px solid var(--border);
    border-radius: 18px;
    padding: 14px;
    display: flex;
    flex-direction: column;
    gap: 10px;
    transition: border-color .15s, box-shadow .15s;
    min-height: 140px;
  }
  .menu-card.active {
    border-color: rgba(249,115,22,.5);
    box-shadow: 0 0 0 1px rgba(249,115,22,.15), 0 8px 20px rgba(249,115,22,.08);
  }

  /* ---- Badge pill ---- */
  .badge {
    display: inline-flex; align-items: center;
    padding: 2px 10px;
    border-radius: 999px;
    font-size: 10px; font-weight: 800;
    letter-spacing: .07em; text-transform: uppercase;
  }

  /* ---- Qty counter ---- */
  .qty-btn {
    width: 40px; height: 40px;
    border-radius: 12px;
    display: flex; align-items: center; justify-content: center;
    font-size: 20px; font-weight: 800;
    background: var(--border);
    border: none; outline: none;
  }

  /* ---- Order card (monitor) ---- */
  .order-card {
    background: var(--surface);
    border: 1.5px solid var(--border);
    border-radius: 20px;
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  /* ---- Item row inside order card ---- */
  .item-row {
    background: rgba(9,13,22,.5);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 10px 14px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  /* ---- Spinner ---- */
  .spinner {
    width: 18px; height: 18px;
    border: 2px solid rgba(255,255,255,.2);
    border-top-color: #fff;
    border-radius: 50%;
    animation: spin .7s linear infinite;
    display: inline-block; vertical-align: middle; margin-right: 6px;
  }
  @keyframes spin { to { transform: rotate(360deg); } }

  /* ---- Toast ---- */
  #toast {
    position: fixed;
    bottom: calc(20px + env(safe-area-inset-bottom));
    left: 50%; transform: translateX(-50%) translateY(80px);
    background: #1a2332;
    border: 1px solid var(--border);
    color: var(--text);
    padding: 12px 22px;
    border-radius: 999px;
    font-size: 13px; font-weight: 700;
    box-shadow: 0 8px 32px rgba(0,0,0,.5);
    z-index: 9999;
    transition: transform .3s cubic-bezier(.22,1,.36,1), opacity .3s;
    opacity: 0; pointer-events: none;
    white-space: nowrap;
  }
  #toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }

  /* ---- Responsive grid ---- */
  .menu-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
  }
  @media (min-width: 480px)  { .menu-grid { grid-template-columns: repeat(3, 1fr); } }
  @media (min-width: 720px)  { .menu-grid { grid-template-columns: repeat(4, 1fr); } }

  /* ---- Header sticky (packer & presenter) ---- */
  .sticky-header {
    position: sticky; top: 0; z-index: 10;
    background: var(--bg);
  }

  /* ---- Input ---- */
  input[type=text] {
    background: #0d1421;
    border: 1.5px solid var(--border);
    border-radius: 14px;
    padding: 11px 16px;
    color: var(--text);
    font-size: 15px; font-weight: 600;
    outline: none;
    width: 100%;
    transition: border-color .15s;
    -webkit-appearance: none;
    appearance: none;
  }
  input[type=text]::placeholder { color: var(--muted); font-weight: 500; }
  input[type=text]:focus { border-color: var(--orange); }
</style>
</head>
<body>

<!-- ===================== ROLE SCREEN ===================== -->
<div id="role-screen" class="screen active" style="justify-content:center;align-items:center;padding:24px;">
  <div style="width:100%;max-width:340px;display:flex;flex-direction:column;align-items:center;gap:20px;">

    <!-- Logo -->
    <div style="text-align:center;margin-bottom:8px;">
      <div style="font-size:42px;font-weight:900;letter-spacing:-.03em;
                  background:linear-gradient(135deg,#f97316,#f59e0b);
                  -webkit-background-clip:text;-webkit-text-fill-color:transparent;
                  background-clip:text;line-height:1.1;">
        GACOAN
      </div>
      <div style="font-size:11px;font-weight:800;letter-spacing:.2em;color:var(--muted);margin-top:4px;">
        PACKING ACCURACY SYSTEM
      </div>
    </div>

    <!-- Buttons -->
    <button onclick="openPacker()" class="tap"
      style="width:100%;padding:18px;border-radius:18px;border:none;
             background:linear-gradient(135deg,#f97316,#ea580c);
             color:#fff;font-size:17px;font-weight:900;letter-spacing:.03em;
             box-shadow:0 12px 32px rgba(249,115,22,.25);">
      📦&nbsp; INTERFAS PACKER
    </button>

    <button onclick="openPresenter()" class="tap"
      style="width:100%;padding:18px;border-radius:18px;border:none;
             background:linear-gradient(135deg,#3b82f6,#1d4ed8);
             color:#fff;font-size:17px;font-weight:900;letter-spacing:.03em;
             box-shadow:0 12px 32px rgba(59,130,246,.2);">
      🖥&nbsp; MONITOR QC
    </button>

    <div style="font-size:11px;color:var(--muted);text-align:center;margin-top:4px;">
      Pilih peran sesuai posisi tim
    </div>
  </div>
</div>


<!-- ===================== PACKER SCREEN ===================== -->
<div id="packer-screen" class="screen">

  <!-- Sticky header -->
  <div class="sticky-header pt-safe px-safe">
    <div style="padding:12px 0;display:flex;flex-direction:column;gap:10px;
                border-bottom:1px solid var(--border);margin-bottom:4px;">

      <!-- Row: nama customer + reset -->
      <div style="display:flex;gap:10px;align-items:center;">
        <input id="customer-name" type="text" placeholder="Nama meja / customer…"
               inputmode="text" autocomplete="off" style="flex:1;"/>
        <button onclick="resetOrder()" class="tap"
          style="padding:11px 14px;border-radius:14px;border:none;
                 background:#1f2937;color:#94a3b8;
                 font-size:12px;font-weight:800;letter-spacing:.06em;white-space:nowrap;flex-shrink:0;">
          RESET
        </button>
      </div>

      <!-- Row: total + submit -->
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <div style="display:flex;align-items:center;gap:8px;">
          <span style="font-size:11px;font-weight:800;color:var(--muted);letter-spacing:.08em;">TOTAL ITEM</span>
          <span id="total-item"
            style="font-size:30px;font-weight:900;color:var(--orange);line-height:1;">0</span>
        </div>

        <button id="submit-btn" onclick="submitOrder()" class="tap"
          style="padding:12px 26px;border-radius:14px;border:none;
                 background:linear-gradient(135deg,#10b981,#059669);
                 color:#fff;font-size:14px;font-weight:900;letter-spacing:.06em;
                 box-shadow:0 6px 20px rgba(16,185,129,.2);">
          ✓&nbsp;DONE
        </button>
      </div>
    </div>
  </div>

  <!-- Scrollable menu grid -->
  <div class="scroll-area px-safe pb-safe" style="padding-top:12px;">
    <div id="menu-grid" class="menu-grid" style="padding-bottom:32px;">
      <!-- Diisi JS -->
    </div>
  </div>
</div>


<!-- ===================== PRESENTER SCREEN ===================== -->
<div id="presenter-screen" class="screen">

  <!-- Sticky header -->
  <div class="sticky-header pt-safe px-safe">
    <div style="padding:12px 0 10px;display:flex;justify-content:space-between;
                align-items:center;border-bottom:1px solid var(--border);margin-bottom:2px;">
      <div style="display:flex;align-items:center;gap:8px;">
        <span style="font-size:20px;">📋</span>
        <span style="font-size:18px;font-weight:900;letter-spacing:-.01em;">MONITOR QC</span>
      </div>
      <div id="order-count" class="badge"
        style="background:rgba(59,130,246,.12);color:#60a5fa;">
        0 ORDER
      </div>
    </div>
  </div>

  <!-- Scrollable order list -->
  <div class="scroll-area px-safe pb-safe" style="padding-top:12px;">
    <div id="presenter-list" style="display:flex;flex-direction:column;gap:12px;padding-bottom:32px;">
      <div id="empty-msg"
        style="text-align:center;color:var(--muted);padding:80px 0;font-size:14px;font-weight:600;">
        Belum ada antrian pesanan.
      </div>
    </div>
  </div>
</div>


<!-- Toast notification -->
<div id="toast"></div>


<!-- ===================== JAVASCRIPT ===================== -->
<script>
/* ---- Firebase ---- */
const firebaseConfig = {
  apiKey: "AIzaSyCbfeWmArHKHXWxhr5p9c756vl5KrJ9pUE",
  authDomain: "akurasi-sistem.firebaseapp.com",
  databaseURL: "https://akurasi-sistem-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "akurasi-sistem",
  storageBucket: "akurasi-sistem.firebasestorage.app",
  messagingSenderId: "526061479850",
  appId: "1:526061479850:web:90462c55e0f86ab3366dd3"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();
const ordersRef = db.ref("gacoan_orders");

/* ---- Menu master ---- */
const menuList = [
  { id: "udang-keju",    nama: "Udang Keju",    dot:"#f97316" },
  { id: "udang-rambutan",nama: "Udang Rambutan", dot:"#ef4444" },
  { id: "siomay",        nama: "Siomay",         dot:"#f59e0b" },
  { id: "lumpia-udang",  nama: "Lumpia Udang",   dot:"#3b82f6" }
];

/* ---- State ---- */
let items = {};
let submitting = false;

/* ---- Toast helper ---- */
let toastTimer;
function showToast(msg, emoji = ""){
  const el = document.getElementById("toast");
  el.innerHTML = emoji ? `${emoji} ${msg}` : msg;
  el.classList.add("show");
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => el.classList.remove("show"), 2200);
}

/* ---- Audio feedback (opsional, tidak blocking) ---- */
function tapSound(){
  try {
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.connect(gain); gain.connect(ctx.destination);
    osc.type = "sine";
    osc.frequency.setValueAtTime(880, ctx.currentTime);
    gain.gain.setValueAtTime(.06, ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(.001, ctx.currentTime + .12);
    osc.start(ctx.currentTime);
    osc.stop(ctx.currentTime + .12);
  } catch(e){}
}

/* ---- Navigation ---- */
function showScreen(id){
  document.querySelectorAll(".screen").forEach(s => s.classList.remove("active"));
  document.getElementById(id).classList.add("active");
  // Scroll to top setiap pindah screen
  const scrollArea = document.querySelector(`#${id} .scroll-area`);
  if(scrollArea) scrollArea.scrollTop = 0;
}

function openPacker(){
  showScreen("packer-screen");
  renderMenu();
}

function openPresenter(){
  showScreen("presenter-screen");
  listenOrders();
}

/* ---- Render menu cards ---- */
function renderMenu(){
  const container = document.getElementById("menu-grid");
  let html = "";

  menuList.forEach(menu => {
    const qty  = items[menu.id]?.qty || 0;
    const active = qty > 0;
    html += `
      <div class="menu-card ${active ? "active" : ""}">
        <!-- Label -->
        <div style="display:flex;align-items:center;gap:6px;">
          <span style="width:8px;height:8px;border-radius:50%;background:${menu.dot};flex-shrink:0;"></span>
          <span style="font-size:9px;font-weight:800;color:var(--muted);letter-spacing:.1em;text-transform:uppercase;">DIMSUM</span>
        </div>
        <!-- Nama -->
        <div style="font-size:16px;font-weight:900;letter-spacing:-.01em;line-height:1.25;flex:1;">
          ${menu.nama}
        </div>
        <!-- Counter -->
        <div style="display:flex;align-items:center;justify-content:space-between;gap:8px;">
          <button class="qty-btn tap" onclick="minusQty('${menu.id}')"
            style="color:#f87171;">−</button>
          <span style="font-size:28px;font-weight:900;min-width:36px;text-align:center;
                       color:${active ? "var(--orange)" : "var(--muted)"};">
            ${qty}
          </span>
          <button class="qty-btn tap" onclick="plusQty('${menu.id}','${menu.nama}')"
            style="color:#34d399;">+</button>
        </div>
      </div>
    `;
  });

  container.innerHTML = html;
  updateTotal();
}

function plusQty(id, nama){
  tapSound();
  items[id] ? items[id].qty++ : (items[id] = { id, nama, qty: 1 });
  renderMenu();
}

function minusQty(id){
  tapSound();
  if(!items[id]) return;
  items[id].qty--;
  if(items[id].qty <= 0) delete items[id];
  renderMenu();
}

function updateTotal(){
  const total = Object.values(items).reduce((s, i) => s + i.qty, 0);
  document.getElementById("total-item").textContent = total;
}

function resetOrder(){
  tapSound();
  items = {};
  document.getElementById("customer-name").value = "";
  renderMenu();
}

/* ---- Submit ---- */
async function submitOrder(){
  if(submitting) return;
  const customer = document.getElementById("customer-name").value.trim();
  if(!customer){ showToast("Masukkan nama meja / customer!", "⚠️"); return; }
  if(Object.keys(items).length === 0){ showToast("Belum ada item yang dipilih!", "⚠️"); return; }

  tapSound();
  submitting = true;

  const btn = document.getElementById("submit-btn");
  btn.disabled = true;
  btn.innerHTML = `<span class="spinner"></span>SAVING…`;

  try {
    await ordersRef.child(Date.now()).set({
      customer,
      timestamp: Date.now(),
      status: "waiting",
      items
    });
    showToast(`Order "${customer}" terkirim!`, "✅");
    resetOrder();
  } catch(err){
    console.error(err);
    showToast("Koneksi gagal, coba lagi!", "❌");
  }

  btn.disabled = false;
  btn.innerHTML = "✓&nbsp;DONE";
  submitting = false;
}

/* ---- Live monitor ---- */
function listenOrders(){
  ordersRef.on("value", snapshot => {
    const list = document.getElementById("presenter-list");
    const countEl = document.getElementById("order-count");

    if(!snapshot.exists()){
      list.innerHTML = `
        <div id="empty-msg"
          style="text-align:center;color:var(--muted);padding:80px 0;font-size:14px;font-weight:600;">
          Belum ada antrian pesanan.
        </div>`;
      countEl.textContent = "0 ORDER";
      return;
    }

    const data = snapshot.val();
    const keys = Object.keys(data).reverse();
    countEl.textContent = `${keys.length} ORDER`;

    let html = "";
    keys.forEach(orderId => {
      const order = data[orderId];
      let itemHtml = "";
      Object.values(order.items).forEach(item => {
        itemHtml += `
          <div class="item-row">
            <span style="font-weight:700;font-size:15px;">${item.nama}</span>
            <span style="font-size:20px;font-weight:900;color:var(--orange);">×${item.qty}</span>
          </div>`;
      });

      const jam = new Date(order.timestamp).toLocaleTimeString("id-ID", { hour:"2-digit", minute:"2-digit" });

      html += `
        <div class="order-card">
          <div style="display:flex;justify-content:space-between;align-items:flex-start;">
            <div>
              <div style="font-size:17px;font-weight:900;letter-spacing:-.01em;">👤 ${order.customer}</div>
              <div style="font-size:11px;color:var(--muted);font-weight:600;margin-top:3px;">Jam ${jam}</div>
            </div>
            <button onclick="finishOrder('${orderId}')" class="tap"
              style="padding:9px 18px;border-radius:12px;border:none;
                     background:linear-gradient(135deg,#10b981,#059669);
                     color:#fff;font-size:12px;font-weight:900;letter-spacing:.06em;
                     flex-shrink:0;">
              CLEAR
            </button>
          </div>
          <div style="display:flex;flex-direction:column;gap:8px;">
            ${itemHtml}
          </div>
        </div>`;
    });

    list.innerHTML = html;
  });
}

function finishOrder(orderId){
  tapSound();
  ordersRef.child(orderId).remove();
}
</script>
</body>
</html>
