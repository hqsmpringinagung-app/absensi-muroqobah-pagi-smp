
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Presensi Pro - SMP Hamalatul Quran</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.23/jspdf.plugin.autotable.min.js"></script>
    <!-- Tailwind CSS for super interactive responsive layouts -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons for high quality visuals -->
    <script src="https://unpkg.com/lucide@latest"></script>
    
    <style>
        :root {
            --primary: #0f172a;
            --primary-light: #1e293b;
            --accent: #2563eb;
            --success: #16a34a;
            --warning: #ca8a04;
            --danger: #dc2626;
            --bg: #f8fafc;
            --card-bg: #ffffff;
        }

        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: var(--bg); 
            color: var(--primary);
            -webkit-font-smoothing: antialiased;
        }

        /* Custom Scrollbar for premium feel */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }

        /* Original Elegant Header styles updated with glassmorphism */
        .header { 
            background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 100%); 
            color: white; 
            border-bottom-left-radius: 32px; 
            border-bottom-right-radius: 32px;
            box-shadow: 0 10px 30px rgba(15, 23, 42, 0.15);
            position: relative;
            overflow: hidden;
        }
        .header::before {
            content: '';
            position: absolute;
            top: -50%; left: -20%;
            width: 300px; height: 300px;
            background: rgba(37, 99, 235, 0.2);
            border-radius: 50%;
            filter: blur(80px);
        }
        
        /* Grid kontainer disetel agar bersandingan (3 kolom di desktop, 2 di tablet, 1 di mobile) */
        .grid-container { 
            display: grid; 
            grid-template-columns: repeat(1, minmax(0, 1fr)); 
            gap: 24px; 
        }
        @media (min-width: 768px) {
            .grid-container {
                grid-template-columns: repeat(2, minmax(0, 1fr));
            }
        }
        @media (min-width: 1024px) {
            .grid-container {
                grid-template-columns: repeat(3, minmax(0, 1fr));
            }
        }
        
        /* Modern Cards */
        .class-box { 
            background: var(--card-bg); 
            border-radius: 24px; 
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.02), 0 8px 10px -6px rgba(0,0,0,0.02), inset 0 0 0 1px rgba(0,0,0,0.04);
            overflow: hidden; 
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .class-box:hover {
            transform: translateY(-4px);
            box-shadow: 0 20px 25px -5px rgba(0,0,0,0.05), 0 10px 10px -5px rgba(0,0,0,0.04);
        }
        .class-box h2 { 
            background: #f1f5f9; 
            padding: 18px; 
            font-size: 1.15rem; 
            font-weight: 700;
            letter-spacing: 0.5px;
            text-align: center; 
            margin: 0; 
            border-bottom: 1px solid #e2e8f0; 
            color: var(--primary-light);
        }
        
        .input-table { width: 100%; border-collapse: collapse; }
        .input-table td { padding: 14px 18px; border-bottom: 1px solid #f1f5f9; vertical-align: middle; }
        
        .student-row { cursor: pointer; transition: all 0.2s ease; }
        .student-row:hover { background-color: #f8fafc; }
        .name-text { font-weight: 600; font-size: 0.95rem; color: #334155; transition: color 0.2s ease; }
        
        .status-container { display: flex; align-items: center; justify-content: flex-end; gap: 12px; }
        .waktu-text { font-size: 0.75rem; font-weight: 700; color: #94a3b8; white-space: nowrap; transition: color 0.2s ease; }

        .check-icon { 
            width: 24px; height: 24px; border: 2px solid #cbd5e1; 
            border-radius: 50%; display: inline-flex; align-items: center; 
            justify-content: center; color: transparent; font-size: 0.75rem;
            transition: all 0.2s ease;
            background: #ffffff;
            flex-shrink: 0;
        }

        /* Color Badges for attendance */
        .status-hijau { background-color: #dcfce7 !important; }
        .status-hijau:hover { background-color: #bbf7d0 !important; }
        .status-hijau .name-text { color: #14532d; font-weight: 700; }
        .status-hijau .check-icon { background-color: var(--success); border-color: var(--success); color: white; transform: scale(1.05); }
        .status-hijau .waktu-text { color: #15803d !important; }

        .status-kuning { background-color: #fef9c3 !important; }
        .status-kuning:hover { background-color: #fef08a !important; }
        .status-kuning .name-text { color: #713f12; font-weight: 700; }
        .status-kuning .check-icon { background-color: var(--warning); border-color: var(--warning); color: white; transform: scale(1.05); }
        .status-kuning .waktu-text { color: #a16207 !important; }

        .status-merah { background-color: #fee2e2 !important; }
        .status-merah:hover { background-color: #fecaca !important; }
        .status-merah .name-text { color: #7f1d1d; font-weight: 700; }
        .status-merah .check-icon { background-color: var(--danger); border-color: var(--danger); color: white; transform: scale(1.05); }
        .status-merah .waktu-text { color: #b91c1c !important; }

        .modal { 
            position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
            background: rgba(15, 23, 42, 0.6); display: none; 
            justify-content: center; align-items: center; z-index: 1000; 
            backdrop-filter: blur(8px); animation: fadeIn 0.3s ease; 
        }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Custom gorgeous Toast notifications */
        #toast-container {
            position: fixed;
            bottom: 24px;
            right: 24px;
            z-index: 9999;
            display: flex;
            flex-direction: column;
            gap: 10px;
            pointer-events: none;
        }
        .toast-card {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-left: 5px solid #3b82f6;
            padding: 16px 20px;
            border-radius: 12px;
            box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1);
            color: #1e293b;
            font-weight: 600;
            font-size: 0.9rem;
            display: flex;
            align-items: center;
            gap: 12px;
            transform: translateY(100px);
            opacity: 0;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            pointer-events: auto;
        }
        .toast-card.show {
            transform: translateY(0);
            opacity: 1;
        }
    </style>
</head>
<body class="bg-slate-50">

    <!-- Custom Toast Container -->
    <div id="toast-container"></div>

    <!-- Custom Modal Dialog (Replacing dangerous confirm) -->
    <div id="confirmModal" class="modal">
        <div class="bg-white p-6 rounded-2xl shadow-xl w-11/12 max-w-md mx-auto text-center transform transition-all">
            <div class="w-16 h-16 bg-red-100 text-red-600 rounded-full flex items-center justify-center mx-auto mb-4 text-2xl">
                <i data-lucide="alert-triangle"></i>
            </div>
            <h3 class="text-lg font-bold text-slate-800 mb-2" id="confirmTitle">Konfirmasi</h3>
            <p class="text-slate-600 mb-6 text-sm" id="confirmMessage">Apakah anda yakin ingin melakukan tindakan ini?</p>
            <div class="flex gap-3 justify-center">
                <button id="btnConfirmCancel" class="px-5 py-2.5 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-sm transition-colors">Batal</button>
                <button id="btnConfirmYes" class="px-5 py-2.5 rounded-xl bg-red-600 hover:bg-red-700 text-white font-bold text-sm transition-colors">Ya, Hapus</button>
            </div>
        </div>
    </div>

    <!-- Main Header -->
    <div class="header px-6 py-8 text-center relative">
        <div class="max-w-7xl mx-auto">
            <h1 class="text-2xl md:text-3xl font-extrabold tracking-wider leading-tight">SMP HAMALATUL QURAN RINGINAGUNG</h1>
            <h2 class="text-md md:text-lg mt-2 opacity-90 font-medium tracking-wide">PRESENSI DIGITAL PRO</h2>
            
            <div class="mt-6 flex flex-col sm:flex-row items-center justify-center gap-4">
                <div id="label-tgl" class="opacity-90 text-sm font-semibold bg-white/10 px-5 py-2 rounded-full border border-white/10 shadow-sm">
                    Sabtu, 11 Juli 2026
                </div>
                <div id="label-jam" class="text-lg font-extrabold bg-white/20 px-6 py-2 rounded-full border border-white/20 shadow-md min-w-[120px] tabular-nums tracking-wider">
                    00:00:00
                </div>
            </div>

            <!-- Legend Section -->
            <div class="mt-6 flex flex-wrap justify-center gap-3">
                <span class="text-xs font-bold px-4 py-2 rounded-full bg-emerald-500 text-white shadow-md">🟢 07:00 - 07:20 Tepat Waktu</span>
                <span class="text-xs font-bold px-4 py-2 rounded-full bg-yellow-500 text-slate-950 shadow-md">🟡 07:21 - 07:45 Peringatan</span>
                <span class="text-xs font-bold px-4 py-2 rounded-full bg-rose-500 text-white shadow-md">🔴 07:46 - 08:30 Terlambat</span>
            </div>
        </div>
    </div>

    <!-- Controller Container for Dynamically adding Classes & Students -->
    <div class="max-w-7xl mx-auto px-4 mt-8">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            
            <!-- Admin Control Panel for Class/Students -->
            <div class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 flex flex-col justify-between">
                <div>
                    <div class="flex items-center gap-2 mb-4">
                        <span class="p-2 bg-indigo-100 text-indigo-600 rounded-xl">
                            <i data-lucide="settings"></i>
                        </span>
                        <h3 class="font-bold text-lg text-slate-800">Manajemen Kelas & Siswa</h3>
                    </div>
                    <p class="text-xs text-slate-500 mb-6 leading-relaxed">Tambahkan kelas baru, daftarkan nama siswa baru secara instan, atau pindahkan siswa/naikkan kelas massal secara otomatis.</p>
                </div>
                
                <div class="space-y-4">
                    <!-- Button trigger to show/hide dynamic forms -->
                    <button onclick="toggleForm('form-kelas')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-slate-50 hover:bg-slate-100 border border-slate-200 font-semibold text-sm transition-colors text-slate-700">
                        <span class="flex items-center gap-2"><i data-lucide="folder-plus" class="w-4 h-4 text-indigo-500"></i> Buat Kelas Baru</span>
                        <i data-lucide="chevron-down" class="w-4 h-4"></i>
                    </button>
                    <!-- Expandable Class Form -->
                    <div id="form-kelas" class="hidden bg-slate-50 p-4 rounded-xl border border-dashed border-slate-200 space-y-3">
                        <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide">Nama Kelas Baru</label>
                        <input type="text" id="input-nama-kelas" placeholder="Contoh: Kelas 10, Kelas 7B" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                        <button onclick="tambahKelasBaru()" class="w-full py-2 rounded-lg bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-xs transition-colors">Simpan Kelas</button>
                    </div>

                    <button onclick="toggleForm('form-siswa')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-slate-50 hover:bg-slate-100 border border-slate-200 font-semibold text-sm transition-colors text-slate-700">
                        <span class="flex items-center gap-2"><i data-lucide="user-plus" class="w-4 h-4 text-sky-500"></i> Daftarkan Siswa Baru</span>
                        <i data-lucide="chevron-down" class="w-4 h-4"></i>
                    </button>
                    <!-- Expandable Student Form -->
                    <div id="form-siswa" class="hidden bg-slate-50 p-4 rounded-xl border border-dashed border-slate-200 space-y-3">
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Pilih Kelas Tujuan</label>
                            <select id="select-kelas-siswa" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500"></select>
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Nama Lengkap Siswa</label>
                            <input type="text" id="input-nama-siswa" placeholder="Masukkan nama lengkap" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                        </div>
                        <button onclick="tambahSiswaBaru()" class="w-full py-2 rounded-lg bg-sky-600 hover:bg-sky-700 text-white font-bold text-xs transition-colors">Simpan Siswa</button>
                    </div>

                    <button onclick="toggleForm('form-pindah')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-slate-50 hover:bg-slate-100 border border-slate-200 font-semibold text-sm transition-colors text-slate-700">
                        <span class="flex items-center gap-2"><i data-lucide="arrow-left-right" class="w-4 h-4 text-emerald-500"></i> Pindahkan / Naik Kelas</span>
                        <i data-lucide="chevron-down" class="w-4 h-4"></i>
                    </button>
                    <!-- Expandable Promotion Form -->
                    <div id="form-pindah" class="hidden bg-slate-50 p-4 rounded-xl border border-dashed border-slate-200 space-y-3">
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Dari Kelas (Asal)</label>
                            <select id="select-pindah-asal" onchange="updateSiswaPindahDropdown()" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500"></select>
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Pilih Siswa</label>
                            <select id="select-pindah-siswa" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500">
                                <option value="ALL">-- SEMUA SISWA (Naik Kelas Massal) --</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Ke Kelas (Tujuan)</label>
                            <select id="select-pindah-tujuan" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500"></select>
                        </div>
                        <button onclick="pindahkanSiswaKelas()" class="w-full py-2 rounded-lg bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs transition-colors">Pindahkan Sekarang</button>
                    </div>
                </div>
            </div>

            <!-- AI Assistant Panel (Periodic Recap System) -->
            <div class="lg:col-span-2 bg-gradient-to-br from-indigo-900 to-slate-900 p-6 rounded-3xl text-white shadow-xl flex flex-col justify-between relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 w-44 h-44 bg-indigo-500/20 rounded-full blur-2xl"></div>
                
                <div>
                    <div class="flex items-center justify-between mb-2">
                        <div class="flex items-center gap-2">
                            <span class="p-2 bg-white/10 text-emerald-400 rounded-xl animate-pulse">
                                <i data-lucide="sparkles"></i>
                            </span>
                            <h3 class="font-bold text-lg">Asisten Rekapitulasi AI</h3>
                        </div>
                        <span class="text-[10px] uppercase font-extrabold tracking-widest px-3 py-1 bg-emerald-500/20 border border-emerald-500/30 text-emerald-300 rounded-full">Gemini 3.1 Flash</span>
                    </div>
                    <p class="text-xs text-indigo-200 leading-relaxed max-w-xl">Butuh laporan berkala 1 minggu atau 1 bulan? Biarkan AI membaca histori presensi, menyusun rekapitulasi, mengevaluasi siswa terlambat, dan memberikan wawasan pedagogis instan.</p>
                </div>

                <div class="mt-6 space-y-4">
                    <div class="flex flex-wrap gap-2">
                        <button onclick="generateRekapAI(7)" class="flex items-center gap-2 px-4 py-2.5 rounded-xl bg-white/10 hover:bg-white/15 text-xs font-bold transition-all border border-white/5 shadow-md">
                            <i class="w-4 h-4 text-emerald-400" data-lucide="calendar"></i> Analisis Rekap 1 Minggu
                        </button>
                        <button onclick="generateRekapAI(30)" class="flex items-center gap-2 px-4 py-2.5 rounded-xl bg-white/10 hover:bg-white/15 text-xs font-bold transition-all border border-white/5 shadow-md">
                            <i class="w-4 h-4 text-cyan-400" data-lucide="calendar-days"></i> Analisis Rekap 1 Bulan
                        </button>
                        <button onclick="bukaModalAI()" class="flex items-center gap-2 px-4 py-2.5 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-xs font-bold transition-all shadow-lg shadow-emerald-950/40">
                            <i class="w-4 h-4" data-lucide="message-square"></i> Tanya AI Lebih Detail / Obrolan
                        </button>
                    </div>
                </div>
            </div>

        </div>
    </div>

    <!-- Main Rosters & Students Grid -->
    <div class="max-w-7xl mx-auto px-4 mt-8">
        <div id="app-grid" class="grid-container">
            <!-- Dynamically populated via init() -->
        </div>
        
        <!-- Large Save Action -->
        <div class="main-actions text-center my-12">
            <button class="btn-save" onclick="simpanData()">💾 SIMPAN DATA SEKARANG</button>
        </div>

        <!-- Historical Records Section -->
        <div class="history-area">
            <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 mb-6">
                <div class="flex items-center gap-2">
                    <span class="p-2 bg-amber-100 text-amber-600 rounded-xl">
                        <i data-lucide="folder-open"></i>
                    </span>
                    <h2 class="text-xl font-extrabold text-slate-800 m-0">📂 Laporan Tersimpan</h2>
                </div>
                <div class="text-xs font-bold bg-slate-100 text-slate-500 py-1.5 px-4 rounded-full border border-slate-200">
                    Sistem Penyimpanan Terenkripsi Lokal
                </div>
            </div>
            
            <div class="overflow-x-auto">
                <table class="history-table">
                    <thead>
                        <tr>
                            <th>Waktu Simpan</th>
                            <th>Total Hadir / Total Siswa</th>
                            <th style="text-align:right">Opsi</th>
                        </tr>
                    </thead>
                    <tbody id="list-histori-body">
                        <!-- populated via renderHistori() -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- MODAL 1: Detail Daily Report -->
    <div id="modalDetail" class="modal">
        <div class="modal-content mx-4 my-8">
            <div class="flex items-center justify-between border-b border-slate-100 pb-4 mb-6">
                <div class="flex items-center gap-2">
                    <span class="p-2 bg-rose-100 text-rose-600 rounded-lg"><i data-lucide="file-text"></i></span>
                    <h3 id="judulModal" class="text-lg font-bold text-slate-800">Detail Laporan</h3>
                </div>
                <button onclick="tutupModal()" class="text-slate-400 hover:text-slate-600 transition-colors"><i data-lucide="x"></i></button>
            </div>
            
            <div class="overflow-y-auto max-h-[50vh] rounded-2xl border border-slate-200 mb-6 shadow-inner">
                <table class="w-full border-collapse">
                    <thead class="sticky top-0 bg-slate-50 border-b border-slate-200">
                        <tr class="text-left text-xs font-bold uppercase text-slate-600">
                            <th class="p-4">No</th>
                            <th class="p-4">Nama Siswa</th>
                            <th class="p-4">Kelas</th>
                            <th class="p-4">Waktu & Tanggal</th>
                            <th class="p-4">Status</th>
                        </tr>
                    </thead>
                    <tbody id="bodiModal"></tbody>
                </table>
            </div>
            
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-4">
                <button class="btn-pdf bg-gradient-to-r from-rose-500 to-red-600 hover:from-rose-600 hover:to-red-700 shadow-md shadow-rose-100 flex items-center justify-center gap-2 py-3.5 rounded-2xl" id="btnPdfP">
                    <i data-lucide="file-down"></i> 📄 PDF Portrait (Harian)
                </button>
                <button class="btn-pdf bg-gradient-to-r from-violet-500 to-fuchsia-600 hover:from-violet-600 hover:to-fuchsia-700 shadow-md shadow-violet-100 flex items-center justify-center gap-2 py-3.5 rounded-2xl" id="btnPdfL">
                    <i data-lucide="file-down"></i> 📑 PDF Landscape (Harian)
                </button>
            </div>
            
            <button onclick="tutupModal()" class="w-full py-3.5 rounded-2xl bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-sm transition-colors">Tutup Jendela</button>
        </div>
    </div>

    <!-- MODAL 2: AI Assistant Deep Intelligence modal -->
    <div id="modalAI" class="modal">
        <div class="modal-content mx-4 my-8 max-w-4xl flex flex-col h-[85vh]">
            <!-- AI Header -->
            <div class="flex items-center justify-between border-b border-slate-100 pb-4 mb-4">
                <div class="flex items-center gap-3">
                    <span class="p-2.5 bg-gradient-to-r from-indigo-500 to-purple-600 text-white rounded-xl shadow-lg shadow-indigo-100 animate-pulse">
                        <i data-lucide="sparkles"></i>
                    </span>
                    <div>
                        <h3 class="text-lg font-extrabold text-slate-800">Panel AI Assistant & Rekap Berkala</h3>
                        <p class="text-xs text-slate-500">Penyusun rekapitulasi, evaluasi tren kehadiran, dan konsultan kesiswaan</p>
                    </div>
                </div>
                <button onclick="tutupModalAI()" class="text-slate-400 hover:text-slate-600 transition-colors"><i data-lucide="x"></i></button>
            </div>

            <!-- AI Interactive Content Panel -->
            <div class="flex-1 overflow-y-auto pr-2 space-y-6" id="ai-modal-body">
                <!-- AI Output Screen -->
                <div id="ai-output-container" class="bg-slate-50 border border-slate-200 rounded-2xl p-5 text-sm leading-relaxed text-slate-800 whitespace-pre-line relative min-h-[150px]">
                    <div id="ai-placeholder" class="absolute inset-0 flex flex-col items-center justify-center text-center p-6 text-slate-400">
                        <i data-lucide="sparkle" class="w-12 h-12 mb-3 text-indigo-400 animate-spin-slow"></i>
                        <h4 class="font-bold text-slate-700 mb-1">Mulai Rekapitulasi Cerdas Anda</h4>
                        <p class="text-xs max-w-md">Gunakan opsi rekap mingguan/bulanan atau tanyakan analisis khusus menggunakan kolom percakapan di bawah.</p>
                    </div>
                    <div id="ai-output-content" class="hidden prose max-w-none text-slate-700">
                        <!-- AI Markdown results populated here -->
                    </div>
                </div>

                <!-- TTS Audio Bar (Hidden till voice is active) -->
                <div id="tts-audio-container" class="hidden bg-indigo-50 border border-indigo-100 rounded-2xl p-4 flex items-center justify-between gap-4">
                    <div class="flex items-center gap-3">
                        <span class="p-2 bg-indigo-100 text-indigo-600 rounded-full animate-bounce">
                            <i data-lucide="volume-2" class="w-5 h-5"></i>
                        </span>
                        <div>
                            <p class="text-xs font-extrabold text-indigo-950 uppercase tracking-wide">Membacakan Analisis AI</p>
                            <p class="text-[10px] text-indigo-600" id="audio-status">Siap didengarkan</p>
                        </div>
                    </div>
                    <audio id="tts-audio" controls class="h-9 max-w-xs"></audio>
                </div>

                <!-- Export Recaps Area -->
                <div id="ai-actions-row" class="hidden flex flex-wrap gap-3 p-3 bg-slate-100 rounded-2xl border border-slate-200">
                    <button onclick="cetakRekapAIPdf()" class="flex-1 min-w-[200px] flex items-center justify-center gap-2 bg-slate-900 hover:bg-slate-800 text-white font-extrabold text-xs py-3 px-4 rounded-xl transition-all shadow-md">
                        <i data-lucide="printer" class="w-4 h-4"></i> Cetak Laporan PDF Rekap Berkala + AI
                    </button>
                    <button onclick="bacakanAnalisisAI()" class="flex-1 min-w-[200px] flex items-center justify-center gap-2 bg-indigo-600 hover:bg-indigo-500 text-white font-extrabold text-xs py-3 px-4 rounded-xl transition-all shadow-md">
                        <i data-lucide="megaphone" class="w-4 h-4"></i> 🔊 Dengarkan Rekap AI (TTS)
                    </button>
                </div>
            </div>

            <!-- Interactive Chat Form -->
            <div class="border-t border-slate-100 pt-4 mt-4">
                <div class="flex items-center gap-2">
                    <input type="text" id="ai-chat-input" placeholder="Tanya AI: 'Siswa mana yang paling rajin?', 'Kelas mana dengan persentase tertinggi?'..." class="flex-1 px-4 py-3 rounded-xl border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 focus:bg-white transition-all">
                    <button onclick="kirimPesanAI()" class="p-3.5 bg-indigo-600 hover:bg-indigo-500 text-white rounded-xl font-bold transition-all shadow-md shadow-indigo-100">
                        <i data-lucide="send" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // LocalStorage keys
        const DB_KEY = 'db_presensi_v8_final';
        const SISWA_KEY = 'db_siswa_v8_final';

        // Initial standard dataset used if storage is empty
        const defaultDataSiswa = {
            "Kelas 7": [
                "Aisyah Mughny",
                "Amirotun Nabilatur",
                "Aqila Adzka",
                "Arbaatul Mafahimul",
                "Hamda Zakiya",
                "Izza dewi",
                "Maryam Qonita",
                "Natarina Deandra",
                "Olivia Zahra",
                "Silvia Maghfiroh",
                "Rahma Shafa",
                "putri Royhana"
            ],
            "Kelas 8": [
                "Asma",
                "Farizka Ammalia",
                "Fathimatuz Zahro",
                "Fatiya Nahdloh Nafiqoh",
                "Fazlina Nurun Nadia",
                "Filza Kamalia Afkarin",
                "Hasna Aisyah Fatin",
                "Iffah Miftahurrohmah",
                "Iren Anggraini",
                "Khasifa Azkayra Sariza Putri",
                "Khansa Shafana Berlina",
                "Khoirunnisa Qonita Ahnaffia",
                "Li'izzah Diana Al-Faizah",
                "Maulidya Farah Medina Mecca",
                "Naifa Al-Faizah",
                "Nayla Deeba Husna Nasyida",
                "Nura Aulia Zahidah",
                "Nurhafidza Mumtazul Hasanah",
                "Rahma Zakiatun Nufus",
                "Rofina Nuha Labibah",
                "Shofi Nailatul Hasanah",
                "Shohwatul Khoirus Salwa",
                "Victoria Nahrul Hayat Az Zahra",
                "Zulfa Nur Laili",
                "Ahmad Fahrizzabid",
                "Emilda Burhani Jamilah",
                "Hafidatussilma Wassyafiq",
                "Hasna Hani Arafa",
                "Azzahra Cecio Keyla Alexita"
            ],
            "Kelas 9": [] // Dikosongkan karena sudah lulus
        };

        // Reactive global state variables
        let dataSiswa = {};
        let temporarySelections = {}; // To preserve attendance state on roster modifications
        let lastAIResponseText = ""; // To hold prompt responses for PDF generation
        let currentlyGeneratingAI = false;

        // --- CUSTOM TOAST NOTIFICATION HELPERS ---
        function showToast(message, type = 'success') {
            const container = document.getElementById('toast-container');
            const card = document.createElement('div');
            card.className = 'toast-card';
            
            let icon = 'info';
            if (type === 'success') {
                icon = 'check-circle';
                card.style.borderLeftColor = 'var(--success)';
            } else if (type === 'error') {
                icon = 'alert-octagon';
                card.style.borderLeftColor = 'var(--danger)';
            } else if (type === 'warning') {
                icon = 'alert-triangle';
                card.style.borderLeftColor = 'var(--warning)';
            }
            
            card.innerHTML = `<i data-lucide="${icon}"></i> <span>${message}</span>`;
            container.appendChild(card);
            lucide.createIcons();
            
            // Trigger animation
            setTimeout(() => card.classList.add('show'), 10);
            
            // Remove after duration
            setTimeout(() => {
                card.classList.remove('show');
                setTimeout(() => card.remove(), 300);
            }, 3500);
        }

        // --- CUSTOM CONFIRMATION DIALOG (Replacing Native confirm) ---
        function customConfirm(title, message, onYes, onNo = null) {
            const modal = document.getElementById('confirmModal');
            document.getElementById('confirmTitle').innerText = title;
            document.getElementById('confirmMessage').innerText = message;
            
            modal.style.display = 'flex';
            
            const btnYes = document.getElementById('btnConfirmYes');
            const btnCancel = document.getElementById('btnConfirmCancel');
            
            // Clone to clear previous events
            const newBtnYes = btnYes.cloneNode(true);
            const newBtnCancel = btnCancel.cloneNode(true);
            
            btnYes.parentNode.replaceChild(newBtnYes, btnYes);
            btnCancel.parentNode.replaceChild(newBtnCancel, btnCancel);
            
            newBtnYes.addEventListener('click', () => {
                modal.style.display = 'none';
                if(onYes) onYes();
            });
            
            newBtnCancel.addEventListener('click', () => {
                modal.style.display = 'none';
                if(onNo) onNo();
            });
        }

        function updateJamAktif() {
            const now = new Date();
            const opsiJam = { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: false };
            document.getElementById('label-jam').innerText = now.toLocaleTimeString('id-ID', opsiJam).replace(/\./g, ':');
        }

        // Load dataset from storage or write defaults
        function loadStudentDatabase() {
            const saved = localStorage.getItem(SISWA_KEY);
            if (saved) {
                try {
                    dataSiswa = JSON.parse(saved);
                } catch (e) {
                    dataSiswa = {...defaultDataSiswa};
                }
            } else {
                dataSiswa = {...defaultDataSiswa};
                localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
            }
        }

        // Synchronize and render rosters UI
        function init() {
            // Setup daily header date
            document.getElementById('label-tgl').innerText = new Date().toLocaleDateString('id-ID', { weekday: 'long', day: 'numeric', month: 'long', year: 'numeric' });
            
            updateJamAktif();
            setInterval(updateJamAktif, 1000);

            // Fetch state from local storage
            loadStudentDatabase();
            populateSelectOptions();

            const grid = document.getElementById('app-grid');
            grid.innerHTML = "";
            
            for (const [kls, siswa] of Object.entries(dataSiswa)) {
                let html = `<div class="class-box" id="card-${kls.replace(/\s+/g, '')}">
                    <div class="relative">
                        <h2>${kls}</h2>
                        <button onclick="hapusKelas('${kls}')" class="absolute right-4 top-4 text-slate-400 hover:text-red-500 transition-colors" title="Hapus Kelas">
                            <i data-lucide="trash-2" class="w-4 h-4"></i>
                        </button>
                    </div>`;
                
                if (siswa.length === 0) {
                    html += `<div class="py-12 px-6 text-center text-slate-400 font-semibold text-xs italic flex flex-col items-center justify-center gap-2">
                        <i data-lucide="users-round" class="w-8 h-8 text-slate-300"></i>
                        Belum ada siswa di kelas ini.<br>Mulai tambahkan siswa baru!
                    </div>`;
                } else {
                    html += `<table class="input-table"><tbody>`;
                    siswa.forEach((n, i) => {
                        const id = kls.replace(/\s+/g, '') + i;
                        html += `<tr class="student-row" id="${id}" onclick="toggleAbsen('${id}', '${kls}', '${n}')">
                            <td class="relative">
                                <span class="name-text">${i+1}. ${n}</span>
                                <button onclick="event.stopPropagation(); hapusSiswa('${kls}', ${i})" class="absolute right-2 top-1/2 -translate-y-1/2 opacity-0 hover:opacity-100 focus:opacity-100 text-slate-300 hover:text-red-500 transition-all" title="Hapus Siswa">
                                    <i data-lucide="user-minus" class="w-3.5 h-3.5"></i>
                                </button>
                            </td>
                            <td style="text-align:right" class="w-1/3">
                                <div class="status-container">
                                    <span class="waktu-text">-</span>
                                    <span class="check-icon">✔</span>
                                </div>
                            </td>
                        </tr>`;
                    });
                    html += `</tbody></table>`;
                }
                grid.innerHTML += html + `</div>`;
            }

            // Restore temporary active selections if any (e.g. on dynamic modification during attendance run)
            restoreSelections();

            renderHistori();
            lucide.createIcons();
        }

        // Preserve current selections temporarily
        function saveCurrentSelections() {
            temporarySelections = {};
            const activeRows = document.querySelectorAll('.student-row.status-hijau, .student-row.status-kuning, .student-row.status-merah');
            activeRows.forEach(row => {
                const id = row.getAttribute('id');
                const timeText = row.querySelector('.waktu-text').innerText;
                let statusClass = '';
                if (row.classList.contains('status-hijau')) statusClass = 'status-hijau';
                else if (row.classList.contains('status-kuning')) statusClass = 'status-kuning';
                else if (row.classList.contains('status-merah')) statusClass = 'status-merah';
                
                temporarySelections[id] = { statusClass, timeText };
            });
        }

        // Restore temporary active selections
        function restoreSelections() {
            for (const [id, value] of Object.entries(temporarySelections)) {
                const row = document.getElementById(id);
                if (row) {
                    row.classList.add(value.statusClass);
                    row.querySelector('.waktu-text').innerText = value.timeText;
                }
            }
        }

        // Populate dynamic drop-down select options for dynamic rosters
        function populateSelectOptions() {
            const select = document.getElementById('select-kelas-siswa');
            const selectAsal = document.getElementById('select-pindah-asal');
            const selectTujuan = document.getElementById('select-pindah-tujuan');
            
            select.innerHTML = "";
            selectAsal.innerHTML = "";
            selectTujuan.innerHTML = "";
            
            Object.keys(dataSiswa).forEach(kls => {
                const opt = `<option value="${kls}">${kls}</option>`;
                select.innerHTML += opt;
                selectAsal.innerHTML += opt;
                selectTujuan.innerHTML += opt;
            });
            
            updateSiswaPindahDropdown();
        }

        // Update the student dropdown dynamically inside the promotion menu
        function updateSiswaPindahDropdown() {
            const selectAsal = document.getElementById('select-pindah-asal');
            const selectSiswa = document.getElementById('select-pindah-siswa');
            
            const klsAsal = selectAsal.value;
            selectSiswa.innerHTML = `<option value="ALL">-- SEMUA SISWA (Naik Kelas Massal) --</option>`;
            
            if (klsAsal && dataSiswa[klsAsal]) {
                dataSiswa[klsAsal].forEach(siswa => {
                    selectSiswa.innerHTML += `<option value="${siswa}">${siswa}</option>`;
                });
            }
        }

        // Execute class promotion / student migration
        function pindahkanSiswaKelas() {
            const klsAsal = document.getElementById('select-pindah-asal').value;
            const klsTujuan = document.getElementById('select-pindah-tujuan').value;
            const targetSiswa = document.getElementById('select-pindah-siswa').value;
            
            if (!klsAsal || !klsTujuan) {
                showToast("Pilih kelas asal dan tujuan terlebih dahulu!", "error");
                return;
            }
            
            if (klsAsal === klsTujuan) {
                showToast("Kelas asal dan tujuan tidak boleh sama!", "warning");
                return;
            }
            
            if (targetSiswa === "ALL") {
                const totalSiswa = dataSiswa[klsAsal].length;
                if (totalSiswa === 0) {
                    showToast(`Kelas ${klsAsal} kosong, tidak ada siswa untuk dipindahkan!`, "warning");
                    return;
                }
                
                customConfirm(
                    "Naik Kelas / Pindah Massal", 
                    `Apakah Anda yakin ingin memindahkan seluruh (${totalSiswa}) siswa dari ${klsAsal} ke ${klsTujuan}?`, 
                    () => {
                        saveCurrentSelections();
                        
                        // Move all students to the destination
                        dataSiswa[klsTujuan] = dataSiswa[klsTujuan].concat(dataSiswa[klsAsal]);
                        dataSiswa[klsTujuan].sort();
                        
                        // Empty the source class
                        dataSiswa[klsAsal] = [];
                        
                        localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
                        showToast(`Kenaikan kelas berhasil! Seluruh siswa ${klsAsal} dipindahkan ke ${klsTujuan}.`, "success");
                        toggleForm('form-pindah');
                        init();
                    }
                );
            } else {
                customConfirm(
                    "Pindah Kelas Siswa", 
                    `Apakah Anda yakin ingin memindahkan ${targetSiswa} dari ${klsAsal} ke ${klsTujuan}?`, 
                    () => {
                        saveCurrentSelections();
                        
                        // Remove from source class array
                        dataSiswa[klsAsal] = dataSiswa[klsAsal].filter(s => s !== targetSiswa);
                        
                        // Add to target class array
                        dataSiswa[klsTujuan].push(targetSiswa);
                        dataSiswa[klsTujuan].sort();
                        
                        localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
                        showToast(`${targetSiswa} berhasil dipindahkan ke ${klsTujuan}!`, "success");
                        toggleForm('form-pindah');
                        init();
                    }
                );
            }
        }

        // Toggle UI dynamic form expandables
        function toggleForm(id) {
            const el = document.getElementById(id);
            if (el.classList.contains('hidden')) {
                el.classList.remove('hidden');
            } else {
                el.classList.add('hidden');
            }
        }

        // Dynamically append new school class to Database
        function tambahKelasBaru() {
            const input = document.getElementById('input-nama-kelas');
            const klsBaru = input.value.trim();
            if (!klsBaru) {
                showToast("Nama kelas tidak boleh kosong!", "error");
                return;
            }
            if (dataSiswa[klsBaru]) {
                showToast("Kelas tersebut sudah terdaftar!", "warning");
                return;
            }

            // Preserve current state selections
            saveCurrentSelections();

            dataSiswa[klsBaru] = [];
            localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
            
            showToast(`Kelas ${klsBaru} berhasil ditambahkan!`, "success");
            input.value = "";
            toggleForm('form-kelas');
            init();
        }

        // Dynamically append new Student Name to a Class
        function tambahSiswaBaru() {
            const selectKls = document.getElementById('select-kelas-siswa');
            const inputSiswa = document.getElementById('input-nama-siswa');
            
            const kls = selectKls.value;
            const namaSiswa = inputSiswa.value.trim();
            
            if (!kls) {
                showToast("Silakan buat kelas terlebih dahulu!", "warning");
                return;
            }
            if (!namaSiswa) {
                showToast("Nama siswa tidak boleh kosong!", "error");
                return;
            }
            if (dataSiswa[kls].includes(namaSiswa)) {
                showToast("Nama siswa sudah ada di kelas tersebut!", "warning");
                return;
            }

            // Preserve current state selections
            saveCurrentSelections();

            dataSiswa[kls].push(namaSiswa);
            // Sort student names dynamically for neat alphabetical display
            dataSiswa[kls].sort();
            localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
            
            showToast(`${namaSiswa} berhasil didaftarkan di ${kls}!`, "success");
            inputSiswa.value = "";
            toggleForm('form-siswa');
            init();
        }

        // Safely wipe out a class
        function hapusKelas(kls) {
            customConfirm("Hapus Kelas", `Apakah Anda yakin ingin menghapus ${kls} beserta seluruh siswanya secara permanen?`, () => {
                saveCurrentSelections();
                delete dataSiswa[kls];
                localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
                showToast(`Kelas ${kls} berhasil dihapus!`, "success");
                init();
            });
        }

        // Safely wipe out a student
        function hapusSiswa(kls, index) {
            const nama = dataSiswa[kls][index];
            customConfirm("Hapus Siswa", `Apakah Anda yakin ingin menghapus siswa ${nama} dari ${kls}?`, () => {
                saveCurrentSelections();
                dataSiswa[kls].splice(index, 1);
                localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
                showToast(`Siswa ${nama} berhasil dihapus!`, "success");
                init();
            });
        }

        // Original attendance toggling logic preserved with elegant improvements
        function toggleAbsen(id, kls, nama) {
            const row = document.getElementById(id);
            const txtWaktu = row.querySelector('.waktu-text');
            
            if(!row.classList.contains('status-hijau') && !row.classList.contains('status-kuning') && !row.classList.contains('status-merah')) {
                const now = new Date();
                const jam = now.getHours();
                const menit = now.getMinutes();

                // Attendance status threshold check
                if (jam === 7 && menit >= 0 && menit <= 20) {
                    row.classList.add('status-hijau'); // 07:00 - 07:20
                } else if (jam === 7 && menit >= 21 && menit <= 45) {
                    row.classList.add('status-kuning'); // 07:21 - 07:45
                } else {
                    row.classList.add('status-merah'); // outside normal boundaries -> Late
                }
                
                const h = now.toLocaleDateString('id-ID', { weekday: 'short' });
                const t = now.toLocaleDateString('id-ID', { day: 'numeric', month: 'short' });
                const j = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }).replace(/\./g, ':');
                txtWaktu.innerText = `${h}, ${t} - ${j}`;
                showToast(`${nama} hadir!`, "success");
            } else {
                row.className = "student-row";
                txtWaktu.innerText = "-";
                showToast("Presensi dibatalkan.", "warning");
            }
        }

        function simpanData() {
            const allRows = document.querySelectorAll('.student-row');
            const activeRows = document.querySelectorAll('.status-hijau, .status-kuning, .status-merah');
            
            if(activeRows.length === 0) {
                showToast("Pilih minimal satu siswa yang hadir sebelum menyimpan!", "error");
                return;
            }

            const dataPayload = Array.from(allRows).map(r => {
                let color = "#64748b"; 
                let statusText = "TANPA KETERANGAN";
                let bgColor = "#f8fafc";
                let isHadir = false;

                if(r.classList.contains('status-hijau')) {
                    color = "#16a34a"; statusText = "TEPAT WAKTU"; bgColor = "#dcfce7"; isHadir = true;
                } else if(r.classList.contains('status-kuning')) { 
                    color = "#ca8a04"; statusText = "PERINGATAN"; bgColor = "#fef9c3"; isHadir = true;
                } else if(r.classList.contains('status-merah')) { 
                    color = "#dc2626"; statusText = "TERLAMBAT"; bgColor = "#fee2e2"; isHadir = true;
                }

                return {
                    nama: r.querySelector('.name-text').innerText.replace(/^\d+\.\s*/, ''), 
                    kelas: r.closest('.class-box').querySelector('h2').innerText,
                    waktu: r.querySelector('.waktu-text').innerText,
                    status: statusText,
                    hexColor: color,
                    bgHex: bgColor,
                    hadir: isHadir
                };
            });

            const sesi = {
                id: Date.now(),
                waktuSimpan: new Date().toLocaleString('id-ID', { weekday:'long', day:'numeric', month:'short', year:'numeric', hour:'2-digit', minute:'2-digit' }),
                totalSiswa: allRows.length,
                totalHadir: activeRows.length,
                data: dataPayload
            };

            let db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            db.unshift(sesi);
            localStorage.setItem(DB_KEY, JSON.stringify(db));
            
            showToast("Data Berhasil Disimpan!", "success");
            setTimeout(() => {
                location.reload();
            }, 1000);
        }

        function renderHistori() {
            const tbody = document.getElementById('list-histori-body');
            const db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            if(db.length === 0) { 
                tbody.innerHTML = "<tr><td colspan='3' style='text-align:center; padding:25px; color:#94a3b8;'>Belum ada laporan tersimpan.</td></tr>"; 
                return; 
            }
            tbody.innerHTML = db.map(s => `
                <tr class="hover:bg-slate-50 transition-colors">
                    <td class="font-bold text-slate-700">${s.waktuSimpan}</td>
                    <td><span class="text-emerald-600 font-extrabold">${s.totalHadir}</span> / ${s.totalSiswa} Siswa</td>
                    <td style="text-align:right" class="space-x-1">
                        <button class="bg-indigo-50 text-indigo-700 hover:bg-indigo-100 px-4 py-2 rounded-xl text-xs font-bold transition-all" onclick="bukaDetail(${s.id})">Lihat</button>
                        <button class="bg-rose-50 text-rose-600 hover:bg-rose-100 px-4 py-2 rounded-xl text-xs font-bold transition-all" onclick="hapusLaporan(${s.id})">Hapus</button>
                    </td>
                </tr>`).join('');
            lucide.createIcons();
        }

        function bukaDetail(id) {
            const db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            const s = db.find(x => x.id === id);
            document.getElementById('judulModal').innerText = "Arsip: " + s.waktuSimpan;
            
            const dataTerurut = [
                ...s.data.filter(d => d.hadir),
                ...s.data.filter(d => !d.hadir)
            ];

            document.getElementById('bodiModal').innerHTML = dataTerurut.map((d, i) => `
                <tr style="background-color:${d.bgHex}; border-bottom:1px solid #e2e8f0;" class="text-sm">
                    <td style="padding:14px; color:#475569; font-weight:600;">${i+1}</td>
                    <td style="padding:14px; color:var(--primary); font-weight:700;">${d.nama}</td>
                    <td style="padding:14px; color:#475569; font-weight:600;">${d.kelas}</td>
                    <td style="padding:14px; color:#64748b; font-weight:600;">${d.waktu}</td>
                    <td style="padding:14px; color:${d.hexColor}; font-weight:800; letter-spacing:0.5px;">${d.status}</td>
                </tr>`).join('');
            
            document.getElementById('btnPdfP').onclick = () => cetak(s, 'p');
            document.getElementById('btnPdfL').onclick = () => cetak(s, 'l');
            document.getElementById('modalDetail').style.display = 'flex';
        }

        function hapusLaporan(id) {
            customConfirm("Hapus Laporan", "Apakah Anda yakin ingin menghapus arsip laporan presensi ini secara permanen?", () => {
                let db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
                localStorage.setItem(DB_KEY, JSON.stringify(db.filter(x => x.id !== id)));
                showToast("Laporan berhasil dihapus.", "success");
                renderHistori();
            });
        }

        function tutupModal() { 
            document.getElementById('modalDetail').style.display = 'none'; 
        }

        // Opens the AI dialog panel
        function bukaModalAI() {
            document.getElementById('modalAI').style.display = 'flex';
            lucide.createIcons();
        }

        function tutupModalAI() {
            document.getElementById('modalAI').style.display = 'none';
            // Stop any active TTS audio reading
            const audio = document.getElementById('tts-audio');
            audio.pause();
        }

        // Compile and aggregate periodic statistics from historical local storage
        function kumpulkanDataHistori(hariMundur = 7) {
            const db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            if (db.length === 0) return null;

            // Gather elements within bounds
            const cutoffDate = Date.now() - (hariMundur * 24 * 60 * 60 * 1000);
            const sesiValid = db.filter(s => s.id >= cutoffDate);
            
            if (sesiValid.length === 0) return null;

            let totalPresensiSesi = sesiValid.length;
            let totalHadirSiswa = 0;
            let totalTerdaftarSiswa = 0;
            let keterlambatanTotal = 0;
            let peringatanTotal = 0;
            let alpaTotal = 0;
            
            // Student-specific frequency analysis
            let frekuensiTerlambat = {};
            let frekuensiAlpa = {};

            sesiValid.forEach(sesi => {
                totalHadirSiswa += sesi.totalHadir;
                totalTerdaftarSiswa += sesi.totalSiswa;

                sesi.data.forEach(siswa => {
                    if (siswa.status === "TERLAMBAT") {
                        keterlambatanTotal++;
                        frekuensiTerlambat[siswa.nama] = (frekuensiTerlambat[siswa.nama] || 0) + 1;
                    } else if (siswa.status === "PERINGATAN") {
                        peringatanTotal++;
                    } else if (siswa.status === "TANPA KETERANGAN") {
                        alpaTotal++;
                        frekuensiAlpa[siswa.nama] = (frekuensiAlpa[siswa.nama] || 0) + 1;
                    }
                });
            });

            // Convert to arrays and sort
            const daftarTerlambatSering = Object.entries(frekuensiTerlambat)
                .sort((a, b) => b[1] - a[1])
                .slice(0, 5);

            const daftarAlpaSering = Object.entries(frekuensiAlpa)
                .sort((a, b) => b[1] - a[1])
                .slice(0, 5);

            return {
                periodeHari: hariMundur,
                totalSesi: totalPresensiSesi,
                kehadiranRerata: ((totalHadirSiswa / totalTerdaftarSiswa) * 100).toFixed(1),
                totalSiswa: totalTerdaftarSiswa,
                terlambat: keterlambatanTotal,
                peringatan: peringatanTotal,
                alpa: alpaTotal,
                siswaSeringTerlambat: daftarTerlambatSering,
                siswaSeringAlpa: daftarAlpaSering,
                arsipRaw: sesiValid.map(s => ({
                    tanggal: s.waktuSimpan.split(',')[0] || s.waktuSimpan,
                    hadir: s.totalHadir,
                    total: s.totalSiswa
                }))
            };
        }

        // Contact Gemini API to run deep pedagogical assessment on compiled stats
        async function generateRekapAI(hariMundur = 7) {
            if (currentlyGeneratingAI) return;
            
            const metadata = kumpulkanDataHistori(hariMundur);
            if (!metadata) {
                showToast("Data histori presensi kosong! Harap lakukan presensi minimal 1 kali.", "warning");
                return;
            }

            currentlyGeneratingAI = true;
            bukaModalAI();

            // Setup loading state view
            const outputContent = document.getElementById('ai-output-content');
            const placeholder = document.getElementById('ai-placeholder');
            const actionRow = document.getElementById('ai-actions-row');
            
            placeholder.innerHTML = `
                <div class="flex flex-col items-center justify-center py-8">
                    <div class="animate-spin rounded-full h-10 w-10 border-t-2 border-b-2 border-indigo-600 mb-4"></div>
                    <p class="font-bold text-indigo-950 text-sm">Menganalisis Laporan Kehadiran Periodik...</p>
                    <p class="text-[10px] text-slate-500 mt-1">Menggunakan LLM Gemini 3.1 Flash</p>
                </div>
            `;
            placeholder.classList.remove('hidden');
            outputContent.classList.add('hidden');
            actionRow.classList.add('hidden');

            const periodLabel = hariMundur === 7 ? "Mingguan (7 Hari)" : "Bulanan (30 Hari)";
            const prompt = `
            Act as an educational administration consultant for SMP Hamalatul Quran Ringinagung.
            Analyze the following attendance aggregation data for the last ${periodLabel} period:
            - Average Attendance Rate: ${metadata.kehadiranRerata}%
            - Total Days Monitored: ${metadata.totalSesi} days
            - Total Registered Records: ${metadata.totalSiswa} rows
            - Cases of Extreme Tardiness (>45 mins late): ${metadata.terlambat} cases
            - Cases of Warning Tardiness (21-45 mins late): ${metadata.peringatan} cases
            - Cases of Absence/Alpa: ${metadata.alpa} cases
            - Top Students with Tardiness: ${JSON.stringify(metadata.siswaSeringTerlambat)}
            - Top Students with Absence: ${JSON.stringify(metadata.siswaSeringAlpa)}
            - Raw Daily Logs Summary: ${JSON.stringify(metadata.arsipRaw)}

            Format your response in a structured Bahasa Indonesia report:
            1. **Ringkasan Eksekutif**: Tulis paragraf menarik, motivatif, dan ringkas mengenai tingkat kedisplinan umum siswa.
            2. **Analisis Tren Kehadiran**: Analisis tren naik/turun kehadiran berdasarkan data harian.
            3. **Evaluasi Kasus Terlambat & Alpa**: Soroti siswa yang paling sering terlambat atau alpa, berikan interpretasi pedagogis.
            4. **Rekomendasi Tindakan Administrasi**: Langkah taktis yang harus diambil guru/sekolah untuk mengatasi keterlambatan ini.

            Keep it extremely clean, highly professional, educational, and motivational. Use clean Markdown elements.
            `;

            try {
                const response = await callGeminiAPI(prompt);
                
                lastAIResponseText = response; // Save to global state
                renderMarkdownToHTML(response);

                placeholder.classList.add('hidden');
                outputContent.classList.remove('hidden');
                actionRow.classList.remove('hidden');
                
                showToast("Analisis AI Berhasil Dibuat!", "success");
            } catch (err) {
                console.error(err);
                showToast("Gagal memanggil Asisten AI. Coba beberapa saat lagi.", "error");
                tutupModalAI();
            } finally {
                currentlyGeneratingAI = false;
            }
        }

        // Custom manual queries to AI
        async function kirimPesanAI() {
            const input = document.getElementById('ai-chat-input');
            const query = input.value.trim();
            if (!query) return;

            if (currentlyGeneratingAI) return;
            currentlyGeneratingAI = true;

            const outputContent = document.getElementById('ai-output-content');
            const placeholder = document.getElementById('ai-placeholder');
            const actionRow = document.getElementById('ai-actions-row');

            // Save history representation for context
            const db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            const dbSummary = db.map(s => ({
                waktu: s.waktuSimpan,
                totalSiswa: s.totalSiswa,
                hadir: s.totalHadir
            })).slice(0, 10); // feed last 10 logs for memory space

            placeholder.innerHTML = `
                <div class="flex flex-col items-center justify-center py-8">
                    <div class="animate-spin rounded-full h-10 w-10 border-t-2 border-b-2 border-indigo-600 mb-4"></div>
                    <p class="font-bold text-indigo-950 text-sm">Berpikir...</p>
                </div>
            `;
            placeholder.classList.remove('hidden');
            outputContent.classList.add('hidden');
            actionRow.classList.add('hidden');

            const systemPrompt = "Anda adalah Asisten AI Administrator untuk SMP Hamalatul Quran Ringinagung. Berikan jawaban yang terstruktur, edukatif, dan ramah sesuai data kehadiran.";
            const finalPrompt = `
            Berikut adalah histori kehadiran siswa terbaru: ${JSON.stringify(dbSummary)}.
            Daftar siswa saat ini: ${JSON.stringify(dataSiswa)}.
            Pertanyaan Pengguna: "${query}"
            Jawablah dengan relevan dan sopan menggunakan Bahasa Indonesia yang formal namun ramah.
            `;

            try {
                const response = await callGeminiAPI(finalPrompt, systemPrompt);
                lastAIResponseText = response;
                renderMarkdownToHTML(response);

                placeholder.classList.add('hidden');
                outputContent.classList.remove('hidden');
                actionRow.classList.remove('hidden');
                
                input.value = "";
            } catch (e) {
                showToast("Koneksi bermasalah. Coba lagi.", "error");
            } finally {
                currentlyGeneratingAI = false;
            }
        }

        // Core Gemini Fetcher with exponential backoff capability
        async function callGeminiAPI(prompt, systemInstruction = "") {
            const apiKey = ""; // Canvas injects this automatically at runtime
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;

            const payload = {
                contents: [{ parts: [{ text: prompt }] }],
            };

            if (systemInstruction) {
                payload.systemInstruction = {
                    parts: [{ text: systemInstruction }]
                };
            }

            let delay = 1000;
            const maxRetries = 3;

            for (let i = 0; i < maxRetries; i++) {
                try {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (response.ok) {
                        const result = await response.json();
                        return result.candidates?.[0]?.content?.parts?.[0]?.text || "Maaf, respon AI kosong.";
                    }
                } catch (e) {
                    // Exponential backoff logic (suppressed logging to match criteria)
                    await new Promise(res => setTimeout(res, delay));
                    delay *= 2;
                }
            }
            throw new Error("API call failed after retries.");
        }

        // Convert simplistic markdown elements to visual html representation
        function renderMarkdownToHTML(text) {
            let html = text
                .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
                .replace(/\*(.*?)\*/g, '<em>$1</em>')
                .replace(/### (.*?)\n/g, '<h4 class="text-md font-extrabold text-slate-900 mt-4 mb-2">$1</h4>')
                .replace(/## (.*?)\n/g, '<h3 class="text-lg font-extrabold text-slate-900 mt-6 mb-3 border-b border-slate-200 pb-1">$1</h3>')
                .replace(/# (.*?)\n/g, '<h2 class="text-xl font-extrabold text-indigo-900 mt-8 mb-4">$1</h2>')
                .replace(/^- (.*?)\n/gm, '<li class="ml-4 list-disc text-slate-700">$1</li>');

            document.getElementById('ai-output-content').innerHTML = html;
        }

        // TTS Integration utilizing gemini-2.5-flash-preview-tts model
        async function bacakanAnalisisAI() {
            if (!lastAIResponseText) return;

            const ttsBox = document.getElementById('tts-audio-container');
            const statusLabel = document.getElementById('audio-status');
            ttsBox.classList.remove('hidden');
            statusLabel.innerText = "Menyiapkan suara AI...";

            // Clean the raw text from complex MD syntax before sending
            const cleanText = lastAIResponseText
                .replace(/[*#]/g, '')
                .substring(0, 350) + "... Laporan lengkap tersedia di cetakan PDF.";

            const payload = {
                contents: [{
                    parts: [{ text: `Say in a clear and motivating Indonesian tone: ${cleanText}` }]
                }],
                generationConfig: {
                    responseModalities: ["AUDIO"],
                    speechConfig: {
                        voiceConfig: {
                            prebuiltVoiceConfig: { voiceName: "Sulafat" } // Warm, friendly speaker
                        }
                    }
                },
                model: "gemini-2.5-flash-preview-tts"
            };

            const apiKey = "";
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${apiKey}`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error("TTS failed");

                const result = await response.json();
                const part = result?.candidates?.[0]?.content?.parts?.[0];
                const audioData = part?.inlineData?.data;
                const mimeType = part?.inlineData?.mimeType;

                if (audioData && mimeType && mimeType.startsWith("audio/")) {
                    const sampleRate = parseInt(mimeType.match(/rate=(\d+)/)?.[1] || "24000", 10);
                    const pcmData = base64ToArrayBuffer(audioData);
                    const pcm16 = new Int16Array(pcmData);
                    const wavBlob = pcmToWav(pcm16, sampleRate);
                    const audioUrl = URL.createObjectURL(wavBlob);

                    const audioEl = document.getElementById('tts-audio');
                    audioEl.src = audioUrl;
                    audioEl.play();
                    statusLabel.innerText = "Memutarkan analisis suara";
                }
            } catch (err) {
                statusLabel.innerText = "Gagal memuat suara.";
                showToast("Sistem audio bermasalah.", "error");
            }
        }

        // Helper conversion blocks for PCM Audio
        function base64ToArrayBuffer(base64) {
            const binaryString = atob(base64);
            const len = binaryString.length;
            const bytes = new Uint8Array(len);
            for (let i = 0; i < len; i++) {
                bytes[i] = binaryString.charCodeAt(i);
            }
            return bytes.buffer;
        }

        function pcmToWav(pcm16Array, sampleRate) {
            const buffer = new ArrayBuffer(44 + pcm16Array.length * 2);
            const view = new DataView(buffer);

            // RIFF identifier
            writeString(view, 0, 'RIFF');
            view.setUint32(4, 36 + pcm16Array.length * 2, true);
            writeString(view, 8, 'WAVE');

            // Format chunk
            writeString(view, 12, 'fmt ');
            view.setUint32(16, 16, true);
            view.setUint16(20, 1, true); // Linear PCM
            view.setUint16(22, 1, true); // Mono channel
            view.setUint32(24, sampleRate, true);
            view.setUint32(28, sampleRate * 2, true); // Byte rate
            view.setUint16(32, 2, true); // Block align
            view.setUint16(34, 16, true); // Bits per sample

            // Data chunk
            writeString(view, 36, 'data');
            view.setUint32(40, pcm16Array.length * 2, true);

            // Write PCM audio data
            let offset = 44;
            for (let i = 0; i < pcm16Array.length; i++, offset += 2) {
                view.setInt16(offset, pcm16Array[i], true);
            }

            return new Blob([buffer], { type: 'audio/wav' });
        }

        function writeString(view, offset, string) {
            for (let i = 0; i < string.length; i++) {
                view.setUint8(offset + i, string.charCodeAt(i));
            }
        }

        // Main function to print Daily Records (Portrait / Landscape)
        function cetak(sesi, orientasi) {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF({ orientation: orientasi });
            const pageWidth = doc.internal.pageSize.getWidth();
            
            // Header Instansi Sekolahan
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(15);
            doc.setTextColor(30, 41, 59);
            doc.text("SMP HAMALATUL QURAN RINGINAGUNG", pageWidth / 2, 14, { align: 'center' });
            
            doc.setFontSize(11);
            doc.setFont("Helvetica", "normal");
            doc.text("LAPORAN PRESENSI DIGITAL SISWA", pageWidth / 2, 20, { align: 'center' });
            
            doc.setFontSize(9);
            doc.setTextColor(100, 116, 139);
            doc.text(`Waktu Arsip: ${sesi.waktuSimpan}`, pageWidth / 2, 25, { align: 'center' });
            
            const siswaHadir = sesi.data.filter(x => x.hadir);
            const siswaAbsen = sesi.data.filter(x => !x.hadir);

            // --- 1. TABEL SISWA HADIR ---
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(22, 163, 74);
            doc.text(`A. DAFTAR SISWA HADIR (${siswaHadir.length} Siswa)`, 14, 33);

            doc.autoTable({ 
                startY: 36, 
                head: [['No', 'Nama', 'Kelas', 'Waktu Presensi', 'Status']], 
                body: siswaHadir.map((x, i) => [i+1, x.nama, x.kelas, x.waktu, x.status]),
                headStyles: { fillColor: [22, 163, 74], fontStyle: 'bold' },
                styles: { font: "Helvetica", fontSize: 9 },
                didParseCell: function (data) {
                    if (data.section === 'body') {
                        const statusSiswa = data.row.raw[4];
                        
                        if (statusSiswa === 'TEPAT WAKTU') {
                            data.cell.styles.fillColor = [220, 252, 231]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [22, 163, 74]; 
                        } else if (statusSiswa === 'PERINGATAN') {
                            data.cell.styles.fillColor = [254, 249, 195]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [202, 138, 4];  
                        } else if (statusSiswa === 'TERLAMBAT') {
                            data.cell.styles.fillColor = [254, 226, 226]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [220, 38, 38];  
                        }
                    }
                }
            });

            // --- 2. TABEL SISWA TIDAK HADIR ---
            let nextY = doc.lastAutoTable.finalY + 12;
            
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(220, 38, 38);
            doc.text(`B. DAFTAR SISWA TIDAK HADIR / ALPA (${siswaAbsen.length} Siswa)`, 14, nextY);

            doc.autoTable({ 
                startY: nextY + 3, 
                head: [['No', 'Nama', 'Kelas', 'Keterangan']], 
                body: siswaAbsen.map((x, i) => [i+1, x.nama, x.kelas, x.status]),
                headStyles: { fillColor: [220, 38, 38], fontStyle: 'bold' },
                styles: { font: "Helvetica", fontSize: 9 },
                didParseCell: function (data) {
                    if (data.section === 'body') {
                        data.cell.styles.fillColor = [248, 250, 252]; 
                        data.cell.styles.textColor = [150, 0, 0]; 
                    }
                }
            });
            
            const namaFileBersih = sesi.waktuSimpan.replace(/[\/\\*?:|<>]/g, '-').replace(/\s+/g, '_').split(',_')[0];
            doc.save(`Presensi_SMP_HQ_${namaFileBersih}.pdf`);
        }

        // Advanced AI PDF Generator compiling historic tables AND the AI evaluation text
        function cetakRekapAIPdf() {
            if (!lastAIResponseText) return;

            const { jsPDF } = window.jspdf;
            const doc = new jsPDF({ orientation: 'p' });
            const pageWidth = doc.internal.pageSize.getWidth();

            // Headings
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(15);
            doc.setTextColor(30, 41, 59);
            doc.text("SMP HAMALATUL QURAN RINGINAGUNG", pageWidth / 2, 14, { align: 'center' });
            
            doc.setFontSize(11);
            doc.setFont("Helvetica", "bold");
            doc.setTextColor(79, 70, 229);
            doc.text("LAPORAN REKAPITULASI BERKALA & ANALISIS AI", pageWidth / 2, 20, { align: 'center' });
            
            doc.setFontSize(8);
            doc.setFont("Helvetica", "normal");
            doc.setTextColor(148, 163, 184);
            doc.text("Dihasilkan secara cerdas menggunakan Model AI Gemini", pageWidth / 2, 24, { align: 'center' });

            // Fetch last stats
            const meta = kumpulkanDataHistori(7); // default back to last 7 days representation
            if (!meta) return;

            // Summary Info table block
            doc.setFontSize(10);
            doc.setFont("Helvetica", "bold");
            doc.setTextColor(30, 41, 59);
            doc.text("I. METRIK KEDISPLINAN SISWA", 14, 34);

            doc.autoTable({
                startY: 37,
                head: [['Indikator Laporan', 'Nilai / Jumlah']],
                body: [
                    ['Kehadiran Rata-rata', `${meta.kehadiranRerata}%`],
                    ['Jumlah Presensi Tersimpan', `${meta.totalSesi} Sesi harian`],
                    ['Kasus Terlambat (>45 mnt)', `${meta.terlambat} Kali`],
                    ['Kasus Peringatan (21-45 mnt)', `${meta.peringatan} Kali`],
                    ['Siswa Tanpa Keterangan (Alpa)', `${meta.alpa} Kali`]
                ],
                headStyles: { fillColor: [79, 70, 229] },
                styles: { fontSize: 9 }
            });

            // Write AI analysis block dynamically
            let nextY = doc.lastAutoTable.finalY + 12;
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(30, 41, 59);
            doc.text("II. EVALUASI DAN REKOMENDASI ASISTEN AI", 14, nextY);

            doc.setFont("Helvetica", "normal");
            doc.setFontSize(8.5);
            doc.setTextColor(51, 65, 85);

            // Sanitize text formatting rules before writing to PDF representation
            const cleanParagraphs = lastAIResponseText
                .replace(/[*#]/g, '')
                .split('\n')
                .filter(p => p.trim() !== "");

            let cursorY = nextY + 5;
            cleanParagraphs.forEach(p => {
                const textLines = doc.splitTextToSize(p, pageWidth - 28);
                
                // Keep cursor checks
                if (cursorY + (textLines.length * 4) > doc.internal.pageSize.getHeight() - 15) {
                    doc.addPage();
                    cursorY = 15;
                }
                
                doc.text(textLines, 14, cursorY);
                cursorY += (textLines.length * 4.5) + 3;
            });

            // Signature block at the very end
            if (cursorY + 30 > doc.internal.pageSize.getHeight() - 15) {
                doc.addPage();
                cursorY = 15;
            }
            
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(8.5);
            doc.text("Mengetahui,", 14, cursorY + 10);
            doc.text("Kepala Sekolah SMP HQ", 14, cursorY + 14);
            
            doc.setLineWidth(0.2);
            doc.setDrawColor(203, 213, 225);
            doc.line(14, cursorY + 32, 60, cursorY + 32);

            doc.save("Rekap_Absensi_Berkala_AI_SMP_HQ.pdf");
            showToast("Laporan Rekapitulasi AI berhasil diunduh!", "success");
        }

        window.onload = init;
    </script>
</body>
</html>
