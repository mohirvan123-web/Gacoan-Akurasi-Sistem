<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gacoan QC System</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <style>
        body { font-family: sans-serif; }
        .hidden { display: none; }
    </style>
</head>
<body class="bg-gray-100 min-h-screen flex flex-col justify-between">

    <header class="bg-red-600 text-white p-4 shadow-md text-center">
        <h1 class="text-xl font-bold tracking-wide">GACOAN QC SYSTEM</h1>
        <p class="text-xs opacity-80" id="current-role">Pilih Peran Anda</p>
    </header>

    <main class="flex-grow p-4 max-w-md mx-auto w-full">
        
        <div id="menu-view" class="space-y-4 pt-10">
            <button onclick="switchView('packer')" class="w-full bg-orange-500 hover:bg-orange-600 text-white font-bold py-4 px-6 rounded-2xl shadow-lg transition duration-200 text-lg flex flex-col items-center">
                <span>📦 TIM PACKER</span>
                <span class="text-xs font-normal opacity-80 mt-1">(Scan Barcode Produk)</span>
            </button>
            
            <button onclick="switchView('presenter')" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 px-6 rounded-2xl shadow-lg transition duration-200 text-lg flex flex-col items-center">
                <span>💁‍♂️ TIM PRESENTER</span>
                <span class="text-xs font-normal opacity-80 mt-1">(Cek & Validasi Pesanan)</span>
            </button>
        </div>

        <div id="packer-view" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-md">
                <h2 class="font-bold text-gray-700 mb-2 flex justify-between items-center">
                    <span>Scanner Kamera</span>
                    <button onclick="goBack()" class="text-sm text-red-500 font-normal">← Kembali</button>
                </h2>
                <div id="reader" class="overflow-hidden rounded-lg bg-gray-200" style="width: 100%;"></div>
            </div>

            <div class="bg-white p-4 rounded-xl shadow-md">
                <h3 class="font-bold text-sm text-gray-500 uppercase tracking-wider mb-2">Terakhir di-scan:</h3>
                <div id="scan-result" class="text-lg font-bold text-gray-800 bg-gray-50 p-3 rounded-lg border border-dashed border-gray-300 text-center">
                    Belum ada produk dikemas
                </div>
            </div>
        </div>

        <div id="presenter-view" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-md">
                <h2 class="font-bold text-gray-700 flex justify-between items-center mb-4">
                    <span>Daftar Menu Siap Sajir</span>
                    <button onclick="goBack()" class="text-sm text-red-500 font-normal">← Kembali</button>
                </h2>
                
                <div id="presenter-list" class="divide-y divide-gray-200 max-h-96 overflow-y-auto">
                    <p class="text-gray-400 text-center py-4">Menunggu tim packer memasukkan menu...</p>
                </div>
            </div>

            <button onclick="resetOrder()" class="w-full bg-gray-800 text-white text-sm font-semibold py-3 rounded-xl hover:bg-gray-900 transition shadow">
                Clear / Pesanan Baru
            </button>
        </div>

    </main>

    <footer class="bg-white p-2 text-center text-xs text-gray-400 border-t border-gray-200">
        Mie Gacoan Operations © 2026
    </footer>

    <script>
        // 1. KONFIGURASI FIREBASE (Ganti dengan akun Firebase-mu sendiri nanti)
        // Ini adalah konfigurasi dummy (bisa dipakai uji coba lokal sementara, namun wajib diganti agar real-time antar HP berfungsi)
        const firebaseConfig = {
            apiKey: "AIzaSyDummyKeyJanganLupaDigantiYa12345",
            authDomain: "gacoan-qc.firebaseapp.com",
            databaseURL: "https://gacoan-qc-default-rtdb.firebaseio.com/", // Ganti URL ini dengan RTDB milikmu
            projectId: "gacoan-qc",
            storageBucket: "gacoan-qc.appspot.com",
            messagingSenderId: "123456789",
            appId: "1:123456789:web:abcdef"
        };
        
        // Inisialisasi Firebase
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();
        const orderRef = database.ref('current_order');

        // Objek Pembantu Nama Menu berdasarkan Barcode (Sesuaikan dengan SKU / Barcode Mie Gacoan)
        const menuDatabase = {
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

        let html5QrcodeScanner = null;

        // 2. NAVIGASI TAMPILAN
        function switchView(view) {
            document.getElementById('menu-view').classList.add('hidden');
            document.getElementById('packer-view').classList.add('hidden');
            document.getElementById('presenter-view').classList.add('hidden');

            if (view === 'packer') {
                document.getElementById('packer-view').classList.remove('hidden');
                document.getElementById('current-role').innerText = "Mode: TIM PACKER (SCANNER)";
                startScanner();
            } else if (view === 'presenter') {
                document.getElementById('presenter-view').classList.remove('hidden');
                document.getElementById('current-role').innerText = "Mode: TIM PRESENTER (MONITOR)";
                stopScanner();
                listenToDatabase();
            }
        }

        function goBack() {
            document.getElementById('menu-view').classList.remove('hidden');
            document.getElementById('packer-view').classList.add('hidden');
            document.getElementById('presenter-view').classList.add('hidden');
            document.getElementById('current-role').innerText = "Pilih Peran Anda";
            stopScanner();
        }

        // 3. LOGIKA TIM PACKER (SCANNER BARCODE)
        function startScanner() {
            if (!html5QrcodeScanner) {
                html5QrcodeScanner = new Html5QrcodeScanner("reader", { 
                    fps: 10, 
                    qrbox: { width: 250, height: 150 },
                    rememberLastUsedCamera: true
                });
                
                html5QrcodeScanner.render(onScanSuccess, onScanFailure);
            }
        }

        function stopScanner() {
            if (html5QrcodeScanner) {
                html5QrcodeScanner.clear().then(() => {
                    html5QrcodeScanner = null;
                }).catch(error => console.error("Gagal stop scanner:", error));
            }
        }

        function onScanSuccess(decodedText, decodedResult) {
            // Deteksi menu berdasarkan database lokal, jika tidak terdaftar pakai kode aslinya
            const namaMenu = menuDatabase[decodedText] || "Menu Asing (" + decodedText + ")";
            
            document.getElementById('scan-result').innerText = `✅ Terkirim: ${namaMenu}`;
            
            // Push data langsung ke Firebase Realtime Database
            orderRef.push({
                barcode: decodedText,
                nama: namaMenu,
                timestamp: firebase.database.ServerValue.TIMESTAMP
            });

            // Beri efek getar singkat di HP jika didukung
            if (navigator.vibrate) navigator.vibrate(100);
        }

        function onScanFailure(error) {
            // Diabaikan saja agar tidak memenuhi console saat kamera sedang mencari kode
        }

        // 4. LOGIKA TIM PRESENTER (MONITORING DATA)
        function listenToDatabase() {
            // Fungsi on('value') akan otomatis terpicu setiap kali ada data baru di Firebase
            orderRef.on('value', (snapshot) => {
                const listContainer = document.getElementById('presenter-list');
                listContainer.innerHTML = ''; // Reset tampilan list

                if (snapshot.exists()) {
                    const data = snapshot.val();
                    
                    // Kelompokkan data yang sama untuk hitung quantity (Qty)
                    const summary = {};
                    
                    Object.keys(data).forEach(key => {
                        const item = data[key];
                        if (summary[item.nama]) {
                            summary[item.nama].qty += 1;
                        } else {
                            summary[item.nama] = { qty: 1, time: new Date(item.timestamp).toLocaleTimeString('id-ID', {hour: '2-digit', minute:'2-digit'}) };
                        }
                    });

                    // Render ke bentuk HTML checklist
                    Object.keys(summary).forEach(menuName => {
                        const item = summary[menuName];
                        listContainer.innerHTML += `
                            <div class="flex items-center justify-between py-3">
                                <div class="flex items-center space-x-3">
                                    <input type="checkbox" class="w-5 h-5 text-blue-600 rounded border-gray-300 focus:ring-blue-500">
                                    <span class="text-gray-800 font-medium">${menuName}</span>
                                </div>
                                <div class="flex items-center space-x-2">
                                    <span class="bg-red-100 text-red-600 font-bold px-2.5 py-0.5 rounded-full text-sm">x${item.qty}</span>
                                    <span class="text-xs text-gray-400">${item.time}</span>
                                </div>
                            </div>
                        `;
                    });
                } else {
                    listContainer.innerHTML = '<p class="text-gray-400 text-center py-4">Belum ada item yang di-scan.</p>';
                }
            });
        }

        // Fungsi Presenter untuk menghapus orderan setelah selesai / transaksi baru
        function resetOrder() {
            if (confirm("Apakah pesanan ini sudah selesai dikroscek dan ingin dikosongkan?")) {
                orderRef.set(null);
            }
        }
    </script>
</body>
</html>
