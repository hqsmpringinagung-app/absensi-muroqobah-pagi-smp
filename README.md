<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Presensi Pro - SMP Hamalatul Quran</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.23/jspdf.plugin.autotable.min.js"></script>
    <!-- Tailwind CSS untuk sistem grid adaptif berkinerja tinggi -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons untuk gaya vektor yang responsif dan tajam -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Supabase JS Client library -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    
    <style>
        :root {
            --primary: #0f172a;
            --primary-light: #1e293b;
            --accent: #2563eb;
            --success: #16a34a;
            --warning: #ca8a04;
            --danger: #dc2626;
            --sakit: #0284c7;
            --izin: #7c3aed;
            --bg: #f8fafc;
            --card-bg: #ffffff;
        }

        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: var(--bg); 
            color: var(--primary);
            -webkit-font-smoothing: antialiased;
        }

        /* Scrollbar premium yang membulat */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 10px;
        }

        /* Header Glassmorphism */
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
        
        /* Grid kontainer 3 kolom di desktop */
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
        
        /* Kartu Modern */
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
            font-size: 1.1rem; 
            font-weight: 700;
            letter-spacing: 0.5px;
            text-align: center; 
            margin: 0; 
            border-bottom: 1px solid #e2e8f0; 
            color: var(--primary-light);
        }
        
        .input-table { width: 100%; border-collapse: collapse; }
        .input-table td { padding: 18px 16px; border-bottom: 1px solid #f1f5f9; vertical-align: middle; }
        
        .student-row { cursor: pointer; transition: all 0.2s ease; }
        .student-row:hover { background-color: #f8fafc; }
        
        /* Baris nama dibesarkan sesuai instruksi */
        .name-text { 
            font-weight: 700; 
            font-size: 1.05rem; 
            color: #1e293b; 
            transition: color 0.2s ease; 
        }
        
        .status-container { display: flex; align-items: center; justify-content: flex-end; gap: 10px; }
        .waktu-text { font-size: 0.75rem; font-weight: 700; color: #94a3b8; white-space: nowrap; transition: color 0.2s ease; }

        .check-icon { 
            width: 28px; height: 28px; border: 2px solid #cbd5e1; 
            border-radius: 50%; display: inline-flex; align-items: center; 
            justify-content: center; color: transparent; font-size: 0.85rem;
            transition: all 0.2s ease;
            background: #ffffff;
            flex-shrink: 0;
            font-weight: 800;
        }

        /* Lencana warna untuk status kehadiran */
        .status-hijau { background-color: #dcfce7 !important; }
        .status-hijau:hover { background-color: #bbf7d0 !important; }
        .status-hijau .name-text { color: #14532d; }
        .status-hijau .check-icon { background-color: var(--success); border-color: var(--success); color: white; transform: scale(1.05); }
        .status-hijau .waktu-text { color: #15803d !important; }

        .status-kuning { background-color: #fef9c3 !important; }
        .status-kuning:hover { background-color: #fef08a !important; }
        .status-kuning .name-text { color: #713f12; }
        .status-kuning .check-icon { background-color: var(--warning); border-color: var(--warning); color: white; transform: scale(1.05); }
        .status-kuning .waktu-text { color: #a16207 !important; }

        .status-merah { background-color: #fee2e2 !important; }
        .status-merah:hover { background-color: #fecaca !important; }
        .status-merah .name-text { color: #7f1d1d; }
        .status-merah .check-icon { background-color: var(--danger); border-color: var(--danger); color: white; transform: scale(1.05); }
        .status-merah .waktu-text { color: #b91c1c !important; }

        .status-sakit { background-color: #e0f2fe !important; }
        .status-sakit:hover { background-color: #bae6fd !important; }
        .status-sakit .name-text { color: #0369a1; }
        .status-sakit .check-icon { background-color: var(--sakit); border-color: var(--sakit); color: white; transform: scale(1.05); }
        .status-sakit .waktu-text { color: #0369a1 !important; }

        .status-izin { background-color: #f3e8ff !important; }
        .status-izin:hover { background-color: #e9d5ff !important; }
        .status-izin .name-text { color: #6d28d9; }
        .status-izin .check-icon { background-color: var(--izin); border-color: var(--izin); color: white; transform: scale(1.05); }
        .status-izin .waktu-text { color: #6d28d9 !important; }

        /* Desain tombol kustom */
        .btn-save {
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            color: white;
            font-weight: 800;
            font-size: 0.95rem;
            padding: 16px 32px;
            border-radius: 16px;
            box-shadow: 0 10px 20px rgba(15, 23, 42, 0.15);
            transition: all 0.3s ease;
            letter-spacing: 0.5px;
        }
        .btn-save:hover {
            transform: translateY(-2px);
            box-shadow: 0 15px 25px rgba(15, 23, 42, 0.25);
            filter: brightness(1.1);
        }

        .btn-pdf-now {
            background: linear-gradient(135deg, #e11d48 0%, #be123c 100%);
            color: white;
            font-weight: 800;
            font-size: 0.95rem;
            padding: 16px 32px;
            border-radius: 16px;
            box-shadow: 0 10px 20px rgba(225, 29, 72, 0.15);
            transition: all 0.3s ease;
            letter-spacing: 0.5px;
        }
        .btn-pdf-now:hover {
            transform: translateY(-2px);
            box-shadow: 0 15px 25px rgba(225, 29, 72, 0.25);
            filter: brightness(1.1);
        }

        /* Area & Tabel Histori */
        .history-area {
            background: white;
            padding: 28px;
            border-radius: 28px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.02);
            border: 1px solid #e2e8f0;
            margin-top: 40px;
        }
        .history-table {
            width: 100%;
            border-collapse: collapse;
        }
        .history-table th {
            text-align: left;
            padding: 16px;
            background: #f8fafc;
            color: #475569;
            font-weight: 700;
            font-size: 0.85rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            border-bottom: 2px solid #e2e8f0;
        }
        .history-table td {
            padding: 16px;
            border-bottom: 1px solid #f1f5f9;
            font-size: 0.95rem;
        }

        .modal { 
            position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
            background: rgba(15, 23, 42, 0.6); display: none; 
            justify-content: center; align-items: center; z-index: 1000; 
            backdrop-filter: blur(8px); animation: fadeIn 0.3s ease; 
        }
        .modal-content {
            background: white;
            padding: 24px;
            border-radius: 28px;
            width: 95%;
            max-width: 48rem;
            max-height: 90vh;
            overflow-y: auto;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
        }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        #toast-container {
            position: fixed;
            bottom: 24px;
            right: 24px;
            z-index: 9999;
            display: flex;
            flex-direction: column;
            gap: 10px;
            pointer-events: none;
            max-width: 90vw;
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

    <div id="toast-container"></div>

    <!-- Custom Confirmation Modal -->
    <div id="confirmModal" class="modal">
        <div class="bg-white p-6 rounded-2xl shadow-xl w-11/12 max-w-md mx-auto text-center transform transition-all">
            <div class="w-16 h-16 bg-red-100 text-red-600 rounded-full flex items-center justify-center mx-auto mb-4 text-2xl">
                <i data-lucide="alert-triangle"></i>
            </div>
            <h3 class="text-lg font-bold text-slate-800 mb-2" id="confirmTitle">Konfirmasi</h3>
            <p class="text-slate-600 mb-6 text-sm" id="confirmMessage">Apakah anda yakin ingin melakukan tindakan ini?</p>
            <div class="flex gap-3 justify-center">
                <button id="btnConfirmCancel" class="flex-1 sm:flex-initial px-5 py-2.5 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-sm transition-colors">Batal</button>
                <button id="btnConfirmYes" class="flex-1 sm:flex-initial px-5 py-2.5 rounded-xl bg-red-600 hover:bg-red-700 text-white font-bold text-sm transition-colors">Ya, Lanjutkan</button>
            </div>
        </div>
    </div>

    <!-- Header Section -->
    <div class="header px-4 py-8 md:px-6 md:py-12 text-center relative">
        <div class="max-w-7xl mx-auto">
            <!-- Lencana Status Sinkronisasi Realtime -->
            <div class="flex justify-center mb-4">
                <div id="sync-badge" class="flex items-center gap-2 bg-emerald-950/40 border border-emerald-500/30 backdrop-blur px-4 py-1.5 rounded-full text-[10px] md:text-xs font-bold text-emerald-300">
                    <span class="relative flex h-2 w-2">
                        <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                        <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500" id="sync-dot"></span>
                    </span>
                    <span id="sync-text">Menghubungkan Database Supabase...</span>
                </div>
            </div>

            <h1 class="text-xl md:text-3xl font-extrabold tracking-wider leading-tight">SMP HAMALATUL QURAN RINGINAGUNG</h1>
            <h2 class="text-sm md:text-lg mt-2 opacity-90 font-medium tracking-wide">PRESENSI DIGITAL PRO (MUTUAL MONITORING LIVE)</h2>
            
            <div class="mt-6 flex flex-col sm:flex-row items-center justify-center gap-4">
                <div id="label-tgl" class="opacity-90 text-xs md:text-sm font-semibold bg-white/10 px-5 py-2 rounded-full border border-white/10 shadow-sm">
                    Memuat Tanggal...
                </div>
                <div id="label-jam" class="text-md md:text-lg font-extrabold bg-white/20 px-6 py-2 rounded-full border border-white/20 shadow-md min-w-[120px] tabular-nums tracking-wider">
                    00:00:00
                </div>
            </div>

            <!-- Legend Section with Time Slots -->
            <div class="mt-6 flex flex-wrap justify-center gap-2 md:gap-3">
                <span class="text-[10px] md:text-xs font-bold px-3 py-1.5 md:px-4 md:py-2 rounded-full bg-emerald-500 text-white shadow-md">🟢 07:00 - 07:15 Aman</span>
                <span class="text-[10px] md:text-xs font-bold px-3 py-1.5 md:px-4 md:py-2 rounded-full bg-yellow-500 text-slate-950 shadow-md">🟡 07:16 - 07:40 Terlambat</span>
                <span class="text-[10px] md:text-xs font-bold px-3 py-1.5 md:px-4 md:py-2 rounded-full bg-rose-500 text-white shadow-md">🔴 07:41 - 08:20 Peringatan</span>
                <span class="text-[10px] md:text-xs font-bold px-3 py-1.5 md:px-4 md:py-2 rounded-full bg-sky-600 text-white shadow-md">🔵 Sakit</span>
                <span class="text-[10px] md:text-xs font-bold px-3 py-1.5 md:px-4 md:py-2 rounded-full bg-violet-600 text-white shadow-md">🟣 Izin</span>
            </div>
        </div>
    </div>

    <div class="max-w-7xl mx-auto px-4 mt-8">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            
            <!-- Panel Kontrol Administratif -->
            <div class="bg-white p-5 md:p-6 rounded-3xl shadow-sm border border-slate-100 flex flex-col justify-between">
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
                    <!-- Tombol expander form dinamis -->
                    <button onclick="toggleForm('form-kelas')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-slate-50 hover:bg-slate-100 border border-slate-200 font-semibold text-sm transition-colors text-slate-700">
                        <span class="flex items-center gap-2"><i data-lucide="folder-plus" class="w-4 h-4 text-indigo-500"></i> Buat Kelas Baru</span>
                        <i data-lucide="chevron-down" class="w-4 h-4 text-slate-400"></i>
                    </button>
                    <!-- Form Tambah Kelas -->
                    <div id="form-kelas" class="hidden bg-slate-50 p-4 rounded-xl border border-dashed border-slate-200 space-y-3">
                        <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide">Nama Kelas Baru</label>
                        <input type="text" id="input-nama-kelas" placeholder="Contoh: Kelas 7A, Kelas 8B" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                        <button onclick="tambahKelasBaru()" class="w-full py-2.5 rounded-lg bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-xs transition-colors">Simpan Kelas</button>
                    </div>

                    <button onclick="toggleForm('form-siswa')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-slate-50 hover:bg-slate-100 border border-slate-200 font-semibold text-sm transition-colors text-slate-700">
                        <span class="flex items-center gap-2"><i data-lucide="user-plus" class="w-4 h-4 text-sky-500"></i> Daftarkan Siswa Baru</span>
                        <i data-lucide="chevron-down" class="w-4 h-4 text-slate-400"></i>
                    </button>
                    <!-- Form Tambah Siswa -->
                    <div id="form-siswa" class="hidden bg-slate-50 p-4 rounded-xl border border-dashed border-slate-200 space-y-3">
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Pilih Kelas Tujuan</label>
                            <select id="select-kelas-siswa" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500"></select>
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-600 uppercase tracking-wide mb-1">Nama Lengkap Siswa</label>
                            <input type="text" id="input-nama-siswa" placeholder="Masukkan nama lengkap" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                        </div>
                        <button onclick="tambahSiswaBaru()" class="w-full py-2.5 rounded-lg bg-sky-600 hover:bg-sky-700 text-white font-bold text-xs transition-colors">Simpan Siswa</button>
                    </div>

                    <button onclick="toggleForm('form-pindah')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-slate-50 hover:bg-slate-100 border border-slate-200 font-semibold text-sm transition-colors text-slate-700">
                        <span class="flex items-center gap-2"><i data-lucide="arrow-left-right" class="w-4 h-4 text-emerald-500"></i> Pindahkan / Naik Kelas</span>
                        <i data-lucide="chevron-down" class="w-4 h-4 text-slate-400"></i>
                    </button>
                    <!-- Form Migrasi / Kenaikan Kelas -->
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
                        <button onclick="pindahkanSiswaKelas()" class="w-full py-2.5 rounded-lg bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs transition-colors">Pindahkan Sekarang</button>
                    </div>

                    <!-- tombol expander form database Supabase manual -->
                    <button onclick="toggleForm('form-config-db')" class="w-full flex items-center justify-between px-4 py-3 rounded-xl bg-amber-50 hover:bg-amber-100/80 border border-amber-200 font-semibold text-sm transition-colors text-amber-900">
                        <span class="flex items-center gap-2"><i data-lucide="database" class="w-4 h-4 text-amber-600"></i> Pengaturan Database Supabase</span>
                        <i data-lucide="chevron-down" class="w-4 h-4 text-amber-500"></i>
                    </button>
                    <!-- Form Pengaturan Supabase Cloud -->
                    <div id="form-config-db" class="hidden bg-amber-50/50 p-4 rounded-xl border border-dashed border-amber-200 space-y-3 text-left">
                        <div>
                            <label class="block text-[10px] font-bold text-slate-600 uppercase tracking-wide mb-1">Supabase URL (API URL)</label>
                            <input type="text" id="input-supabase-url" placeholder="https://xxxxxx.supabase.co" class="w-full px-3 py-2 rounded-lg border border-slate-300 text-xs focus:outline-none focus:ring-2 focus:ring-amber-500 bg-white">
                        </div>
                        <div>
                            <label class="block text-[10px] font-bold text-slate-600 uppercase tracking-wide mb-1">Supabase Anon Key (Public Key)</label>
                            <input type="text" id="input-supabase-key" placeholder="eyJhbGciOi..." class="w-full px-3 py-2 rounded-lg border border-slate-300 text-xs focus:outline-none focus:ring-2 focus:ring-amber-500 bg-white">
                        </div>
                        <button onclick="simpanConfigDatabase()" class="w-full py-2.5 rounded-lg bg-amber-600 hover:bg-amber-700 text-white font-bold text-xs transition-colors">Aktifkan Database Supabase</button>
                        <button onclick="hapusConfigDatabase()" class="w-full py-2.5 rounded-lg bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold text-xs transition-colors">Gunakan Penyimpanan Lokal</button>
                        
                        <!-- Petunjuk Pembuatan Tabel Supabase -->
                        <div class="mt-2 p-2 bg-white rounded-lg border border-amber-200 text-[10px] text-slate-600">
                            <span class="font-bold text-amber-800">💡 SQL Editor Schema:</span>
                            <p class="mt-1">Jalankan instruksi ini di SQL Editor Supabase Anda agar database siap digunakan dan tidak terblokir sistem keamanan:</p>
                            <pre class="mt-1 bg-slate-900 text-slate-100 p-2 rounded text-[9px] overflow-x-auto font-mono">
-- 1. Buat Tabel Data
CREATE TABLE IF NOT EXISTS presensi_data (
  key TEXT PRIMARY KEY,
  value JSONB
);

-- 2. Buat Tabel Riwayat
CREATE TABLE IF NOT EXISTS presensi_records (
  id BIGINT PRIMARY KEY,
  waktu_simpan TEXT,
  total_siswa INT,
  total_hadir INT,
  data JSONB
);

-- 3. MATIKAN ROW LEVEL SECURITY (Penting!)
ALTER TABLE presensi_data DISABLE ROW LEVEL SECURITY;
ALTER TABLE presensi_records DISABLE ROW LEVEL SECURITY;</pre>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Dashboard Analisis Disiplin Berkala -->
            <div class="lg:col-span-2 bg-gradient-to-br from-indigo-950 to-slate-900 p-5 md:p-6 rounded-3xl text-white shadow-xl flex flex-col justify-between relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 w-44 h-44 bg-indigo-500/20 rounded-full blur-2xl"></div>
                
                <div>
                    <div class="flex items-center justify-between mb-2">
                        <div class="flex items-center gap-2">
                            <span class="p-2 bg-indigo-600 text-indigo-200 rounded-xl">
                                <i data-lucide="bar-chart-3"></i>
                            </span>
                            <h3 class="font-bold text-lg">Dashboard Rekapitulasi & Disiplin Berkala</h3>
                        </div>
                        <span class="text-[10px] uppercase font-extrabold tracking-widest px-3 py-1 bg-indigo-50/10 border border-indigo-500/30 text-indigo-300 rounded-full">Sistem Otomatis</span>
                    </div>
                    <p class="text-xs text-indigo-200 leading-relaxed max-w-xl">Menganalisis tingkat keterlambatan, ketepatan waktu (ontime), sakit, izin, serta ketidakhadiran siswa secara kumulatif langsung dari arsip secara real-time.</p>
                </div>

                <div class="mt-6 space-y-4">
                    <div class="flex flex-wrap gap-2">
                        <button onclick="bukaDashboardRekap(7)" class="flex-1 sm:flex-initial flex items-center justify-center gap-2 px-4 py-3 rounded-xl bg-white/10 hover:bg-white/15 text-xs font-bold transition-all border border-white/5 shadow-md">
                            <i class="w-4 h-4 text-emerald-400" data-lucide="calendar"></i> Rekap 1 Minggu Terakhir
                        </button>
                        <button onclick="bukaDashboardRekap(30)" class="flex-1 sm:flex-initial flex items-center justify-center gap-2 px-4 py-3 rounded-xl bg-white/10 hover:bg-white/15 text-xs font-bold transition-all border border-white/5 shadow-md">
                            <i class="w-4 h-4 text-cyan-400" data-lucide="calendar-days"></i> Rekap 1 Bulan Terakhir
                        </button>
                    </div>
                </div>
            </div>

        </div>
    </div>

    <!-- Tombol Filter Cepat Kelas di Perangkat Mobile -->
    <div class="max-w-7xl mx-auto px-4 mt-8 md:mt-10">
        <div class="bg-white p-2 rounded-2xl border border-slate-100 shadow-sm flex flex-wrap gap-1" id="class-nav-tabs">
            <button onclick="filterTampilanKelas('ALL')" id="tab-all" class="flex-1 text-center py-2.5 px-4 rounded-xl text-xs font-extrabold transition-all bg-slate-900 text-white">
                Semua Kelas
            </button>
        </div>
    </div>

    <!-- Kotak Grid Presensi Utama -->
    <div class="max-w-7xl mx-auto px-4 mt-6">
        <div id="app-grid" class="grid-container">
            <!-- Diisi secara dinamis melalui init() yang menampilkan semua kelas -->
        </div>
        
        <!-- Tombol Aksi Simpan & Unduh PDF -->
        <div class="main-actions flex flex-col sm:flex-row justify-center items-center gap-4 my-10 md:my-12">
            <button class="btn-save w-full sm:w-auto flex items-center justify-center gap-2 py-4" onclick="simpanData()">
                <i data-lucide="save"></i> 💾 SIMPAN DATA HARI INI
            </button>
            <button class="btn-pdf-now w-full sm:w-auto flex items-center justify-center gap-2 py-4" onclick="cetakSesiAktif()">
                <i data-lucide="file-down"></i> 📄 UNDUH PDF HARI INI
            </button>
        </div>

        <!-- Area Histori dan Riwayat Laporan -->
        <div class="history-area mb-12">
            <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 mb-6">
                <div class="flex items-center gap-2">
                    <span class="p-2 bg-amber-100 text-amber-600 rounded-xl">
                        <i data-lucide="folder-open"></i>
                    </span>
                    <h2 class="text-lg md:text-xl font-extrabold text-slate-800 m-0">📂 Laporan Tersimpan</h2>
                </div>
                <div class="text-[10px] md:text-xs font-bold bg-slate-100 text-slate-500 py-1.5 px-4 rounded-full border border-slate-200" id="db-type-label">
                    Penyimpanan Lokal
                </div>
            </div>
            
            <div class="overflow-x-auto rounded-xl border border-slate-100">
                <table class="history-table min-w-[600px] sm:min-w-full">
                    <thead>
                        <tr>
                            <th>Waktu Simpan</th>
                            <th>Total Hadir / Total Siswa</th>
                            <th style="text-align:right">Opsi</th>
                        </tr>
                    </thead>
                    <tbody id="list-histori-body">
                        <!-- Diisi secara dinamis melalui renderHistori() -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- MODAL 1: Detail Laporan Harian -->
    <div id="modalDetail" class="modal">
        <div class="modal-content mx-4 my-8">
            <div class="flex items-center justify-between border-b border-slate-100 pb-4 mb-6">
                <div class="flex items-center gap-2">
                    <span class="p-2 bg-rose-100 text-rose-600 rounded-lg"><i data-lucide="file-text"></i></span>
                    <h3 id="judulModal" class="text-sm md:text-lg font-bold text-slate-800">Detail Laporan</h3>
                </div>
                <button onclick="tutupModal()" class="text-slate-400 hover:text-slate-600 transition-colors"><i data-lucide="x"></i></button>
            </div>
            
            <div class="overflow-x-auto rounded-2xl border border-slate-200 mb-6 shadow-inner">
                <table class="w-full border-collapse min-w-[500px]">
                    <thead class="bg-slate-50 border-b border-slate-200">
                        <tr class="text-left text-[10px] md:text-xs font-bold uppercase text-slate-600">
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
                <button class="bg-gradient-to-r from-rose-500 to-red-600 hover:from-rose-600 hover:to-red-700 text-white font-extrabold text-xs md:text-sm py-3.5 px-6 rounded-2xl shadow-md shadow-rose-100 flex items-center justify-center gap-2" id="btnPdfP">
                    <i data-lucide="file-down"></i> 📄 PDF Portrait (Harian)
                </button>
                <button class="bg-gradient-to-r from-violet-500 to-fuchsia-600 hover:from-violet-600 hover:to-fuchsia-700 text-white font-extrabold text-xs md:text-sm py-3.5 px-6 rounded-2xl shadow-md shadow-violet-100 flex items-center justify-center gap-2" id="btnPdfL">
                    <i data-lucide="file-down"></i> 📑 PDF Landscape (Harian)
                </button>
            </div>
            
            <button onclick="tutupModal()" class="w-full py-3.5 rounded-2xl bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-sm transition-colors">Tutup Jendela</button>
        </div>
    </div>

    <!-- MODAL 2: Dasbor Analisis Statistik Kedisiplinan -->
    <div id="modalRekap" class="modal">
        <div class="modal-content mx-4 my-8 max-w-5xl flex flex-col">
            <!-- Header -->
            <div class="flex items-center justify-between border-b border-slate-100 pb-4 mb-4">
                <div class="flex items-center gap-3">
                    <span class="p-2 bg-indigo-600 text-white rounded-xl shadow-lg">
                        <i data-lucide="trending-up"></i>
                    </span>
                    <div>
                        <h3 class="text-sm md:text-lg font-extrabold text-slate-800" id="rekap-modal-title">Rekapitulasi Disiplin & Presensi</h3>
                        <p class="text-[10px] md:text-xs text-slate-500" id="rekap-modal-subtitle">Menghitung akumulasi keterlambatan dan kepatuhan siswa</p>
                    </div>
                </div>
                <button onclick="tutupDashboardRekap()" class="text-slate-400 hover:text-slate-600 transition-colors"><i data-lucide="x"></i></button>
            </div>

            <!-- Area Konten Utama -->
            <div class="space-y-6">
                <!-- Filter & Pencarian Laporan Berkala -->
                <div class="flex flex-col sm:flex-row gap-3">
                    <div class="flex-1 relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                            <i data-lucide="search" class="w-4 h-4"></i>
                        </span>
                        <input type="text" id="rekap-search" oninput="filterTabelRekap()" placeholder="Cari nama santri..." class="w-full pl-9 pr-4 py-2.5 rounded-xl border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <select id="rekap-filter-kelas" onchange="filterTabelRekap()" class="sm:w-48 px-3 py-2.5 rounded-xl border border-slate-200 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500">
                        <option value="ALL">Semua Kelas</option>
                    </select>
                </div>

                <!-- Lencana Rangkuman Statistik -->
                <div class="grid grid-cols-2 md:grid-cols-7 gap-3">
                    <div class="bg-indigo-50 border border-indigo-100 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-indigo-500 uppercase tracking-wide">Rerata Hadir</div>
                        <div class="text-xl md:text-2xl font-extrabold text-indigo-900 mt-1" id="stat-rerata">0%</div>
                    </div>
                    <div class="bg-emerald-50 border border-emerald-100 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-emerald-600 uppercase tracking-wide">Ontime</div>
                        <div class="text-xl md:text-2xl font-extrabold text-emerald-900 mt-1" id="stat-aman">0</div>
                    </div>
                    <div class="bg-yellow-50 border border-yellow-100 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-yellow-600 uppercase tracking-wide">Lambat</div>
                        <div class="text-xl md:text-2xl font-extrabold text-yellow-900 mt-1" id="stat-terlambat">0</div>
                    </div>
                    <div class="bg-rose-50 border border-rose-100 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-rose-600 uppercase tracking-wide">Peringatan</div>
                        <div class="text-xl md:text-2xl font-extrabold text-rose-900 mt-1" id="stat-peringatan">0</div>
                    </div>
                    <div class="bg-sky-50 border border-sky-100 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-sky-600 uppercase tracking-wide">Sakit</div>
                        <div class="text-xl md:text-2xl font-extrabold text-sky-900 mt-1" id="stat-sakit">0</div>
                    </div>
                    <div class="bg-violet-50 border border-violet-100 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-violet-600 uppercase tracking-wide">Izin</div>
                        <div class="text-xl md:text-2xl font-extrabold text-violet-900 mt-1" id="stat-izin">0</div>
                    </div>
                    <div class="bg-slate-50 border border-slate-200 p-4 rounded-2xl text-center shadow-sm">
                        <div class="text-[10px] font-bold text-slate-500 uppercase tracking-wide">Alpa</div>
                        <div class="text-xl md:text-2xl font-extrabold text-slate-900 mt-1" id="stat-alpa">0</div>
                    </div>
                </div>

                <!-- Tabel Detail Kehadiran Siswa Lengkap -->
                <div class="border border-slate-200 rounded-2xl overflow-hidden shadow-inner bg-white">
                    <div class="overflow-x-auto max-h-[35vh]">
                        <table class="w-full text-left border-collapse min-w-[700px]" id="table-rekap-data">
                            <thead class="sticky top-0 bg-slate-50 border-b border-slate-200 text-slate-600 text-[10px] font-extrabold uppercase tracking-wide">
                                <tr>
                                    <th class="p-3">Siswa</th>
                                    <th class="p-3">Kelas</th>
                                    <th class="p-3 text-center text-emerald-600">Aman (🟢)</th>
                                    <th class="p-3 text-center text-yellow-600">Terlambat (🟡)</th>
                                    <th class="p-3 text-center text-rose-600">Peringatan (🔴)</th>
                                    <th class="p-3 text-center text-sky-600">Sakit (🔵)</th>
                                    <th class="p-3 text-center text-violet-600">Izin (🟣)</th>
                                    <th class="p-3 text-center text-slate-500">Alpa (⚪)</th>
                                    <th class="p-3 text-center">Rasio Kehadiran</th>
                                </tr>
                            </thead>
                            <tbody class="text-xs text-slate-700 divide-y divide-slate-100" id="table-rekap-body">
                                <!-- Diisi secara dinamis melalui JavaScript -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- Tombol Aksi Bawah Modal Rekap -->
            <div class="border-t border-slate-100 pt-4 mt-4 flex flex-col sm:flex-row gap-3">
                <button onclick="cetakLaporanRekapBerkala()" class="flex-1 flex items-center justify-center gap-2 bg-slate-900 hover:bg-slate-800 text-white font-extrabold text-xs py-3.5 px-4 rounded-xl transition-all shadow-md">
                    <i data-lucide="file-text" class="w-4 h-4"></i> Cetak Laporan PDF Rekapitulasi Berkala
                </button>
                <button onclick="tutupDashboardRekap()" class="sm:w-48 py-3.5 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-xs transition-colors">
                    Tutup Dashboard
                </button>
            </div>
        </div>
    </div>

    <!-- Integrasi Logika Sinkronisasi Supabase Real-time -->
    <script>
        // Kunci penyimpanan lokal
        const DB_KEY = 'db_presensi_supabase_v10';
        const SISWA_KEY = 'db_siswa_supabase_v10';

        // Nilai Default Kredensial Database Supabase (Hardcoded)
        const DEFAULT_SUPABASE_URL = "https://ogbvyeypznbwurmsmwld.supabase.co";
        const DEFAULT_SUPABASE_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Im9nYnZ5ZXlwem5id3VybXNtd2xkIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODE3OTM1MzgsImV4cCI6MjA5NzM2OTUzOH0.LSO8qrGBs85lkSD5mzVL7zOBO5LTHJX90v7Q-FJEYQo";

        // Ganti nama variabel global supabase agar tidak bentrok dengan modul eksternal Supabase JS
        let supabaseClient = null; 
        let isCloudActive = false;
        let isBroadcasting = false;
        let pollingInterval = null; // Ganti real-time channel WebSocket dengan interval polling HTTP

        // Daftar default nama siswa awal untuk Kelas 7, 8, dan 9
        const defaultDataSiswa = {
            "Kelas 7": [
                "Aisyah Mughny", "Amirotun Nabilatur", "Aqila Adzka", "Arbaatul Mafahimul",
                "Hamda Zakiya", "Izza dewi", "Maryam Qonita", "Natarina Deandra",
                "Olivia Zahra", "Silvia Maghfiroh", "Rahma Shafa", "putri Royhana"
            ],
            "Kelas 8": [
                "Asma", "Farizka Ammalia", "Fathimatuz Zahro", "Fatiya Nahdloh Nafiqoh",
                "Fazlina Nurun Nadia", "Filza Kamalia Afkarin", "Hasna Aisyah Fatin",
                "Iffah Miftahurrohmah", "Iren Anggraini", "Khasifa Azkayra Sariza Putri",
                "Khansa Shafana Berlina", "Khoirunnisa Qonita Ahnaffia", "Li'izzah Diana Al-Faizah",
                "Maulidya Farah Medina Mecca", "Naifa Al-Faizah", "Nayla Deeba Husna Nasyida",
                "Nura Aulia Zahidah", "Nurhafidza Mumtazul Hasanah", "Rahma Zakiatun Nufus",
                "Rofina Nuha Labibah", "Shofi Nailatul Hasanah", "Shohwatul Khoirus Salwa",
                "Victoria Nahrul Hayat Az Zahra", "Zulfa Nur Laili", "Ahmad Fahrizzabid",
                "Emilda Burhani Jamilah", "Hafidatussilma Wassyafiq", "Hasna Hani Arafa",
                "Azzahra Cecio Keyla Alexita"
            ],
            "Kelas 9": [
                "Ahmad Syarifuddin", "Bima Sakti Pratama", "Candra Wijaya", "Dwi Setyawan",
                "Fajar Ramadhan", "Guntur Wibowo", "Hendra Lesmana", "Indra Wijaya",
                "Joko Susilo", "Kurniawan Dwi"
            ]
        };

        // State global aplikasi
        let dataSiswa = {};
        let temporarySelections = {};
        let dataRekapAktif = null;

        // --- MANAJEMEN STATUS UI SINKRONISASI ---
        function setSyncStatus(status, text) {
            const syncBadge = document.getElementById('sync-badge');
            const syncText = document.getElementById('sync-text');
            const syncDot = document.getElementById('sync-dot');
            const dbLabel = document.getElementById('db-type-label');
            
            if (!syncBadge || !syncText || !syncDot) return;
            
            if (status === 'connecting') {
                syncDot.className = "relative inline-flex rounded-full h-2 w-2 bg-amber-500 animate-pulse";
                syncText.innerText = text || "Menghubungkan Database Cloud...";
                syncBadge.className = "flex items-center gap-2 bg-amber-950/40 border border-amber-500/30 backdrop-blur px-4 py-1.5 rounded-full text-[10px] md:text-xs font-bold text-amber-300";
            } else if (status === 'online') {
                syncDot.className = "relative inline-flex rounded-full h-2 w-2 bg-emerald-500";
                syncText.innerText = text || "Terhubung Real-time ke Supabase";
                syncBadge.className = "flex items-center gap-2 bg-emerald-950/40 border border-emerald-500/30 backdrop-blur px-4 py-1.5 rounded-full text-[10px] md:text-xs font-bold text-emerald-300";
                if (dbLabel) dbLabel.innerText = "Supabase Cloud Sync";
            } else if (status === 'error') {
                syncDot.className = "relative inline-flex rounded-full h-2 w-2 bg-rose-500 animate-pulse";
                syncText.innerText = text;
                syncBadge.className = "flex items-center gap-2 bg-rose-950/40 border border-rose-500/30 backdrop-blur px-4 py-1.5 rounded-full text-[10px] md:text-xs font-bold text-rose-300";
                if (dbLabel) dbLabel.innerText = "Koneksi Offline (Lokal)";
            } else if (status === 'offline') {
                syncDot.className = "relative inline-flex rounded-full h-2 w-2 bg-sky-500";
                syncText.innerText = text || "Mode Mandiri (Penyimpanan Lokal)";
                syncBadge.className = "flex items-center gap-2 bg-sky-950/40 border border-sky-500/30 backdrop-blur px-4 py-1.5 rounded-full text-[10px] md:text-xs font-bold text-sky-300";
                if (dbLabel) dbLabel.innerText = "Penyimpanan Enkripsi Lokal";
            }
        }

        // Mode cadangan jika kegagalan koneksi terdeteksi
        function useOfflineFallback() {
            isCloudActive = false;
            if (pollingInterval) {
                clearInterval(pollingInterval);
                pollingInterval = null;
            }
            loadStudentDatabase();
            init();
        }

        // --- INISIALISASI KONEKSI DATABASE CLOUD SUPABASE ---
        async function setupSupabase() {
            setSyncStatus('connecting');

            // Gunakan nilai localStorage jika ada, jika tidak, otomatis gunakan nilai default bawaan yang ditanam
            const supabaseUrl = localStorage.getItem('supabase_url') || DEFAULT_SUPABASE_URL;
            const supabaseKey = localStorage.getItem('supabase_anon_key') || DEFAULT_SUPABASE_KEY;

            if (!supabaseUrl || !supabaseKey) {
                console.warn("Kredensial database Supabase tidak terdeteksi. Silakan isi di form.");
                useOfflineFallback();
                return;
            }

            try {
                // Gunakan instansi lokal supabaseClient dan memanggil window.supabase untuk inisialisasi
                supabaseClient = window.supabase.createClient(supabaseUrl, supabaseKey);
                
                // Uji koneksi dengan mengambil baris pengaturan/dataSiswa
                const { data, error } = await supabaseClient
                    .from('presensi_data')
                    .select('value')
                    .eq('key', 'classes')
                    .single();

                if (error && error.code !== 'PGRST116') {
                    console.error("Kesalahan membaca Supabase:", error);
                    // RLS Aktif biasanya memicu error 401 Unauthorized / 403 Forbidden
                    if (error.status === 401 || error.status === 403 || error.message.includes('policy')) {
                        setSyncStatus('error', 'Supabase Terkunci (RLS Aktif)');
                        showToast(`Sambungan gagal: Silakan matikan RLS pada tabel Supabase Anda! (Error: ${error.message})`, "error");
                    } else {
                        setSyncStatus('error', error.message);
                        showToast(`Gagal memuat tabel Supabase: ${error.message}. Pastikan skema tabel sudah dibuat.`, "warning");
                    }
                    useOfflineFallback();
                    return;
                }

                isCloudActive = true;
                setSyncStatus('online', 'Supabase Cloud Aktif');

                if (data && data.value) {
                    dataSiswa = data.value;
                } else {
                    // Jika data classes belum ada di supabase, upsert default
                    dataSiswa = {...defaultDataSiswa};
                    const { error: insertErr } = await supabaseClient.from('presensi_data').upsert({ key: 'classes', value: dataSiswa });
                    if (insertErr) {
                        console.error("Gagal melakukan inisialisasi awal ke Supabase:", insertErr);
                        showToast(`Kesalahan tulis awal: ${insertErr.message}. Harap matikan RLS tabel.`, "error");
                    }
                }

                init();

                // Setup metode polling HTTP pengganti WebSocket untuk singkronisasi live monitoring
                setupPollingSync();
                
                // Muat laporan-laporan terdahulu dari database Supabase
                muatLaporanDariSupabase();

            } catch (e) {
                console.error("Supabase Gagal Terhubung:", e);
                setSyncStatus('error', 'Gagal hubung: ' + e.message);
                useOfflineFallback();
            }
        }

        // --- SISTEM SINKRONISASI MUTUAL LIVE SEMENTARA VIA POLLING HTTP (BEBAS WEBSOCKET) ---
        function setupPollingSync() {
            if (!isCloudActive || !supabaseClient) return;

            // Bersihkan polling lama jika ada
            if (pollingInterval) clearInterval(pollingInterval);

            // Melakukan request pemuatan berkala status klik santri harian dari DB Supabase setiap 3.5 detik
            pollingInterval = setInterval(async () => {
                if (isBroadcasting) return; // Jangan lakukan polling saat perangkat ini sedang mengirim data

                try {
                    const { data, error } = await supabaseClient
                        .from('presensi_data')
                        .select('value')
                        .eq('key', 'current_session')
                        .single();

                    if (!error && data && data.value) {
                        const sessionData = data.value;
                        if (sessionData.date === new Date().toDateString()) {
                            temporarySelections = sessionData.selections || {};
                            restoreSelections();
                        }
                    }
                } catch (e) {
                    console.warn("Polling Sync Gagal:", e);
                }
            }, 3500);

            console.log("Sistem Mutual Live Monitoring via HTTP polling aktif.");
        }

        // Memuat arsip laporan tersimpan dari Supabase
        async function muatLaporanDariSupabase() {
            if (!isCloudActive || !supabaseClient) return;

            try {
                const { data, error } = await supabaseClient
                    .from('presensi_records')
                    .select('*')
                    .order('id', { ascending: false });

                if (error) throw error;

                if (data) {
                    const results = data.map(item => ({
                        id: item.id,
                        waktuSimpan: item.waktu_simpan,
                        totalSiswa: item.total_siswa,
                        totalHadir: item.total_hadir,
                        data: item.data
                    }));
                    localStorage.setItem(DB_KEY, JSON.stringify(results));
                    renderHistori();
                }
            } catch (e) {
                console.warn("Gagal memuat arsip records dari cloud:", e);
            }
        }

        // Menyimpan & Mengaktifkan Kredensial Supabase Manual Pengguna
        function simpanConfigDatabase() {
            const url = document.getElementById('input-supabase-url').value.trim();
            const key = document.getElementById('input-supabase-key').value.trim();

            if (!url || !key) {
                showToast("Harap lengkapi isi Supabase URL dan Anon Key!", "error");
                return;
            }

            localStorage.setItem('supabase_url', url);
            localStorage.setItem('supabase_anon_key', key);
            showToast("Konfigurasi database disimpan! Memulai ulang sistem...", "success");
            
            setTimeout(() => {
                window.location.reload();
            }, 1000);
        }

        // Menghapus Konfigurasi Kustom & Kembali ke Penyimpanan Lokal
        function hapusConfigDatabase() {
            localStorage.removeItem('supabase_url');
            localStorage.removeItem('supabase_anon_key');
            showToast("Konfigurasi dihapus. Beralih kembali ke mode penyimpanan lokal...", "warning");
            
            setTimeout(() => {
                window.location.reload();
            }, 1000);
        }

        // Menyiarkan status ketukan layar aktif hari ini ke database agar pemantau lain tersinkronisasi
        async function broadcastActiveSelections() {
            saveCurrentSelections();
            if (!isCloudActive || !supabaseClient) return;

            isBroadcasting = true;
            const syncDot = document.getElementById('sync-dot');
            const syncText = document.getElementById('sync-text');
            
            if (syncDot && syncText) {
                syncDot.className = "relative inline-flex rounded-full h-2 w-2 bg-yellow-400";
                syncText.innerText = "Menyiarkan pemantauan...";
            }

            try {
                // Gunakan upsert HTTP biasa ke tabel presensi_data untuk membagikan status klik
                const payload = {
                    date: new Date().toDateString(),
                    selections: temporarySelections
                };
                
                await supabaseClient
                    .from('presensi_data')
                    .upsert({ key: 'current_session', value: payload });

            } catch(e) {
                console.warn("Gagal menyiarkan status pemantauan:", e);
            } finally {
                isBroadcasting = false;
                setTimeout(() => {
                    if (syncDot && syncText) {
                        syncDot.className = "relative inline-flex rounded-full h-2 w-2 bg-emerald-500";
                        syncText.innerText = "Monitoring Aktif (Real-time)";
                    }
                }, 800);
            }
        }

        // Memperbarui struktur roster siswa ke Cloud Supabase
        async function updateSiswaDatabase() {
            if (isCloudActive && supabaseClient) {
                try {
                    const { error } = await supabaseClient
                        .from('presensi_data')
                        .upsert({ key: 'classes', value: dataSiswa });
                    if (error) throw error;
                } catch (e) {
                    console.error("Gagal menyimpan struktur siswa ke cloud:", e);
                    showToast("Gagal menyinkronkan daftar siswa ke Cloud: " + (e.message || ""), "error");
                }
            } else {
                localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
            }
        }

        // --- SISTEM PENGEMBALIAN PENYIMPANAN LOKAL ---
        function loadStudentDatabase() {
            const saved = localStorage.getItem(SISWA_KEY);
            if (saved) {
                try { dataSiswa = JSON.parse(saved); } catch (e) { dataSiswa = {...defaultDataSiswa}; }
            } else {
                dataSiswa = {...defaultDataSiswa};
                localStorage.setItem(SISWA_KEY, JSON.stringify(dataSiswa));
            }
        }

        // Memilah dan mengurutkan kunci kelas berdasarkan tingkat angka
        function getSortedClassKeys() {
            return Object.keys(dataSiswa).sort((a, b) => {
                const numA = parseInt(a.replace(/\D/g, ''), 10);
                const numB = parseInt(b.replace(/\D/g, ''), 10);
                
                if (!isNaN(numA) && !isNaN(numB)) {
                    if (numA !== numB) return numA - numB;
                } else if (!isNaN(numA)) {
                    return -1; 
                } else if (!isNaN(numB)) {
                    return 1;
                }
                return a.localeCompare(b, undefined, { numeric: true, sensitivity: 'base' });
            });
        }

        // Menyembunyikan kelas yang tidak dipilih pada tab navigasi cepat mobile
        function filterTampilanKelas(selectedKls) {
            const classBoxes = document.querySelectorAll('.class-box');
            const tabs = document.querySelectorAll('#class-nav-tabs button');
            
            tabs.forEach(tab => {
                tab.classList.remove('bg-slate-900', 'text-white');
                tab.classList.add('bg-slate-100', 'text-slate-700', 'hover:bg-slate-200');
            });
            
            const activeTabId = selectedKls === 'ALL' ? 'tab-all' : 'tab-' + selectedKls.replace(/\s+/g, '');
            const activeTab = document.getElementById(activeTabId);
            if (activeTab) {
                activeTab.classList.remove('bg-slate-100', 'text-slate-700', 'hover:bg-slate-200');
                activeTab.classList.add('bg-slate-900', 'text-white');
            }

            classBoxes.forEach(box => {
                if (selectedKls === 'ALL') {
                    box.classList.remove('hidden');
                } else {
                    const targetId = 'card-' + selectedKls.replace(/\s+/g, '');
                    if (box.id === targetId) {
                        box.classList.remove('hidden');
                    } else {
                        box.classList.add('hidden');
                    }
                }
            });
        }

        // --- NOTIFIKASI TOAST ---
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
            
            setTimeout(() => card.classList.add('show'), 10);
            
            setTimeout(() => {
                card.classList.remove('show');
                setTimeout(() => card.remove(), 300);
            }, 3500);
        }

        // --- DIALOG KONFIRMASI KUSTOM ---
        function customConfirm(title, message, onYes, onNo = null) {
            const modal = document.getElementById('confirmModal');
            document.getElementById('confirmTitle').innerText = title;
            document.getElementById('confirmMessage').innerText = message;
            
            modal.style.display = 'flex';
            
            const btnYes = document.getElementById('btnConfirmYes');
            const btnCancel = document.getElementById('btnConfirmCancel');
            
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
            const label = document.getElementById('label-jam');
            if (label) {
                label.innerText = now.toLocaleTimeString('id-ID', opsiJam).replace(/\./g, ':');
            }
        }

        // Render Lembar Absensi Utama
        function init() {
            const labelTgl = document.getElementById('label-tgl');
            if (labelTgl) {
                labelTgl.innerText = new Date().toLocaleDateString('id-ID', { weekday: 'long', day: 'numeric', month: 'long', year: 'numeric' });
            }

            populateSelectOptions();

            const grid = document.getElementById('app-grid');
            if (!grid) return;
            grid.innerHTML = "";
            
            const sortedClasses = getSortedClassKeys();
            
            // Membuat Tab Navigasi Kelas Cepat secara dinamis
            const tabContainer = document.getElementById('class-nav-tabs');
            if (tabContainer) {
                tabContainer.innerHTML = `<button onclick="filterTampilanKelas('ALL')" id="tab-all" class="flex-1 text-center py-2 px-3 rounded-xl text-xs font-extrabold transition-all bg-slate-900 text-white min-w-[100px]">Semua Kelas</button>`;
                sortedClasses.forEach(kls => {
                    const cleanId = 'tab-' + kls.replace(/\s+/g, '');
                    tabContainer.innerHTML += `<button onclick="filterTampilanKelas('${kls}')" id="${cleanId}" class="flex-1 text-center py-2 px-3 rounded-xl text-xs font-extrabold transition-all bg-slate-100 text-slate-700 hover:bg-slate-200 min-w-[100px]">${kls}</button>`;
                });
            }

            // Membuat Kartu Kelas
            sortedClasses.forEach(kls => {
                const siswa = dataSiswa[kls];
                let html = `<div class="class-box" id="card-${kls.replace(/\s+/g, '')}">
                    <div class="relative text-center">
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
                            <td class="relative flex flex-col gap-3 pr-4">
                                <span class="name-text">${i+1}. ${n}</span>
                                
                                <!-- Tombol aksi langsung Sakit, Izin, Batal di bawah nama siswa -->
                                <div class="flex items-center gap-1.5 mt-1">
                                    <button onclick="event.stopPropagation(); setSakit('${id}', '${kls}', '${n}')" class="px-3.5 py-1.5 rounded-lg bg-sky-50 hover:bg-sky-100 border border-sky-200 text-sky-700 font-extrabold text-[10px] tracking-wide transition-all shadow-sm">
                                        🔵 Sakit
                                    </button>
                                    <button onclick="event.stopPropagation(); setIzin('${id}', '${kls}', '${n}')" class="px-3.5 py-1.5 rounded-lg bg-violet-50 hover:bg-violet-100 border border-violet-200 text-violet-700 font-extrabold text-[10px] tracking-wide transition-all shadow-sm">
                                        🟣 Izin
                                    </button>
                                    <button onclick="event.stopPropagation(); resetAbsen('${id}', '${kls}', '${n}')" class="p-1.5 rounded-lg bg-slate-50 hover:bg-slate-200 border border-slate-200 text-slate-500 font-extrabold text-[10px] transition-all shadow-sm flex items-center justify-center" title="Batal">
                                        <i data-lucide="x" class="w-3.5 h-3.5"></i>
                                    </button>
                                    <button onclick="event.stopPropagation(); hapusSiswa('${kls}', ${i})" class="p-1.5 rounded-lg hover:bg-red-50 text-slate-300 hover:text-red-500 transition-all ml-auto" title="Hapus Siswa">
                                        <i data-lucide="user-minus" class="w-3.5 h-3.5"></i>
                                    </button>
                                </div>
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
            });

            restoreSelections();
            renderHistori();
            lucide.createIcons();
        }

        // Menyimpan status kehadiran aktif sementara ke memori lokal
        function saveCurrentSelections() {
            temporarySelections = {};
            const activeRows = document.querySelectorAll('.student-row.status-hijau, .student-row.status-kuning, .student-row.status-merah, .student-row.status-sakit, .student-row.status-izin');
            activeRows.forEach(row => {
                const id = row.getAttribute('id');
                const timeText = row.querySelector('.waktu-text').innerText;
                let statusClass = '';
                if (row.classList.contains('status-hijau')) statusClass = 'status-hijau';
                else if (row.classList.contains('status-kuning')) statusClass = 'status-kuning';
                else if (row.classList.contains('status-merah')) statusClass = 'status-merah';
                else if (row.classList.contains('status-sakit')) statusClass = 'status-sakit';
                else if (row.classList.contains('status-izin')) statusClass = 'status-izin';
                
                temporarySelections[id] = { statusClass, timeText };
            });
        }

        // Memulihkan data penanda kehadiran aktif ke tampilan layar
        function restoreSelections() {
            document.querySelectorAll('.student-row').forEach(row => {
                row.className = "student-row";
                row.querySelector('.waktu-text').innerText = "-";
                row.querySelector('.check-icon').innerText = "✔";
            });

            for (const [id, value] of Object.entries(temporarySelections)) {
                const row = document.getElementById(id);
                if (row) {
                    row.className = `student-row ${value.statusClass}`;
                    row.querySelector('.waktu-text').innerText = value.timeText;
                    const chk = row.querySelector('.check-icon');
                    if (value.statusClass === 'status-sakit') chk.innerText = 'S';
                    else if (value.statusClass === 'status-izin') chk.innerText = 'I';
                    else chk.innerText = '✔';
                }
            }
        }

        // Memasukkan daftar kelas ke menu drop-down pilihan di panel kontrol
        function populateSelectOptions() {
            const select = document.getElementById('select-kelas-siswa');
            const selectAsal = document.getElementById('select-pindah-asal');
            const selectTujuan = document.getElementById('select-pindah-tujuan');
            
            if (!select || !selectAsal || !selectTujuan) return;
            
            select.innerHTML = "";
            selectAsal.innerHTML = "";
            selectTujuan.innerHTML = "";
            
            const sortedClasses = getSortedClassKeys();
            sortedClasses.forEach(kls => {
                const opt = `<option value="${kls}">${kls}</option>`;
                select.innerHTML += opt;
                selectAsal.innerHTML += opt;
                selectTujuan.innerHTML += opt;
            });
            
            updateSiswaPindahDropdown();
        }

        // Memperbarui pilihan drop-down siswa pada formulir perpindahan kelas
        function updateSiswaPindahDropdown() {
            const selectAsal = document.getElementById('select-pindah-asal');
            const selectSiswa = document.getElementById('select-pindah-siswa');
            if (!selectAsal || !selectSiswa) return;
            
            const klsAsal = selectAsal.value;
            selectSiswa.innerHTML = `<option value="ALL">-- SEMUA SISWA (Naik Kelas Massal) --</option>`;
            
            if (klsAsal && dataSiswa[klsAsal]) {
                dataSiswa[klsAsal].forEach(siswa => {
                    selectSiswa.innerHTML += `<option value="${siswa}">${siswa}</option>`;
                });
            }
        }

        // Eksekusi mutasi atau pemindahan kenaikan kelas siswa
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
                    async () => {
                        saveCurrentSelections();
                        dataSiswa[klsTujuan] = dataSiswa[klsTujuan].concat(dataSiswa[klsAsal]);
                        dataSiswa[klsTujuan].sort();
                        dataSiswa[klsAsal] = [];
                        
                        await updateSiswaDatabase();
                        showToast(`Kenaikan kelas berhasil! Seluruh siswa ${klsAsal} dipindahkan ke ${klsTujuan}.`, "success");
                        toggleForm('form-pindah');
                        init();
                    }
                );
            } else {
                customConfirm(
                    "Pindah Kelas Siswa", 
                    `Apakah Anda yakin ingin memindahkan ${targetSiswa} dari ${klsAsal} ke ${klsTujuan}?`, 
                    async () => {
                        saveCurrentSelections();
                        dataSiswa[klsAsal] = dataSiswa[klsAsal].filter(s => s !== targetSiswa);
                        dataSiswa[klsTujuan].push(targetSiswa);
                        dataSiswa[klsTujuan].sort();
                        
                        await updateSiswaDatabase();
                        showToast(`${targetSiswa} berhasil dipindahkan ke ${klsTujuan}!`, "success");
                        toggleForm('form-pindah');
                        init();
                    }
                );
            }
        }

        function toggleForm(id) {
            const el = document.getElementById(id);
            if (el.classList.contains('hidden')) {
                el.classList.remove('hidden');
            } else {
                el.classList.add('hidden');
            }
        }

        // Tambah kustom kelas baru harian
        async function tambahKelasBaru() {
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

            saveCurrentSelections();
            dataSiswa[klsBaru] = [];
            await updateSiswaDatabase();
            
            showToast(`Kelas ${klsBaru} berhasil ditambahkan!`, "success");
            input.value = "";
            toggleForm('form-kelas');
            init();
        }

        // Daftarkan nama siswa baru secara instan
        async function tambahSiswaBaru() {
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

            saveCurrentSelections();
            dataSiswa[kls].push(namaSiswa);
            dataSiswa[kls].sort();
            await updateSiswaDatabase();
            
            showToast(`${namaSiswa} berhasil didaftarkan di ${kls}!`, "success");
            inputSiswa.value = "";
            toggleForm('form-siswa');
            init();
        }

        // Hapus kelas secara permanen
        function hapusKelas(kls) {
            customConfirm("Hapus Kelas", `Apakah Anda yakin ingin menghapus ${kls} beserta seluruh siswanya secara permanen?`, async () => {
                saveCurrentSelections();
                delete dataSiswa[kls];
                await updateSiswaDatabase();
                showToast(`Kelas ${kls} berhasil dihapus!`, "success");
                init();
            });
        }

        // Hapus nama siswa terpilih
        function hapusSiswa(kls, index) {
            const nama = dataSiswa[kls][index];
            customConfirm("Hapus Siswa", `Apakah Anda yakin ingin menghapus siswa ${nama} dari ${kls}?`, async () => {
                saveCurrentSelections();
                dataSiswa[kls].splice(index, 1);
                await updateSiswaDatabase();
                showToast(`Siswa ${nama} berhasil dihapus!`, "success");
                init();
            });
        }

        // --- AKSI KETUKAN DAN TOMBOL PRESENSI ---

        // Sentuh area baris nama siswa mana saja untuk mendaftarkan kehadiran
        function toggleAbsen(id, kls, nama) {
            const row = document.getElementById(id);
            const txtWaktu = row.querySelector('.waktu-text');
            const chk = row.querySelector('.check-icon');
            const now = new Date();
            
            const h = now.toLocaleDateString('id-ID', { weekday: 'short' });
            const t = now.toLocaleDateString('id-ID', { day: 'numeric', month: 'short' });
            const j = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }).replace(/\./g, ':');
            const formattedTime = `${h}, ${t} - ${j}`;

            const jam = now.getHours();
            const fontMenit = now.getMinutes();

            if (row.classList.contains('status-sakit') || row.classList.contains('status-izin')) {
                resetAbsen(id, kls, nama);
                return;
            }

            chk.innerText = '✔';

            if (!row.classList.contains('status-hijau') && !row.classList.contains('status-kuning') && !row.classList.contains('status-merah')) {
                if (jam === 7 && fontMenit >= 0 && fontMenit <= 15) {
                    row.classList.add('status-hijau');
                    showToast(`${nama} AMAN 🟢`, "success");
                } else if (jam === 7 && fontMenit >= 16 && fontMenit <= 40) {
                    row.classList.add('status-kuning');
                    showToast(`${nama} TERLAMBAT 🟡`, "warning");
                } else if ((jam === 7 && fontMenit >= 41) || (jam === 8 && fontMenit <= 20)) {
                    row.classList.add('status-merah');
                    showToast(`${nama} PERINGATAN 🔴`, "error");
                } else {
                    row.classList.add('status-merah');
                    showToast(`${nama} PERINGATAN 🔴 (Luar Jam Presensi)`, "error");
                }
                txtWaktu.innerText = formattedTime;
            } else if (row.classList.contains('status-hijau')) {
                row.className = "student-row status-kuning";
                txtWaktu.innerText = formattedTime;
                showToast(`${nama} ditandai TERLAMBAT 🟡`, "warning");
            } else if (row.classList.contains('status-kuning')) {
                row.className = "student-row status-merah";
                txtWaktu.innerText = formattedTime;
                showToast(`${nama} ditandai PERINGATAN 🔴`, "error");
            } else {
                row.className = "student-row";
                txtWaktu.innerText = "-";
                showToast(`Status presensi ${nama} dibatalkan`, "warning");
            }

            broadcastActiveSelections();
        }

        // Mengatur status Sakit (S) secara langsung
        function setSakit(id, kls, nama) {
            const row = document.getElementById(id);
            const txtWaktu = row.querySelector('.waktu-text');
            const chk = row.querySelector('.check-icon');
            
            const now = new Date();
            const h = now.toLocaleDateString('id-ID', { weekday: 'short' });
            const t = now.toLocaleDateString('id-ID', { day: 'numeric', month: 'short' });
            const j = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }).replace(/\./g, ':');
            
            row.className = "student-row status-sakit";
            txtWaktu.innerText = `${h}, ${t} - ${j}`;
            chk.innerText = 'S';
            
            showToast(`${nama} ditandai SAKIT 🔵`, "success");
            broadcastActiveSelections();
        }

        // Mengatur status Izin (I) secara langsung
        function setIzin(id, kls, nama) {
            const row = document.getElementById(id);
            const txtWaktu = row.querySelector('.waktu-text');
            const chk = row.querySelector('.check-icon');
            
            const now = new Date();
            const h = now.toLocaleDateString('id-ID', { weekday: 'short' });
            const t = now.toLocaleDateString('id-ID', { day: 'numeric', month: 'short' });
            const j = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }).replace(/\./g, ':');
            
            row.className = "student-row status-izin";
            txtWaktu.innerText = `${h}, ${t} - ${j}`;
            chk.innerText = 'I';
            
            showToast(`${nama} ditandai IZIN 🟣`, "success");
            broadcastActiveSelections();
        }

        // Mengembalikan baris santri ke status Tanpa Keterangan (Reset)
        function resetAbsen(id, kls, nama) {
            const row = document.getElementById(id);
            const txtWaktu = row.querySelector('.waktu-text');
            const chk = row.querySelector('.check-icon');
            
            row.className = "student-row";
            txtWaktu.innerText = "-";
            chk.innerText = "✔";
            
            showToast(`Status ${nama} di-reset`, "warning");
            broadcastActiveSelections();
        }

        // Mengambil susunan payload data presensi santri aktif di layar
        function dapatkanPayloadSesiAktif() {
            const allRows = document.querySelectorAll('.student-row');
            const dataPayload = Array.from(allRows).map(r => {
                let color = "#64748b"; 
                let statusText = "TANPA KETERANGAN";
                let bgColor = "#f8fafc";
                let isHadir = false;

                if(r.classList.contains('status-hijau')) {
                    color = "#16a34a"; statusText = "AMAN"; bgColor = "#dcfce7"; isHadir = true;
                } else if(r.classList.contains('status-kuning')) { 
                    color = "#ca8a04"; statusText = "TERLAMBAT"; bgColor = "#fef9c3"; isHadir = true;
                } else if(r.classList.contains('status-merah')) { 
                    color = "#dc2626"; statusText = "PERINGATAN"; bgColor = "#fee2e2"; isHadir = true;
                } else if(r.classList.contains('status-sakit')) { 
                    color = "#0284c7"; statusText = "SAKIT"; bgColor = "#e0f2fe"; isHadir = true;
                } else if(r.classList.contains('status-izin')) { 
                    color = "#7c3aed"; statusText = "IZIN"; bgColor = "#f3e8ff"; isHadir = true;
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

            const activeRows = document.querySelectorAll('.student-row.status-hijau, .student-row.status-kuning, .student-row.status-merah, .student-row.status-sakit, .student-row.status-izin');
            return {
                id: Date.now(),
                waktuSimpan: new Date().toLocaleString('id-ID', { weekday:'long', day:'numeric', month:'short', year:'numeric', hour:'2-digit', minute:'2-digit' }),
                totalSiswa: allRows.length,
                totalHadir: activeRows.length,
                data: dataPayload
            };
        }

        // Menyimpan data absensi final ke server database Supabase
        async function simpanData() {
            const payload = dapatkanPayloadSesiAktif();
            if(payload.totalHadir === 0) {
                showToast("Pilih minimal satu siswa terlebih dahulu!", "error");
                return;
            }

            let localDb = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            localDb.unshift(payload);

            if (isCloudActive && supabaseClient) {
                try {
                    // Menyimpan rekam data harian baru ke Supabase
                    const { error } = await supabaseClient
                        .from('presensi_records')
                        .insert({
                            id: payload.id,
                            waktu_simpan: payload.waktuSimpan,
                            total_siswa: payload.totalSiswa,
                            total_hadir: payload.totalHadir,
                            data: payload.data
                        });

                    if (error) {
                        // Lempar error agar ditangkap blok catch untuk diagnosa toast
                        throw error;
                    }
                    
                    showToast("Presensi berhasil disimpan secara real-time ke Supabase Cloud!", "success");
                    
                    // Reset status visual baris presensi harian setelah disetujui tersimpan
                    document.querySelectorAll('.student-row').forEach(row => {
                        row.className = "student-row";
                        row.querySelector('.waktu-text').innerText = "-";
                        row.querySelector('.check-icon').innerText = "✔";
                    });
                    temporarySelections = {};
                    
                    // Bersihkan status sesi penanda aktif di database setelah berhasil disimpan
                    const emptyPayload = {
                        date: new Date().toDateString(),
                        selections: {}
                    };
                    await supabaseClient
                        .from('presensi_data')
                        .upsert({ key: 'current_session', value: emptyPayload });

                    broadcastActiveSelections();
                    
                    // Muat ulang data terbaru
                    await muatLaporanDariSupabase();

                } catch (e) {
                    console.error("Gagal menyimpan ke Cloud:", e);
                    // Menampilkan pesan kesalahan Postgres/RLS spesifik dari Supabase ke pengguna
                    const detailedError = e.message || e.details || JSON.stringify(e);
                    showToast(`Gagal menyimpan ke Cloud: ${detailedError}. Data dicadangkan di lokal.`, "error");
                    
                    // Cadangkan ke penyimpanan lokal
                    localStorage.setItem(DB_KEY, JSON.stringify(localDb));
                    renderHistori();
                }
            } else {
                localStorage.setItem(DB_KEY, JSON.stringify(localDb));
                showToast("Data Berhasil Disimpan secara Lokal!", "success");
                
                // Reset status visual baris presensi harian
                document.querySelectorAll('.student-row').forEach(row => {
                    row.className = "student-row";
                    row.querySelector('.waktu-text').innerText = "-";
                    row.querySelector('.check-icon').innerText = "✔";
                });
                temporarySelections = {};
                renderHistori();
            }
        }

        // Unduh laporan presensi harian ke bentuk dokumen PDF
        function cetakSesiAktif() {
            const payload = dapatkanPayloadSesiAktif();
            if (payload.totalHadir === 0) {
                showToast("Isi presensi minimal satu siswa sebelum mengunduh PDF!", "warning");
                return;
            }
            cetak(payload, 'p');
            showToast("Mengunduh laporan PDF...", "success");
        }

        // Render riwayat daftar laporan tersimpan
        function renderHistori() {
            const tbody = document.getElementById('list-histori-body');
            if (!tbody) return;

            const localDb = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            if(localDb.length === 0) { 
                tbody.innerHTML = "<tr><td colspan='3' style='text-align:center; padding:25px; color:#94a3b8;'>Belum ada laporan tersimpan.</td></tr>"; 
                return; 
            }
            tbody.innerHTML = localDb.map(s => `
                <tr class="hover:bg-slate-50 transition-colors">
                    <td class="font-bold text-slate-700">${s.waktuSimpan}</td>
                    <td><span class="text-emerald-600 font-extrabold">${s.totalHadir}</span> / ${s.totalSiswa} Siswa</td>
                    <td style="text-align:right" class="space-x-1 whitespace-nowrap">
                        <button class="bg-indigo-50 text-indigo-700 hover:bg-indigo-100 px-4 py-2 rounded-xl text-xs font-bold transition-all inline-block" onclick="bukaDetail(${s.id})">Lihat</button>
                        <button class="bg-rose-50 text-rose-600 hover:bg-rose-100 px-4 py-2 rounded-xl text-xs font-bold transition-all inline-block" onclick="hapusLaporan(${s.id})">Hapus</button>
                    </td>
                </tr>`).join('');
            lucide.createIcons();
        }

        // Membuka modal detail laporan riwayat harian
        function bukaDetail(id) {
            const localDb = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            const s = localDb.find(x => x.id === id);
            document.getElementById('judulModal').innerText = "Arsip: " + s.waktuSimpan;
            
            const dataTerurut = [
                ...s.data.filter(d => d.hadir),
                ...s.data.filter(d => !d.hadir)
            ];

            document.getElementById('bodiModal').innerHTML = dataTerurut.map((d, i) => `
                <tr style="background-color:${d.bgHex}; border-bottom:1px solid #e2e8f0;" class="text-xs md:text-sm">
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

        // Menghapus berkas laporan riwayat dari database Supabase
        function hapusLaporan(id) {
            customConfirm("Hapus Laporan", "Apakah Anda yakin ingin menghapus arsip laporan ini?", async () => {
                let localDb = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
                localDb = localDb.filter(x => x.id !== id);

                if (isCloudActive && supabaseClient) {
                    try {
                        const { error } = await supabaseClient
                            .from('presensi_records')
                            .delete()
                            .eq('id', id);

                        if (error) throw error;
                        showToast("Laporan berhasil dihapus dari Cloud.", "success");
                    } catch (e) {
                        console.error("Gagal hapus data cloud:", e);
                        showToast(`Gagal hapus dari Cloud: ${e.message}`, "error");
                        localStorage.setItem(DB_KEY, JSON.stringify(localDb));
                    }
                } else {
                    localStorage.setItem(DB_KEY, JSON.stringify(localDb));
                    showToast("Laporan lokal berhasil dihapus.", "success");
                }
                
                if (isCloudActive) {
                    await muatLaporanDariSupabase();
                } else {
                    renderHistori();
                }
            });
        }

        function tutupModal() { 
            document.getElementById('modalDetail').style.display = 'none'; 
        }

        // Melakukan kalkulasi statistik kedisiplinan (Hadir, Sakit, Izin, Alpa) berkala harian
        function hitungStatistikDisiplin(hariMundur = 7) {
            const localDb = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            if (localDb.length === 0) return null;

            const cutoffDate = Date.now() - (hariMundur * 24 * 60 * 60 * 1000);
            const sesiValid = localDb.filter(s => s.id >= cutoffDate);
            
            if (sesiValid.length === 0) return null;

            let totalSesi = sesiValid.length;
            let totalHadirGlobal = 0;
            let totalTerdaftarGlobal = 0;
            let akumulasiAman = 0;
            let akumulasiTerlambat = 0;
            let akumulasiPeringatan = 0;
            let akumulasiSakit = 0;
            let akumulasiIzin = 0;
            let akumulasiAlpa = 0;

            let studentMap = {};

            // Inisialisasi awal seluruh roster siswa untuk meminimalisir status kosong
            Object.entries(dataSiswa).forEach(([kelas, listSiswa]) => {
                listSiswa.forEach(nama => {
                    studentMap[nama] = {
                        nama: nama,
                        kelas: kelas,
                        aman: 0,
                        terlambat: 0,
                        peringatan: 0,
                        sakit: 0,
                        izin: 0,
                        alpa: 0,
                        totalSesi: 0
                    };
                });
            });

            sesiValid.forEach(sesi => {
                totalHadirGlobal += sesi.totalHadir;
                totalTerdaftarGlobal += sesi.totalSiswa;

                sesi.data.forEach(siswa => {
                    if (!studentMap[siswa.nama]) {
                        studentMap[siswa.nama] = {
                            nama: siswa.nama,
                            kelas: siswa.kelas,
                            aman: 0,
                            terlambat: 0,
                            peringatan: 0,
                            sakit: 0,
                            izin: 0,
                            alpa: 0,
                            totalSesi: 0
                        };
                    }

                    const profile = studentMap[siswa.nama];
                    profile.totalSesi++;

                    if (siswa.status === "AMAN") {
                        profile.aman++;
                        akumulasiAman++;
                    } else if (siswa.status === "TERLAMBAT") {
                        profile.terlambat++;
                        akumulasiTerlambat++;
                    } else if (siswa.status === "PERINGATAN") {
                        profile.peringatan++;
                        akumulasiPeringatan++;
                    } else if (siswa.status === "SAKIT") {
                        profile.sakit++;
                        akumulasiSakit++;
                    } else if (siswa.status === "IZIN") {
                        profile.izin++;
                        akumulasiIzin++;
                    } else {
                        profile.alpa++;
                        akumulasiAlpa++;
                    }
                });
            });

            const daftarSiswa = Object.values(studentMap).map(profile => {
                const totalSesiSiswa = profile.totalSesi || totalSesi;
                const totalHadir = profile.aman + profile.terlambat + profile.peringatan + profile.sakit + profile.izin;
                const rasioHadir = totalSesiSiswa > 0 ? ((totalHadir / totalSesiSiswa) * 100).toFixed(1) : "0.0";
                
                return {
                    ...profile,
                    totalSesiSiswa,
                    rasioHadir: parseFloat(rasioHadir)
                };
            });

            const rerataSesi = ((totalHadirGlobal / totalTerdaftarGlobal) * 100).toFixed(1);

            return {
                periodeHari: hariMundur,
                totalSesi: totalSesi,
                kehadiranRerata: rerataSesi,
                totalAman: akumulasiAman,
                totalTerlambat: akumulasiTerlambat,
                totalPeringatan: akumulasiPeringatan,
                totalSakit: akumulasiSakit,
                totalIzin: akumulasiIzin,
                totalAlpa: akumulasiAlpa,
                daftarSiswa: daftarSiswa
            };
        }

        // Membuka jendela dasbor rekapitulasi analisis berkala
        function bukaDashboardRekap(hariMundur = 7) {
            const rekap = hitungStatistikDisiplin(hariMundur);
            if (!rekap) {
                showToast("Histori presensi kosong! Harap lakukan presensi minimal 1 kali terlebih dahulu.", "warning");
                return;
            }

            dataRekapAktif = rekap;

            const labelPeriode = hariMundur === 7 ? "1 Minggu Terakhir" : "1 Bulan Terakhir";
            document.getElementById('rekap-modal-title').innerText = `Rekapitulasi Disiplin & Presensi (${labelPeriode})`;
            document.getElementById('rekap-modal-subtitle').innerText = `Agregasi kepatuhan siswa dalam rentang waktu ${hariMundur} hari terakhir`;

            document.getElementById('stat-rerata').innerText = `${rekap.kehadiranRerata}%`;
            document.getElementById('stat-aman').innerText = rekap.totalAman;
            document.getElementById('stat-terlambat').innerText = rekap.totalTerlambat;
            document.getElementById('stat-peringatan').innerText = rekap.totalPeringatan;
            document.getElementById('stat-sakit').innerText = rekap.totalSakit;
            document.getElementById('stat-izin').innerText = rekap.totalIzin;
            document.getElementById('stat-alpa').innerText = rekap.totalAlpa;

            // Membuat pilihan opsi filter kelas di dalam modal analisis
            const filterKelas = document.getElementById('rekap-filter-kelas');
            filterKelas.innerHTML = `<option value="ALL">Semua Kelas</option>`;
            
            const sortedClasses = getSortedClassKeys();
            sortedClasses.forEach(kls => {
                filterKelas.innerHTML += `<option value="${kls}">${kls}</option>`;
            });

            document.getElementById('rekap-search').value = "";
            renderTabelRekap(rekap.daftarSiswa);

            document.getElementById('modalRekap').style.display = 'flex';
            lucide.createIcons();
        }

        // Render tabel data kumulatif santri dalam dasbor modal
        function renderTabelRekap(listSiswa) {
            const tbody = document.getElementById('table-rekap-body');
            if (!tbody) return;
            
            if (listSiswa.length === 0) {
                tbody.innerHTML = `<tr><td colspan="9" class="p-6 text-center text-slate-400 italic">Tidak ada nama siswa yang cocok dengan filter</td></tr>`;
                return;
            }

            tbody.innerHTML = listSiswa.map(s => {
                let badgeColor = "bg-slate-100 text-slate-700";
                if (s.rasioHadir >= 90) badgeColor = "bg-emerald-100 text-emerald-800 font-bold";
                else if (s.rasioHadir >= 75) badgeColor = "bg-yellow-100 text-yellow-800";
                else badgeColor = "bg-rose-100 text-rose-800 font-bold";

                const cellAman = s.aman > 0 ? `<span class="bg-emerald-50 text-emerald-700 border border-emerald-200 px-2.5 py-1 rounded-full font-bold text-[11px] inline-block min-w-[28px]">${s.aman}</span>` : `<span class="text-slate-300">-</span>`;
                const cellTerlambat = s.terlambat > 0 ?  `<span class="bg-amber-50 text-amber-700 border border-amber-200 px-2.5 py-1 rounded-full font-bold text-[11px] inline-block min-w-[28px]">${s.terlambat}</span>` : `<span class="text-slate-300">-</span>`;
                const cellPeringatan = s.peringatan > 0 ? `<span class="bg-rose-50 text-rose-700 border border-rose-200 px-2.5 py-1 rounded-full font-bold text-[11px] inline-block min-w-[28px]">${s.peringatan}</span>` : `<span class="text-slate-300">-</span>`;
                const cellSakit = s.sakit > 0 ? `<span class="bg-sky-50 text-sky-700 border border-sky-200 px-2.5 py-1 rounded-full font-bold text-[11px] inline-block min-w-[28px]">${s.sakit}</span>` : `<span class="text-slate-300">-</span>`;
                const cellIzin = s.izin > 0 ? `<span class="bg-violet-50 text-violet-700 border border-violet-200 px-2.5 py-1 rounded-full font-bold text-[11px] inline-block min-w-[28px]">${s.izin}</span>` : `<span class="text-slate-300">-</span>`;
                const cellAlpa = s.alpa > 0 ? `<span class="bg-slate-100 text-slate-600 border border-slate-200 px-2.5 py-1 rounded-full font-bold text-[11px] inline-block min-w-[28px]">${s.alpa}</span>` : `<span class="text-slate-300">-</span>`;

                return `
                    <tr class="hover:bg-slate-50 transition-colors">
                        <td class="p-3 font-semibold text-slate-800">${s.nama}</td>
                        <td class="p-3 font-medium text-slate-500">${s.kelas}</td>
                        <td class="p-3 text-center">${cellAman}</td>
                        <td class="p-3 text-center">${cellTerlambat}</td>
                        <td class="p-3 text-center">${cellPeringatan}</td>
                        <td class="p-3 text-center">${cellSakit}</td>
                        <td class="p-3 text-center">${cellIzin}</td>
                        <td class="p-3 text-center">${cellAlpa}</td>
                        <td class="p-3 text-center">
                            <span class="px-2 py-1 rounded text-[10px] ${badgeColor}">${s.rasioHadir}%</span>
                        </td>
                    </tr>
                `;
            }).join('');
        }

        // Menyaring data santri dalam dasbor secara dinamis saat melakukan input pencarian
        function filterTabelRekap() {
            if (!dataRekapAktif) return;

            const searchVal = document.getElementById('rekap-search').value.toLowerCase().trim();
            const filterKelas = document.getElementById('rekap-filter-kelas').value;

            const filtered = dataRekapAktif.daftarSiswa.filter(s => {
                const matchSearch = s.nama.toLowerCase().includes(searchVal);
                const matchKelas = filterKelas === "ALL" || s.kelas === filterKelas;
                return matchSearch && matchKelas;
            });

            renderTabelRekap(filtered);
        }

        function tutupDashboardRekap() {
            document.getElementById('modalRekap').style.display = 'none';
        }

        // Pembuatan dokumen cetak laporan analisis berkala menggunakan jspdf
        function cetakLaporanRekapBerkala() {
            if (!dataRekapAktif) return;

            const { jsPDF } = window.jspdf;
            const doc = new jsPDF({ orientation: 'p' });
            const pageWidth = doc.internal.pageSize.getWidth();
            const labelPeriode = dataRekapAktif.periodeHari === 7 ? "Mingguan (7 Hari)" : "Bulanan (30 Hari)";

            doc.setFont("Helvetica", "bold");
            doc.setFontSize(14);
            doc.setTextColor(15, 23, 42);
            doc.text("SMP HAMALATUL QURAN RINGINAGUNG", pageWidth / 2, 14, { align: 'center' });
            
            doc.setFontSize(11);
            doc.setTextColor(37, 99, 235);
            doc.text(`LAPORAN REKAPITULASI KEDISPLINAN SISWA (${labelPeriode.toUpperCase()})`, pageWidth / 2, 20, { align: 'center' });
            
            doc.setFontSize(8);
            doc.setFont("Helvetica", "normal");
            doc.setTextColor(100, 116, 139);
            doc.text(`Laporan dibuat secara otomatis pada tanggal: ${new Date().toLocaleString('id-ID')}`, pageWidth / 2, 24, { align: 'center' });

            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(15, 23, 42);
            doc.text("A. RINGKASAN STATISTIK KEHADIRAN SEKOLAH", 14, 33);

            doc.autoTable({
                startY: 36,
                head: [['Indikator Kinerja Kehadiran', 'Persentase / Sesi Kumulatif']],
                body: [
                    ['Rata-rata Tingkat Kehadiran Sekolah', `${dataRekapAktif.kehadiranRerata}%`],
                    ['Jumlah Pemantauan Hari Efektif Presensi', `${dataRekapAktif.totalSesi} Hari Sesi`],
                    ['Total Siswa Hadir Tepat Waktu (Aman)', `${dataRekapAktif.totalAman} Sesi`],
                    ['Total Siswa Datang Terlambat (Terlambat)', `${dataRekapAktif.totalTerlambat} Sesi`],
                    ['Total Siswa Kategori Pembinaan (Peringatan)', `${dataRekapAktif.totalPeringatan} Sesi`],
                    ['Total Siswa Sakit (Sakit)', `${dataRekapAktif.totalSakit} Sesi`],
                    ['Total Siswa Izin (Izin)', `${dataRekapAktif.totalIzin} Sesi`],
                    ['Total Sesi Tidak Hadir / Alpa (Alpa)', `${dataRekapAktif.totalAlpa} Sesi`],
                ],
                headStyles: { fillColor: [37, 99, 235], fontStyle: 'bold' },
                styles: { font: "Helvetica", fontSize: 9 }
            });

            let nextY = doc.lastAutoTable.finalY + 12;
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(15, 23, 42);
            doc.text("B. RINCIAN KEDISPLINAN DAN KEHADIRAN SISWA", 14, nextY);

            const sortedSiswa = [...dataRekapAktif.daftarSiswa].sort((a, b) => {
                const kA = a.kelas;
                const kB = b.kelas;
                if (kA !== kB) {
                    const numA = parseInt(kA.replace(/\D/g, ''), 10);
                    const numB = parseInt(kB.replace(/\D/g, ''), 10);
                    if (!isNaN(numA) && !isNaN(numB)) return numA - numB;
                    return kA.localeCompare(kB);
                }
                return a.nama.localeCompare(b.nama);
            });

            doc.autoTable({
                startY: nextY + 3,
                head: [['No', 'Nama Siswa', 'Kelas', 'Aman', 'Terlambat', 'Peringatan', 'Sakit', 'Izin', 'Alpa', 'Rasio']],
                body: sortedSiswa.map((s, i) => [
                    i + 1, 
                    s.nama, 
                    s.kelas, 
                    s.aman, 
                    s.terlambat, 
                    s.peringatan,
                    s.sakit,
                    s.izin, 
                    s.alpa, 
                    `${s.rasioHadir}%`
                ]),
                headStyles: { fillColor: [15, 23, 42], fontStyle: 'bold' },
                styles: { font: "Helvetica", fontSize: 8 },
                columnStyles: {
                    0: { cellWidth: 8 },
                    2: { cellWidth: 15 },
                    3: { cellWidth: 14, halign: 'center' },
                    4: { cellWidth: 16, halign: 'center' },
                    5: { cellWidth: 18, halign: 'center' },
                    6: { cellWidth: 14, halign: 'center' },
                    7: { cellWidth: 14, halign: 'center' },
                    8: { cellWidth: 14, halign: 'center' },
                    9: { cellWidth: 16, halign: 'center' }
                },
                didParseCell: function (data) {
                    if (data.section === 'body') {
                        const cellValue = parseInt(data.cell.text[0], 10);
                        if (cellValue > 0) {
                            if (data.column.index === 3) {
                                data.cell.styles.fillColor = [220, 252, 231];
                                data.cell.styles.textColor = [20, 83, 45];
                            } else if (data.column.index === 4) {
                                data.cell.styles.fillColor = [254, 249, 195];
                                data.cell.styles.textColor = [113, 63, 18];
                            } else if (data.column.index === 5) {
                                data.cell.styles.fillColor = [254, 226, 226];
                                data.cell.styles.textColor = [127, 29, 29];
                            } else if (data.column.index === 6) {
                                data.cell.styles.fillColor = [224, 242, 254];
                                data.cell.styles.textColor = [3, 105, 161];
                            } else if (data.column.index === 7) {
                                data.cell.styles.fillColor = [243, 232, 255];
                                data.cell.styles.textColor = [109, 40, 217];
                            } else if (data.column.index === 8) {
                                data.cell.styles.fillColor = [241, 245, 249];
                                data.cell.styles.textColor = [71, 85, 105];
                            }
                        }
                    }
                }
            });

            let footerY = doc.lastAutoTable.finalY + 12;
            if (footerY + 30 > doc.internal.pageSize.getHeight() - 15) {
                doc.addPage();
                footerY = 15;
            }

            doc.setFont("Helvetica", "bold");
            doc.setFontSize(8.5);
            doc.text("Mengetahui,", 14, footerY + 10);
            doc.text("Kepala Sekolah SMP HQ", 14, footerY + 14);
            
            doc.setLineWidth(0.2);
            doc.setDrawColor(203, 213, 225);
            doc.line(14, footerY + 32, 60, footerY + 32);

            doc.save("Rekapitulasi_Disiplin_HQ_Hari_" + dataRekapAktif.periodeHari + ".pdf");
            showToast("Laporan PDF Rekapitulasi Berhasil Diunduh!", "success");
        }

        // Pembuatan lembar dokumen PDF Harian
        function cetak(sesi, orientasi) {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF({ orientation: orientasi });
            const pageWidth = doc.internal.pageSize.getWidth();
            
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(15);
            doc.setTextColor(30, 41, 59);
            doc.text("SMP HAMALATUL QURAN RINGINAGUNG", pageWidth / 2, 14, { align: 'center' });
            
            doc.setFontSize(11);
            doc.setFont("Helvetica", "normal");
            doc.text("LAPORAN PRESENSI DIGITAL SISWA", pageWidth / 2, 20, { align: 'center' });
            
            doc.setFontSize(9);
            doc.setTextColor(100, 116, 139);
            doc.text("Waktu Arsip: " + sesi.waktuSimpan, pageWidth / 2, 25, { align: 'center' });
            
            const siswaHadir = sesi.data.filter(x => x.hadir);
            const siswaAbsen = sesi.data.filter(x => !x.hadir);

            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(22, 163, 74);
            doc.text("A. DAFTAR SISWA MASUK / SAKIT / IZIN (" + siswaHadir.length + " Siswa)", 14, 33);

            doc.autoTable({ 
                startY: 36, 
                head: [['No', 'Nama', 'Kelas', 'Waktu Presensi', 'Status']], 
                body: siswaHadir.map((x, i) => [i+1, x.nama, x.kelas, x.waktu, x.status]),
                headStyles: { fillColor: [22, 163, 74], fontStyle: 'bold' },
                styles: { font: "Helvetica", fontSize: 9 },
                didParseCell: function (data) {
                    if (data.section === 'body') {
                        const statusSiswa = data.row.raw[4];
                        if (statusSiswa === 'AMAN') {
                            data.cell.styles.fillColor = [220, 252, 231]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [22, 163, 74]; 
                        } else if (statusSiswa === 'TERLAMBAT') {
                            data.cell.styles.fillColor = [254, 249, 195]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [202, 138, 4];  
                        } else if (statusSiswa === 'PERINGATAN') {
                            data.cell.styles.fillColor = [254, 226, 226]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [220, 38, 38];  
                        } else if (statusSiswa === 'SAKIT') {
                            data.cell.styles.fillColor = [224, 242, 254]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [3, 105, 161];  
                        } else if (statusSiswa === 'IZIN') {
                            data.cell.styles.fillColor = [243, 232, 255]; 
                            if(data.column.index === 4) data.cell.styles.textColor = [109, 40, 217];  
                        }
                    }
                }
            });

            // --- TABEL DAFTAR SISWA ALPA ---
            let nextY = doc.lastAutoTable.finalY + 12;
            doc.setFont("Helvetica", "bold");
            doc.setFontSize(10);
            doc.setTextColor(220, 38, 38); 
            doc.text("B. DAFTAR SISWA TIDAK HADIR / SAKIT / IZIN / ALPA (" + siswaAbsen.length + " Siswa)", 14, nextY);

            doc.autoTable({ 
                startY: nextY + 3, 
                head: [['No', 'Nama', 'Kelas', 'Keterangan']], 
                body: siswaAbsen.map((x, i) => [i+1, x.nama, x.kelas, x.status]),
                headStyles: { fillColor: [15, 23, 42], fontStyle: 'bold' },
                styles: { font: "Helvetica", fontSize: 9 },
                didParseCell: function (data) {
                    if (data.section === 'body') {
                        const statusSiswa = data.row.raw[3];
                        if (statusSiswa === 'SAKIT') {
                            data.cell.styles.fillColor = [224, 242, 254]; 
                            data.cell.styles.textColor = [3, 105, 161];
                            data.cell.styles.fontStyle = 'bold';
                        } else if (statusSiswa === 'IZIN') {
                            data.cell.styles.fillColor = [243, 232, 255]; 
                            data.cell.styles.textColor = [109, 40, 217];
                            data.cell.styles.fontStyle = 'bold';
                        } else {
                            data.cell.styles.fillColor = [254, 226, 226]; 
                            data.cell.styles.textColor = [150, 0, 0]; 
                        }
                    }
                }
            });
            
            const namaFileBersih = sesi.waktuSimpan.replace(/[\/\\*?:|<>]/g, '-').replace(/\s+/g, '_').split(',_')[0];
            doc.save("Presensi_SMP_HQ_" + namaFileBersih + ".pdf");
        }

        // Binding seluruh fungsi interaktif ke lingkup window (Aturan ESM)
        window.filterTampilanKelas = filterTampilanKelas;
        window.updateSiswaPindahDropdown = updateSiswaPindahDropdown;
        window.pindahkanSiswaKelas = pindahkanSiswaKelas;
        window.toggleForm = toggleForm;
        window.tambahKelasBaru = tambahKelasBaru;
        window.tambahSiswaBaru = tambahSiswaBaru;
        window.hapusKelas = hapusKelas;
        window.hapusSiswa = hapusSiswa;
        window.toggleAbsen = toggleAbsen;
        window.setSakit = setSakit;
        window.setIzin = setIzin;
        window.resetAbsen = resetAbsen;
        window.simpanData = simpanData;
        window.cetakSesiAktif = cetakSesiAktif;
        window.renderHistori = renderHistori;
        window.bukaDetail = bukaDetail;
        window.hapusLaporan = hapusLaporan;
        window.tutupModal = tutupModal;
        window.bukaDashboardRekap = bukaDashboardRekap;
        window.renderTabelRekap = renderTabelRekap;
        window.filterTabelRekap = filterTabelRekap;
        window.tutupDashboardRekap = tutupDashboardRekap;
        window.cetakLaporanRekapBerkala = cetakLaporanRekapBerkala;
        window.simpanConfigDatabase = simpanConfigDatabase;
        window.hapusConfigDatabase = hapusConfigDatabase;

        // Jalankan koneksi saat halaman dimuat sepenuhnya
        window.addEventListener('DOMContentLoaded', () => {
            setupSupabase();
            updateJamAktif();
            setInterval(updateJamAktif, 1000);

            // Tampilkan kembali input config jika tersimpan di localStorage
            const savedUrl = localStorage.getItem('supabase_url');
            const savedKey = localStorage.getItem('supabase_anon_key');
            if (savedUrl && document.getElementById('input-supabase-url')) {
                document.getElementById('input-supabase-url').value = savedUrl;
            }
            if (savedKey && document.getElementById('input-supabase-key')) {
                document.getElementById('input-supabase-key').value = savedKey;
            }
        });
    </script>
</body>
</html>
