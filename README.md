<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Gacoan Delivery & QC System</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <script type="text/javascript" src="https://unpkg.com/@zxing/library@latest"></script>
    <style>
        .hidden { display: none; }
        /* Memastikan video kamera responsif dan pas di dalam kotak */
        #interactive video {
            width: 100%;
            height: 100%;
            object-fit: cover;
            border-radius: 1rem;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col justify-between antialiased font-sans">

    <div class="bg-blue-700 text-white px-5 pt-2 pb-1 flex justify-between items-center text-xs font-semibold tracking-wider">
        <span id="live-clock">09:41</span>
        <div class="flex space-x-1.5 items-center">
            <span>📶</span><span>🔋</span>
        </div>
    </div>

    <main class="flex-grow flex flex-col max-w-md mx-auto w-full bg-white shadow-xl overflow-hidden relative">
        
        <div id="role-view" class="p-6 flex flex-col justify-center items-center my-auto space-y-6 w-full">
            <div class="text-center space-y-2">
                <div class="w-16 h-16 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center text-3xl mx-auto shadow-sm">📦</div>
                <h2 class="text-xl font-extrabold text-slate-900 tracking-wide">GACOAN acc SYSTEM</h2>
                <p class="text-xs text-slate-500 max-w-[250px] mx-auto">Pilih stasiun kerja Anda untuk memulai manajemen pesanan keluar.</p>
            </div>
            <div class="w-full space-y-3">
                <button onclick="switchRole('packer')" class="w-full bg-blue-600 hover:bg-blue-700 active:scale-98 text-white font-bold py-4 rounded-xl transition shadow-md">
                    <span>📦 TIM PACKER</span>
                </button>
                <button onclick="switchRole('presenter')" class="w-full bg-slate-800 hover:bg-slate-900 active:scale-98 text-white font-bold py-4 rounded-xl transition shadow-md">
                    <span>💁‍♂️ TIM PRESENTER (MONITOR)</span>
                </button>
            </div>
        </div>

        <div id="packer-master-container" class="hidden flex-grow flex flex-col">
            
            <div id="packer-step-1" class="flex-grow flex flex-col justify-between p-5">
                <div class="space-y-6">
                    <div class="space-y-1">
                        <h2 class="text-2xl font-black text-slate-900 tracking-tight">Pesanan Keluar</h2>
                        <p class="text-xs text-slate-400 font-medium">Track pesanan barang keluar / menu gacoan</p>
                    </div>

                    <div class="bg-slate-50 border border-slate-200 rounded-2xl p-4 space-y-4 shadow-xs">
                        <div class="flex items-center space-x-2 text-blue-600 font-bold text-sm">
                            <span>👤</span> <span>Data Pemesan</span>
                        </div>
                        <div class="space-y-1.5">
                            <label class="text-xs font-bold text-slate-500">Nama Customer / Pemesan</label>
                            <input type="text" id="cust-name-input" placeholder="Masukkan nama customer / nomor meja" class="w-full bg-white border border-slate-200 rounded-xl px-4 py-3 text-sm focus:outline-none focus:border-blue-500 shadow-xs">
                        </div>
                        <div class="space-y-1.5">
                            <label class="text-xs font-bold text-slate-500">Catatan (Opsional)</label>
                            <textarea id="cust-note-input" rows="3" placeholder="Masukkan catatan jika ada" class="w-full bg-white border border-slate-200 rounded-xl px-4 py-3 text-sm focus:outline-none focus:border-blue-500 resize-none shadow-xs"></textarea>
                        </div>
                    </div>
                </div>

                <button onclick="packerGoToStep2()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-xl shadow-lg flex items-center justify-center space-x-2 transition mt-4">
                    <span>LANJUT KE SCAN</span> <span>→</span>
                </button>
            </div>

            <div id="packer-step-2" class="hidden flex-grow flex flex-col justify-between p-4 space-y-4">
                <div class="bg-slate-50 border border-slate-200 rounded-xl p-3 flex justify-between items-center shadow-xs">
                    <div class="flex items-center space-x-3">
                        <div class="w-8 h-8 bg-blue-600 text-white rounded-full flex items-center justify-center text-xs font-bold">👤</div>
                        <div>
                            <p class="text-[10px] text-slate-400 font-bold uppercase tracking-tight">Customer</p>
                            <h4 id="display-cust-name" class="text-sm font-bold text-slate-800">-</h4>
                        </div>
                    </div>
                    <button onclick="packerBackToStep1()" class="text-xs font-bold text-blue-600 bg-blue-50 border border-blue-200 px-3 py-1.5 rounded-lg hover:bg-blue-100">GANTI</button>
                </div>

                <div class="relative w-full aspect-[4/3] bg-black rounded-2xl overflow-hidden border border-slate-200 shadow-md">
                    <div id="interactive" class="w-full h-full">
                        <video id="video-preview"></video>
                    </div>
                    
                    <div class="absolute inset-0 pointer-events-none flex flex-col justify-between p-6 z-10">
                        <div class="flex justify-between">
                            <div class="w-6 h-6 border-t-4 border-l-4 border-blue-600 rounded-tl-sm"></div>
                            <div class="w-6 h-6 border-t-4 border-r-4 border-blue-600 rounded-tr-sm"></div>
                        </div>
                        <div class="w-full h-0.5 bg-red-500 opacity-90 shadow-[0_0_8px_#ef4444] animate-pulse"></div>
                        <div class="flex justify-between">
                            <div class="w-6 h-6 border-b-4 border-l-4 border-blue-600 rounded-bl-sm"></div>
                            <div class="w-6 h-6 border-b-4 border-r-4 border-blue-600 rounded-br-sm"></div>
                        </div>
                    </div>
                </div>

                <div class="flex items-center space-x-2 text-xs text-slate-400 font-bold justify-center">
                    <div class="h-px bg-slate-200 w-full"></div>
                    <span>atau</span>
                    <div class="h-px bg-slate-200 w-full"></div>
                </div>

                <div class="flex space-x-2">
                    <input type="text" id="manual-barcode-input" placeholder="Input barcode manual" class="flex-grow bg-slate-50 border border-slate-200 rounded-xl px-4 py-2.5 text-xs focus:outline-none focus:border-blue-500">
                    <button onclick="addBarcodeManual()" class="bg-blue-600 hover:bg-blue-700 text-white font-bold text-xs px-4 rounded-xl transition uppercase tracking-wider">TAMBAH</button>
                </div>

                <div class="flex-grow flex flex-col min-h-[150px] max-h-[220px]">
                    <div class="flex justify-between items-center mb-1.5 px-1">
                        <span class="text-xs font-bold text-slate-700" id="packer-list-count">Daftar Item (0)</span>
                        <button onclick="clearPackerTempList()" class="text-[10px] font-bold text-red-500 hover:underline">🗑 HAPUS SEMUA</button>
                    </div>
                    <div id="packer-temp-list" class="flex-grow overflow-y-auto border border-slate-100 rounded-xl divide-y divide-slate-100 bg-slate-50/50 p-2 space-y-1.5">
                        <p class="text-xs text-slate-400 text-center py-8 italic">Belum ada item yang di-scan.</p>
                    </div>
                </div>

                <button onclick="packerGoToStep3()" class="w-full bg-blue-700 hover:bg-blue-800 text-white font-bold py-3.5 rounded-xl shadow-md flex items-center justify-center space-x-2 text-sm transition">
                    <span>LANJUT KE SUBMIT</span> <span>→</span>
                </button>
            </div>

            <div id="packer-step-3" class="hidden flex-grow flex flex-col justify-between p-5 space-y-4">
                <div class="space-y-5 flex-grow overflow-y-auto pr-1">
                    <div class="text-center space-y-1.5 py-4">
                        <div class="w-12 h-12 bg-green-100 text-green-600 rounded-full flex items-center justify-center text-xl mx-auto shadow-xs">✓</div>
                        <h3 class="text-lg font-extrabold text-slate-900">Pesanan Berhasil Disubmit!</h3>
                        <p class="text-xs text-slate-400 font-medium">Data pesanan telah tersimpan ke monitor presenter.</p>
                    </div>

                    <div class="bg-slate-50 border border-slate-200 rounded-2xl p-4 space-y-3 shadow-xs text-xs">
                        <h4 class="font-bold text-slate-700 border-b border-slate-200 pb-1.5 text-sm">Ringkasan Pesanan</h4>
                        <div class="flex justify-between"><span class="text-slate-400 font-medium">Customer</span><span class="font-bold text-slate-800" id="summary-cust-name">-</span></div>
                        <div class="flex justify-between"><span class="text-slate-400 font-medium">Waktu Mulai</span><span class="font-semibold text-slate-700" id="summary-time">-</span></div>
                        <div class="flex justify-between"><span class="text-slate-400 font-medium">Total Item</span><span class="font-bold text-blue-600" id="summary-total-item">0 Barcode</span></div>
                        <div class="flex justify-between items-center"><span class="text-slate-400 font-medium">Status</span><span class="bg-green-100 text-green-700 font-bold px-2 py-0.5 rounded-md border border-green-200 text-[10px]">SELESAI</span></div>
                    </div>

                    <div class="space-y-2">
                        <h4 class="text-xs font-bold text-slate-600 px-1">Daftar Barcode</h4>
                        <div id="summary-barcode-list" class="divide-y divide-slate-100 border border-slate-100 rounded-xl p-2 bg-white max-h-48 overflow-y-auto space-y-1"></div>
                    </div>
                </div>

                <div class="space-y-2 pt-2">
                    <button onclick="resetToNewOrder()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3.5 rounded-xl shadow-md text-sm transition">
                        ➕ PESANAN BARU
                    </button>
                </div>
            </div>
        </div>

        <div id="presenter-master-container" class="hidden flex-grow flex flex-col p-4 space-y-4">
            <div class="bg-slate-800 text-white border border-slate-700 rounded-2xl p-4 shadow-lg flex-grow flex flex-col min-h-[400px]">
                <div class="border-b border-slate-700 pb-3 mb-3 flex justify-between items-center">
                    <div>
                        <h2 class="text-sm font-black tracking-wide text-slate-300 uppercase">MONITOR ANTRIAN PACKING</h2>
                        <p class="text-[10px] text-slate-400 font-medium">Real-time Sinkronisasi Mie Gacoan</p>
                    </div>
                    <span id="presenter-order-count" class="bg-blue-500/20 text-blue-400 text-xs font-bold px-2.5 py-1 rounded-md border border-blue-500/30">0 Pesanan</span>
                </div>
                <div id="presenter-live-orders" class="flex-grow overflow-y-auto space-y-4 pr-1">
                    <p class="text-slate-500 text-center py-20 text-sm italic">Belum ada pesanan masuk dari Tim Packer...</p>
                </div>
            </div>
            <button onclick="location.reload()" class="w-full bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold py-3 rounded-xl transition text-xs border border-slate-300">
                ← KEMBALI KE SELEKSI ROLE
            </button>
        </div>

    </main>

    <div class="bg-white border-t border-slate-200 px-6 py-3 max-w-md mx-auto w-full flex justify-around items-center shadow-lg text-slate-400 text-[11px] font-bold">
        <button onclick="location.reload()" class="flex flex-col items-center space-y-1 text-blue-600">
            <span class="text-xl">📋</span>
            <span>Pesanan Keluar</span>
        </button>
        <button onclick="alert('Fitur Riwayat dalam pengembangan')" class="flex flex-col items-center space-y-1 hover:text-slate-600 transition">
            <span class="text-xl">📁</span>
            <span>Riwayat</span>
        </button>
    </div>

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
        const rootOrdersRef = database.ref('gacoan_active_orders');

        // Master Database SKU Gacoan
        const gacoanSKUDatabase = {
            "8991000012345": "Mie Gacoan Lvl 1",
            "8991000056789": "Mie Hompimpa Lvl 3",
            "8991000098765": "Udang Keju (Isi 3)",
            "8992000011111": "Udang Rambutan",
            "8993000022222": "Es Gobak Sodor"
        };

        let codeReader = null;
        let packerTempItems = []; 
        let currentCustomerName = "";
        let currentCustomerNote = "";
        let lastScannedCode = "";
        let lastScannedTime = 0;

        // Jam Live digital bagian atas
        setInterval(() => {
            const now = new Date();
            document.getElementById('live-clock').innerText = now.toLocaleTimeString('id-ID', {hour: '2-digit', minute:'2-digit'});
        }, 1000);

        function switchRole(role) {
            document.getElementById('role-view').classList.add('hidden');
            if (role === 'packer') {
                document.getElementById('packer-master-container').classList.remove('hidden');
            } else if (role === 'presenter') {
                document.getElementById('presenter-master-container').classList.remove('hidden');
                listenToPresenterDatabase();
            }
        }

        function packerGoToStep2() {
            const name = document.getElementById('cust-name-input').value.trim();
            if (!name) {
                alert("Harap masukkan Nama Customer / No. Meja!");
                return;
            }
            currentCustomerName = name;
            currentCustomerNote = document.getElementById('cust-note-input').value.trim();
            
            document.getElementById('display-cust-name').innerText = currentCustomerName;
            document.getElementById('packer-step-1').classList.add('hidden');
            document.getElementById('packer-step-2').classList.remove('hidden');
            
            startZXing1DScanner();
        }

        function packerBackToStep1() {
            stopZXingScanner();
            document.getElementById('packer-step-2').classList.add('hidden');
            document.getElementById('packer-step-1').classList.remove('hidden');
        }

        // ================= KUNCI UTAMA SINKRONISASI BARCODE GARIS (1D) DECODER =================
        function startZXing1DScanner() {
            // Menggunakan BrowserBarcodeReader khusus untuk tipe Barcode Garis (1D)
            codeReader = new ZXing.BrowserBarcodeReader();
            
            codeReader.decodeFromVideoDevice(undefined, 'video-preview', (result, err) => {
                if (result) {
                    const code = result.text;
                    const now = Date.now();

                    // Mencegah scan ganda berturut-turut untuk item yang sama (Cooldown 1.5 detik)
                    if (code === lastScannedCode && (now - lastScannedTime) < 1500) {
                        return;
                    }

                    lastScannedCode = code;
                    lastScannedTime = now;

                    if (navigator.vibrate) navigator.vibrate(80); // Beri efek getar di HP
                    
                    insertItemToTempList(code);
                }
                if (err && !(err instanceof ZXing.NotFoundException)) {
                    console.error("Kesalahan Sensor Kamera:", err);
                }
            }).catch(err => {
                console.error("Gagal Mengakses Hardware Kamera:", err);
                alert("Kamera gagal diakses. Pastikan Anda berada di jaringan aman (HTTPS / GitHub Pages).");
            });
        }

        function stopZXingScanner() {
            if (codeReader) {
                codeReader.reset();
                codeReader = null;
            }
        }

        function addBarcodeManual() {
            const codeInput = document.getElementById('manual-barcode-input');
            const code = codeInput.value.trim();
            if (code) {
                insertItemToTempList(code);
                codeInput.value = "";
            }
        }

        function insertItemToTempList(barcodeStr) {
            const timeStr = new Date().toLocaleTimeString('id-ID', {hour: '2-digit', minute:'2-digit', second:'2-digit'});
            const namaMenu = gacoanSKUDatabase[barcodeStr] || "SKU Baru (" + barcodeStr + ")";
            
            packerTempItems.push({
                barcode: barcodeStr,
                nama: namaMenu,
                waktu: timeStr
            });
            renderPackerTempList();
        }

        function removeSingleTempItem(index) {
            packerTempItems.splice(index, 1);
            renderPackerTempList();
        }

        function clearPackerTempList() {
            packerTempItems = [];
            renderPackerTempList();
        }

        function renderPackerTempList() {
            const listContainer = document.getElementById('packer-temp-list');
            document.getElementById('packer-list-count').innerText = `Daftar Item (${packerTempItems.length})`;
            
            if (packerTempItems.length === 0) {
                listContainer.innerHTML = '<p class="text-xs text-slate-400 text-center py-8 italic">Belum ada item yang di-scan.</p>';
                return;
            }
            
            listContainer.innerHTML = "";
            packerTempItems.forEach((item, index) => {
                listContainer.innerHTML += `
                    <div class="bg-white border border-slate-100 rounded-xl p-3 flex justify-between items-center shadow-2xs">
                        <div>
                            <p class="text-xs font-bold text-slate-800">${item.nama}</p>
                            <p class="text-[10px] text-slate-400 font-mono mt-0.5">${item.waktu} • Code: ${item.barcode}</p>
                        </div>
                        <button onclick="removeSingleTempItem(${index})" class="text-slate-300 hover:text-red-500 font-bold px-2 text-sm transition">×</button>
                    </div>
                `;
            });
        }

        function packerGoToStep3() {
            if (packerTempItems.length === 0) {
                alert("Daftar item kosong! Harap scan minimal 1 produk.");
                return;
            }

            stopZXingScanner(); // Matikan kamera agar menghemat baterai HP tim dapur

            const timeFinal = new Date().toLocaleDateString('id-ID', {day: 'numeric', month: 'short', year: 'numeric'}) + ' ' + new Date().toLocaleTimeString('id-ID', {hour:'2-digit', minute:'2-digit'});

            rootOrdersRef.push({
                customer: currentCustomerName,
                catatan: currentCustomerNote,
                waktuMulai: timeFinal,
                items: packerTempItems
            });

            document.getElementById('summary-cust-name').innerText = currentCustomerName;
            document.getElementById('summary-time').innerText = timeFinal;
            document.getElementById('summary-total-item').innerText = `${packerTempItems.length} Barcode`;

            const summaryList = document.getElementById('summary-barcode-list');
            summaryList.innerHTML = "";
            packerTempItems.forEach((item, index) => {
                summaryList.innerHTML += `
                    <div class="flex items-center justify-between py-2 text-xs">
                        <div class="flex items-center space-x-2">
                            <span class="w-4 h-4 bg-emerald-50 text-emerald-600 font-bold rounded-sm text-[10px] flex items-center justify-center">${index + 1}</span>
                            <span class="font-bold text-slate-800">${item.nama}</span>
                        </div>
                        <span class="text-slate-400 font-mono text-[10px]">${item.waktu}</span>
                    </div>
                `;
            });

            document.getElementById('packer-step-2').classList.add('hidden');
            document.getElementById('packer-step-3').classList.remove('hidden');
        }

        function resetToNewOrder() {
            packerTempItems = [];
            document.getElementById('cust-name-input').value = "";
            document.getElementById('cust-note-input').value = "";
            document.getElementById('packer-step-3').classList.add('hidden');
            document.getElementById('packer-step-1').classList.remove('hidden');
        }

        // ================= MONITOR REAL-TIME SINKRONISASI PRESENTER =================
        function listenToPresenterDatabase() {
            rootOrdersRef.on('value', (snapshot) => {
                const container = document.getElementById('presenter-live-orders');
                container.innerHTML = "";

                if (snapshot.exists()) {
                    const allOrders = snapshot.val();
                    let count = 0;

                    Object.keys(allOrders).forEach(orderId => {
                        const order = allOrders[orderId];
                        count++;

                        const itemSummary = {};
                        order.items.forEach(it => {
                            itemSummary[it.nama] = (itemSummary[it.nama] || 0) + 1;
                        });

                        let itemsHtml = "";
                        Object.keys(itemSummary).forEach(name => {
                            itemsHtml += `
                                <label class="flex items-center justify-between py-2 px-1 hover:bg-slate-700/50 rounded-lg cursor-pointer transition">
                                    <div class="flex items-center space-x-2.5">
                                        <input type="checkbox" class="w-4 h-4 text-blue-600 bg-slate-700 border-slate-600 rounded focus:ring-0 accent-blue-500">
                                        <span class="text-xs font-semibold text-slate-200">${name}</span>
                                    </div>
                                    <span class="bg-blue-500 text-white font-black text-[11px] px-2.5 py-0.5 rounded-md">x${itemSummary[name]}</span>
                                </label>
                            `;
                        });

                        container.innerHTML += `
                            <div class="bg-slate-700/40 border border-slate-700 rounded-xl p-3.5 space-y-2.5">
                                <div class="flex justify-between items-start border-b border-slate-700 pb-2">
                                    <div>
                                        <h3 class="text-sm font-black text-white tracking-wide">👤 ${order.customer}</h3>
                                        <p class="text-[10px] text-slate-400 mt-0.5">⏱ ${order.waktuMulai} ${order.catatan ? '• 📝 ' + order.catatan : ''}</p>
                                    </div>
                                    <button onclick="clearSingleOrder('${orderId}')" class="text-[10px] bg-emerald-600 hover:bg-emerald-700 text-white px-2 py-1 rounded-md font-bold transition shadow-xs">DONE / SERAHKAN</button>
                                </div>
                                <div class="divide-y divide-slate-800/40 space-y-1">
                                    ${itemsHtml}
                                </div>
                            </div>
                        `;
                    });

                    document.getElementById('presenter-order-count').innerText = `${count} Pesanan`;
                } else {
                    document.getElementById('presenter-order-count').innerText = '0 Pesanan';
                    container.innerHTML = '<p class="text-slate-500 text-center py-20 text-sm italic">Belum ada pesanan masuk dari Tim Packer...</p>';
                }
            });
        }

        function clearSingleOrder(orderId) {
            if (confirm("Serahkan pesanan dan hapus dari monitor antrian?")) {
                rootOrdersRef.child(orderId).remove();
            }
        }
    </script>
</body>
</html>
