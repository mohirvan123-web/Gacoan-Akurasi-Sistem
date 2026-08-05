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

        /* Styling spesifik untuk "+ Lainnya" */
        .item-other.active {
            background: var(--blue);
            border-color: var(--blue);
        }

        /* INPUT UNTUK ITEM LAINNYA (SESUAI GAMBAR) */
        #otherItemInput {
            margin-top: 10px;
            border-color: var(--pink);
            display: none; /* Tersembunyi secara default */
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

        /* STATUS */
        .status {
            margin-top: 12px;
            padding: 12px;
            border-radius: 15px;
            text-align: center;
            font-weight: 800;
            font-size: 14px;
        }

        .status.good {
            background: #DCFCE7;
            color: #15803D;
        }

        .status.warning {
            background: #FEF3C7;
            color: #B45309;
        }

        .status.bad {
            background: #FEE2E2;
            color: #B91C1C;
        }

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

        .stat.blue {
            background: #EAFBFF;
        }

        .stat-title {
            font-size: 11px;
            font-weight: 700;
            color: var(--soft);
        }

        .stat-value {
            font-family: Poppins;
            font-size: 25px;
            font-weight: 900;
            margin-top: 5px;
        }

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
        }

        .btn-primary {
            background: linear-gradient(120deg, var(--pink), var(--pink-dark));
            color: white;
        }

        /* DASHBOARD */
        .dashboard-box {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
        }

        .dashboard-card {
            padding: 15px;
            border-radius: 18px;
            background: white;
            border: 1px solid var(--border);
            text-align: center;
        }

        .dashboard-number {
            font-family: Poppins;
            font-size: 25px;
            font-weight: 900;
        }

        .rank-item {
            display: flex;
            justify-content: space-between;
            padding: 12px;
            border-bottom: 1px solid var(--border);
        }

        .rank-item:last-child {
            border-bottom: none;
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
                <div class="subtitle">Gacoan Quality Control</div>
            </div>
        </div>
    </div>

    <!-- OUTLET -->
    <div class="card">
        <div class="title">
            <span class="dot"></span> Outlet
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
            <span class="dot"></span> Input Kalibrasi
        </div>

        <label for="crew">Nama Crew</label>
        <input id="crew" placeholder="mis. Edo, Ibnu, Via...">
        <br><br>

        <label>Item Kalibrasi</label>
        <div class="items">
            <div class="item active" data-item="Acin">Acin (8g)</div>
            <div class="item" data-item="Bagor">Bagor (4g)</div>
            <div class="item" data-item="Basic">Basic (12g)</div>
            <div class="item" data-item="Minyak Mie">Minyak Mie (14g)</div>
            <div class="item" data-item="Adonan">Adonan (15g)</div>
            <!-- Tombol Lainnya disesuaikan seperti gambar -->
            <div class="item item-other" data-item="Lainnya">+ Lainnya</div>
        </div>
        
        <!-- INPUT FIELD TAMBAHAN UNTUK "LAINNYA" (SESUAI GAMBAR) -->
        <input type="text" id="otherItemInput" placeholder="Atau ketik item lain...">

    </div>

    <!-- GRAMASI -->
    <div class="card">
        <div class="title">
            <span class="dot"></span> 10 Trial Gramasi
        </div>

        <div class="gram-grid" id="gramGrid"></div>
        <div id="status" class="status">Menunggu input</div>

        <div class="stats">
            <div class="stat">
                <div class="stat-title">Average</div>
                <div class="stat-value" id="avg">-</div>
            </div>
            <div class="stat blue">
                <div class="stat-title">Benchmark</div>
                <div class="stat-value" id="benchmark">-</div>
            </div>
        </div>

        <button class="btn-primary" id="save">Simpan Kalibrasi</button>
    </div>

    <!-- DASHBOARD -->
    <div class="card">
        <div class="title">
            <span class="dot"></span> Dashboard Hari Ini
        </div>

        <div class="dashboard-box">
            <div class="dashboard-card">
                <div>Total Trial</div>
                <div class="dashboard-number" id="totalTrial">0</div>
            </div>
            <div class="dashboard-card">
                <div>Accuracy %</div>
                <div class="dashboard-number" id="accuracy">0%</div>
            </div>
        </div>

        <br>
        <div class="title">
            <span class="dot"></span> Ranking Crew
        </div>
        <div id="ranking">
            Belum ada data
        </div>
    </div>

</div>

<script>
// ================================
// CONFIG
// ================================
// GANTI DENGAN URL API ANDA SENDIRI
const API_URL = "https://script.google.com/macros/s/AKfycbyTuteQYO1o4hYqS_dY_iURED4fYvrBTKo29EIolJuKLQGLTW9fFPzxKg2Os7qEjYXA/exec";

const BENCHMARK = {
    "Acin": 8,
    "Bagor": 4,
    "Basic": 12,
    "Minyak Mie": 14,
    "Adonan": 15,
    "Lainnya": 0 // Lainnya dianggap tanpa standar (0)
};

let selectedItem = "Acin";
let isOtherItem = false;
let gramValues = [];
let history = [];

const gramGrid = document.getElementById("gramGrid");
const statusBox = document.getElementById("status");
const avgText = document.getElementById("avg");
const benchmarkText = document.getElementById("benchmark");
const otherItemInput = document.getElementById("otherItemInput");

// ================================
// NAMA OUTLET
// ================================
function getOutletName(code){
    const outlet = {
        MLGMON: "MALANG MONDOROKO",
        MLGJAK: "MALANG JAKARTA",
        MLGSOE: "MALANG SOEKARNO HATTA",
        MLGRON: "MALANG RONGGOWARSITO",
        MLGDIR: "MALANG DIRGANTARA"
    };
    return outlet[code] || code;
}

// ================================
// KIRIM DATA KE SPREADSHEET
// ================================
async function sendToSheet(data){
    try {
        await fetch(API_URL, {
            method: "POST",
            mode: "no-cors",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify(data)
        });
        console.log("Data terkirim");
    } catch(err) {
        console.log(err);
    }
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
            calculate();
        });
    });
}

createGramInput();

// ================================
// ITEM SELECT LOGIC (UPDATED)
// ================================
document.querySelectorAll(".items .item").forEach(btn => {
    btn.onclick = function(){
        // Reset status tombol
        document.querySelectorAll(".items .item").forEach(x => x.classList.remove("active"));
        
        // Aktifkan tombol yang diklik
        this.classList.add("active");
        
        selectedItem = this.dataset.item;
        
        // Logika khusus untuk "+ Lainnya"
        if (selectedItem === "Lainnya") {
            isOtherItem = true;
            otherItemInput.style.display = "block"; // Tampilkan input field pink
            otherItemInput.focus();
            benchmarkText.innerHTML = "Kalibrasi Bebas";
        } else {
            isOtherItem = false;
            otherItemInput.style.display = "none"; // Sembunyikan input field pink
            otherItemInput.value = ""; // Reset isi input
            benchmarkText.innerHTML = BENCHMARK[selectedItem] + " g";
        }
        
        calculate();
    };
});

benchmarkText.innerHTML = BENCHMARK[selectedItem] + " g";

// ================================
// CALCULATION ENGINE
// ================================
function calculate(){
    let data = gramValues.filter(x => x !== undefined && !isNaN(x));

    if(data.length === 0){
        avgText.innerHTML = "-";
        statusBox.className = "status";
        statusBox.innerHTML = "Menunggu input";
        return;
    }

    let avg = data.reduce((a, b) => a + b, 0) / data.length;
    avgText.innerHTML = avg.toFixed(2) + " g";

    // Jika item lainnya, tidak ada standar akurasi
    if(isOtherItem){
        statusBox.className = "status good";
        statusBox.innerHTML = "✓ RECORD DATA";
        document.getElementById("accuracy").innerHTML = "100.0%";
        return;
    }

    let target = BENCHMARK[selectedItem];
    let deviation = Math.abs(avg - target);

    if(deviation === 0){
        statusBox.className = "status good";
        statusBox.innerHTML = "✓ SESUAI BENCHMARK";
    } else if(deviation <= 1){
        statusBox.className = "status warning";
        statusBox.innerHTML = "⚠ SELISIH 1 GRAM";
    } else {
        statusBox.className = "status bad";
        statusBox.innerHTML = "✕ OUT OF STANDARD";
    }

    calculateAccuracy(avg, target);
}

// ================================
// ACCURACY SCORE
// ================================
function calculateAccuracy(avg, target){
    if(target === 0) {
        document.getElementById("accuracy").innerHTML = "100.0%";
        return;
    }
    let accuracy = 100 - (Math.abs(avg - target) / target * 100);
    if(accuracy < 0) accuracy = 0;
    document.getElementById("accuracy").innerHTML = accuracy.toFixed(1) + "%";
}

// ================================
// RANKING CREW
// ================================
function renderRanking(){
    let rank = {};

    history.forEach(x => {
        if(!rank[x.CREW]){
            rank[x.CREW] = [];
        }
        rank[x.CREW].push(Number(x.SCORE));
    });

    let result = [];
    Object.keys(rank).forEach(name => {
        let avg = rank[name].reduce((a, b) => a + b, 0) / rank[name].length;
        result.push({
            name: name,
            score: avg
        });
    });

    result.sort((a, b) => b.score - a.score);

    let html = "";
    result.forEach((x, i) => {
        html += `
            <div class="rank-item">
                <b>${i+1}. ${x.name}</b>
                <span>${x.score.toFixed(1)}%</span>
            </div>
        `;
    });

    document.getElementById("ranking").innerHTML = html || "Belum ada data";
}

// ================================
// DASHBOARD COUNTER
// ================================
function updateDashboard(){
    document.getElementById("totalTrial").innerHTML = history.length;

    if(history.length > 0){
        let avgScore = history.reduce((a, b) => a + Number(b.SCORE), 0) / history.length;
        document.getElementById("accuracy").innerHTML = avgScore.toFixed(1) + "%";
    }
}

// ================================
// SAVE DATA (UPDATED)
// ================================
document.getElementById("save").addEventListener("click", () => {
    let crew = document.getElementById("crew").value.trim();
    let outlet = document.getElementById("outlet").value;
    let data = gramValues.filter(x => x !== undefined && !isNaN(x));
    
    // Tentukan nama item yang akan disimpan
    let finalItemName = selectedItem;
    if (isOtherItem) {
        finalItemName = otherItemInput.value.trim();
        if (!finalItemName) {
            finalItemName = "Lainnya (Tanpa Nama)";
        }
    }

    if(!crew){
        alert("Isi nama crew dahulu");
        return;
    }

    if(data.length < 10){
        alert("Lengkapi 10 trial");
        return;
    }

    let avg = data.reduce((a, b) => a + b, 0) / 10;
    
    let target, deviation, status, score;

    // Logika perhitungan data berbeda jika "+ Lainnya"
    if (isOtherItem) {
        target = 0;
        deviation = 0;
        status = "OK (Record)";
        score = 100; // Selalu 100% karena tidak ada pembanding
    } else {
        target = BENCHMARK[selectedItem];
        deviation = Math.abs(avg - target);
        status = "NG";
        if(deviation === 0){
            status = "OK";
        } else if(deviation <= 1){
            status = "WARNING";
        }
        score = 100 - (deviation / target * 100);
        if(score < 0) score = 0;
    }

    let result = {
        DATE: new Date().toLocaleString(),
        OUTLETCODE: outlet,
        ITEM: finalItemName, // Menggunakan nama dari input field jika ada
        CREW: crew,
        BENCHMARK: target,
        DATA: data.join(","),
        AVERAGE: avg.toFixed(2),
        DEVIATION: deviation.toFixed(2),
        STATUS: status,
        SCORE: score.toFixed(1)
    };

    history.push(result);

    sendToSheet({
        OUTLETCODE: result.OUTLETCODE,
        OUTLETNAME: getOutletName(result.OUTLETCODE),
        CREW: result.CREW,
        ITEM: result.ITEM,
        BENCHMARK: result.BENCHMARK === 0 ? "-" : result.BENCHMARK,
        TRIAL: result.DATA,
        AVERAGE: result.AVERAGE,
        DEVIATION: result.DEVIATION,
        STATUS: result.STATUS,
        SCORE: result.SCORE
    });

    renderRanking();
    updateDashboard();

    alert("Kalibrasi tersimpan untuk item: " + finalItemName);

    // Reset Form
    createGramInput();
    avgText.innerHTML = "-";
    statusBox.className = "status";
    statusBox.innerHTML = "Menunggu input";
    if (isOtherItem) {
        otherItemInput.value = "";
    }
});
</script>

</body>
</html>
