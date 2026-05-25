<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Gacoan Akurasi Sistem</title>
    <!-- Tailwind CSS v4 -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- Library Scan Barcode Performa Tinggi -->
    <script src="https://unpkg.com/html5-qrcode"></script>
    <!-- Firebase SDK (Versi CDN Stabil) -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <style>
        /* Efek Kedip Hijau saat Packer Berhasil Scan */
        @keyframes flash-success {
            0% { border-color: rgba(34, 197, 94, 1); box-shadow: 0 0 20px rgba(34, 197, 94, 0.6); }
            100% { border-color: rgba(75, 85, 99, 1); box-shadow: none; }
        }
        .hidden { display: none; }
    </style>
</head>
<body class="bg-gray-900 text-white min-h-screen flex flex-col justify-between antialiased select-none">

    <!-- TOP BAR / HEADER -->
    <header class="bg-gray-800/90 backdrop-blur-md border-b border-gray-700 px-4 py-4 sticky top-0 z-50 flex items-center justify-between shadow-lg">
        <div class="flex items-center space-x-3">
            <div id="status-indicator" class="w-3 h-3 bg-gray-500 rounded-full"></div>
            <div>
                <h1 class="text-sm font-bold tracking-wider text-gray-100" id="app-title">GACOAN AKURASI SISTEM</h1>
                <p class="text-[10px] text-gray-400 font-medium uppercase tracking-wider" id="app-subtitle">Silakan Pilih Peran</p>
            </div>
        </div>
        <button id="btn-back" onclick="goBack()" class="hidden bg-gray-700 hover:bg-gray-600 active:scale-95 text-xs font-semibold px-3 py-1.5 rounded-xl border border-gray-600 transition">
            ← Menu
        </button>
    </header>

    <!-- KONTEN UTAMA (RESPONSIF MOBILE-FIRST) -->
    <main class="flex-grow flex flex-col p-4 max-w-md mx-auto w-full justify-center">
        
        <!-- ==================== VIEW 0: MENU UTAMA ==================== -->
        <div id="menu-view" class="space-y-4 w-full">
            <div class="text-center mb-6">
                <p class="text-gray-400 text-sm">Cek Produk </p>
            </div>
            
            <button onclick="switchView('packer')" class="w-full bg-linear-to-br from-amber-500 to-orange-600 hover:from-amber-600 hover:to-orange-700 active:scale-98 text-white font-bold py-5 px-6 rounded-2xl shadow-xl transition-all duration-150 flex flex-col items-center border border-orange-400/20">
                <span class="text-2xl mb-1">📦</span>
                <span class="text-lg tracking-wide">TIM PACKER</span>
                <span class="text-[11px] font-normal opacity-80 mt-0.5">(Isi Menu / Scan Barcode)</span>
            </button>
            
            <button onclick="switchView('presenter')" class="w-full bg-linear-to-br from-blue-500 to-indigo-600 hover:from-blue-600 hover:to-indigo-700 active:scale-98 text-white font-bold py-5 px-6 rounded-2xl shadow-xl transition-all duration-150 flex flex-col items-center border border-blue-400/20">
                <span class="text-2xl mb-1">💁‍♂️</span>
                <span class="text-lg tracking-wide">TIM PRESENTER</span>
                <span class="text-[11px] font-normal opacity-80 mt-0.5">(Cek Kroscek Pesanan)</span>
            </button>
        </div>

        <!-- ==================== VIEW 1: TAMPILAN PACKER ==================== -->
        <div id="packer-view" class="hidden w-full space-y-5">
            <!-- Kamera Frame -->
            <div class="relative w-full aspect-square bg-black rounded-3xl overflow-hidden border-2 border-gray-700 shadow-2xl transition-all duration-300" id="scanner-wrapper">
                <div id="interactive" class="w-full h-full object-cover"></div>
                
                <!-- Bingkai Sasaran Barcode -->
                <div class="absolute inset-0 pointer-events-none flex flex-col justify-between p-10">
                    <div class="flex justify-between">
                        <div class="w-8 h-8 border-t-4 border-l-4 border-orange-500 rounded-tl-lg"></div>
                        <div class="w-8 h-8 border-t-4 border-r-4 border-orange-500 rounded-tr-lg"></div>
                    </div>
                    <div class="w-full h-0.5 bg-orange-500 opacity-60 shadow-[0_0_8px_#f97316] animate-bounce"></div>
                    <div class="flex justify-between">
                        <div class="w-8 h-8 border-b-4 border-l-4 border-orange-500 rounded-bl-lg"></div>
                        <div class="w-8 h-8 border-b-4 border-r-4 border-orange-500 rounded-br-lg"></div>
                    </div>
                </div>

                <div class="absolute top-4 left-1/2 transform -translate-x-1/2 bg-black/70 backdrop-blur-sm px-4 py-1 rounded-full border border-white/10 text-center pointer-events-none">
                    <p class="text-[11px] font-medium text-gray-300 tracking-wider">Arahkan Kamera ke Barcode</p>
                </div>
            </div>

            <!-- Umpan Balik Hasil Scan -->
            <div class="bg-gray-800 border border-gray-700 rounded-2xl p-4 flex items-center justify-between shadow-lg">
                <div class="flex items-center space-x-3 overflow-hidden">
                    <div class="w-10 h-10 bg-gray-700 rounded-xl flex items-center justify-center text-lg" id="product-icon">📦</div>
                    <div class="overflow-hidden">
                        <p class="text-xs text-gray-400 font-semibold tracking-wider uppercase">Scan Terakhir</p>
                        <h3 class="font-bold text-base text-gray-100 truncate" id="scanned-product-name">Siap scan produk...</h3>
                    </div>
                </div>
                <span id="scan-badge" class="hidden text-[10px] font-bold text-green-400 bg-green-500/10 px-2.5 py-1 rounded-md border border-green-500/20 shrink-0">TERKIRIM</span>
            </div>

            <!-- Tombol Senter Sampingan -->
            <button onclick="toggleFlash()" class="w-full bg-gray-800 hover:bg-gray-700 border border-gray-700 text-xs font-semibold py-3 px-4 rounded-xl transition duration-150 flex items-center justify-center space-x-2 active:scale-98">
                <span>🔦</span> <span>Nyalakan Senter (Android Only)</span>
            </button>
        </div>

        <!-- ==================== VIEW 2: TAMPILAN PRESENTER ==================== -->
        <div id="presenter-view" class="hidden w-full flex flex-col space-y-4">
            <div class="bg-gray-800 border border-gray-700 rounded-2xl p-4 shadow-lg flex-grow flex flex-col min-h-[350px]">
                <h2 class="text-sm font-bold text-gray-400 uppercase tracking-wider mb-3 border-b border-gray-700 pb-2 flex justify-between items-center">
                    <span>Daftar Menu Masuk</span>
                    <span id="item-counter" class="bg-blue-500/10 text-blue-400 text-xs px-2 py-0.5 rounded-md border border-blue-500/20">0 Jenis</span>
                </h2>
                
                <!-- Container Item Ter-scan (Dinamis) -->
                <div id="presenter-list" class="divide-y divide-gray-700 overflow-y-auto pr-1 flex-grow space-y-1">
                    <p class="text-gray-500 text-center py-10 text-sm">Menunggu Tim Packer memasukkan produk...</p>
                </div>
            </div>

            <!-- Tombol Clear Transaksi -->
            <button onclick="resetOrder()" class="w-full bg-red-600/20 hover:bg-red-600 text-red-400 hover:text-white border border-red-500/20 font-semibold py-3.5 rounded-xl transition active:scale-98 shadow-md text-sm">
                Clear / Pesanan Selesai
            </button>
        </div>

    </main>

    <!-- FOOTER -->
    <footer class="bg-gray-950/40 p-3 text-center text-[10px] text-gray-500 border-t border-gray-800 tracking-wider">
        MIE GACOAN QC LOGISTICS • SINGOSARI
    </footer>

    <!-- ==================== CODE ENGINE (JAVASCRIPT) ==================== -->
    <script>
        // 1. KONFIGURASI FIREBASE (Ganti dengan config milik Anda dari Firebase Console)
        const firebaseConfig = {
            apiKey: "AIzaSyDummyKeyJanganLupaDigantiYa12345",
            authDomain: "gacoan-qc.firebaseapp.com",
            databaseURL: "https://gacoan-qc-default-rtdb.firebaseio.com/", 
            projectId: "gacoan-qc",
            storageBucket: "gacoan-qc.appspot.com",
            messagingSenderId: "123456789",
            appId: "1:123456789:web:abcdef"
        };
        
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();
        const orderRef = database.ref('current_order');

        // Master Menu Gacoan berdasarkan Barcode (Sesuaikan dengan SKU aslinya)
        const gacoanMenu = {
            "89910011": "Mie Suit",
            "89910022": "Mie Hompimpa Lvl 1",
            "89910023": "Mie Hompimpa Lvl 2",
            "89910033": "Mie Gacoan Lvl 1",
            "89910034": "Mie Gacoan Lvl 2",
            "89920011": "Siomay (Isi 3)",
            "89920022": "Udang Keju (Isi 3)",
            "89920033": "Udang Rambutan (Isi 3)",
            "89930011": "Es Gobak Sodor",
            "89930022": "Es Teklek"
        };

        let html5QrCode = null;
        let isFlashOn = false;

        // 2. SISTEM NAVIGASI & ROUTING TAMPILAN
        function switchView(view) {
            document.getElementById('menu-view').classList.add('hidden');
            document.getElementById('packer-view').classList.add('hidden');
            document.getElementById('presenter-view').classList.add('hidden');
            document.getElementById('btn-back').classList.remove('hidden');

            const indicator = document.getElementById('status-indicator');

            if (view === 'packer') {
                document.getElementById('packer-view').classList.remove('hidden');
                document.getElementById('app-title').innerText = "STASIUN PACKER";
                document.getElementById('app-subtitle').innerText = "Input Mode (Scan Kamera)";
                indicator.className = "w-3 h-3 bg-amber-500 rounded-full animate-pulse";
                startHighSpeedScanner();
            } else if (view === 'presenter') {
                document.getElementById('presenter-view').classList.remove('hidden');
                document.getElementById('app-title').innerText = "STASIUN PRESENTER";
                document.getElementById('app-subtitle').innerText = "Validasi & Crosscheck Mode";
                indicator.className = "w-3 h-3 bg-blue-500 rounded-full animate-pulse";
                stopScanner();
                listenToDatabase();
            }
        }

        function goBack() {
            document.getElementById('menu-view').classList.remove('hidden');
            document.getElementById('packer-view').classList.add('hidden');
            document.getElementById('presenter-view').classList.add('hidden');
            document.getElementById('btn-back').classList.add('hidden');
            
            document.getElementById('app-title').innerText = "GACOAN QC SYSTEM";
            document.getElementById('app-subtitle').innerText = "Silakan Pilih Peran";
            document.getElementById('status-indicator').className = "w-3 h-3 bg-gray-500 rounded-full";
            
            stopScanner();
            orderRef.off(); // Matikan listener database agar hemat kuota data internet
        }

        // 3. OPTIMALISASI ENGINE SCANNER (Kamera Android & iOS)
        function startHighSpeedScanner() {
            if (!html5QrCode) {
                html5QrCode = new Html5Qrcode("interactive");
                
                const config = {
                    fps: 20, // Dipercepat 2x lipat untuk scan secepat kilat
                    qrbox: function(width, height) {
                        // Kotak fokus melebar pipih, sangat ideal menangkap barcode batangan produk makanan
                        return { width: width * 0.85, height: height * 0.4 };
                    },
                    aspectRatio: 1.0, // Mengunci rasio kotak di iOS Safari agar kamera tidak gepeng/stretching
                    experimentalFeatures: {
                        useBarCodeDetectorIfSupported: true // Memakai chip hardware bawaan OS ponsel jika didukung
                    }
                };

                html5QrCode.start({ facingMode: "environment" }, config, onScanSuccess)
                .catch(err => {
                    console.error("Gagal Akses Kamera: ", err);
                    document.getElementById('scanned-product-name').innerText = "Izin Kamera Ditolak / Tidak Ditemukan";
                });
            }
        }

        function stopScanner() {
            if (html5QrCode) {
                html5QrCode.stop().then(() => { html5QrCode = null; })
                .catch(err => console.log("Gagal mematikan kamera:", err));
            }
        }

        function onScanSuccess(decodedText, decodedResult) {
            // Beri jeda 1.2 detik setelah scan berhasil agar tidak terjadi input berulang secara tidak sengaja
            html5QrCode.pause(true);
            setTimeout(() => { if(html5QrCode) html5QrCode.resume(); }, 1200);

            const namaMenu = gacoanMenu[decodedText] || "Menu Baru (" + decodedText + ")";
            
            // Efek Kedipan Hijau pada bingkai kamera
            const wrapper = document.getElementById('scanner-wrapper');
            wrapper.style.animation = 'flash-success 0.5s ease-out';
            setTimeout(() => { wrapper.style.animation = ''; }, 500);

            // Perbarui UI Packer
            document.getElementById('scanned-product-name').innerText = namaMenu;
            const badge = document.getElementById('scan-badge');
            badge.classList.remove('hidden');

            // Haptic Feedback (Vibrasi HP) - Membantu Packer tahu data masuk tanpa harus melihat HP
            if (navigator.vibrate) navigator.vibrate(80);

            // Kirim langsung ke Firebase secara Real-time
            orderRef.push({
                barcode: decodedText,
                nama: namaMenu,
                timestamp: firebase.database.ServerValue.TIMESTAMP
            });
        }

        // 4. LOGIKA REAL-TIME PRESENTER (CHECKLIST OUTPUT)
        function listenToDatabase() {
            orderRef.on('value', (snapshot) => {
                const listContainer = document.getElementById('presenter-list');
                listContainer.innerHTML = '';

                if (snapshot.exists()) {
                    const data = snapshot.val();
                    const summary = {};
                    let totalJenis = 0;

                    // Mengelompokkan item yang sama dan menjumlahkan kuantitasnya (Qty)
                    Object.keys(data).forEach(key => {
                        const item = data[key];
                        if (summary[item.nama]) {
                            summary[item.nama].qty += 1;
                        } else {
                            summary[item.nama] = { 
                                qty: 1, 
                                time: new Date(item.timestamp).toLocaleTimeString('id-ID', {hour: '2-digit', minute:'2-digit'}) 
                            };
                            totalJenis++;
                        }
                    });

                    document.getElementById('item-counter').innerText = `${totalJenis} Jenis`;

                    // Render ke bentuk item list checklist
                    Object.keys(summary).forEach(menuName => {
                        const item = summary[menuName];
                        listContainer.innerHTML += `
                            <label class="flex items-center justify-between py-3.5 px-2 hover:bg-gray-700/30 rounded-xl transition cursor-pointer select-none">
                                <div class="flex items-center space-x-3.5">
                                    <input type="checkbox" class="w-5 h-5 text-blue-600 bg-gray-700 border-gray-600 rounded-lg focus:ring-blue-500 focus:ring-offset-gray-800 accent-blue-500">
                                    <span class="text-sm font-semibold text-gray-200">${menuName}</span>
                                </div>
                                <div class="flex items-center space-x-3">
                                    <span class="bg-red-500/10 text-red-400 font-extrabold px-3 py-0.5 rounded-full text-xs border border-red-500/20">
                                        x${item.qty}
                                    </span>
                                    <span class="text-[11px] text-gray-500 font-medium">${item.time}</span>
                                </div>
                            </label>
                        `;
                    });
                } else {
                    document.getElementById('item-counter').innerText = '0 Jenis';
                    listContainer.innerHTML = '<p class="text-gray-500 text-center py-10 text-sm">Belum ada item yang di-scan.</p>';
                }
            });
        }

        function resetOrder() {
            if (confirm("Apakah pesanan ini sudah selesai dicek dan ingin dikosongkan untuk pesanan berikutnya?")) {
                orderRef.set(null);
            }
        }

        // Fitur Kendali Senter Flashlight (Khusus web browser berbasis Android Chrome)
        function toggleFlash() {
            if (html5QrCode && html5QrCode.getState() === 2) { 
                isFlashOn = !isFlashOn;
                html5QrCode.applyVideoConstraints({
                    advanced: [{ torch: isFlashOn }]
                }).catch(() => console.log("Perangkat atau OS memblokir kontrol senter eksternal browser."));
            }
        }
    </script>
</body>
</html>
