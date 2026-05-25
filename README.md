<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>

<title>Gacoan QC System</title>

<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>

<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<script src="https://unpkg.com/@zxing/library@latest"></script>

<style>
html, body{
height:100%;
overflow:hidden;
background:#0f172a;
}

.hidden{
display:none;
}

#interactive video{
width:100%;
height:100%;
object-fit:cover;
}

.scan-line{
animation:scan 2s linear infinite;
}

@keyframes scan{
0%{transform:translateY(-120px);}
100%{transform:translateY(120px);}
}

.no-scrollbar::-webkit-scrollbar{
display:none;
}
</style>
</head>

<body class="h-screen overflow-hidden text-white">

<!-- ROLE -->
<div id="role-view" class="h-full flex flex-col justify-center items-center px-6 gap-5 bg-slate-950">

<div class="text-center">
<h1 class="text-3xl font-black">GACOAN QC</h1>
<p class="text-slate-400 text-sm mt-2">Packing Accuracy System</p>
</div>

<button onclick="switchRole('packer')"
class="w-full max-w-xs bg-orange-500 active:scale-95 transition rounded-2xl py-5 font-black text-xl shadow-2xl">
📦 PACKER
</button>

<button onclick="switchRole('presenter')"
class="w-full max-w-xs bg-blue-600 active:scale-95 transition rounded-2xl py-5 font-black text-xl shadow-2xl">
🖥 PRESENTER
</button>

</div>

<!-- PACKER -->
<div id="packer-screen" class="hidden h-full flex flex-col bg-slate-950">

<!-- TOP -->
<div class="px-4 pt-3 pb-2 flex gap-2">

<input
id="customer-name"
placeholder="Nama Customer / Meja"
class="flex-1 bg-slate-800 border border-slate-700 rounded-2xl px-4 text-sm outline-none"/>

<button
onclick="resetOrder()"
class="bg-red-500 px-4 rounded-2xl font-bold">
RESET
</button>

</div>

<!-- MAIN -->
<div class="flex-1 flex gap-2 px-3 pb-3 overflow-hidden">

<!-- CAMERA -->
<div class="w-[48%] bg-black rounded-3xl relative overflow-hidden border border-slate-700">

<div id="interactive" class="w-full h-full">
<video id="video-preview"></video>
</div>

<div class="absolute inset-0 flex items-center justify-center pointer-events-none">

<div class="w-52 h-40 border-4 border-orange-500 rounded-2xl relative">

<div class="absolute top-0 left-0 w-full h-1 bg-red-500 scan-line"></div>

</div>

</div>

</div>

<!-- RIGHT -->
<div class="flex-1 flex flex-col overflow-hidden">

<!-- HEADER -->
<div class="bg-slate-800 rounded-2xl px-4 py-3 mb-2">

<div class="flex justify-between items-center">

<div>
<p class="text-xs text-slate-400">TOTAL ITEM</p>
<h2 id="total-item" class="text-3xl font-black text-orange-400">0</h2>
</div>

<button
id="submit-btn"
onclick="submitOrder()"
class="bg-emerald-500 active:scale-95 transition px-5 py-4 rounded-2xl font-black text-lg shadow-xl">
DONE
</button>

</div>

</div>

<!-- MANUAL -->
<div class="flex gap-2 mb-2">

<input
id="manual-input"
placeholder="Barcode manual"
class="flex-1 bg-slate-800 border border-slate-700 rounded-xl px-3 text-sm outline-none"/>

<button
onclick="manualAdd()"
class="bg-orange-500 px-4 rounded-xl font-bold">
+
</button>

</div>

<!-- ITEM LIST -->
<div
id="item-list"
class="flex-1 overflow-y-auto no-scrollbar space-y-2">

<div class="text-center text-slate-500 text-sm pt-20">
Belum ada item
</div>

</div>

</div>

</div>

</div>

<!-- PRESENTER -->
<div id="presenter-screen"
class="hidden h-full bg-slate-950 p-3 overflow-hidden">

<div class="h-full flex flex-col">

<div class="flex justify-between items-center mb-3">

<h1 class="font-black text-2xl">
📋 MONITOR QC
</h1>

<div
id="order-count"
class="bg-blue-500/20 text-blue-400 px-4 py-2 rounded-xl font-bold">
0 ORDER
</div>

</div>

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

/* FIREBASE */

const firebaseConfig = {

apiKey: "YOUR_API_KEY",
authDomain: "YOUR_DOMAIN",
databaseURL: "YOUR_DATABASE_URL",
projectId: "YOUR_PROJECT_ID",
storageBucket: "YOUR_BUCKET",
messagingSenderId: "YOUR_SENDER",
appId: "YOUR_APP_ID"

};

firebase.initializeApp(firebaseConfig);

const db = firebase.database();
const ordersRef = db.ref("gacoan_active_orders");

/* SKU */

const skuDB = {

"8991000012345":"Mie Gacoan Lv 1",
"8991000056789":"Mie Hompimpa Lv 3",
"8991000098765":"Udang Keju",
"8992000011111":"Udang Rambutan",
"8993000022222":"Es Gobak Sodor"

};

/* STATE */

let scanner = null;

let items = {};

let lastScan = "";
let lastScanTime = 0;

let submitting = false;

/* ROLE */

function switchRole(role){

document.getElementById("role-view").classList.add("hidden");

if(role === "packer"){

document.getElementById("packer-screen").classList.remove("hidden");

startScanner();

}else{

document.getElementById("presenter-screen").classList.remove("hidden");

listenPresenter();

}

}

/* SCANNER */

function startScanner(){

const hints = new Map();

hints.set(
ZXing.DecodeHintType.POSSIBLE_FORMATS,
[
ZXing.BarcodeFormat.CODE_128,
ZXing.BarcodeFormat.EAN_13
]
);

scanner = new ZXing.BrowserMultiFormatReader(hints);

scanner.decodeFromVideoDevice(
undefined,
"video-preview",
(result, err)=>{

if(result){

const code = result.text;
const now = Date.now();

if(code === lastScan && now - lastScanTime < 1200){
return;
}

lastScan = code;
lastScanTime = now;

addItem(code);

if(navigator.vibrate){
navigator.vibrate(60);
}

}

}
);

}

/* ADD ITEM */

function addItem(barcode){

const nama = skuDB[barcode] || "SKU BARU";

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

/* MANUAL */

function manualAdd(){

const input = document.getElementById("manual-input");

const code = input.value.trim();

if(!code) return;

addItem(code);

input.value = "";

}

/* RENDER */

function renderItems(){

const container = document.getElementById("item-list");

const keys = Object.keys(items);

document.getElementById("total-item").innerText =
keys.reduce((a,b)=>a + items[b].qty,0);

if(keys.length === 0){

container.innerHTML = `
<div class="text-center text-slate-500 text-sm pt-20">
Belum ada item
</div>
`;

return;

}

let html = "";

keys.forEach(barcode=>{

const item = items[barcode];

html += `

<div class="bg-slate-800 rounded-2xl p-3">

<div class="flex justify-between items-center">

<div>

<h3 class="font-bold text-sm">
${item.nama}
</h3>

<p class="text-[10px] text-slate-500 mt-1">
${barcode}
</p>

</div>

<div class="flex items-center gap-2">

<button
onclick="minusQty('${barcode}')"
class="w-8 h-8 rounded-lg bg-red-500 font-black">
-
</button>

<div class="w-8 text-center font-black text-lg">
${item.qty}
</div>

<button
onclick="plusQty('${barcode}')"
class="w-8 h-8 rounded-lg bg-emerald-500 font-black">
+
</button>

</div>

</div>

</div>

`;

});

container.innerHTML = html;

}

/* QTY */

function plusQty(barcode){

items[barcode].qty += 1;

renderItems();

}

function minusQty(barcode){

items[barcode].qty -= 1;

if(items[barcode].qty <= 0){

delete items[barcode];

}

renderItems();

}

/* SUBMIT */

function submitOrder(){

if(submitting) return;

const customer =
document.getElementById("customer-name").value.trim();

if(!customer){

alert("Masukkan nama customer");

return;

}

if(Object.keys(items).length === 0){

alert("Item kosong");

return;

}

submitting = true;

const btn = document.getElementById("submit-btn");

btn.disabled = true;

btn.innerText = "LOADING...";

const orderId = Date.now();

ordersRef.child(orderId).set({

customer,
timestamp:Date.now(),
items

}).then(()=>{

resetOrder();

btn.disabled = false;
btn.innerText = "DONE";

submitting = false;

});

}

/* RESET */

function resetOrder(){

items = {};

document.getElementById("customer-name").value = "";

renderItems();

}

/* PRESENTER */

function listenPresenter(){

ordersRef.on("value",(snapshot)=>{

const container =
document.getElementById("presenter-list");

if(!snapshot.exists()){

container.innerHTML = `
<div class="text-center text-slate-500 pt-40">
Belum ada pesanan
</div>
`;

document.getElementById("order-count").innerText =
"0 ORDER";

return;

}

const data = snapshot.val();

const keys = Object.keys(data).reverse();

document.getElementById("order-count").innerText =
`${keys.length} ORDER`;

let html = "";

keys.forEach(orderId=>{

const order = data[orderId];

let itemHtml = "";

Object.values(order.items).forEach(item=>{

itemHtml += `

<div class="flex justify-between items-center bg-slate-700 rounded-xl px-3 py-2">

<div>

<h4 class="font-bold text-sm">
${item.nama}
</h4>

<p class="text-[10px] text-slate-400">
${item.barcode}
</p>

</div>

<div class="text-orange-400 font-black text-xl">
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
class="bg-emerald-500 px-4 py-2 rounded-xl font-black">
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

});

}

/* FINISH */

function finishOrder(orderId){

ordersRef.child(orderId).remove();

}

</script>

</body>
</html>
