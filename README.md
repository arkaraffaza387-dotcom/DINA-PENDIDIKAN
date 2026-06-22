<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>GoldenClass • Sistem Pembelajaran</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css">
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/umd/supabase.min.js"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            gelap: '#0A0C14',
            kartu: '#12151F',
            utama: '#1E40AF',
            emas: '#EAB308',
            sekunder: '#3B82F6',
            lembut: '#94A3B8'
          }
        }
      }
    }
  </script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&amp;family=Playfair+Display:wght@700&display=swap');
    body { 
      font-family: 'Inter', sans-serif; 
      background: linear-gradient(135deg, #0A0C14 0%, #1A1F2E 100%);
    }
    .kaca { 
      background: rgba(18, 21, 31, 0.92); 
      border: 1px solid rgba(234, 179, 8, 0.15); 
      backdrop-filter: blur(12px); 
    }
    .gold-glow { box-shadow: 0 0 30px -5px rgb(234 179 8 / 0.4); }
    .header-gold {
      background: linear-gradient(90deg, #EAB308, #FACC15);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
    .sembunyi { display: none !important; }
    .card-hover:hover {
      transform: translateY(-6px);
      box-shadow: 0 25px 30px -5px rgb(0 0 0 / 0.2);
    }
  </style>
</head>
<body class="min-h-screen text-white">

<script>
  const SUPABASE_URL = "https://hzpvbigkwustmwzttkvv.supabase.co";
  const SUPABASE_KEY = "sb_publishable_7Bv46KYh1TDiydTqpw_iBw_rJXGxjQd";
  const { createClient } = supabase;
  const supabaseClient = createClient(SUPABASE_URL, SUPABASE_KEY);

  let penggunaAktif = null;
  let tipePengguna = null;
  let kelasAktif = null;
  let soalList = [];
</script>

<!-- HALAMAN LOGIN -->
<div id="halamanMasuk" class="min-h-screen flex items-center justify-center p-6">
  <div class="kaca rounded-3xl p-10 w-full max-w-md gold-glow">
    <div class="text-center mb-8">
      <div class="inline-flex items-center gap-3 mb-4">
        <i class="fa fa-graduation-cap text-6xl text-emas"></i>
        <h1 class="text-4xl font-bold header-gold">GoldenClass</h1>
      </div>
      <p class="text-lembut">Platform Pembelajaran Elegan</p>
    </div>

    <div class="flex gap-2 mb-8 bg-kartu p-1 rounded-2xl">
      <button id="tabGuru" class="flex-1 py-3 rounded-xl font-medium bg-utama text-white">🧑‍🏫 Guru</button>
      <button id="tabMurid" class="flex-1 py-3 rounded-xl font-medium">🎓 Murid</button>
    </div>

    <!-- FORM GURU -->
    <div id="formGuru">
      <div class="space-y-5">
        <div>
          <label class="block text-sm text-lembut mb-1.5">Email Guru</label>
          <input type="email" id="gEmail" class="w-full bg-gelap border border-white/10 focus:border-emas rounded-2xl px-5 py-3 outline-none" placeholder="guru@sekolah.sch.id">
        </div>
        <div>
          <label class="block text-sm text-lembut mb-1.5">Kata Sandi</label>
          <input type="password" id="gSandi" class="w-full bg-gelap border border-white/10 focus:border-emas rounded-2xl px-5 py-3 outline-none">
        </div>
        <button onclick="masukGuru()" class="w-full bg-gradient-to-r from-emas to-yellow-400 text-gelap font-bold py-4 rounded-2xl text-lg">MASUK SEBAGAI GURU</button>
      </div>
      <p class="text-center text-sm mt-6 text-lembut">Belum punya akun? <a href="#" onclick="tampilDaftarGuru()" class="text-emas hover:underline">Daftar</a></p>
    </div>

    <!-- FORM DAFTAR GURU -->
    <div id="formDaftarGuru" class="sembunyi">
      <div class="space-y-5">
        <div>
          <label class="block text-sm text-lembut mb-1.5">Email Guru</label>
          <input type="email" id="dgEmail" class="w-full bg-gelap border border-white/10 focus:border-emas rounded-2xl px-5 py-3 outline-none">
        </div>
        <div>
          <label class="block text-sm text-lembut mb-1.5">Kata Sandi (min. 6 karakter)</label>
          <input type="password" id="dgSandi" class="w-full bg-gelap border border-white/10 focus:border-emas rounded-2xl px-5 py-3 outline-none">
        </div>
        <button onclick="daftarGuru()" class="w-full bg-emas text-gelap font-bold py-4 rounded-2xl text-lg">DAFTAR SEKARANG</button>
        <p class="text-xs text-lembut text-center">Setelah daftar, cek email untuk konfirmasi.</p>
      </div>
      <p class="text-center text-sm mt-6"><a href="#" onclick="kembaliKeMasuk()" class="text-emas hover:underline">← Kembali</a></p>
    </div>

    <!-- FORM MURID -->
    <div id="formMurid" class="sembunyi">
      <div class="space-y-5">
        <div>
          <label class="block text-sm text-lembut mb-1.5">Nama Pengguna</label>
          <input type="text" id="mNama" class="w-full bg-gelap border border-white/10 focus:border-emas rounded-2xl px-5 py-3 outline-none" placeholder="murid01">
        </div>
        <div>
          <label class="block text-sm text-lembut mb-1.5">Kata Sandi</label>
          <input type="password" id="mSandi" class="w-full bg-gelap border border-white/10 focus:border-emas rounded-2xl px-5 py-3 outline-none">
        </div>
        <button onclick="masukMurid()" class="w-full bg-gradient-to-r from-sekunder to-blue-500 font-bold py-4 rounded-2xl text-lg">MASUK SEBAGAI MURID</button>
      </div>
    </div>

    <p id="pesanUtama" class="text-center mt-6 text-sm min-h-[1.5rem]"></p>
  </div>
</div>

<!-- PANEL GURU -->
<div id="halamanGuru" class="sembunyi min-h-screen p-6">
  <div class="max-w-6xl mx-auto">
    <div class="flex justify-between items-center mb-8">
      <div>
        <h2 class="text-3xl font-bold header-gold">Panel Guru</h2>
        <p class="text-lembut">Selamat datang, <span id="namaGuru" class="text-emas"></span></p>
      </div>
      <button onclick="keluarSistem()" class="px-6 py-3 bg-red-500/10 hover:bg-red-500/20 text-red-300 rounded-2xl flex items-center gap-2">
        <i class="fa fa-sign-out"></i> Keluar
      </button>
    </div>

    <!-- Pilih Kelas -->
    <div class="kaca rounded-3xl p-8 mb-8">
      <h3 class="text-xl font-semibold mb-4 flex items-center gap-2"><i class="fa fa-chalkboard text-emas"></i> Pilih Kelas</h3>
      <div class="grid grid-cols-2 md:grid-cols-5 gap-4" id="daftarKelasGuru"></div>
      <button onclick="mulaiSesiKelas()" id="btnMulaiKelas" class="mt-8 w-full bg-emas text-gelap font-bold py-4 rounded-2xl text-lg disabled:opacity-50">MULAI SESI KELAS</button>
    </div>

    <!-- Aktivitas -->
    <div class="grid md:grid-cols-3 gap-6">
      <div onclick="buatAktivitas('membaca')" class="kaca rounded-3xl p-8 card-hover cursor-pointer">
        <div class="text-5xl mb-4">📖</div>
        <h4 class="font-semibold text-xl">Membaca & Rangkuman</h4>
        <p class="text-lembut text-sm mt-1">Berikan materi atau minta rangkuman</p>
      </div>
      <div onclick="buatAktivitas('quiz')" class="kaca rounded-3xl p-8 card-hover cursor-pointer">
        <div class="text-5xl mb-4">❓</div>
        <h4 class="font-semibold text-xl">Quiz Interaktif</h4>
        <p class="text-lembut text-sm mt-1">Buat manual atau pakai AI</p>
      </div>
      <div onclick="buatAktivitas('ulangan')" class="kaca rounded-3xl p-8 card-hover cursor-pointer">
        <div class="text-5xl mb-4">📝</div>
        <h4 class="font-semibold text-xl">Latihan Ulangan</h4>
        <p class="text-lembut text-sm mt-1">Tes pemahaman siswa</p>
      </div>
    </div>

    <div id="aktivitasContainer" class="mt-10"></div>
  </div>
</div>

<!-- HALAMAN MURID -->
<div id="halamanMurid" class="sembunyi min-h-screen p-6">
  <div class="max-w-2xl mx-auto">
    <div class="kaca rounded-3xl p-10">
      <div class="text-center mb-10">
        <div class="text-6xl mb-4">🎓</div>
        <h2 class="text-3xl font-bold">Selamat datang, <span id="namaMurid" class="text-emas"></span></h2>
      </div>

      <div class="space-y-6">
        <div>
          <label class="block text-sm text-lembut mb-2">Pilih Kelas</label>
          <select id="selectKelasMurid" class="w-full bg-gelap border border-white/10 rounded-2xl px-5 py-4 text-lg">
            <option value="">Pilih Kelas...</option>
            <option value="6A">6A</option><option value="6B">6B</option>
            <option value="7A">7A</option><option value="7B">7B</option>
            <option value="8A">8A</option>
          </select>
        </div>

        <div>
          <label class="block text-sm text-lembut mb-2">Mata Pelajaran</label>
          <select id="selectMapelMurid" class="w-full bg-gelap border border-white/10 rounded-2xl px-5 py-4 text-lg">
            <option value="">Pilih Mata Pelajaran...</option>
            <option value="Matematika">Matematika</option>
            <option value="Bahasa Indonesia">Bahasa Indonesia</option>
            <option value="IPA">IPA</option>
            <option value="IPS">IPS</option>
            <option value="Bahasa Inggris">Bahasa Inggris</option>
          </select>
        </div>

        <button onclick="gabungKelasMurid()" class="w-full bg-gradient-to-r from-emas to-yellow-400 text-gelap font-bold py-5 rounded-3xl text-lg">GABUNG KELAS</button>
      </div>

      <div id="waitingRoom" class="sembunyi mt-10 text-center">
        <div class="animate-pulse text-7xl mb-6">⏳</div>
        <h3 class="text-2xl font-medium">Menunggu Guru Memulai Sesi...</h3>
        <p class="text-lembut mt-2">Guru sedang mempersiapkan kelas</p>
      </div>

      <div id="aktivitasMurid" class="sembunyi mt-10"></div>
    </div>
  </div>
</div>

<script>
// ================== HELPER ==================
function tampilPesan(id, teks, sukses = false) {
  const el = document.getElementById(id);
  el.innerHTML = teks;
  el.className = `text-center text-sm mt-6 ${sukses ? 'text-emas' : 'text-red-400'}`;
}

// ================== TAB ==================
document.getElementById('tabGuru').onclick = () => {
  document.getElementById('tabGuru').classList.add('bg-utama', 'text-white');
  document.getElementById('tabMurid').classList.remove('bg-utama', 'text-white');
  document.getElementById('formGuru').classList.remove('sembunyi');
  document.getElementById('formMurid').classList.add('sembunyi');
  document.getElementById('formDaftarGuru').classList.add('sembunyi');
};

document.getElementById('tabMurid').onclick = () => {
  document.getElementById('tabMurid').classList.add('bg-utama', 'text-white');
  document.getElementById('tabGuru').classList.remove('bg-utama', 'text-white');
  document.getElementById('formMurid').classList.remove('sembunyi');
  document.getElementById('formGuru').classList.add('sembunyi');
  document.getElementById('formDaftarGuru').classList.add('sembunyi');
};

// ================== AUTH ==================
async function daftarGuru() {
  const email = document.getElementById('dgEmail').value.trim();
  const sandi = document.getElementById('dgSandi').value;

  if (!email || !sandi) return tampilPesan('pesanUtama', '⚠️ Email dan kata sandi harus diisi');
  if (sandi.length < 6) return tampilPesan('pesanUtama', '⚠️ Kata sandi minimal 6 karakter');

  tampilPesan('pesanUtama', '⏳ Sedang mendaftarkan...');

  const { data, error } = await supabaseClient.auth.signUp({
    email,
    password: sandi,
    options: { emailRedirectTo: window.location.href }
  });

  if (error) return tampilPesan('pesanUtama', '❌ ' + error.message);

  tampilPesan('pesanUtama', `✅ Pendaftaran berhasil!<br>Cek email <b>${email}</b> untuk konfirmasi.`, true);
}

async function masukGuru() {
  const email = document.getElementById('gEmail').value.trim();
  const sandi = document.getElementById('gSandi').value;

  if (!email || !sandi) return tampilPesan('pesanUtama', '⚠️ Isi email dan sandi');

  const { data: { user }, error } = await supabaseClient.auth.signInWithPassword({ email, password: sandi });
  if (error) return tampilPesan('pesanUtama', '❌ ' + error.message);

  penggunaAktif = user;
  tipePengguna = 'guru';
  document.getElementById('namaGuru').textContent = email.split('@')[0];

  document.getElementById('halamanMasuk').classList.add('sembunyi');
  document.getElementById('halamanGuru').classList.remove('sembunyi');
  renderKelasGuru();
}

async function masukMurid() {
  const nama = document.getElementById('mNama').value.trim();
  const sandi = document.getElementById('mSandi').value;

  if (!nama || !sandi) return tampilPesan('pesanUtama', '⚠️ Isi nama pengguna dan sandi');

  const { data, error } = await supabaseClient.from('akun').select('*').eq('tipe','murid').eq('pengguna',nama).eq('sandi',sandi).maybeSingle();
  if (error || !data) return tampilPesan('pesanUtama', '❌ Akun tidak ditemukan atau sandi salah');

  penggunaAktif = { nama };
  tipePengguna = 'murid';
  document.getElementById('namaMurid').textContent = nama;

  document.getElementById('halamanMasuk').classList.add('sembunyi');
  document.getElementById('halamanMurid').classList.remove('sembunyi');
}

// ================== KELAS & SESI ==================
function renderKelasGuru() {
  const kelasList = ['6A','6B','7A','7B','8A','8B'];
  const container = document.getElementById('daftarKelasGuru');
  container.innerHTML = kelasList.map(k => `
    <div onclick="pilihKelasGuru('\( {k}')" id="kelas- \){k}" class="kaca p-6 rounded-2xl text-center cursor-pointer card-hover border-2 border-transparent hover:border-emas transition-all">
      <div class="text-4xl mb-3">🏫</div>
      <div class="font-bold text-3xl text-emas">${k}</div>
    </div>
  `).join('');
}

let kelasTerpilihGuru = null;
function pilihKelasGuru(kelas) {
  kelasTerpilihGuru = kelas;
  document.querySelectorAll('#daftarKelasGuru > div').forEach(el => {
    el.classList.toggle('border-emas', el.id === `kelas-${kelas}`);
  });
}

function mulaiSesiKelas() {
  if (!kelasTerpilihGuru) return alert("Pilih kelas terlebih dahulu!");
  kelasAktif = kelasTerpilihGuru;
  alert(`✅ Sesi kelas ${kelasAktif} telah dimulai!\nMurid sekarang dapat bergabung.`);
}

// ================== AKTIVITAS GURU ==================
function buatAktivitas(tipe) {
  if (!kelasAktif) return alert("Harap mulai sesi kelas terlebih dahulu!");

  const container = document.getElementById('aktivitasContainer');
  container.innerHTML = '';

  if (tipe === 'membaca') {
    container.innerHTML = `
      <div class="kaca rounded-3xl p-8">
        <h3 class="text-2xl font-bold mb-6">📖 Materi Membaca - ${kelasAktif}</h3>
        <textarea id="inputRingkasan" rows="8" class="w-full bg-gelap border border-white/10 rounded-2xl p-6 text-lg" placeholder="Tulis ringkasan materi di sini..."></textarea>
        <div class="flex gap-4 mt-6">
          <button onclick="kirimMateriMembaca()" class="flex-1 bg-emas text-gelap font-bold py-4 rounded-2xl">KIRIM KE MURID</button>
          <button onclick="generateRingkasanAI()" class="flex-1 bg-blue-600 font-bold py-4 rounded-2xl">GENERATE AI</button>
        </div>
      </div>`;
  } else if (tipe === 'quiz') {
    container.innerHTML = `
      <div class="kaca rounded-3xl p-8">
        <h3 class="text-2xl font-bold mb-6">❓ Buat Quiz - ${kelasAktif}</h3>
        <div class="flex gap-3 mb-6">
          <button onclick="modeManualQuiz()" class="flex-1 py-4 bg-kartu rounded-2xl">Input Manual</button>
          <button onclick="modeAIPrompt()" class="flex-1 py-4 bg-emas text-gelap rounded-2xl font-bold">Generate dengan AI</button>
        </div>
        <div id="quizBuilder"></div>
      </div>`;
  }
}

function generateRingkasanAI() {
  const topik = prompt("Masukkan topik mata pelajaran (contoh: Fotosintesis):");
  if (topik) {
    document.getElementById('inputRingkasan').value = `Ringkasan Materi: ${topik}\n\n1. Pengertian...\n2. Proses...\n3. Contoh...\n\nTugas: Rangkum dalam 150-200 kata.`;
  }
}

function kirimMateriMembaca() {
  const teks = document.getElementById('inputRingkasan').value.trim();
  if (!teks) return alert("Tulis materi terlebih dahulu!");
  alert(`✅ Materi telah dikirim ke murid kelas ${kelasAktif}`);
}

// Quiz Manual
function modeManualQuiz() {
  document.getElementById('quizBuilder').innerHTML = `
    <input type="text" id="soalBaru" class="w-full bg-gelap border border-white/10 rounded-2xl px-5 py-4 mb-4" placeholder="Tulis soal di sini...">
    <button onclick="tambahSoalManual()" class="bg-emas text-gelap px-8 py-3 rounded-2xl mb-6">+ Tambah Soal</button>
    <div id="listSoal" class="space-y-3"></div>
    <button onclick="kirimQuiz()" class="mt-8 w-full py-4 bg-gradient-to-r from-emas to-yellow-400 text-gelap font-bold rounded-2xl">KIRIM QUIZ KE MURID</button>
  `;
  soalList = [];
}

function tambahSoalManual() {
  const input = document.getElementById('soalBaru');
  if (input.value.trim()) {
    soalList.push(input.value.trim());
    renderSoalList();
    input.value = '';
  }
}

function renderSoalList() {
  document.getElementById('listSoal').innerHTML = soalList.map((s,i) => `
    <div class="bg-kartu p-4 rounded-2xl">${i+1}. ${s}</div>
  `).join('');
}

function kirimQuiz() {
  if (soalList.length === 0) return alert("Tambahkan minimal 1 soal!");
  alert(`✅ Quiz dengan ${soalList.length} soal telah dikirim ke kelas ${kelasAktif}`);
}

function modeAIPrompt() {
  const prompt = prompt("Masukkan topik quiz (contoh: Sistem Tata Surya):");
  if (prompt) alert(`✅ Quiz tentang "${prompt}" telah digenerate dan dikirim ke murid!`);
}

// ================== MURID SIDE ==================
function gabungKelasMurid() {
  const kelas = document.getElementById('selectKelasMurid').value;
  const mapel = document.getElementById('selectMapelMurid').value;

  if (!kelas || !mapel) return alert("Pilih kelas dan mata pelajaran!");

  kelasAktif = kelas;
  document.getElementById('waitingRoom').classList.remove('sembunyi');

  setTimeout(() => {
    document.getElementById('waitingRoom').classList.add('sembunyi');
    tampilAktivitasMurid();
  }, 1800);
}

function tampilAktivitasMurid() {
  const container = document.getElementById('aktivitasMurid');
  container.classList.remove('sembunyi');
  container.innerHTML = `
    <div class="text-center mb-6">
      <h3 class="text-2xl font-bold text-emas">Kelas ${kelasAktif} - Aktivitas Guru</h3>
    </div>
    <div class="bg-gelap p-8 rounded-3xl leading-relaxed">
      <p class="text-lembut">Materi / Quiz dari guru akan muncul di sini secara real-time.</p>
    </div>
    <textarea class="w-full mt-6 bg-gelap border border-white/10 rounded-3xl p-6 h-48" placeholder="Tulis jawaban / rangkuman Anda di sini..."></textarea>
    <button onclick="kirimJawabanMurid()" class="mt-6 w-full bg-emas text-gelap py-5 rounded-3xl font-bold">KIRIM JAWABAN</button>
  `;
}

function kirimJawabanMurid() {
  alert("✅ Jawaban Anda berhasil dikirim ke guru!");
}

// ================== GLOBAL ==================
function kembaliKeMasuk() {
  document.getElementById('formDaftarGuru').classList.add('sembunyi');
  document.getElementById('formGuru').classList.remove('sembunyi');
}

function keluarSistem() {
  if (confirm("Keluar dari sistem?")) location.reload();
}

// Keyboard support
document.addEventListener('keydown', e => { if (e.key === "Escape") keluarSistem(); });
</script>
</body>
</html>
