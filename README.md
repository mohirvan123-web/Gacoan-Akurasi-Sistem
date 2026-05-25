<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Gacoan AKURASI SISTEM</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <style>
        .hidden { display: none; }
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { 
            -webkit-appearance: none; margin: 0; 
        }
    </style>
</head>
<body class="bg-gray-900 text-white min-h-screen flex flex-col justify-between antialiased select-none">

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

    <main class="flex-grow flex flex-col p-4 max-w-md mx-auto w-full justify-center">
        
        <div id="menu-view" class="space-y-4 w-full">
            <button onclick="switchView('packer')" class="w-full bg-linear-to-br from-amber-500 to-orange-600 text-white font-bold py-5 px-6 rounded-2xl shadow-xl flex flex-col items-center border border-orange-400/20">
                <span class="text-2xl mb-1">📦</span>
                <span class="text-lg tracking-wide">TIM PACKER</span>
                <span class="text-[11px] font-normal opacity-80 mt-0.5">(Multi-Qty & Scan Mode)</span>
            </button>
            
            <button onclick="switchView('presenter')" class="w-full bg-linear-to-br from-blue-500 to-indigo-600 text-white font-bold py-5 px-6 rounded-2xl shadow-xl flex flex-col items-center border border-blue-400/20">
                <span class="text-2xl mb-1">💁‍♂️</span>
                <span class="text-lg tracking-wide">TIM PRESENTER</span>
                <span class="text-[11px] font-normal opacity-80 mt-0.5">(Validasi & Kroscek)</span>
            </button>
        </div>

        <div id="packer-view" class="hidden w-full space-y-4">
            
            <div id="camera-box" class="relative w-full aspect-square bg-black rounded-3xl overflow-hidden border-2 border-gray-700 shadow-2xl">
                <div id="interactive" class="w-full h-full object-cover"></div>
                
                <div class="absolute inset-0 pointer-events-none flex flex-col justify-between p-10">
                    <div class="flex justify-between"><div class="w-8 h-8 border-t-4 border-l-4 border-orange-500 rounded-tl-lg"></div><div class="w-8 h-8 border-t-4 border-r-4 border-orange-500 rounded-tr-lg"></div></div>
                    <div class="w-full h-0.5 bg-orange-500 opacity-60 shadow-[0_0_8px_#f97316] animate-bounce"></div>
                    <div class="flex justify-between"><div class="w-8 h-8 border-b-4 border-l-4 border-orange-500 rounded-bl-lg"></div><div class="w-8 h-8 border-b-4 border-r-4 border-orange-500 rounded-br-lg"></div></div>
                </div>
                <div class="absolute top-4 left-1/2 transform -translate-x-1/2 bg-black/70 backdrop-blur-sm px-4 py-1 rounded-full text-[11px] font-medium tracking-wider text-gray-300">
                    Scan Barcode Produk
                </div>
            </div>

            <div id="qty-box" class="hidden bg-gray-800 border-2 border-orange-500 rounded-3xl p-5 shadow-2xl space-y-5 animate-fadeIn">
                <div class="text-center">
                    <span class="text-[10px] bg-orange-500/20 text-orange-400 font-bold px-3 py-1 rounded-full border border-orange-500/30 tracking-widest uppercase">PRODUK TERKUNCI</span>
                    <h2 id="active-product-name" class="text-2xl font-extrabold text-white mt-2 tracking-wide">Nama Produk</h2>
                    <p id="active-barcode" class="text-xs text-gray-400 font-mono mt-0.5">SKU: -</p>
                </div>

                <div class="flex items-center justify-center space-x-4 py-2">
                    <button onclick="adjustQty(-1)" class="w-14 h-14 bg-gray-700 hover:bg-gray-600 active:scale-90 rounded-2xl text-2xl font-bold transition flex items-center justify-center select-none shadow-md">-</button>
                    <input type="number" id="input-qty" value="1" min="1" class="w-24 h-14 bg-gray-900 border border-gray-700 rounded-2xl text-center text-2xl font-black text-orange-400 focus:outline-none focus:border-orange-500 shadow-inner">
                    <button onclick="adjustQty(1)" class="w-14 h-14 bg-gray-700 hover:bg-gray-600 active:scale-90 rounded-2xl text-2xl font-bold transition flex items-center justify-center select-none shadow-md">+</button>
                </div>

                <div class="pt-2">
                    <button onclick="submitProductToFirebase()" class="w-full bg-linear-to-r from-orange-500 to-amber-500 hover:from-orange-600 hover:to-amber-600 active:scale-95 text-white font-black py-4 rounded-xl transition shadow-xl tracking-wider text-base flex items-center justify-center space-x-2">
                        <span>📥</span> <span>ADD / PINDAH MENU</span>
                    </button>
                </div>
            </div>

            <div class="bg-gray-800/50 border border-gray-800 rounded-2xl p-4">
                <p class="text-xs font-bold text-gray-400 tracking-wider uppercase mb-2">Log Scan Terakhir Anda:</p>
                <div id="packer-log-text" class="text-sm font-medium text-gray-300 italic">Belum ada menu yang dikirim.</div>
            </div>
        </div>

        <div id="presenter-view" class="hidden w-full flex flex-col space-y-4">
            <div class="bg-gray-800 border border-gray-700 rounded-2xl p-4 shadow-lg flex-grow flex flex-col min-h-[350px]">
                <h2 class="text-sm font-bold text-gray-400 uppercase tracking-wider mb-3 border-b border-gray-700 pb-2 flex justify-between items-center">
                    <span>Daftar Menu Masuk</span>
                    <span id="item-counter" class="bg-blue-500/10 text-blue-400 text-xs px-2 py-0.5 rounded-md border border-blue-500/20">0 Jenis</span>
                </h2>
                
                <div id="presenter-list" class="divide-y divide-gray-700 overflow-y-auto pr-1 flex-grow space-y-1">
                    <p class="text-gray-500 text-center py-10 text-sm">Menunggu Tim Packer memasukkan produk...</p>
                </div>
            </div>

            <button onclick="resetOrder()" class="w-full bg-red-600/20 hover:bg-red-600 text-red-400 hover:text-white border border-red-500/20 font-semibold py-3.5 rounded-xl transition active:scale-98 shadow-md text-sm">
                Clear / Pesanan Selesai
            </button>
        </div>

    </main>

    <footer class="bg-gray-950/40 p-3 text-center text-[10px] text-gray-500 border-t border-gray-800 tracking-wider">
        MIE GACOAN QC LOGISTICS • SINGOSARI
    </footer>

    <script>
        // 1. FIREBASE CONFIG
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

        // SKU Database Mie Gacoan
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
        let currentScannedItem = null; // Menyimpan data produk yang sedang aktif di-scan

        // 2. NAVIGATION SYSTEMS
        function switchView(view) {
            document.getElementById('menu-view').classList.add('hidden');
            document.getElementById('packer-view').classList.add('hidden');
            document.getElementById('presenter-view').classList.add('hidden');
            document.getElementById('btn-back').classList.remove('hidden');

            const indicator = document.getElementById('status-indicator');

            if (view === 'packer') {
                document.getElementById('packer-view').classList.remove('hidden');
                document.getElementById('app-title').innerText = "STASIUN PACKER";
                document.getElementById('app-subtitle').innerText = "Mode Input Cepat (Scan + Qty)";
                indicator.className = "w-3 h-3 bg-amber-500 rounded-full animate-pulse";
                resetPackerUI(); // Pastikan kamera menyala di awal
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
            orderRef.off();
        }

        // 3. PACKER CONTROLLER (SCAN + MULTI QTY LOGIC)
        function startHighSpeedScanner() {
            if (!html5QrCode) {
                html5QrCode = new Html5Qrcode("interactive");
                const config = {
                    fps: 25, // Dioptimalkan lebih cepat lagi
                    qrbox: function(width, height) { return { width: width * 0.85, height: height * 0.4 }; },
                    aspectRatio: 1.0,
                    experimentalFeatures: { useBarCodeDetectorIfSupported: true }
                };
                html5QrCode.start({ facingMode: "environment" }, config, onScanSuccess).catch(err => console.error(err));
            }
        }

        function stopScanner() {
            if (html5QrCode) {
                html5QrCode.stop().then(() => { html5QrCode = null; }).catch(err => console.log(err));
            }
        }

        function onScanSuccess(decodedText, decodedResult) {
            // Haptic Feedback (Vibrasi Pendek) tanda barcode masuk
            if (navigator.vibrate) navigator.vibrate(60);

            const namaMenu = gacoanMenu[decodedText] || "Menu Baru (" + decodedText + ")";
            
            // Simpan produk yang berhasil discan ke variabel temporary
            currentScannedItem = {
                barcode: decodedText,
                nama: namaMenu
            };

            // Matikan kamera terlebih dahulu agar HP tidak panas dan hemat baterai saat input angka Qty
            html5QrCode.stop().then(() => {
                html5QrCode = null;
                
                // Switch UI: Sembunyikan kamera, munculkan kotak Qty
                document.getElementById('camera-box').classList.add('hidden');
                document.getElementById('qty-box').classList.remove('hidden');

                // Set isi informasi produk aktif
                document.getElementById('active-product-name').innerText = namaMenu;
                document.getElementById('active-barcode').innerText = `SKU: ${decodedText}`;
                document.getElementById('input-qty').value = 1; // Default qty = 1
            }).catch(err => console.log(err));
        }

        // Fungsi Tombol + dan -
        function adjustQty(amount) {
            const qtyInput = document.getElementById('input-qty');
            let currentVal = parseInt(qtyInput.value) || 1;
            currentVal += amount;
            if (currentVal < 1) currentVal = 1;
            qtyInput.value = currentVal;
        }

        // Fungsi Tombol "ADD / PINDAH MENU" (Kirim Data Berdasarkan Qty yang ditentukan)
        function submitProductToFirebase() {
            if (!currentScannedItem) return;

            const finalQty = parseInt(document.getElementById('input-qty').value) || 1;

            // Push ke Firebase sebanyak jumlah Qty yang diinput secara simultan
            for (let i = 0; i < finalQty; i++) {
                orderRef.push({
                    barcode: currentScannedItem.barcode,
                    nama: currentScannedItem.nama,
                    timestamp: firebase.database.ServerValue.TIMESTAMP
                });
            }

            // Update Log singkat di bawah untuk Packer
            document.getElementById('packer-log-text').innerText = `✔ Terkirim: ${currentScannedItem.nama} (x${finalQty})`;

            // Beri getar dua kali tanda sukses terkirim ke sistem
            if (navigator.vibrate) navigator.vibrate([40, 40, 40]);

            // Reset tampilan ke kamera lagi untuk scan menu berikutnya
            resetPackerUI();
        }

        function resetPackerUI() {
            currentScannedItem = null;
            document.getElementById('qty-box').classList.add('hidden');
            document.getElementById('camera-box').classList.remove('hidden');
            startHighSpeedScanner();
        }

        // 4. PRESENTER CONTROLLER (CHECKLIST SINKRONISASI)
        function listenToDatabase() {
            orderRef.on('value', (snapshot) => {
                const listContainer = document.getElementById('presenter-list');
                listContainer.innerHTML = '';

                if (snapshot.exists()) {
                    const data = snapshot.val();
                    const summary = {};
                    let totalJenis = 0;

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

                    Object.keys(summary).forEach(menuName => {
                        const item = summary[menuName];
                        listContainer.innerHTML += `
                            <label class="flex items-center justify-between py-4 px-2 hover:bg-gray-700/30 rounded-xl transition cursor-pointer select-none">
                                <div class="flex items-center space-x-3.5">
                                    <input type="checkbox" class="w-6 h-6 text-blue-600 bg-gray-700 border-gray-600 rounded-lg accent-blue-500">
                                    <span class="text-sm font-bold text-gray-200">${menuName}</span>
                                </div>
                                <div class="flex items-center space-x-3">
                                    <span class="bg-red-500 text-white font-black px-3 py-1 rounded-lg text-sm shadow-md">
                                        x${item.qty}
                                    </span>
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
    </script>
</body>
</html>
