<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gacoan Calibration System</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@600;700;800;900&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">

    <style>
        :root {
            --pink: #EC1A6B;
            --pink-dark: #C4105A;
            --blue: #4FC3E8;
            --green: #1FAF6E;
            --orange: #F59E0B;
            --red: #DC2626;
            --bg: #F8FAFC;
            --white: #FFFFFF;
            --text: #1A1A2E;
            --soft: #64748B;
            --border: #EADCE5;
        }

        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            padding-bottom: 40px;
            font-family: 'Inter', sans-serif;
            background: linear-gradient(180deg, #EAFBFF, #FFFFFF 25%);
            color: var(--text);
        }

        .app {
            max-width: 520px;
            margin: auto;
            padding: 15px;
        }

        /* HEADER */
        .header {
            background: white;
            border-radius: 22px;
            padding: 20px;
            box-shadow: 0 8px 25px rgba(0,0,0,.05);
        }

        .brand {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .logo {
            width: 45px;
            height: 45px;
            border-radius: 14px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 22px;
            font-weight: 900;
            color: white;
            background: linear-gradient(135deg, var(--pink), var(--blue));
        }

        h1 {
            font-family: Poppins;
            font-size: 20px;
            margin: 0;
            font-weight: 900;
        }

        .subtitle {
            font-size: 12px;
            color: var(--soft);
        }

        /* CARD */
        .card {
            background: white;
            margin-top: 15px;
            padding: 18px;
            border-radius: 22px;
            border: 1px solid var(--border);
            box-shadow: 0 8px 25px rgba(236,26,107,.07);
        }

        .title {
            font-family: Poppins;
            font-size: 15px;
            font-weight: 800;
            margin-bottom: 15px;
            display: flex;
            gap: 8px;
            align-items: center;
            justify-content: space-between;
        }

        .title-left {
            display: flex;
            gap: 8px;
            align-items: center;
        }

        .dot {
            width: 9px;
            height: 9px;
            background: var(--pink);
            border-radius: 50%;
        }

        /* PROGRESS BAR 0/10 */
        .progress-wrap {
            margin-bottom: 14px;
        }

        .progress-label {
            display: flex;
            justify-content: space-between;
            font-size: 11px;
            font-weight: 700;
            color: var(--soft);
            margin-bottom: 6px;
        }

        .progress-count {
            font-family: Poppins;
            font-weight: 800;
            color: var(--pink-dark);
        }

        .progress-track {
            width: 100%;
            height: 10px;
            border-radius: 100px;
            background: #F1F0F5;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            width: 0%;
            border-radius: 100px;
            background: linear-gradient(90deg, var(--blue), var(--pink));
            transition: width 0.25s ease;
        }

        /* INPUT */
        label {
            font-size: 11px;
            font-weight: 700;
            color: var(--soft);
            text-transform: uppercase;
            display: block;
            margin-bottom: 6px;
        }

        input, select {
            width: 100%;
            padding: 13px;
            border-radius: 14px;
            border: 1.5px solid var(--border);
            font-size: 15px;
            font-weight: 600;
            outline: none;
            transition: all 0.2s;
        }

        input:focus, select:focus {
            border-color: var(--pink);
        }

        .row-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }

        /* ITEM BUTTON */
        .items {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 10px;
        }

        .item {
            padding: 9px 14px;
            border-radius: 100px;
            background: white;
            border: 1.5px solid var(--border);
            font-size: 13px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.2s;
        }

        .item.active {
            background: var(--pink);
            color: white;
            border-color: var(--pink);
        }

        /* Styling spesifik untuk "+ Lainnya" */
        .item-other.active {
            background: var(--blue);
            border-color: var(--blue);
        }

        /* INPUT UNTUK ITEM LAINNYA */
        #otherItemInput {
            margin-top: 10px;
            border-color: var(--pink);
            display: none;
        }

        #otherItemInput:focus {
            box-shadow: 0 0 0 3px rgba(236, 26, 107, 0.15);
        }

        /* GRAM INPUT */
        .gram-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 8px;
        }

        .gram {
            position: relative;
        }

        .gram span {
            position: absolute;
            top: 4px;
            left: 7px;
            font-size: 9px;
            color: #CBD5E1;
        }

        .gram input {
            padding-top: 18px;
            text-align: center;
            font-family: Poppins;
            font-weight: 800;
            background: #FFF5FA;
        }

        .gram input.filled {
            background: #FFE9F3;
            border-color: var(--pink);
        }

        /* STATUS */
        .status {
            margin-top: 12px;
            padding: 12px;
            border-radius: 15px;
            text-align: center;
            font-weight: 800;
            font-size: 14px;
        }

        .status.good { background: #DCFCE7; color: #15803D; }
        .status.warning { background: #FEF3C7; color: #B45309; }
        .status.bad { background: #FEE2E2; color: #B91C1C; }

        /* RESULT */
        .stats {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 15px;
        }

        .stat {
            padding: 15px;
            border-radius: 18px;
            background: #FFF0F7;
            text-align: center;
        }

        .stat.blue { background: #EAFBFF; }
        .stat-title { font-size: 11px; font-weight: 700; color: var(--soft); }
        .stat-value { font-family: Poppins; font-size: 25px; font-weight: 900; margin-top: 5px; }
        .stat-sub { font-size: 10px; color: var(--soft); margin-top: 2px; }

        /* BUTTON */
        button {
            width: 100%;
            margin-top: 15px;
            padding: 15px;
            border: none;
            border-radius: 16px;
            font-family: Poppins;
            font-size: 15px;
            font-weight: 800;
            cursor: pointer;
            transition: background 0.2s;
        }

        .btn-primary {
            background: linear-gradient(120deg, var(--pink), var(--pink-dark));
            color: white;
        }

        .btn-primary:hover { background: var(--pink-dark); }

        .btn-wa {
            background: #25D366;
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .btn-wa:hover { background: #1da851; }

        /* TABEL PENYIMPANAN SEMENTARA */
        .temp-storage {
            margin-top: 20px;
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 12px;
            background: white;
            border-radius: 12px;
            overflow: hidden;
            border: 1px solid var(--border);
        }

        th, td {
            text-align: left;
            padding: 10px;
            border-bottom: 1px solid var(--border);
        }

        th {
            background: #F8FAFC;
            color: var(--soft);
            text-transform: uppercase;
            font-weight: 700;
        }

        tr:last-child td { border-bottom: none; }

        .status-badge {
            padding: 2px 6px;
            border-radius: 4px;
            font-weight: 700;
            font-size: 10px;
        }
        .bg-ok { background: #DCFCE7; color: #15803D; }
        .bg-warn { background: #FEF3C7; color: #B45309; }
        .bg-ng { background: #FEE2E2; color: #B91C1C; }

        .del-btn {
            background: none;
            border: none;
            color: var(--red);
            font-weight: 800;
            font-size: 11px;
            cursor: pointer;
            padding: 4px 8px;
            margin: 0;
            width: auto;
        }

    </style>
</head>
<body>

<div class="app">

    <!-- HEADER -->
    <div class="header">
        <div class="brand">
            <div class="logo">G</div>
            <div>
                <h1>Calibration System</h1>
                <div class="subtitle">Deviation Decrease</div>
            </div>
        </div>
    </div>

    <!-- OUTLET -->
    <div class="card">
        <div class="title">
            <div class="title-left"><span class="dot"></span> Outlet</div>
        </div>
        <select id="outlet">
            <option value="MLGMON">MLGMON - MALANG MONDOROKO</option>
            <option value="MLGJAK">MLGJAK - MALANG JAKARTA</option>
            <option value="MLGSOE">MLGSOE - MALANG SOEKARNO HATTA</option>
            <option value="MLGRON">MLGRON - MALANG RONGGOWARSITO</option>
            <option value="MLGDIR">MLGDIR - MALANG DIRGANTARA</option>
        </select>
    </div>

    <!-- INPUT -->
    <div class="card">
        <div class="title">
            <div class="title-left"><span class="dot"></span> Input Kalibrasi</div>
        </div>

        <label for="crew">Nama Crew</label>
        <input id="crew" placeholder="mis. Edo, Ibnu, Via...">
        <br><br>

        <label>Item Kalibrasi</label>
        <div class="items">
            <div class="item active" data-item="Acin">Acin</div>
            <div class="item" data-item="Bagor">Bagor</div>
            <div class="item" data-item="Basic">Basic</div>
            <div class="item" data-item="Minyak Mie">Minyak Mie</div>
            <div class="item" data-item="Adonan">Adonan</div>
            <div class="item item-other" data-item="Lainnya">+ Lainnya</div>
        </div>

        <input type="text" id="otherItemInput" placeholder="Atau ketik item lain...">

        <br><br>
        <label for="targetInput">Target / Acuan (gram)</label>
        <input type="number" id="targetInput" placeholder="mis. 8" step="0.1">
    </div>

    <!-- GRAMASI -->
    <div class="card">
        <div class="title">
            <div class="title-left"><span class="dot"></span> 10 Trial Gramasi</div>
        </div>

        <div class="progress-wrap">
            <div class="progress-label">
                <span>Progress Input</span>
                <span class="progress-count" id="progressCount">0/10</span>
            </div>
            <div class="progress-track">
                <div class="progress-fill" id="progressFill"></div>
            </div>
        </div>

        <div class="gram-grid" id="gramGrid"></div>
        <div id="status" class="status">Menunggu input</div>

        <div class="stats">
            <div class="stat">
                <div class="stat-title">Average</div>
                <div class="stat-value" id="avg">-</div>
            </div>
            <div class="stat blue">
                <div class="stat-title">Modus</div>
                <div class="stat-value" id="modus">-</div>
                <div class="stat-sub" id="modusSub"></div>
            </div>
        </div>

        <button class="btn-primary" id="saveToTemp">
            Simpan & Tambah Item Lain
        </button>
    </div>

    <!-- TABEL PENYIMPANAN SEMENTARA & PENGIRIMAN -->
    <div class="card" id="tempStorageCard" style="display:none;">
        <div class="title">
            <div class="title-left"><span class="dot"></span> Data Siap Kirim (Keranjang)</div>
        </div>

        <div class="temp-storage">
            <table id="tempTable">
                <thead>
                    <tr>
                        <th>Item</th>
                        <th>Avg / Modus</th>
                        <th>Status</th>
                        <th></th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Data masuk disini -->
                </tbody>
            </table>
        </div>

        <br>
        <button class="btn-wa" id="finalSend">
            <svg width="20" height="20" viewBox="0 0 448 512" fill="white"><path d="M380.9 97.1C339 55.1 283.2 32 223.9 32c-122.4 0-222 99.6-222 222 0 39.1 10.2 77.3 29.6 111L3 480l117.7-30.9c32.4 17.7 68.9 27 106.1 27h.1c122.3 0 224.1-99.6 224.1-222 0-59.3-25.2-115-67.1-117zm-157 338.7h-.1c-34 0-67.3-9.2-96.5-26.3L97 435.8l24.6-89.9c-18.7-29.7-28.6-64.1-28.6-99.3 0-108.6 88.4-197 197-197 52.6 0 102 20.5 139.1 57.6s57.6 86.5 57.6 139.1c0 108.6-88.4 197-197 197zm105.1-147.5c-5.8-2.9-34.1-16.8-39.4-18.8-5.3-2-9.1-2.9-13.1 2.9-3.9 5.8-15.3 18.8-18.8 22.7-3.4 3.9-6.9 4.4-12.6 1.5-5.8-2.9-24.3-8.9-46.3-28.5-17.1-15.3-28.7-34.2-32.1-40-3.4-5.8-.4-8.9 2.5-11.8 2.6-2.6 5.8-6.8 8.7-10.1 2.9-3.4 3.9-5.8 5.8-9.6 1.9-3.9.9-7.4-.5-10.3-1.4-2.9-13.1-31.5-18-43.3-4.7-11.5-9.6-9.9-13.1-10.1-3.4-.2-7.3-.2-11.2-.2-3.9 0-10.3 1.5-15.7 7.4-5.4 5.8-20.7 20.3-20.7 49.4 0 29.1 21.2 57.2 24.1 61.2 2.9 3.9 41.5 63.4 100.6 88.9 14.1 6.1 25.1 9.8 33.6 12.5 14.1 4.5 27 3.9 37.1 2.4 11.2-1.7 34.1-13.9 38.9-27.3 4.8-13.4 4.8-24.9 3.4-27.3-1.5-2.4-5.3-3.9-11.2-6.8z"/></svg>
            Kirim ke Spreadsheet & Pilih Kontak WA
        </button>
        <p style="font-size:10px; color:var(--soft); margin-top:8px; text-align:center;">
            Data dikirim ke spreadsheet dahulu, lalu WhatsApp terbuka agar kamu pilih sendiri kontak/grup tujuan.
        </p>
    </div>

</div>

<script>
// ================================
// CONFIG
// ================================
// GANTI DENGAN URL API ANDA DARI GOOGLE APPS SCRIPT
const API_URL = "https://script.google.com/macros/s/AKfycbyTuteQYO1o4hYqS_dY_iURED4fYvrBTKo29EIolJuKLQGLTW9fFPzxKg2Os7qEjYXA/exec";

let selectedItem = "Acin";
let isOtherItem = false;
let gramValues = [];

// KERANJANG SEMENTARA
let tempCart = [];

const gramGrid = document.getElementById("gramGrid");
const statusBox = document.getElementById("status");
const avgText = document.getElementById("avg");
const modusText = document.getElementById("modus");
const modusSub = document.getElementById("modusSub");
const otherItemInput = document.getElementById("otherItemInput");
const targetInput = document.getElementById("targetInput");
const tempStorageCard = document.getElementById("tempStorageCard");
const tempTableBody = document.querySelector("#tempTable tbody");
const progressFill = document.getElementById("progressFill");
const progressCount = document.getElementById("progressCount");

// ================================
// NAMA OUTLET
// ================================
function getOutletName(code){
    const outlet = {
        MLGMON: "MALANG MONDOROKO", MLGJAK: "MALANG JAKARTA", MLGSOE: "MALANG SOEKARNO HATTA",
        MLGRON: "MALANG RONGGOWARSITO", MLGDIR: "MALANG DIRGANTARA"
    };
    return outlet[code] || code;
}

// ================================
// CREATE 10 INPUT GRAM
// ================================
function createGramInput(){
    gramGrid.innerHTML = "";
    gramValues = [];
    for(let i = 0; i < 10; i++){
        let div = document.createElement("div");
        div.className = "gram";
        div.innerHTML = `
            <span>${i+1}</span>
            <input type="number" class="gram-input" data-index="${i}" placeholder="-">
        `;
        gramGrid.appendChild(div);
    }

    document.querySelectorAll(".gram-input").forEach(input => {
        input.addEventListener("input", function(){
            let index = this.dataset.index;
            gramValues[index] = this.value === "" ? undefined : Number(this.value);
            this.classList.toggle("filled", this.value !== "");
            calculate();
        });
    });

    updateProgress();
}

createGramInput();

// ================================
// PROGRESS BAR 0/10
// ================================
function updateProgress(){
    let filled = gramValues.filter(x => x !== undefined && !isNaN(x)).length;
    progressCount.textContent = `${filled}/10`;
    progressFill.style.width = (filled / 10 * 100) + "%";
}

// ================================
// LOGIKA PEMILIHAN ITEM
// ================================
document.querySelectorAll(".items .item").forEach(btn => {
    btn.onclick = function(){
        document.querySelectorAll(".items .item").forEach(x => x.classList.remove("active"));
        this.classList.add("active");
        selectedItem = this.dataset.item;

        if (selectedItem === "Lainnya") {
            isOtherItem = true;
            otherItemInput.style.display = "block";
            otherItemInput.focus();
        } else {
            isOtherItem = false;
            otherItemInput.style.display = "none";
            otherItemInput.value = "";
        }
        calculate();
    };
});

// ================================
// HITUNG MODUS
// ================================
function hitungModus(data){
    if(data.length === 0) return { modus: [], freq: 0 };
    let freqMap = {};
    data.forEach(v => { freqMap[v] = (freqMap[v] || 0) + 1; });
    let maxFreq = Math.max(...Object.values(freqMap));
    let modus = Object.keys(freqMap)
        .filter(k => freqMap[k] === maxFreq)
        .map(Number);
    return { modus, freq: maxFreq };
}

// ================================
// MESIN KALKULASI
// ================================
function calculate(){
    updateProgress();
    let data = gramValues.filter(x => x !== undefined && !isNaN(x));

    if(data.length === 0){
        avgText.innerHTML = "-";
        modusText.innerHTML = "-";
        modusSub.innerHTML = "";
        statusBox.className = "status";
        statusBox.innerHTML = "Menunggu input";
        return;
    }

    let avg = data.reduce((a, b) => a + b, 0) / data.length;
    avgText.innerHTML = avg.toFixed(2) + " g";

    let { modus, freq } = hitungModus(data);
    if(modus.length === data.length){
        // semua nilai unik, tidak ada modus dominan
        modusText.innerHTML = "-";
        modusSub.innerHTML = "Semua nilai unik";
    } else {
        modusText.innerHTML = modus.map(m => m + "g").join(", ");
        modusSub.innerHTML = `muncul ${freq}x`;
    }

    let target = parseFloat(targetInput.value);

    if(isNaN(target)){
        statusBox.className = "status good";
        statusBox.innerHTML = "✓ RECORD DATA (tanpa target)";
        return;
    }

    let deviation = Math.abs(avg - target);

    if(deviation === 0){
        statusBox.className = "status good";
        statusBox.innerHTML = "✓ SESUAI TARGET";
    } else if(deviation <= 1){
        statusBox.className = "status warning";
        statusBox.innerHTML = "⚠ SELISIH 1 GRAM";
    } else {
        statusBox.className = "status bad";
        statusBox.innerHTML = "✕ OUT OF STANDARD";
    }
}

targetInput.addEventListener("input", calculate);

// ================================
// SIMPAN SEMENTARA KE KERANJANG
// ================================
document.getElementById("saveToTemp").addEventListener("click", () => {
    let crew = document.getElementById("crew").value.trim();
    let data = gramValues.filter(x => x !== undefined && !isNaN(x));

    let finalItemName = selectedItem;
    if (isOtherItem) {
        finalItemName = otherItemInput.value.trim();
        if (!finalItemName) finalItemName = "Lainnya (Tanpa Nama)";
    }

    if(!crew){ alert("Isi nama crew dahulu"); return; }
    if(data.length < 10){ alert("Lengkapi 10 trial gramasi"); return; }

    let avg = data.reduce((a, b) => a + b, 0) / 10;
    let { modus, freq } = hitungModus(data);
    let modusLabel = (modus.length === data.length) ? "-" : modus.join("/");

    let target = parseFloat(targetInput.value);
    let hasTarget = !isNaN(target);

    let deviation = 0;
    let status = "OK";
    let score = 100;

    if(hasTarget){
        deviation = Math.abs(avg - target);
        status = "NG";
        if(deviation === 0) status = "OK";
        else if(deviation <= 1) status = "WARNING";
        score = target !== 0 ? (100 - (deviation / target * 100)) : 100;
        if(score < 0) score = 0;
    }

    let result = {
        outletCode: document.getElementById("outlet").value,
        crew: crew,
        item: finalItemName,
        target: hasTarget ? target : "-",
        data: data.join(","),
        average: avg.toFixed(2),
        modus: modusLabel,
        deviation: hasTarget ? deviation.toFixed(2) : "-",
        status: status,
        score: hasTarget ? score.toFixed(1) : "-"
    };

    tempCart.push(result);
    updateTempTable();

    tempStorageCard.style.display = "block";

    // Reset Form Input Gramasi & Target (Nama Crew & Outlet tetap)
    createGramInput();
    targetInput.value = "";
    avgText.innerHTML = "-";
    modusText.innerHTML = "-";
    modusSub.innerHTML = "";
    statusBox.className = "status";
    statusBox.innerHTML = "Menunggu input";
    if (isOtherItem) otherItemInput.value = "";

    alert(`Item ${finalItemName} ditambahkan ke keranjang siap kirim.`);
});

// Update tampilan tabel keranjang
function updateTempTable(){
    tempTableBody.innerHTML = "";
    tempCart.forEach((x, i) => {
        let badgeClass = "bg-ng";
        if(x.status === "OK") badgeClass = "bg-ok";
        if(x.status === "WARNING") badgeClass = "bg-warn";

        let row = `<tr>
            <td><b>${x.item}</b><br><span style="color:var(--soft); font-size:10px;">Crew: ${x.crew}</span></td>
            <td style="font-family:Poppins; font-weight:800; font-size:13px;">${x.average}g<br><span style="font-weight:600; font-size:10px; color:var(--soft);">modus: ${x.modus}</span></td>
            <td><span class="status-badge ${badgeClass}">${x.status}</span></td>
            <td><button class="del-btn" data-index="${i}">Hapus</button></td>
        </tr>`;
        tempTableBody.innerHTML += row;
    });

    document.querySelectorAll(".del-btn").forEach(btn => {
        btn.onclick = function(){
            tempCart.splice(Number(this.dataset.index), 1);
            updateTempTable();
            if(tempCart.length === 0) tempStorageCard.style.display = "none";
        };
    });
}

// ================================
// FITUR FINAL: KIRIM SPREADSHEET, LALU BUKA WA (PILIH KONTAK SENDIRI)
// ================================
document.getElementById("finalSend").addEventListener("click", async function() {
    if(tempCart.length === 0) { alert("Keranjang kosong!"); return; }

    // 1. FORMAT TEKS UNTUK WA
    let outletName = getOutletName(tempCart[0].outletCode);
    let tgl = new Date().toLocaleDateString('id-ID', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });

    let textWa = `*LAPORAN KALIBRASI QC GACOAN*\n`;
    textWa += `--------------------------------\n`;
    textWa += `*Outlet:* ${outletName}\n`;
    textWa += `*Tanggal:* ${tgl}\n`;
    textWa += `*Total Item:* ${tempCart.length}\n`;
    textWa += `--------------------------------\n\n`;

    tempCart.forEach((x, i) => {
        textWa += `*${i+1}. ${x.item}* (Crew: ${x.crew})\n`;
        textWa += `Target: ${x.target}g | Avg: *${x.average}g* | Modus: ${x.modus}g | Stat: *${x.status}*\n`;
        textWa += `Data: [${x.data}]\n\n`;
    });

    // 2. BUKA WA SEGERA (di dalam handler klik yang sama) SUPAYA TIDAK DIBLOKIR POPUP BLOCKER
    //    Harus terjadi tanpa jeda "await" sebelum window.open dipanggil.
    let urlWa = `https://wa.me/?text=${encodeURIComponent(textWa)}`;
    let waTab = window.open(urlWa, '_blank');

    if (!waTab) {
        alert("Browser memblokir tab WhatsApp. Mohon izinkan popup untuk situs ini, lalu coba lagi.");
        return;
    }

    this.disabled = true;
    this.innerHTML = "Mengirim ke Spreadsheet...";

    // 3. KIRIM DATA KE SPREADSHEET SATU PER SATU
    let successCount = 0;
    for (let itemData of tempCart) {
        let payload = {
            OUTLETCODE: itemData.outletCode,
            OUTLETNAME: getOutletName(itemData.outletCode),
            CREW: itemData.crew,
            ITEM: itemData.item,
            TARGET: itemData.target,
            TRIAL: itemData.data,
            AVERAGE: itemData.average,
            MODUS: itemData.modus,
            DEVIATION: itemData.deviation,
            STATUS: itemData.status,
            SCORE: itemData.score
        };

        try {
            await fetch(API_URL, {
                method: "POST",
                mode: "no-cors",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(payload)
            });
            successCount++;
            console.log(`Item ${itemData.item} terkirim ke Sheet`);
        } catch(err) {
            console.error(`Gagal mengirim ${itemData.item}`, err);
        }
    }

    if (successCount === tempCart.length) {
        console.log(`${successCount} data berhasil dikirim ke Spreadsheet.`);
    } else {
        alert(`Hanya ${successCount} dari ${tempCart.length} data berhasil dikirim ke Spreadsheet. Cek koneksi lalu coba kirim ulang jika perlu.`);
    }

    // 4. RESET SEMUANYA
    tempCart = [];
    updateTempTable();
    tempStorageCard.style.display = "none";
    document.getElementById("crew").value = "";

    this.disabled = false;
    this.innerHTML = `<svg width="20" height="20" viewBox="0 0 448 512" fill="white" style="margin-right:8px;"><path d="M380.9 97.1C339 55.1 283.2 32 223.9 32c-122.4 0-222 99.6-222 222 0 39.1 10.2 77.3 29.6 111L3 480l117.7-30.9c32.4 17.7 68.9 27 106.1 27h.1c122.3 0 224.1-99.6 224.1-222 0-59.3-25.2-115-67.1-117zm-157 338.7h-.1c-34 0-67.3-9.2-96.5-26.3L97 435.8l24.6-89.9c-18.7-29.7-28.6-64.1-28.6-99.3 0-108.6 88.4-197 197-197 52.6 0 102 20.5 139.1 57.6s57.6 86.5 57.6 139.1c0 108.6-88.4 197-197 197zm105.1-147.5c-5.8-2.9-34.1-16.8-39.4-18.8-5.3-2-9.1-2.9-13.1 2.9-3.9 5.8-15.3 18.8-18.8 22.7-3.4 3.9-6.9 4.4-12.6 1.5-5.8-2.9-24.3-8.9-46.3-28.5-17.1-15.3-28.7-34.2-32.1-40-3.4-5.8-.4-8.9 2.5-11.8 2.6-2.6 5.8-6.8 8.7-10.1 2.9-3.4 3.9-5.8 5.8-9.6 1.9-3.9.9-7.4-.5-10.3-1.4-2.9-13.1-31.5-18-43.3-4.7-11.5-9.6-9.9-13.1-10.1-3.4-.2-7.3-.2-11.2-.2-3.9 0-10.3 1.5-15.7 7.4-5.4 5.8-20.7 20.3-20.7 49.4 0 29.1 21.2 57.2 24.1 61.2 2.9 3.9 41.5 63.4 100.6 88.9 14.1 6.1 25.1 9.8 33.6 12.5 14.1 4.5 27 3.9 37.1 2.4 11.2-1.7 34.1-13.9 38.9-27.3 4.8-13.4 4.8-24.9 3.4-27.3-1.5-2.4-5.3-3.9-11.2-6.8z"/></svg> Kirim ke Spreadsheet & Pilih Kontak WA`;

});
</script>

</body>
</html>
