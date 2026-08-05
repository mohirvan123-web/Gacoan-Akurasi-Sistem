<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kalibrasi Gacoan Singosari</title>
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
            display: flex;
            align-items: center;
            justify-content: space-between;
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
            color: var(--text);
        }

        .dot {
            width: 9px;
            height: 9px;
            background: var(--pink);
            border-radius: 50%;
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

        #otherItemInput {
            margin-top: 10px;
            border-color: var(--pink);
            display: none;
        }

        /* ROW SHIFT/DATE */
        .row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-bottom: 20px;
        }

        /* GRAM INPUT & PROGRESS */
        .gram-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 8px;
            margin-bottom: 10px;
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

        /* PROGRESS BAR & COUNTER (FITUR BARU) */
        .progress-container {
            width: 100%;
            margin-bottom: 15px;
        }

        .progress-bar-bg {
            width: 100%;
            height: 6px;
            background: #EADCE5;
            border-radius: 100px;
            overflow: hidden;
            position: relative;
        }

        .progress-bar-fill {
            height: 100%;
            background: linear-gradient(90deg, var(--blue), var(--pink));
            width: 0%;
            transition: width 0.3s ease;
        }

        .progress-counter {
            text-align: right;
            font-size: 11px;
            color: var(--soft);
            margin-bottom: 4px;
            font-weight: 600;
        }

        /* RESULT STATS */
        .stats {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 15px;
            margin-bottom: 15px;
        }

        .stat {
            padding: 15px;
            border-radius: 18px;
            background: white;
            border: 1px solid var(--border);
            text-align: center;
        }

        .stat-title {
            font-size: 11px;
            font-weight: 700;
            color: var(--soft);
            text-transform: uppercase;
        }

        .stat-value {
            font-family: Poppins;
            font-size: 28px;
            font-weight: 900;
            margin-top: 5px;
        }

        .stat-avg .stat-value { color: var(--pink); }
        .stat-mode .stat-value { color: var(--blue); }

        .stat-unit {
            font-size: 12px;
            color: var(--soft);
            margin-top: -3px;
            display: block;
        }

        /* BUTTONS */
        button {
            width: 100%;
            padding: 15px;
            border: none;
            border-radius: 16px;
            font-family: Poppins;
            font-size: 15px;
            font-weight: 800;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        button:disabled {
            opacity: 0.5;
            cursor: not_allowed;
        }

        .btn-save {
            background: linear-gradient(120deg, var(--pink), var(--pink-dark));
            color: white;
            margin-bottom: 10px;
        }
        
        .btn-save:hover { background: var(--pink-dark); }

        .btn-clean {
            background: none;
            color: var(--soft);
            font-size: 13px;
            font-weight: 600;
            padding: 5px;
            margin-top: 5px;
        }

        .btn-wa {
            background: var(--green);
            color: white;
            margin-top: 20px;
        }
        .btn-wa:hover { background: #1da851; }

        .btn-delete {
            background: none;
            border: 1px solid var(--border);
            color: var(--soft);
            margin-top: 10px;
            font-size: 13px;
        }
        .btn-delete:hover { background: #FFF1F1; border-color: var(--red); color: var(--red); }

        /* RIWAYAT (FITUR LAMA) */
        .history-list {
            margin-top: 10px;
        }

        .history-item {
            padding: 12px;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .history-item:last-child { border-bottom: none; }

        .history-meta {
            font-size: 12px;
            color: var(--soft);
        }

        .history-avg {
            font-family: Poppins;
            font-weight: 800;
            font-size: 16px;
            color: var(--pink);
        }

        .empty-state {
            text-align: center;
            color: var(--soft);
            font-size: 13px;
            padding: 20px;
        }

    </style>
</head>
<body>

<div class="app">

    <!-- HEADER -->
    <div class="header card">
        <div class="brand">
            <div class="logo">G</div>
            <h1>Kalibrasi Gacoan Singosari</h1>
        </div>
    </div>

    <!-- CARD INPUT -->
    <div class="card">
        <div class="title">
            <span class="dot"></span> Input Kalibrasi
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
            <div class="item item-other" data-item="Lainnya">+ Lainnya</div>
        </div>
        <input type="text" id="otherItemInput" placeholder="Ketik item lain...">
        <br>

        <div class="row">
            <div>
                <label for="shift">Shift</label>
                <select id="shift">
                    <option>Morning</option>
                    <option selected>Middle</option>
                    <option>Evening</option>
                </select>
            </div>
            <div>
                <label>Tanggal</label>
                <input type="text" id="dateDisplay" readonly style="background:#F1F5F9; color:var(--soft);">
            </div>
        </div>

        <label>Gramasi (10 Percobaan)</label>
        <div class="gram-grid" id="gramGrid"></div>

        <!-- PROGRESS BAR & COUNTER (FITUR BARU) -->
        <div class="progress-container">
            <div class="progress-counter" id="progressCounter">0 / 10 terisi</div>
            <div class="progress-bar-bg">
                <div class="progress-bar-fill" id="progressBarFill"></div>
            </div>
        </div>

        <div class="stats">
            <div class="stat stat-avg">
                <div class="stat-title">AVERAGE</div>
                <div class="stat-value" id="avg">-</div>
                <span class="stat-unit">gram</span>
            </div>
            <div class="stat stat-mode">
                <div class="stat-title">MODUS</div>
                <div class="stat-value" id="mode">-</div>
                <span class="stat-unit" id="modeCount">gram</span>
            </div>
        </div>

        <!-- Tombol Simpan ke Sheet & Lokal -->
        <button class="btn-save" id="saveCalibration">
            <!-- Icon floppy disk sederhana -->
            <svg width="18" height="18" viewBox="0 0 448 512" fill="white"><path d="M433.941 129.941l-83.882-83.882A48 48 0 0 0 316.118 32H48C21.49 32 0 53.49 0 80v352c0 26.51 21.49 48 48 48h352c0 26.51 48 21.49 48-48V161.882a48 48 0 0 0-14.059-33.941zM224 416c-35.343 0-64-28.657-64-64s28.657-64 64-64 64 28.657 64 64-28.657 64-64 64zm96-304.52V212c0 6.627-5.373 12-12 12H76c-6.627 0-12-5.373-12-12V108c0-6.627 5.373-12 12-12h228.52c3.183 0 6.235 1.264 8.485 3.515l3.515 3.515c2.251 2.251 3.515 5.303 3.515 8.485z"/></svg>
            Simpan Kalibrasi
        </button>
        <button class="btn-clean" id="clearForm">Bersihkan form</button>
    </div>

    <!-- CARD RIWAYAT (FITUR LAMA) -->
    <div class="card">
        <div class="title">
            <span class="dot"></span> Riwayat Hari Ini
        </div>
        <div id="historyList" class="history-list">
            <div class="empty-state">Belum ada kalibrasi tersimpan hari ini.</div>
        </div>
    </div>

    <!-- TOMBOL KIRIM WA (ALUR PICKER) -->
    <button class="btn-wa" id="sendRekapWa">
        <!-- Icon WA -->
        <svg width="18" height="18" viewBox="0 0 448 512" fill="white"><path d="M380.9 97.1C339 55.1 283.2 32 223.9 32c-122.4 0-222 99.6-222 222 0 39.1 10.2 77.3 29.6 111L3 480l117.7-30.9c32.4 17.7 68.9 27 106.1 27h.1c122.3 0 224.1-99.6 224.1-222 0-59.3-25.2-115-67.1-117zm-157 338.7h-.1c-34 0-67.3-9.2-96.5-26.3L97 435.8l24.6-89.9c-18.7-29.7-28.6-64.1-28.6-99.3 0-108.6 88.4-197 197-197 52.6 0 102 20.5 139.1 57.6s57.6 86.5 57.6 139.1c0 108.6-88.4 197-197 197zm105.1-147.5c-5.8-2.9-34.1-16.8-39.4-18.8-5.3-2-9.1-2.9-13.1 2.9-3.9 5.8-15.3 18.8-18.8 22.7-3.4 3.9-6.9 4.4-12.6 1.5-5.8-2.9-24.3-8.9-46.3-28.5-17.1-15.3-28.7-34.2-32.1-40-3.4-5.8-.4-8.9 2.5-11.8 2.6-2.6 5.8-6.8 8.7-10.1 2.9-3.4 3.9-5.8 5.8-9.6 1.9-3.9.9-7.4-.5-10.3-1.4-2.9-13.1-31.5-18-43.3-4.7-11.5-9.6-9.9-13.1-10.1-3.4-.2-7.3-.2-11.2-.2-3.9 0-10.3 1.5-15.7 7.4-5.4 5.8-20.7 20.3-20.7 49.4 0 29.1 21.2 57.2 24.1 61.2 2.9 3.9 41.5 63.4 100.6 88.9 14.1 6.1 25.1 9.8 33.6 12.5 14.1 4.5 27 3.9 37.1 2.4 11.2-1.7 34.1-13.9 38.9-27.3 4.8-13.4 4.8-24.9 3.4-27.3-1.5-2.4-5.3-3.9-11.2-6.8z"/></svg>
        Susun Rekap & Kirim ke WhatsApp
    </button>
    <button class="btn-delete" id="deleteAllHistory">Hapus Semua Riwayat Hari Ini</button>

</div>

<script>
// ================================
// CONFIG
// ================================
// GANTI DENGAN URL API ANDA DARI GOOGLE APPS SCRIPT
const API_URL = "https://script.google.com/macros/s/AKfycbyTuteQYO1o4hYqS_dY_iURED4fYvrBTKo29EIolJuKLQGLTW9fFPzxKg2Os7qEjYXA/exec";
const NAMA_RESTO = "Gacoan Singosari";

let selectedItem = "Acin";
let isOtherItem = false;
let gramValues = [];

// PENYIMPANAN LOKAL (UNTUK REKAP)
let dailyHistory = [];

const gramGrid = document.getElementById("gramGrid");
const avgText = document.getElementById("avg");
const modeText = document.getElementById("mode");
const modeCountText = document.getElementById("modeCount");
const otherItemInput = document.getElementById("otherItemInput");
const dateDisplay = document.getElementById("dateDisplay");
const progressCounter = document.getElementById("progressCounter");
const progressBarFill = document.getElementById("progressBarFill");
const historyList = document.getElementById("historyList");

// Tampilkan Tanggal Hari Ini
const today = new Date();
dateDisplay.value = today.toLocaleDateString('id-ID', { weekday: 'short', year: 'numeric', month: 'short', day: 'numeric' });

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
            updateProgress();
            calculate();
        });
    });
    updateProgress(); // init 0/10
}

createGramInput();

// ================================
// PEMILIHAN ITEM
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
// UPDATE PROGRESS (FITUR BARU)
// ================================
function updateProgress() {
    let filledCount = gramValues.filter(x => x !== undefined && !isNaN(x)).length;
    progressCounter.innerHTML = `${filledCount} / 10 terisi`;
    let percentage = (filledCount / 10) * 100;
    progressBarFill.style.width = `${percentage}%`;
}

// ================================
// MESIN KALKULASI (MEAN & MODUS)
// ================================
function calculate(){
    let data = gramValues.filter(x => x !== undefined && !isNaN(x));

    if(data.length === 0){
        avgText.innerHTML = "-"; modeText.innerHTML = "-"; modeCountText.innerHTML = "gram"; return;
    }

    // 1. MEAN
    let avg = data.reduce((a, b) => a + b, 0) / data.length;
    avgText.innerHTML = avg.toFixed(2);

    // 2. MODUS
    let modeMap = {}; let maxCount = 0; let modes = [];
    data.forEach(num => {
        modeMap[num] = (modeMap[num] || 0) + 1;
        if (modeMap[num] > maxCount) maxCount = modeMap[num];
    });
    for (let num in modeMap) {
        if (modeMap[num] === maxCount) modes.push(num);
    }
    if(maxCount > 1) {
        modeText.innerHTML = modes.join(", ");
        modeCountText.innerHTML = `gram (${maxCount}x)`;
    } else {
        modeText.innerHTML = "-"; modeCountText.innerHTML = "gram";
    }
}

// ================================
// SIMPAN KE SHEET & LOKAL (GABUNGAN FITUR)
// ================================
document.getElementById("saveCalibration").addEventListener("click", async function() {
    let crew = document.getElementById("crew").value.trim();
    let shift = document.getElementById("shift").value;
    let data = gramValues.filter(x => x !== undefined && !isNaN(x));
    
    let finalItemName = selectedItem;
    if (isOtherItem) {
        finalItemName = otherItemInput.value.trim();
        if (!finalItemName) finalItemName = "Lainnya (Tanpa Nama)";
    }

    // Validasi
    if(!crew){ alert("Isi nama crew dahulu"); return; }
    if(data.length < 10){ alert("Lengkapi 10 trial (10/10 terisi) sebelum menyimpan."); return; }

    this.disabled = true;
    this.innerHTML = "Menyimpan...";

    let avg = data.reduce((a, b) => a + b, 0) / 10;
    
    // Hitung Modus untuk data lokal
    let modeMap = {}; let maxCount = 0; let modes = [];
    data.forEach(num => { modeMap[num] = (modeMap[num] || 0) + 1; if (modeMap[num] > maxCount) maxCount = modeMap[num]; });
    for (let num in modeMap) { if (modeMap[num] === maxCount) modes.push(num); }
    let modusFormatted = maxCount > 1 ? `${modes.join(", ")} (${maxCount}x)` : "-";

    // 1. Simpan Secara Lokal (Untuk Rekap WA)
    let logData = {
        time: new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }),
        crew: crew,
        item: finalItemName,
        shift: shift,
        data: data.join(", "),
        average: avg.toFixed(2),
        modus: modusFormatted
    };
    dailyHistory.push(logData);
    updateHistoryUI();

    // 2. Kirim ke Google Spreadsheet (Untuk Database Pusat)
    let payload = {
        NAMA_RESTO: NAMA_RESTO,
        CREW: crew,
        ITEM: finalItemName,
        SHIFT: shift,
        TRIAL_DATA: data.join(", "),
        AVERAGE: avg.toFixed(2)
    };

    try {
        await fetch(API_URL, {
            method: "POST",
            mode: "no-cors",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(payload)
        });
        console.log("Data berhasil dikirim ke Spreadsheet");
        alert(`Kalibrasi ${finalItemName} tersimpan di riwayat dan database.`);
    } catch(err) {
        console.error("Gagal mengirim ke Spreadsheet", err);
        alert(`Kalibrasi ${finalItemName} tersimpan di riwayat lokal, namun GAGAL mengirim ke database pusat. Cek koneksi.`);
    }

    // Reset Form Gramasi
    createGramInput();
    avgText.innerHTML = "-"; modeText.innerHTML = "-"; modeCountText.innerHTML = "gram";
    if (isOtherItem) otherItemInput.value = "";
    
    this.disabled = false;
    this.innerHTML = `<svg width="18" height="18" viewBox="0 0 448 512" fill="white"><path d="M433.941 129.941l-83.882-83.882A48 48 0 0 0 316.118 32H48C21.49 32 0 53.49 0 80v352c0 26.51 21.49 48 48 48h352c0 26.51 48 21.49 48-48V161.882a48 48 0 0 0-14.059-33.941zM224 416c-35.343 0-64-28.657-64-64s28.657-64 64-64 64 28.657 64 64-28.657 64-64 64zm96-304.52V212c0 6.627-5.373 12-12 12H76c-6.627 0-12-5.373-12-12V108c0-6.627 5.373-12 12-12h228.52c3.183 0 6.235 1.264 8.485 3.515l3.515 3.515c2.251 2.251 3.515 5.303 3.515 8.485z"/></svg> Simpan Kalibrasi`;
});

// Update Tampilan Riwayat Lokal
function updateHistoryUI() {
    if (dailyHistory.length === 0) {
        historyList.innerHTML = '<div class="empty-state">Belum ada kalibrasi tersimpan hari ini.</div>';
        return;
    }

    historyList.innerHTML = "";
    dailyHistory.forEach(x => {
        let item = `
            <div class="history-item">
                <div>
                    <b>${x.item}</b>
                    <div class="history-meta">${x.time} | Crew: ${x.crew} | ${x.shift}</div>
                </div>
                <div class="history-avg">${x.average}g</div>
            </div>
        `;
        historyList.innerHTML += item;
    });
}

// Bersihkan Form
document.getElementById("clearForm").addEventListener("click", () => {
    createGramInput();
    avgText.innerHTML = "-"; modeText.innerHTML = "-"; modeCountText.innerHTML = "gram";
});

// Hapus Riwayat Lokal
document.getElementById("deleteAllHistory").addEventListener("click", () => {
    if(confirm("Hapus semua riwayat lokal hari ini? (Data di spreadsheet tidak ikut terhapus)")) {
        dailyHistory = [];
        updateHistoryUI();
    }
});

// ================================
// KIRIM REKAP WA (FITUR LAMA YANG DIINGINKAN)
// ================================
document.getElementById("sendRekapWa").addEventListener("click", () => {
    if(dailyHistory.length === 0) { alert("Belum ada data kalibrasi hari ini untuk direkap."); return; }
    
    let tglFormated = today.toLocaleDateString('id-ID', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
    
    // FORMAT TEKS REKAP WA
    let textWa = `*REKAP KALIBRASI GACOAN*\n`;
    textWa += `--------------------------------\n`;
    textWa += `*Resto:* ${NAMA_RESTO}\n`;
    textWa += `*Tanggal:* ${tglFormated}\n`;
    textWa += `*Total Item:* ${dailyHistory.length}\n`;
    textWa += `--------------------------------\n\n`;

    dailyHistory.forEach((x, i) => {
        textWa += `*${i+1}. ${x.item}* (Crew: ${x.crew})\n`;
        textWa += `Shift: ${x.shift} | Jam: ${x.time}\n`;
        textWa += `Avg: *${x.average} gram* | Modus: *${x.modus}*\n`;
        textWa += `Data: [${x.data}]\n\n`;
    });
    
    textWa += `--------------------------------\n`;
    textWa += `_Laporan otomatis sistem QC_`;

    // ALUR BARU: Buka WhatsApp picker tanpa phone=
    let urlWa = `https://api.whatsapp.com/send?text=${encodeURIComponent(textWa)}`;
    
    window.open(urlWa, '_blank');
});
</script>

</body>
</html>
