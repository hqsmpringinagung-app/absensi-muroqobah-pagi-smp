<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Presensi Pro - SMP Hamalatul Quran</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.23/jspdf.plugin.autotable.min.js"></script>
    
    <style>
        :root {
            --primary: #1e293b;
            --success: #16a34a;
            --warning: #ca8a04;
            --danger: #dc2626;
            --bg: #f8fafc;
        }

        body { font-family: 'Inter', sans-serif; background: var(--bg); margin: 0; color: var(--primary); }
        .header { background: var(--primary); color: white; padding: 25px 20px; text-align: center; border-bottom-left-radius: 30px; border-bottom-right-radius: 30px; }
        
        .container { padding: 20px; max-width: 1400px; margin: 0 auto; }
        .grid-container { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 20px; }
        
        .class-box { background: white; border-radius: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); overflow: hidden; margin-bottom: 20px; }
        .class-box h2 { background: #f1f5f9; padding: 15px; font-size: 1.1rem; text-align: center; margin: 0; border-bottom: 1px solid #e2e8f0; }
        
        .input-table { width: 100%; border-collapse: collapse; }
        .input-table td { padding: 12px; border-bottom: 1px solid #f1f5f9; vertical-align: middle; }
        
        .student-row { cursor: pointer; transition: 0.2s; }
        .name-text { font-weight: 700; font-size: 0.95rem; }
        .waktu-text { font-size: 0.75rem; font-weight: 800; color: #64748b; white-space: nowrap; }

        .check-icon { 
            width: 22px; height: 22px; border: 2px solid #e2e8f0; 
            border-radius: 50%; display: inline-flex; align-items: center; 
            justify-content: center; color: transparent; font-size: 0.7rem;
        }

        /* Status Warna Input Utama */
        .status-hijau { background-color: #f0fdf4 !important; color: #16a34a !important; }
        .status-hijau .check-icon { background-color: #16a34a; border-color: #16a34a; color: white; }
        .status-hijau .waktu-text { color: #16a34a !important; }

        .status-kuning { background-color: #fefce8 !important; color: #ca8a04 !important; }
        .status-kuning .check-icon { background-color: #ca8a04; border-color: #ca8a04; color: white; }
        .status-kuning .waktu-text { color: #ca8a04 !important; }

        .status-merah { background-color: #fef2f2 !important; color: #dc2626 !important; }
        .status-merah .check-icon { background-color: #dc2626; border-color: #dc2626; color: white; }
        .status-merah .waktu-text { color: #dc2626 !important; }

        .main-actions { text-align: center; margin: 30px 0; }
        .btn-save { background: var(--primary); color: white; border: none; padding: 15px 40px; border-radius: 50px; font-weight: 800; cursor: pointer; }

        .history-area { background: white; padding: 20px; border-radius: 20px; margin-top: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
        .history-table { width: 100%; border-collapse: collapse; }
        .history-table th { text-align: left; padding: 12px; color: #94a3b8; font-size: 0.8rem; border-bottom: 2px solid #f1f5f9; }
        .history-table td { padding: 12px; border-bottom: 1px solid #f1f5f9; font-size: 0.85rem; font-weight: 700; }

        .modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.8); display: none; justify-content: center; align-items: center; z-index: 1000; backdrop-filter: blur(5px); }
        .modal-content { background: white; padding: 25px; border-radius: 25px; width: 95%; max-width: 900px; max-height: 85vh; overflow-y: auto; }
        
        .btn-pdf { width: 100%; padding: 15px; border-radius: 12px; border: none; font-weight: 800; color: white; cursor: pointer; }
    </style>
</head>
<body>

    <div class="header">
        <h1 style="margin:0; font-size: 1.5rem; letter-spacing: 1px;">SMP HAMALATUL QURAN RINGINAGUNG</h1>
        <h2 style="margin:5px 0 0 0; font-size:1.1rem; opacity:0.9; font-weight:400;">PRESENSI DIGITAL PRO</h2>
        <div id="label-tgl" style="margin-top:10px; opacity:0.7; font-size:0.9rem;"></div>
    </div>

    <div class="container">
        <div id="app-grid" class="grid-container"></div>
        
        <div class="main-actions">
            <button class="btn-save" onclick="simpanData()">💾 SIMPAN DATA SEKARANG</button>
        </div>

        <div class="history-area">
            <h2 style="margin: 0 0 15px 0; font-size: 1.1rem;">📂 Laporan Tersimpan</h2>
            <div style="overflow-x: auto;">
                <table class="history-table">
                    <thead><tr><th>Waktu Simpan</th><th>Total Hadir / Total Siswa</th><th style="text-align:right">Opsi</th></tr></thead>
                    <tbody id="list-histori-body"></tbody>
                </table>
            </div>
        </div>
    </div>

    <div id="modalDetail" class="modal">
        <div class="modal-content">
            <h3 id="judulModal" style="margin-top:0">Detail Laporan</h3>
            <div style="overflow-x:auto">
                <table style="width:100%; border-collapse: collapse;">
                    <thead>
                        <tr style="text-align:left; background:#f8fafc; font-size:0.75rem; color:#64748b;">
                            <th style="padding:12px;">No</th>
                            <th style="padding:12px;">Nama Siswa</th>
                            <th style="padding:12px;">Kelas</th>
                            <th style="padding:12px;">Waktu & Tanggal</th>
                            <th style="padding:12px;">Status</th>
                        </tr>
                    </thead>
                    <tbody id="bodiModal"></tbody>
                </table>
            </div>
            <div style="margin-top: 25px; display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                <button class="btn-pdf" id="btnPdfP" style="background: #ef4444;">📄 PDF Portrait</button>
                <button class="btn-pdf" id="btnPdfL" style="background: #8b5cf6;">📑 PDF Landscape</button>
            </div>
            <button onclick="tutupModal()" style="width:100%; padding:15px; margin-top:10px; border-radius:12px; border:none; background:#f1f5f9; font-weight:800; cursor:pointer; color:#64748b;">Tutup</button>
        </div>
    </div>

<script>
    const DB_KEY = 'db_presensi_v8_final';
    const dataSiswa = {
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
        "Kelas 9": ["-"]
    };

    function init() {
        document.getElementById('label-tgl').innerText = new Date().toLocaleDateString('id-ID', { weekday: 'long', day: 'numeric', month: 'long', year: 'numeric' });
        const grid = document.getElementById('app-grid');
        grid.innerHTML = "";
        for (const [kls, siswa] of Object.entries(dataSiswa)) {
            let html = `<div class="class-box"><h2>${kls}</h2><table class="input-table"><tbody>`;
            siswa.forEach((n, i) => {
                const id = kls.replace(/\s+/g, '') + i;
                html += `<tr class="student-row" id="${id}" onclick="toggleAbsen('${id}')">
                    <td style="width:30px"><span class="check-icon">✔</span></td>
                    <td><span class="name-text">${i+1}. ${n}</span></td>
                    <td style="text-align:right"><span class="waktu-text">-</span></td>
                </tr>`;
            });
            grid.innerHTML += html + `</tbody></table></div>`;
        }
        renderHistori();
    }

    function toggleAbsen(id) {
        const row = document.getElementById(id);
        const txtWaktu = row.querySelector('.waktu-text');
        
        if(!row.classList.contains('status-hijau') && !row.classList.contains('status-kuning') && !row.classList.contains('status-merah')) {
            const now = new Date();
            const jam = now.getHours();
            const menit = now.getMinutes();

            if (jam < 7 || (jam === 7 && menit <= 25)) row.classList.add('status-hijau');
            else if (jam === 7 && menit <= 45) row.classList.add('status-kuning');
            else row.classList.add('status-merah');
            
            const h = now.toLocaleDateString('id-ID', { weekday: 'short' });
            const t = now.toLocaleDateString('id-ID', { day: 'numeric', month: 'short' });
            const j = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }).replace(/\./g, ':');
            txtWaktu.innerText = `${h}, ${t} - ${j}`;
        } else {
            row.className = "student-row";
            txtWaktu.innerText = "-";
        }
    }

    function simpanData() {
        const allRows = document.querySelectorAll('.student-row');
        const activeRows = document.querySelectorAll('.status-hijau, .status-kuning, .status-merah');
        
        if(activeRows.length === 0) return alert("Pilih minimal satu siswa yang hadir sebelum menyimpan!");

        const dataPayload = Array.from(allRows).map(r => {
            let color = "#64748b"; 
            let statusText = "TANPA KETERANGAN";
            let bgColor = "#f8fafc";
            let isHadir = false;

            if(r.classList.contains('status-hijau')) {
                color = "#16a34a"; statusText = "TEPAT WAKTU"; bgColor = "#f0fdf4"; isHadir = true;
            } else if(r.classList.contains('status-kuning')) { 
                color = "#ca8a04"; statusText = "PERINGATAN"; bgColor = "#fefce8"; isHadir = true;
            } else if(r.classList.contains('status-merah')) { 
                color = "#dc2626"; statusText = "TERLAMBAT"; bgColor = "#fef2f2"; isHadir = true;
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
        alert("Data Berhasil Disimpan!");
        location.reload();
    }

    function renderHistori() {
        const tbody = document.getElementById('list-histori-body');
        const db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
        if(db.length === 0) { tbody.innerHTML = "<tr><td colspan='3' style='text-align:center; padding:20px;'>Kosong.</td></tr>"; return; }
        tbody.innerHTML = db.map(s => `
            <tr>
                <td>${s.waktuSimpan}</td>
                <td><span style="color:#16a34a">${s.totalHadir}</span> / ${s.totalSiswa} Siswa</td>
                <td style="text-align:right">
                    <button style="background:#3b82f6; color:white; border:none; padding:6px 12px; border-radius:6px; cursor:pointer;" onclick="bukaDetail(${s.id})">Lihat</button>
                    <button style="background:#fee2e2; color:#ef4444; border:none; padding:6px 12px; border-radius:6px; cursor:pointer;" onclick="hapus(${s.id})">Hapus</button>
                </td>
            </tr>`).join('');
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
            <tr style="background-color:${d.bgHex}; border-bottom:1px solid #eee;">
                <td style="padding:12px; color:#333;">${i+1}</td>
                <td style="padding:12px; color:#333; font-weight:bold;">${d.nama}</td>
                <td style="padding:12px; color:#333;">${d.kelas}</td>
                <td style="padding:12px; color:#333;">${d.waktu}</td>
                <td style="padding:12px; color:${d.hexColor}; font-weight:800;">${d.status}</td>
            </tr>`).join('');
        
        document.getElementById('btnPdfP').onclick = () => cetak(s, 'p');
        document.getElementById('btnPdfL').onclick = () => cetak(s, 'l');
        document.getElementById('modalDetail').style.display = 'flex';
    }

    function hapus(id) {
        if(confirm("Hapus?")) {
            let db = JSON.parse(localStorage.getItem(DB_KEY) || "[]");
            localStorage.setItem(DB_KEY, JSON.stringify(db.filter(x => x.id !== id)));
            renderHistori();
        }
    }

    function tutupModal() { document.getElementById('modalDetail').style.display = 'none'; }

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
                        data.cell.styles.fillColor = [240, 253, 244]; 
                        if(data.column.index === 4) data.cell.styles.textColor = [22, 163, 74]; 
                    } else if (statusSiswa === 'PERINGATAN') {
                        data.cell.styles.fillColor = [254, 252, 232]; 
                        if(data.column.index === 4) data.cell.styles.textColor = [202, 138, 4];  
                    } else if (statusSiswa === 'TERLAMBAT') {
                        data.cell.styles.fillColor = [254, 242, 242]; 
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
    window.onload = init;
</script>
</body>
</html>
