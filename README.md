<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Sistem Ruang Kelas Digital</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css">
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/umd/supabase.min.js"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            dasar: '#050714',
            kartu: '#0F1424',
            aksen_biru: '#165DFF',
            aksen_emas: '#D4AF37',
            lembut: '#A8B2D1',
            batas: '#232940'
          },
          fontFamily: {
            inter: ['Inter', 'sans‑serif']
          },
          boxShadow: {
            'kilau': '0 0 18px rgba(212, 175, 55, 0.15), 0 4px 12px rgba(22, 93, 255, 0.1)'
          }
        }
      }
    }
  </script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
    body { font-family: 'Inter', sans‑serif; background‑color: #050714; background‑image: radial-gradient(circle at top, rgba(22,93,255,0.08), transparent 55%); min‑height: 100vh; }
    .kaca‑mewah { background: rgba(15, 20, 36, 0.85); border: 1px solid rgba(212, 175, 55, 0.18); backdrop‑filter: blur(10px); box‑shadow: 0 8px 24px rgba(0,0,0,0.35); }
    .tombol‑emas { background: linear-gradient(135deg, #D4AF37, #F0C850); color: #050714; font‑weight: 600; }
    .tombol‑emas:hover { filter: brightness(1.08); box‑shadow: 0 0 16px rgba(212,175,55,0.25); }
    .tombol‑biru { background: linear-gradient(135deg, #165DFF, #367BFF); }
    .tombol‑biru:hover { filter: brightness(1.08); box‑shadow: 0 0 16px rgba(22,93,255,0.25); }
    .sembunyi { display: none !important; }
    .garis‑batas { border‑color: #232940; }
    input, select, textarea { background‑color: #050714; border‑color: #232940; color: #fff; outline: none; transition: border‑color .25s; }
    input:focus, select:focus, textarea:focus { border‑color: #D4AF37; }
    .teks‑emas { color: #D4AF37; }
    .teks‑biru { color: #367BFF; }
  </style>
</head>
<body class="text‑white">

<!-- KONEKSI DATABASE KAMU -->
<script>
  const SUPABASE_URL = "https://hzpvbigkwustmwzttkvv.supabase.co";
  const SUPABASE_KEY = "sb_publishable_7Bv46KYh1TDiydTqpw_iBw_rJXGxjQd";
  const { createClient } = supabase;
  const supabase = createClient(SUPABASE_URL, SUPABASE_KEY);

  let penggunaAktif = null;
  let kelasDipilih = null;
  let mapelDipilih = null;
  let statusKelas = { guruMasuk: false, jenisKegiatan: null, isiKegiatan: null };

  const DAFTAR_KELAS = ["6A", "6B", "6C", "5A", "5B", "4A", "4B"];
  const DAFTAR_MAPEL = ["Matematika", "Bahasa Indonesia", "IPA", "IPS", "Bahasa Inggris", "PKN"];
</script>


<!-- HALAMAN UTAMA / MASUK -->
<section id="halamanAwal" class="min‑h‑screen flex items‑center justify‑center p‑4">
  <div class="kaca‑mewah w‑full max‑w‑xl rounded‑3xl p‑8">
    <div class="text‑center mb‑8">
      <h1 class="text‑3xl font‑bold teks‑emas mb‑2">📘 Ruang Kelas Digital</h1>
      <p class="text‑lembut">Sistem pembelajaran terpadu & aman</p>
    </div>

    <div class="flex rounded‑xl overflow‑hidden mb‑8 border garis‑batas">
      <button id="tabGuru" class="flex‑1 py‑3 tombol‑biru">👩‍🏫 Guru</button>
      <button id="tabMurid" class="flex‑1 py‑3 bg‑kartu">🎓 Murid</button>
    </div>

    <!-- Form Guru -->
    <div id="bagianGuru">
      <div class="mb‑4">
        <label class="text‑sm text‑lembut mb‑1 block">Surel Guru</label>
        <input type="email" id="gEmail" class="w‑full rounded‑xl border p‑3" placeholder="guru@sekolah.sch.id">
      </div>
      <div class="mb‑5">
        <label class="text‑sm text‑lembut mb‑1 block">Kata Sandi</label>
        <input type="password" id="gSandi" class="w‑full rounded‑xl border p‑3" placeholder="••••••••••">
      </div>
      <button onclick="masukGuru()" class="w‑full py‑3 rounded‑xl tombol‑biru">Masuk ke Sistem</button>
      <p class="text‑center text‑sm mt‑4 text‑lembut">Belum terdaftar? <a href="#" onclick="tampilDaftar()" class="teks‑emas">Daftar akun baru</a></p>
    </div>

    <!-- Form Daftar Guru -->
    <div id="bagianDaftar" class="sembunyi">
      <div class="mb‑4">
        <label class="text‑sm text‑lembut mb‑1 block">Surel Kerja</label>
        <input type="email" id="dgEmail" class="w‑full rounded‑xl border p‑3">
      </div>
      <div class="mb‑5">
        <label class="text‑sm text‑lembut mb‑1 block">Buat Kata Sandi</label>
        <input type="password" id="dgSandi" class="w‑full rounded‑xl border p‑3">
      </div>
      <button onclick="daftarGuru()" class="w‑full py‑3 rounded‑xl tombol‑emas">Daftar & Kirim Verifikasi</button>
      <p class="text‑center text‑sm mt‑4 text‑lembut"><a href="#" onclick="kembaliMasuk()" class="teks‑emas">← Kembali</a></p>
    </div>

    <!-- Form Murid -->
    <div id="bagianMurid" class="sembunyi">
      <div class="mb‑4">
        <label class="text‑sm text‑lembut mb‑1 block">Nama Pengguna</label>
        <input type="text" id="mNama" class="w‑full rounded‑xl border p‑3" placeholder="murid.anda">
      </div>
      <div class="mb‑5">
        <label class="text‑sm text‑lembut mb‑1 block">Kata Sandi</label>
        <input type="password" id="mSandi" class="w‑full rounded‑xl border p‑3">
      </div>
      <button onclick="masukMurid()" class="w‑full py‑3 rounded‑xl tombol‑biru">Masuk Ruang Belajar</button>
    </div>

    <p id="kotakPesan" class="text‑center text‑sm mt‑5"></p>
  </div>
</section>


<!-- HALAMAN PILIHAN KELAS & MAPEL -->
<section id="halamanPilih" class="sembunyi min‑h‑screen flex items‑center justify‑center p‑4">
  <div class="kaca‑mewah w‑full max‑lg rounded‑3xl p‑8">
    <h2 class="text‑2xl font‑bold teks‑emas mb‑6 text‑center">Pilih Kelas & Mata Pelajaran</h2>

    <div class="grid md:grid‑cols‑2 gap‑5 mb‑7">
      <div>
        <label class="text‑sm text‑lembut mb‑2 block">Kelas</label>
        <select id="pilihKelas" class="w‑full rounded‑xl border p‑3">
          <option value="">— Pilih Kelas —</option>
          ${DAFTAR_KELAS.map(k=>`<option value="${k}">${k}</option>`).join('')}
        </select>
      </div>
      <div>
        <label class="text‑sm text‑lembut mb‑2 block">Mata Pelajaran</label>
        <select id="pilihMapel" class="w‑full rounded‑xl border p‑3">
          <option value="">— Pilih Mapel —</option>
          ${DAFTAR_MAPEL.map(m=>`<option value="${m}">${m}</option>`).join('')}
        </select>
      </div>
    </div>

    <button onclick="bukaRuangKelas()" class="w‑full py‑3 rounded‑xl tombol‑emas">Masuk ke Ruang Kelas</button>
    <div class="text‑right mt‑4"><button onclick="keluar()" class="text‑sm text‑lembut hover:teks‑emas">Keluar Sistem</button></div>
  </div>
</section>


<!-- HALAMAN UTAMA RUANG KELAS -->
<section id="halamanRuang" class="sembunyi min‑h‑screen p‑4 md:p‑8">
  <div class="max‑6xl mx‑auto">
    <div class="kaca‑mewah rounded‑2xl p‑5 mb‑6 flex flex‑wrap justify‑between items‑center">
      <div>
        <h2 class="text‑xl font‑semibold teks‑emas">Ruang Kelas: <span id="tampilKelas">‑</span> • <span id="tampilMapel">‑</span></h2>
        <p class="text‑sm text‑lembut">Status: <span id="statusKelasTeks" class="font‑medium">Menunggu Guru…</span></p>
      </div>
      <button onclick="keluar()" class="mt‑3 md:mt‑0 px‑4 py‑2 rounded‑xl bg‑red‑600/20 text‑red‑300 border border‑red‑400/20">Keluar</button>
    </div>

    <div class="grid md:grid‑cols‑3 gap‑6">
      <!-- AREA UTAMA -->
      <div class="md:col‑span‑2 kaca‑mewah rounded‑2xl p‑6 min‑h‑[400px]">
        <div id="areaTunggu" class="h‑full flex flex‑col items‑center justify‑center text‑lembut">
          <i class="fa fa‑clock‑o text‑6xl mb‑4 teks‑emas/60"></i>
          <p class="text‑lg">Silakan tunggu guru memulai kegiatan pembelajaran…</p>
        </div>

        <div id="areaKegiatan" class="sembunyi">
          <h3 class="text‑xl font‑semibold mb‑4 teks‑biru" id="judulKegiatan">Kegiatan Pembelajaran</h3>
          <div id="isiKegiatan" class="leading‑relaxed"></div>
        </div>
      </div>

      <!-- PANEL KENDALI KHUSUS GURU -->
      <div id="panelGuru" class="sembunyi kaca‑mewah rounded‑2xl p‑5">
        <h3 class="font‑bold teks‑emas mb‑4">⚙️ Kendali Guru</h3>

        <div class="space‑y‑4">
          <div>
            <label class="text‑sm text‑lembut mb‑1 block">Jenis Kegiatan</label>
            <select id="pilihJenis" class="w‑full rounded‑xl border p‑2.5">
              <option value="">— Pilih —</option>
              <option value="membaca">📖 Membaca & Rangkuman</option>
              <option value="kuis">❓ Kuis Singkat</option>
              <option value="ulangan">📝 Latihan / Ulangan</option>
            </select>
          </div>

          <!-- BAGIAN MEMBACA -->
          <div id="bagianMembaca" class="sembunyi">
            <textarea id="teksRangkuman" placeholder="Tulis materi/rangkuman untuk dibaca murid…" class="w‑full rounded‑xl border p‑3 min‑h‑24"></textarea>
            <button onclick="mulaiMembaca()" class="mt‑2 w‑full py‑2 rounded‑xl tombol‑biru">Tayangkan Materi</button>
          </div>

          <!-- BAGIAN KUIS / ULANGAN -->
          <div id="bagianSoal" class="sembunyi">
            <div class="mb‑3">
              <label class="text‑sm text‑lembut mb‑1 block">Cara Buat</label>
              <select id="caraBuatSoal" class="w‑full rounded‑xl border p‑2.5">
                <option value="manual">📝 Masukkan Satu‑satu</option>
                <option value="otomatis">🤖 Buat Otomatis dari Topik</option>
              </select>
            </div>

            <div id="inputManual" class="space‑y‑2">
              <textarea id="daftarSoalManual" placeholder="Contoh:&#10;1. Berapa hasil 2×6? | 12&#10;2. Apa ibu kota Indonesia? | Jakarta" class="w‑full rounded‑xl border p‑2.5 min‑h‑20"></textarea>
            </div>

            <div id="inputOtomatis" class="sembunyi">
              <input type="text" id="topikSoal" placeholder="Topik materi…" class="w‑full rounded‑xl border p‑2.5">
            </div>

            <button onclick="mulaiUjianAtauKuis()" class="mt‑3 w‑full py‑2 rounded‑xl tombol‑emas">Tayangkan & Mulai</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>


<script>
// --- ALIH TAMPILAN AWAL ---
document.getElementById('tabGuru').onclick = () => {
  document.getElementById('tabGuru').classList.replace('bg‑kartu','tombol‑biru');
  document.getElementById('tabMurid').classList.replace('tombol‑biru','bg‑kartu');
  document.getElementById('bagianGuru').classList.remove('sembunyi');
  document.getElementById('bagianMurid').classList.add('sembunyi');
  document.getElementById('bagianDaftar').classList.add('sembunyi');
};
document.getElementById('tabMurid').onclick = () => {
  document.getElementById('tabMurid').classList.replace('bg‑kartu','tombol‑biru');
  document.getElementById('tabGuru').classList.replace('tombol‑biru','bg‑kartu');
  document.getElementById('bagianMurid').classList.remove('sembunyi');
  document.getElementById('bagianGuru').classList.add('sembunyi');
  document.getElementById('bagianDaftar').classList.add('sembunyi');
};
function tampilDaftar() { document.getElementById('bagianGuru').classList.add('sembunyi'); document.getElementById('bagianDaftar').classList.remove('sembunyi'); }
function kembaliMasuk() { document.getElementById('bagianDaftar').classList.add('sembunyi'); document.getElementById('bagianGuru').classList.remove('sembunyi'); }
function pesan(el, teks, ok=false) { const x=document.getElementById(el); x.textContent=teks; x.className=`text‑center text‑sm mt‑5 ${ok?'text‑green‑300':'text‑red‑300'}`; }

// --- LOGIKA MASUK & DAFTAR ---
async function daftarGuru() {
  const e=document.getElementById('dgEmail').value.trim(), s=document.getElementById('dgSandi').value;
  if(!e||!s) return pesan('kotakPesan','⚠️ Isi semua kolom dulu');
  const {error}=await supabase.auth.signUp({email:e,password:s});
  if(error) return pesan('kotakPesan','❌ '+error.message);
  pesan('kotakPesan','✅ Berhasil! Cek surel untuk konfirmasi.',true);
}

async function masukGuru() {
  const e=document.getElementById('gEmail').value.trim(), s=document.getElementById('gSandi').value;
  const {data:{user},error}=await supabase.auth.signInWithPassword({email:e,password:s});
  if(error) return pesan('kotakPesan','❌ '+error.message);
  penggunaAktif={...user, peran:'guru'};
  await supabase.from('akun').upsert([{tipe:'guru',email:e,sandi:'‑',pengguna:null}],{onConflict:'email'});
  lanjutKePemilihan();
}

async function masukMurid() {
  const n=document.getElementById('mNama').value.trim(), s=document.getElementById('mSandi').value;
  const {data,error}=await supabase.from('akun').select('*').eq('tipe','murid').eq('pengguna',n).eq('sandi',s).maybeSingle();
  if(error||!data) return pesan('kotakPesan','❌ Akun tidak ada atau salah sandi');
  penggunaAktif={...data, peran:'murid'};
  await supabase.from('riwayat_masuk').insert([{nama_akun:n,keterangan:'Masuk sistem'}]);
  lanjutKePemilihan();
}

function lanjutKePemilihan() {
  document.getElementById('halamanAwal').classList.add('sembunyi');
  document.getElementById('halamanPilih').classList.remove('sembunyi');
}

function bukaRuangKelas() {
  kelasDipilih=document.getElementById('pilihKelas').value;
  mapelDipilih=document.getElementById('pilihMapel').value;
  if(!kelasDipilih||!mapelDipilih) return alert('Pilih kelas & mata pelajaran dulu!');
  
  document.getElementById('halamanPilih').classList.add('sembunyi');
  document.getElementById('halamanRuang').classList.remove('sembunyi');
  document.getElementById('tampilKelas').textContent=kelasDipilih;
  document.getElementById('tampilMapel').textContent=mapelDipilih;
  
  if(penggunaAktif.peran==='guru') {
    document.getElementById('panelGuru').classList.remove('sembunyi');
    statusKelas.guruMasuk=true;
    document.getElementById('statusKelasTeks').textContent='Guru sedang memimpin pelajaran';
  }

  // Tampilkan/sembunyikan input sesuai jenis
  document.getElementById('pilihJenis').onchange=e=>{
    document.getElementById('bagianMembaca').classList.toggle('sembunyi', e.target.value!=='membaca');
    document.getElementById('bagianSoal').classList.toggle('sembunyi', !['kuis','ulangan'].includes(e.target.value));
  };
  document.getElementById('caraBuatSoal').onchange=e=>{
    document.getElementById('inputManual').classList.toggle('sembunyi', e.target.value!=='manual');
    document.getElementById('inputOtomatis').classList.toggle('sembunyi', e.target.value!=='otomatis');
  };
}

// --- KENDALI KEGIATAN GURU ---
function mulaiMembaca() {
  const isi=document.getElementById('teksRangkuman').value.trim();
  if(!isi) return alert('Tulis materi/rangkuman dulu!');
  statusKelas={...statusKelas, jenisKegiatan:'Membaca & Rangkuman', isiKegiatan:isi};
  perbaruiTampilanMurid(`<div class="prose‑invert max‑none">${isi.replace(/\n/g,'<br>')}</div>`);
}

function mulaiUjianAtauKuis() {
  const jenis=document.getElementById('pilihJenis').value;
  const cara=document.getElementById('caraBuatSoal').value;
  let teksAkhir='';

  if(cara==='manual') teksAkhir=document.getElementById('daftarSoalManual').value;
  else teksAkhir=`[Dibuat otomatis] Topik: ${document.getElementById('topikSoal').value||'Umum'}`;

  statusKelas={...statusKelas, jenisKegiatan:jenis==='kuis'?'Kuis Singkat':'Latihan / Ulangan', isiKegiatan:teksAkhir};
  perbaruiTampilanMurid(`<pre class="whitespace‑pre‑wrap text‑lembut bg‑dasar/60 p‑4 rounded‑xl border garis‑batas">${teksAkhir}</pre>`);
}

function perbaruiTampilanMurid(isiHTML) {
  document.getElementById('areaTunggu').classList.add('sembunyi');
  document.getElementById('areaKegiatan').classList.remove('sembunyi');
  document.getElementById('judulKegiatan').textContent='📚 '+statusKelas.jenisKegiatan;
  document.getElementById('isiKegiatan').innerHTML=isiHTML;
}

function keluar() {
  supabase.auth?.signOut?.(); penggunaAktif=null;
  document.querySelectorAll('input,select,textarea').forEach(el=>el.value='');
  document.getElementById('halamanAwal').classList.remove('sembunyi');
  document.getElementById('halamanPilih').classList.add('sembunyi');
  document.getElementById('halamanRuang').classList.add('sembunyi');
  document.getElementById('areaTunggu').classList.remove('sembunyi');
  document.getElementById('areaKegiatan').classList.add('sembunyi');
  document.getElementById('panelGuru').classList.add('sembunyi');
}
</script>

</body>
</html>
