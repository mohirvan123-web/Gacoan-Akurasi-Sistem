<html lang="id">
<head>

<meta charset="UTF-8"/>

<meta
name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>

<title>Gacoan ACC System</title>

<script src="https://cdn.tailwindcss.com"></script>

<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<script src="https://unpkg.com/@zxing/library@latest"></script>

<style>

html,
body{
height:100%;
overflow:hidden;
background:#020617;
font-family:Arial,sans-serif;
}

.hidden{
display:none;
}

.no-scrollbar::-webkit-scrollbar{
display:none;
}

#scanner{
width:100%;
height:100%;
object-fit:cover;
}

.scan-line{
animation:scanline 2s linear infinite;
}

@keyframes scanline{

0%{
transform:translateY(-120px);
}

100%{
transform:translateY(120px);
}

}

button{
-webkit-tap-highlight-color:transparent;
}

</style>

</head>

<body class="h-screen overflow-hidden text-white">

<!-- ROLE SCREEN -->

<div
id="role-screen"
class="h-full flex flex-col justify-center items-center gap-5 px-6 bg-slate-950">

<div class="text-center">

<h1 class="text-4xl font-black">
GACOAN ACC
</h1>

<p class="text-slate-400 mt-2">
Packing Accuracy System
</p>

</div>

<button
onclick="openPacker()"
class="w-full max-w-xs bg-orange-500 py-5 rounded-3xl font-black text-2xl active:scale-95 transition shadow-2xl">

📦 PACKER

</button>

<button
onclick="openPresenter()"
class="w-full max-w-xs bg-blue-600 py-5 rounded-3xl font-black text-2xl active:scale-95 transition shadow-2xl">

🖥 PRESENTER

</button>

</div>

<!-- PACKER -->

<div
id="packer-screen"
class="hidden h-full flex flex-col p-3 gap-3 bg-slate-950">

<!-- HEADER -->

<div class="flex gap-2 h-14">

<input
id="customer-name"
type="text"
placeholder="Nama customer / meja"
class="flex-1 rounded-2xl bg-slate-800 border border-slate-700 px-4 outline-none text-sm"/>

<button
onclick="resetOrder()"
class="w-24 rounded-2xl bg-red-500 font-bold active:scale-95 transition">

RESET

</button>

</div>

<!-- MAIN -->

<div class="flex-1 flex gap-3 overflow-hidden">

<!-- CAMERA -->

<div class="w-[48%] relative rounded-3xl overflow-hidden bg-black border border-slate-700">

<video
id="scanner"
autoplay
muted
playsinline
></video>

<!-- OVERLAY -->

<div class="absolute inset-0 pointer-events-none flex items-center justify-center">

<div class="w-48 h-36 border-4 border-orange-500 rounded-2xl relative overflow-hidden">

<div class="absolute top-0 left-0 w-full h-1 bg-red-500 scan-line"></div>

</div>

</div>

<!-- STATUS -->

<div
id="camera-status"
class="absolute bottom-3 left-1/2 -translate-x-1/2 bg-black/70 px-3 py-1 rounded-full text-xs">

Membuka kamera...

</div>

</div>

<!-- RIGHT -->

<div class="flex-1 flex flex-col overflow-hidden">

<!-- TOP -->

<div class="bg-slate-800 rounded-3xl p-4 mb-3">

<div class="flex justify-between items-center">

<div>

<p class="text-xs text-slate-400">
TOTAL ITEM
</p>

<h1
id="total-item"
class="text-4xl font-black text-orange-400">
0
</h1>

</div>

<button
id="submit-btn"
onclick="submitOrder()"
class="bg-emerald-500 px-5 py-4 rounded-2xl font-black text-lg active:scale-95 transition">

DONE

</button>

</div>

</div>

<!-- MANUAL -->

<div class="flex gap-2 mb-3 h-12">

<input
id="manual-input"
type="text"
placeholder="Input barcode manual"
class="flex-1 rounded-2xl bg-slate-800 border border-slate-700 px-4 outline-none text-sm"/>

<button
onclick="manualAdd()"
class="w-16 rounded-2xl bg-orange-500 font-black text-xl active:scale-95 transition">

+

</button>

</div>

<!-- ITEM LIST -->

<div
id="item-list"
class="flex-1 overflow-y-auto no-scrollbar space-y-2">

<div class="text-center text-slate-500 pt-24 text-sm">
Belum ada item
</div>

</div>

</div>

</div>

</div>

<!-- PRESENTER -->

<div
id="presenter-screen"
class="hidden h-full bg-slate-950 p-3 overflow-hidden">

<div class="h-full flex flex-col">

<!-- HEADER -->

<div class="flex justify-between items-center mb-3">

<h1 class="text-3xl font-black">
📋 MONITOR QC
</h1>

<div
id="order-count"
class="bg-blue-500/20 text-blue-400 px-4 py-2 rounded-2xl font-bold">

0 ORDER

</div>

</div>

<!-- ORDER LIST -->

<div
id="presenter-list"
class="flex-1 overflow-y-auto no-scrollbar space-y-3">

<div class="text-center text-slate-500 pt-40">
Belum ada pesanan
</div>

</div>

</div>

</div>

<script>

/* =========================
FIREBASE
========================= */

const firebaseConfig = {

apiKey: "ISI_APIKEY_FIREBASE",
authDomain: "ISI_DOMAIN_FIREBASE",
databaseURL: "ISI_DATABASE_URL",
projectId: "ISI_PROJECT_ID",
storageBucket: "ISI_STORAGE_BUCKET",
messagingSenderId: "ISI_SENDER_ID",
appId: "ISI_APP_ID"

};

firebase.initializeApp(firebaseConfig);

const db =
firebase.database();

const ordersRef =
db.ref("gacoan_orders");

/* =========================
SKU DATABASE
========================= */

const skuDB = {

"8991000012345":"Mie Gacoan Lv 1",
"8991000056789":"Mie Hompimpa Lv 3",
"8991000098765":"Udang Keju",
"8992000011111":"Udang Rambutan",
"8993000022222":"Es Gobak Sodor"

};

/* =========================
STATE
========================= */

let codeReader = null;

let items = {};

let lastScannedCode = "";
let lastScannedTime = 0;

let submitting = false;

/* =========================
ROLE
========================= */

function openPacker(){

document
.getElementById("role-screen")
.classList.add("hidden");

document
.getElementById("packer-screen")
.classList.remove("hidden");

startScanner();

}

function openPresenter(){

document
.getElementById("role-screen")
.classList.add("hidden");

document
.getElementById("presenter-screen")
.classList.remove("hidden");

listenOrders();

}

/* =========================
START SCANNER
========================= */

async function startScanner(){

const status =
document.getElementById("camera-status");

try{

status.innerText =
"Meminta izin kamera...";

const hints = new Map();

hints.set(
ZXing.DecodeHintType.POSSIBLE_FORMATS,
[
ZXing.BarcodeFormat.EAN_13,
ZXing.BarcodeFormat.CODE_128
]
);

codeReader =
new ZXing.BrowserMultiFormatReader(hints);

await codeReader.decodeFromConstraints(
{
video:{
facingMode:"environment"
}
},
"scanner",
(result, err)=>{

if(result){

const barcode =
result.text;

const now =
Date.now();

/* ANTI DOUBLE SCAN */

if(
barcode === lastScannedCode &&
(now - lastScannedTime) < 1200
){
return;
}

lastScannedCode = barcode;
lastScannedTime = now;

addItem(barcode);

if(navigator.vibrate){

navigator.vibrate(50);

}

}

}
);

status.innerText =
"Scanner aktif";

}catch(err){

console.error(err);

status.innerText =
"Gagal membuka kamera";

alert(
"Kamera gagal dibuka.\n\nPastikan:\n1. HTTPS aktif\n2. Izin kamera allow\n3. Gunakan Chrome/Safari terbaru"
);

}

}

/* =========================
ADD ITEM
========================= */

function addItem(barcode){

const nama =
skuDB[barcode] ||
`SKU ${barcode}`;

if(items[barcode]){

items[barcode].qty += 1;

}else{

items[barcode] = {

barcode,
nama,
qty:1

};

}

renderItems();

}

/* =========================
MANUAL INPUT
========================= */

function manualAdd(){

const input =
document.getElementById("manual-input");

const barcode =
input.value.trim();

if(!barcode) return;

addItem(barcode);

input.value = "";

}

/* =========================
PLUS QTY
========================= */

function plusQty(barcode){

items[barcode].qty += 1;

renderItems();

}

/* =========================
MINUS QTY
========================= */

function minusQty(barcode){

items[barcode].qty -= 1;

if(items[barcode].qty <= 0){

delete items[barcode];

}

renderItems();

}

/* =========================
RENDER ITEMS
========================= */

function renderItems(){

const container =
document.getElementById("item-list");

const keys =
Object.keys(items);

let total = 0;

keys.forEach(key=>{

total += items[key].qty;

});

document
.getElementById("total-item")
.innerText = total;

if(keys.length === 0){

container.innerHTML = `

<div class="text-center text-slate-500 pt-24 text-sm">
Belum ada item
</div>

`;

return;

}

let html = "";

keys.forEach(barcode=>{

const item =
items[barcode];

html += `

<div class="bg-slate-800 rounded-2xl p-3">

<div class="flex justify-between items-center gap-2">

<div class="flex-1 min-w-0">

<h3 class="font-bold text-sm truncate">
${item.nama}
</h3>

<p class="text-[10px] text-slate-500 mt-1 truncate">
${barcode}
</p>

</div>

<div class="flex items-center gap-2">

<button
onclick="minusQty('${barcode}')"
class="w-10 h-10 rounded-xl bg-red-500 text-xl font-black active:scale-90 transition">

−

</button>

<div class="w-8 text-center text-xl font-black">
${item.qty}
</div>

<button
onclick="plusQty('${barcode}')"
class="w-10 h-10 rounded-xl bg-emerald-500 text-xl font-black active:scale-90 transition">

+

</button>

</div>

</div>

</div>

`;

});

container.innerHTML = html;

}

/* =========================
RESET
========================= */

function resetOrder(){

items = {};

document
.getElementById("customer-name")
.value = "";

renderItems();

}

/* =========================
SUBMIT ORDER
========================= */

async function submitOrder(){

if(submitting) return;

const customer =
document
.getElementById("customer-name")
.value
.trim();

if(!customer){

alert(
"Masukkan nama customer"
);

return;

}

if(
Object.keys(items).length === 0
){

alert(
"Belum ada item"
);

return;

}

submitting = true;

const btn =
document.getElementById("submit-btn");

btn.disabled = true;

btn.innerText = "LOADING";

const orderId =
Date.now();

try{

await ordersRef
.child(orderId)
.set({

customer,
timestamp:Date.now(),
items

});

resetOrder();

}catch(err){

alert(
"Gagal submit order"
);

console.error(err);

}

btn.disabled = false;

btn.innerText = "DONE";

submitting = false;

}

/* =========================
LISTEN ORDERS
========================= */

function listenOrders(){

ordersRef.on(
"value",
(snapshot)=>{

const container =
document.getElementById(
"presenter-list"
);

if(!snapshot.exists()){

container.innerHTML = `

<div class="text-center text-slate-500 pt-40">
Belum ada pesanan
</div>

`;

document
.getElementById("order-count")
.innerText = "0 ORDER";

return;

}

const data =
snapshot.val();

const keys =
Object.keys(data)
.reverse();

document
.getElementById("order-count")
.innerText =
`${keys.length} ORDER`;

let html = "";

keys.forEach(orderId=>{

const order =
data[orderId];

let itemHtml = "";

Object.values(order.items)
.forEach(item=>{

itemHtml += `

<div class="bg-slate-700 rounded-2xl px-3 py-2 flex justify-between items-center">

<div>

<h3 class="font-bold text-sm">
${item.nama}
</h3>

<p class="text-[10px] text-slate-400">
${item.barcode}
</p>

</div>

<div class="text-orange-400 font-black text-2xl">
x${item.qty}
</div>

</div>

`;

});

html += `

<div class="bg-slate-800 rounded-3xl p-4">

<div class="flex justify-between items-start mb-3">

<div>

<h2 class="font-black text-lg">
👤 ${order.customer}
</h2>

<p class="text-xs text-slate-400 mt-1">
${new Date(order.timestamp).toLocaleTimeString('id-ID')}
</p>

</div>

<button
onclick="finishOrder('${orderId}')"
class="bg-emerald-500 px-4 py-2 rounded-2xl font-black active:scale-95 transition">

DONE

</button>

</div>

<div class="space-y-2">

${itemHtml}

</div>

</div>

`;

});

container.innerHTML = html;

}
);

}

/* =========================
FINISH ORDER
========================= */

function finishOrder(orderId){

ordersRef
.child(orderId)
.remove();

}

</script>

</body>
</html>
