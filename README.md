<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>

<title>Gacoan QC Packer</title>

<script src="https://cdn.tailwindcss.com"></script>

<!-- ZXING -->
<script src="https://unpkg.com/@zxing/library@latest"></script>

<style>

html,
body{
height:100%;
overflow:hidden;
background:#020617;
font-family:sans-serif;
}

#scanner video{
width:100%;
height:100%;
object-fit:cover;
border-radius:24px;
}

.no-scrollbar::-webkit-scrollbar{
display:none;
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

</style>
</head>

<body class="h-screen overflow-hidden text-white">

<div class="h-full flex flex-col p-3 gap-3">

<!-- HEADER -->
<div class="flex gap-2">

<input
id="customer-name"
type="text"
placeholder="Nama customer / meja"
class="flex-1 h-14 rounded-2xl bg-slate-800 border border-slate-700 px-4 outline-none text-sm"/>

<button
onclick="resetOrder()"
class="w-24 rounded-2xl bg-red-500 font-bold active:scale-95">
RESET
</button>

</div>

<!-- MAIN -->
<div class="flex-1 flex gap-3 overflow-hidden">

<!-- CAMERA -->
<div class="w-[48%] relative rounded-3xl overflow-hidden bg-black border border-slate-700">

<div id="scanner" class="w-full h-full"></div>

<!-- SCAN OVERLAY -->
<div class="absolute inset-0 pointer-events-none flex items-center justify-center">

<div class="w-48 h-36 border-4 border-orange-500 rounded-2xl relative overflow-hidden">

<div class="absolute top-0 left-0 w-full h-1 bg-red-500 scan-line"></div>

</div>

</div>

<!-- CAMERA STATUS -->
<div
id="camera-status"
class="absolute bottom-3 left-1/2 -translate-x-1/2 bg-black/70 px-3 py-1 rounded-full text-xs text-white">
Membuka kamera...
</div>

</div>

<!-- RIGHT -->
<div class="flex-1 flex flex-col overflow-hidden">

<!-- TOP INFO -->
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
onclick="submitOrder()"
class="bg-emerald-500 px-5 py-4 rounded-2xl font-black text-lg active:scale-95">
DONE
</button>

</div>

</div>

<!-- MANUAL -->
<div class="flex gap-2 mb-3">

<input
id="manual-input"
type="text"
placeholder="Input barcode manual"
class="flex-1 h-12 rounded-2xl bg-slate-800 border border-slate-700 px-4 outline-none text-sm"/>

<button
onclick="manualAdd()"
class="w-16 rounded-2xl bg-orange-500 font-black text-xl active:scale-95">
+
</button>

</div>

<!-- ITEM LIST -->
<div
id="item-list"
class="flex-1 overflow-y-auto no-scrollbar space-y-2">

<div class="text-center text-slate-500 pt-24">
Belum ada item
</div>

</div>

</div>

</div>

</div>

<script>

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

/* =========================
START CAMERA
========================= */

async function startScanner(){

const status = document.getElementById("camera-status");

try{

status.innerText = "Mengakses kamera...";

const hints = new Map();

hints.set(
ZXing.DecodeHintType.POSSIBLE_FORMATS,
[
ZXing.BarcodeFormat.EAN_13,
ZXing.BarcodeFormat.CODE_128
]
);

codeReader = new ZXing.BrowserMultiFormatReader(hints);

const devices = await ZXing.BrowserCodeReader.listVideoInputDevices();

if(devices.length === 0){

status.innerText = "Kamera tidak ditemukan";

return;

}

/* PRIORITAS KAMERA BELAKANG */

let selectedDeviceId = devices[0].deviceId;

const backCam = devices.find(device => {

const label = device.label.toLowerCase();

return (
label.includes("back") ||
label.includes("rear") ||
label.includes("environment")
);

});

if(backCam){

selectedDeviceId = backCam.deviceId;

}

status.innerText = "Scanner aktif";

codeReader.decodeFromVideoDevice(
selectedDeviceId,
"scanner",
(result, err)=>{

if(result){

const barcode = result.text;
const now = Date.now();

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

navigator.vibrate(60);

}

}

}
);

}catch(err){

console.error(err);

status.innerText =
"Gagal membuka kamera";

alert(
"Kamera gagal dibuka.\n\nPastikan:\n1. Website HTTPS\n2. Izin kamera diaktifkan\n3. Gunakan Chrome / Safari terbaru"
);

}

}

/* =========================
ADD ITEM
========================= */

function addItem(barcode){

const nama =
skuDB[barcode] || `SKU ${barcode}`;

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

const barcode = input.value.trim();

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

const keys = Object.keys(items);

let total = 0;

keys.forEach(key=>{

total += items[key].qty;

});

document.getElementById("total-item")
.innerText = total;

if(keys.length === 0){

container.innerHTML = `
<div class="text-center text-slate-500 pt-24">
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
class="w-10 h-10 rounded-xl bg-red-500 text-xl font-black active:scale-90">
−
</button>

<div class="w-8 text-center text-xl font-black">
${item.qty}
</div>

<button
onclick="plusQty('${barcode}')"
class="w-10 h-10 rounded-xl bg-emerald-500 text-xl font-black active:scale-90">
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

document.getElementById("customer-name").value = "";

renderItems();

}

/* =========================
SUBMIT
========================= */

function submitOrder(){

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

alert("Siap integrasi Firebase");

}

/* =========================
AUTO START
========================= */

window.onload = ()=>{

startScanner();

};

</script>

</body>
</html>
