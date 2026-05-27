<!DOCTYPE html>
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

<style>

html,
body{
height:100%;
overflow:hidden;
background:#020617;
font-family:Arial,sans-serif;
-webkit-tap-highlight-color:transparent;
}

.hidden{
display:none;
}

.no-scrollbar::-webkit-scrollbar{
display:none;
}

button{
user-select:none;
}

.tap-btn:active{
transform:scale(.92);
}

</style>

</head>

<body class="h-screen overflow-hidden text-white">

<div
id="role-screen"
class="h-full flex flex-col justify-center items-center gap-5 px-6 bg-slate-950">

<div class="text-center">

<h1 class="text-5xl font-black text-orange-500 tracking-tight">
GACOAN ACC
</h1>

<p class="text-slate-400 mt-2 text-lg">
Packing Accuracy System
</p>

</div>

<button
onclick="openPacker()"
class="tap-btn w-full max-w-xs bg-orange-500 py-5 rounded-3xl font-black text-2xl shadow-2xl transition">

📦 PACKER

</button>

<button
onclick="openPresenter()"
class="tap-btn w-full max-w-xs bg-blue-600 py-5 rounded-3xl font-black text-2xl shadow-2xl transition">

🖥 PRESENTER

</button>

</div>

<div
id="packer-screen"
class="hidden h-full flex flex-col bg-slate-950 p-3 gap-3 overflow-hidden">

<div class="flex gap-2 h-14">

<input
id="customer-name"
type="text"
placeholder="Nama customer / meja"
class="flex-1 bg-slate-800 border border-slate-700 rounded-2xl px-4 text-sm outline-none"/>

<button
onclick="resetOrder()"
class="tap-btn w-24 bg-red-500 rounded-2xl font-bold transition">

RESET

</button>

</div>

<div class="bg-slate-800 rounded-3xl p-4 flex justify-between items-center border border-slate-700">

<div>

<p class="text-slate-400 text-sm">
TOTAL ITEM
</p>

<h1
id="total-item"
class="text-5xl font-black text-orange-400">
0
</h1>

</div>

<button
id="submit-btn"
onclick="submitOrder()"
class="tap-btn bg-emerald-500 px-6 py-4 rounded-2xl text-xl font-black transition shadow-lg">

DONE

</button>

</div>

<div
id="menu-grid"
class="flex-1 overflow-y-auto no-scrollbar grid grid-cols-2 gap-3 pb-2">

</div>

</div>

<div
id="presenter-screen"
class="hidden h-full bg-slate-950 p-3 overflow-hidden">

<div class="h-full flex flex-col">

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
FIREBASE (Sudah Diperbaiki)
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
MENU SIMULASI
========================= */

const menuList = [

{
id:"udang-keju",
nama:"Udang Keju",
color:"bg-orange-500"
},

{
id:"udang-rambutan",
nama:"Udang Rambutan",
color:"bg-red-500"
},

{
id:"siomay",
nama:"Siomay",
color:"bg-yellow-500"
},

{
id:"lumpia-udang",
nama:"Lumpia Udang",
color:"bg-blue-500"
}

];

/* =========================
STATE
========================= */

let items = {};
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

renderMenu();

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
CLICK SOUND
========================= */

function tapSound(){

const audio =
new Audio(
"https://cdn.pixabay.com/download/audio/2022/03/15/audio_c8c8a73467.mp3?filename=click-124467.mp3"
);

audio.volume = 0.15;

audio.play().catch(err => console.log("Audio play blocked or error:", err));

}

/* =========================
RENDER MENU
========================= */

function renderMenu(){

const container =
document.getElementById("menu-grid");

let html = "";

menuList.forEach(menu=>{

const qty =
items[menu.id]?.qty || 0;

html += `

<div class="bg-slate-800 rounded-3xl p-4 flex flex-col justify-between border border-slate-700 min-h-[190px]">

<div>

<div class="${menu.color} inline-block px-3 py-1 rounded-full text-xs font-black mb-3">
MENU
</div>

<h3 class="font-black text-2xl leading-tight">
${menu.nama}
</h3>

</div>

<div class="mt-5 flex items-center justify-between">

<button
onclick="minusQty('${menu.id}')"
class="tap-btn w-16 h-16 rounded-2xl bg-red-500 text-4xl font-black transition shadow-lg">

−

</button>

<div class="text-5xl font-black text-orange-400 min-w-[50px] text-center">
${qty}
</div>

<button
onclick="plusQty('${menu.id}','${menu.nama}')"
class="tap-btn w-16 h-16 rounded-2xl bg-emerald-500 text-4xl font-black transition shadow-lg">

+

</button>

</div>

</div>

`;

});

container.innerHTML = html;

updateTotal();

}

/* =========================
PLUS
========================= */

function plusQty(id,nama){

tapSound();

if(items[id]){

items[id].qty += 1;

}else{

items[id] = {

id,
nama,
qty:1

};

}

renderMenu();

}

/* =========================
MINUS
========================= */

function minusQty(id){

tapSound();

if(!items[id]) return;

items[id].qty -= 1;

if(items[id].qty <= 0){

delete items[id];

}

renderMenu();

}

/* =========================
TOTAL
========================= */

function updateTotal(){

let total = 0;

Object.values(items).forEach(item=>{

total += item.qty;

});

document
.getElementById("total-item")
.innerText = total;

}

/* =========================
RESET
========================= */

function resetOrder(){

tapSound();

items = {};

document
.getElementById("customer-name")
.value = "";

renderMenu();

}

/* =========================
SUBMIT
========================= */

async function submitOrder(){

if(submitting) return;

const customer =
document
.getElementById("customer-name")
.value
.trim();

if(!customer){

alert("Masukkan nama customer");

return;

}

if(
Object.keys(items).length === 0
){

alert("Belum ada item");

return;

}

tapSound();

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
status:"waiting",
items

});

resetOrder();

}catch(err){

console.error(err);

alert("Gagal submit order");

}

btn.disabled = false;

btn.innerText = "DONE";

submitting = false;

}

/* =========================
PRESENTER
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

<div class="bg-slate-700 rounded-2xl p-4 flex justify-between items-center">

<div>

<h3 class="font-bold text-2xl">
${item.nama}
</h3>

</div>

<div class="text-4xl font-black text-orange-400">
x${item.qty}
</div>

</div>

`;

});

html += `

<div class="bg-slate-800 rounded-3xl p-4 border border-slate-700 shadow-xl">

<div class="flex justify-between items-start mb-4">

<div>

<h2 class="font-black text-3xl">
👤 ${order.customer}
</h2>

<p class="text-sm text-slate-400 mt-1">
${new Date(order.timestamp).toLocaleTimeString('id-ID')}
</p>

</div>

<button
onclick="finishOrder('${orderId}')"
class="tap-btn bg-emerald-500 px-5 py-3 rounded-2xl font-black text-lg transition shadow-lg">

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

tapSound();

ordersRef
.child(orderId)
.remove();

}

</script>

</body>
</html>
