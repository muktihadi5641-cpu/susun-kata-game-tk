# 🎮 AI PROJECT CONTEXT — SUSUN KATA GAME TK/PAUD
> **Bawa file ini ke device manapun dan kasih ke AI assistant.**
> AI akan langsung paham semua context, clone repo, dan bisa push ke GitHub yang sama.

---

## ⚡ INSTRUKSI UNTUK AI ASSISTANT (BACA INI DULU)

Halo AI! File ini adalah **panduan lengkap** untuk melanjutkan pengembangan project **Susun Kata Game TK/PAUD**. Ikuti langkah-langkah di bawah ini secara berurutan:

### LANGKAH 1 — Clone Repo GitHub
```bash
# Buat folder project
mkdir -p C:\Claude\Project\SusunKata
cd C:\Claude\Project\SusunKata

# Clone repo
git clone https://github.com/muktihadi5641-cpu/susun-kata-game-tk.git .
```

### LANGKAH 2 — Setup Git Identity
```bash
git config user.name "muktihadi5641-cpu"
git config user.email "muktihadi5641@gmail.com"
```

### LANGKAH 3 — Setup GitHub Authentication (PAT)
User perlu **Personal Access Token (PAT)** agar bisa push dari device baru:
1. Buka https://github.com/settings/tokens
2. Klik **"Generate new token (classic)"**
3. Pilih scope: `repo` (full control)
4. Copy tokennya
5. Jalankan command berikut (ganti TOKEN_DISINI dengan token asli):

```bash
git remote set-url origin https://TOKEN_DISINI@github.com/muktihadi5641-cpu/susun-kata-game-tk.git
```

Atau simpan credential ke Windows Credential Manager:
```bash
git config credential.helper manager-core
# Saat pertama push, Windows akan minta login GitHub
```

### LANGKAH 4 — Verifikasi Setup
```bash
git log --oneline -5   # Lihat 5 commit terakhir
git remote -v          # Pastikan remote benar
```

### LANGKAH 5 — Langsung Bisa Push
```bash
git add -A
git commit -m "pesan commit"
git push origin master
```

---

## 📦 INFORMASI PROJECT

| Field | Value |
|-------|-------|
| **Nama Project** | Susun Kata — Game Edukasi TK/PAUD |
| **GitHub Repo** | https://github.com/muktihadi5641-cpu/susun-kata-game-tk |
| **GitHub Pages (Live)** | https://muktihadi5641-cpu.github.io/susun-kata-game-tk/ |
| **Branch** | `master` |
| **Lokasi File Utama** | `index.html` (single-file app) |
| **GitHub Username** | `muktihadi5641-cpu` |
| **Target Platform** | Smart TV 16:9 (1920x1080), anak TK/PAUD usia 4-6 tahun |
| **Bahasa** | Indonesia + English Mode |

---

## 🏗️ ARSITEKTUR PROJECT

### Single-File App
Seluruh aplikasi ada dalam **1 file**: `index.html`
- Tidak ada bundler, framework, atau dependencies eksternal
- CSS inline dalam `<style>` tag
- JavaScript inline dalam `<script>` tag
- Font dari Google Fonts (online): Fredoka One + Nunito
- Audio dihasilkan oleh **Web Audio API** (native browser, tidak perlu file audio)
- Speech oleh **Web Speech Synthesis API** (native browser)

### Canvas Virtual 1920x1080px
```
<body>
  |-- #gbg          --> Background gradient (position:fixed, cover full viewport)
  |-- #drag-ghost   --> Drag helper element
  `-- #gw           --> Game Wrapper (1920x1080px, scaled via CSS transform)
       |-- #tv-top-bar    --> Persistent controls (Menu btn + Fullscreen btn)
       |-- #menu-confirm  --> Popup konfirmasi kembali ke menu
       |-- #s-title       --> Halaman judul
       |-- #s-chapters    --> Pilih chapter
       |-- #s-game        --> Game susun kata Indonesia
       |-- #s-success     --> Popup jawaban benar
       |-- #s-chend       --> Chapter selesai
       |-- #s-finish      --> Semua chapter selesai
       |-- #s-eng-submode --> Pilih sub-mode English (Find Picture / Letter Bubbles)
       |-- #s-eng-topic   --> Pilih topik English
       |-- #s-eng-game    --> Game English aktif
       `-- #s-eng-cel     --> Celebration English
```

### Scaling Engine (16:9 Full-Bleed)
```javascript
function scl(){
  const W = document.getElementById('gw');
  const vw = window.innerWidth, vh = window.innerHeight;
  // SELALU scale penuh, tanpa black bar
  W.style.transform = `scale(${vw/1920}, ${vh/1080})`;
  W.style.transformOrigin = 'top left';
  W.style.left = '0px';
  W.style.top = '0px';
}
window.addEventListener('resize', scl);
scl();
```

---

## 🎮 FITUR YANG SUDAH DIIMPLEMENTASI

### Mode 1: Susun Kata Bahasa Indonesia
- **5 Chapter**: Polisi, Hewan, Buah, Rumah, Laut
- Setiap chapter: 5 kata bergambar
- Mechanic: drag & drop huruf ke slot kosong
- Gambar: SVG inline buatan custom (chibi/cute style)
- Audio: Web Audio API + Speech Synthesis
- Progress disimpan ke `localStorage` (bintang per chapter)

### Mode 2: Belajar Bahasa Inggris
- **Sub-mode A**: Find the Picture — dengar kata Inggris, pilih gambar yang benar dari 4 pilihan
- **Sub-mode B**: Letter Bubbles — tap huruf dalam urutan benar untuk menyusun kata
- **3 Topik**: Animals (7 kata), Colors (7 kata), Body Parts (7 kata)
- Text-to-speech suara guru ramah anak (pitch tinggi, rate lambat)
- Audio: benar → fanfare, salah → try again sound, celebration → victory

### UI/UX
- Grid 2x3 di chapter select
- Tombol Menu persisten (muncul saat dalam game)
- Dialog konfirmasi saat kembali ke menu
- Tombol Layar Penuh persisten
- Remote TV D-Pad navigation (Arrow keys)
- Escape/Backspace = Back/Return
- Tombol F = toggle fullscreen

---

## 📝 STATE MANAGEMENT

```javascript
// Game State — Susun Kata Indonesia
const gs = {
  ch: null,      // chapter aktif (object dari CHAPTERS array)
  idx: 0,        // index kata saat ini dalam chapter
  slots: [],     // array slot elements
  bubbles: [],   // array bubble (huruf acak) elements
  progress: JSON.parse(localStorage.getItem('sktk_prog') || '{}')
};

// English Game State
const egs = {
  mode: null,    // 'find-pic' atau 'bubbles'
  topic: null,   // topic object { name, words:[...] }
  words: [],     // kata-kata untuk sesi ini (shuffled)
  idx: 0,        // index kata saat ini
  progress: JSON.parse(localStorage.getItem('egtk_prog') || '{}')
};
```

---

## 🔑 KEY FUNCTIONS PENTING

| Fungsi | Keterangan |
|--------|------------|
| `scl()` | Scale game ke full viewport 16:9 |
| `showScreen(id)` | Ganti halaman + toggle menu button visibility |
| `showChapters()` | Kembali ke halaman pilih chapter |
| `resetAll()` | Kembali ke title screen |
| `startGame(ch)` | Mulai chapter susun kata |
| `renderWord()` | Render soal kata saat ini |
| `checkAnswer()` | Cek jawaban, trigger celebrasi |
| `backToChapters()` | Dari game kembali ke chapter select |
| `showMenuConfirm()` | Tampilkan dialog konfirmasi menu |
| `confirmGoMenu()` | Konfirmasi kembali ke menu (stop audio, go chapters) |
| `engOpenMode()` | Buka English mode pilih sub-mode |
| `engStartGame()` | Mulai English game session |
| `engCheckAnswer()` | Cek jawaban English mode |
| `toggleFS()` | Toggle fullscreen browser |
| `renderChapterScreen()` | Render grid 2x3 chapter cards |

---

## 🎨 CSS DESIGN SYSTEM

```css
:root {
  --fg: 'Fredoka One', cursive;   /* font game (judul, tombol) */
  --fu: 'Nunito', sans-serif;     /* font UI (deskripsi, counter) */
}

/* Warna utama */
#FFD600  = gold (primary accent)
#FF6D00  = orange (CTA buttons)
#1565C0  = blue (backgrounds)
#09143c  = dark navy (body background)

/* Canvas */
#gw { width: 1920px; height: 1080px; }

/* Tombol utama */
.btn-main { font-size:68px; padding:28px 88px; border-radius:100px; }

/* Chapter cards */
.chapter-card     { width:300px; height:270px; border-radius:28px; }
.eng-chapter-card { width:300px; height:270px; border-radius:28px; }
```

---

## 🐛 KNOWN ISSUES & SOLUSI

| Issue | Solusi yang Sudah Diterapkan |
|-------|------------------------------|
| Black bar di kiri/kanan | `scale(vw/1920, vh/1080)` tanpa ratio check |
| Flag emoji → "GB" teks | Ganti dengan SVG Union Jack via `createElementNS` |
| Konten chapter terpotong | Kurangi ukuran card 320→300px, padding lebih kecil |
| Remote TV tidak bisa navigasi | D-Pad keydown handler di semua screen |
| Audio robot/datar | Web Audio API custom + Speech Synthesis pitch=1.4 rate=0.8 |

---

## 🚀 WORKFLOW PENGEMBANGAN

```bash
# 1. Edit index.html
# 2. Test lokal: buka index.html di browser
# 3. Commit & push
git add -A
git commit -m "deskripsi perubahan"
git push origin master

# GitHub Pages update dalam 1-2 menit
# Live: https://muktihadi5641-cpu.github.io/susun-kata-game-tk/
```

---

## 📋 COMMIT HISTORY TERAKHIR

```
3feb211  feat: add persistent Menu button with confirm dialog
b61fb4d  fix: build English card via DOM API (SVG Union Jack flag)
7aa9135  fix: full-bleed edge-to-edge 16:9 scale, compact chapter grid
3b57b4d  fix(16:9): full-screen Smart TV layout
7e0fc2f  fix(english-mode): ilustrasi SVG ramah anak, Smart TV 16:9, audio ceria
48d2345  feat: tambah mode Belajar Bahasa Inggris
fa49a98  Initial commit for Susun Kata v2.0
```

---

## 💡 CARA TAMBAH FITUR BARU

### Tambah chapter Indonesia baru:
```javascript
// Di array CHAPTERS, tambah object:
{
  id: 'nama_chapter',
  name: 'Nama Chapter',
  sub: 'Deskripsi Singkat',
  icon: 'EMOJI',
  color1: '#warna1',
  color2: '#warna2',
  bgGrad: 'linear-gradient(...)',
  groundColor: '#warna',
  words: [
    { w: 'KATA', img: '<svg viewBox="0 0 200 200">...</svg>', hint: 'Deskripsi' },
    // ... 5 kata
  ]
}
```

### Tambah topik English baru:
```javascript
// Di array ENG_TOPICS, tambah object:
{
  id: 'topic_id',
  name: 'Topic Name',
  emoji: 'EMOJI',
  words: [
    { en: 'WORD', id: 'Terjemahan', img: '<svg>...</svg>' },
    // ... 7 kata
  ]
}
```

---

## 🔗 LINK PENTING

- **Repo GitHub**: https://github.com/muktihadi5641-cpu/susun-kata-game-tk
- **Live Game**: https://muktihadi5641-cpu.github.io/susun-kata-game-tk/
- **GitHub Token Settings**: https://github.com/settings/tokens

---

*Last updated: 2026-09-18 | Project: Susun Kata Game TK/PAUD v2.0*
