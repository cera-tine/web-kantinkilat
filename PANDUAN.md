# Panduan Web — LombaLink & KantinKilat

Panduan singkat mengubah, mengedit manual, men-deploy ke Vercel, dan memasang GA4.

---

## 1. Isi folder

```
web-lombalink/
├── index.html          ← seluruh web (HTML + CSS + JS jadi satu)
├── vercel.json         ← konfigurasi Vercel
└── PANDUAN.md          ← dokumen ini

web-kantinkilat/
├── index.html
├── vercel.json
└── PANDUAN.md
```

Semua ada di **satu file `index.html`** — sengaja, supaya gampang diedit manual
dan tidak perlu install apa pun.

---

## 2. Yang WAJIB diganti sebelum dipakai (3 tempat)

### 2.1 GA4 — ganti `G-XXXXXXXXXX`

Buka `index.html`, cari `G-XXXXXXXXXX` (Ctrl+F). Ada di **2 baris**:

```
baris 13:  <script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
baris 18:  gtag('config', 'G-XXXXXXXXXX');
```

Ganti **keduanya** dengan Measurement ID dari Rizki.
Contoh kalau ID-nya `G-AB12CD34EF`:

```
baris 13:  <script async src="https://www.googletagmanager.com/gtag/js?id=G-AB12CD34EF"></script>
baris 18:  gtag('config', 'G-AB12CD34EF');
```

> ⚠️ **Penting:** kalau kamu diminta bikin 2 property GA4 (satu per web),
> pakai ID yang **berbeda** di tiap file. Kalau ID-nya sama, data
> LombaLink dan KantinKilat akan bercampur di satu laporan.

### 2.2 Form — ganti endpoint Formspree

Cari `GANTI-DENGAN-ENDPOINT-KAMU`. Ada di **1 baris**:

```
baris 532 (LombaLink) / 557 (KantinKilat):
action="https://formspree.io/f/GANTI-DENGAN-ENDPOINT-KAMU"
```

Cara mendapat endpoint:
1. Daftar gratis di https://formspree.io
2. **New Form** → copy URL endpoint (contoh: `https://formspree.io/f/abcdwxyz`)
3. Ganti baris di atas (biarkan teks `/f/` dan tanda kutipnya)

> Selama endpoint belum diganti, form akan menolak kirim dan menampilkan pesan
> "Form belum tersambung" — ini **disengaja**, supaya tidak ada data yang
> hilang diam-diam saat demo.

### 2.3 (Opsional) Nomor WhatsApp

Belum ada tombol WA di versi ini. Kalau mau ditambah, taruh di bagian `#cta`:

```html
<a href="https://wa.me/62812xxxxxxx" class="btn btn-white btn-lg" data-ga="cta_whatsapp">
  Chat via WhatsApp
</a>
```

---

## 3. Men-deploy ke Vercel (gampang, tanpa CLI)

### Cara A — Lewat website Vercel (paling gampang, disarankan)

1. Pastikan folder web sudah di-push ke GitHub dulu (lihat bagian 5).
2. Buka https://vercel.com → login pakai akun GitHub.
3. **Add New** → **Project**.
4. Pilih repo `Kelompok-2-AI`.
5. Di **Root Directory**, klik **Edit** dan pilih:
   - `TUGAS BERSAMA/web-lombalink` → untuk LombaLink
6. **Framework Preset**: pilih **Other** (karena ini HTML murni, bukan framework).
7. **Build Command** & **Output Directory**: kosongkan.
8. Klik **Deploy**. Selesai dalam ±20 detik.
9. Ulangi langkah 3–8 untuk `web-kantinkilat`.

Hasilnya kamu dapat 2 URL:
```
https://lombalink-xxxx.vercel.app
https://kantinkilat-xxxx.vercel.app
```

### Cara B — Lewat terminal (kalau sudah install Node.js)

```bash
cd "TUGAS BERSAMA/web-lombalink"
npx vercel
```

Ikuti pertanyaannya (login, lalu enter-enter saja). Untuk produksi:
```bash
npx vercel --prod
```

---

## 4. Akses edit manual

### Kalau kamu pemilik repo
Kamu **selalu** bisa edit manual — file HTML-nya ada di repo kalian sendiri,
tidak terkunci di Vercel. Edit langsung:

- **Di GitHub:** buka file → ikon pensil → edit → Commit. Vercel otomatis
  deploy ulang dalam ±20 detik.
- **Di laptop:** clone repo, edit `index.html`, lalu push.

### Kalau mau kasih akses ke orang lain
1. GitHub: **Settings** → **Collaborators** → tambahkan username mereka
2. Vercel: **Project** → **Settings** → **Members** → undang email mereka

> Vercel **tidak menyimpan kode kalian** — Vercel hanya membaca dari GitHub.
> Jadi kalaupun akun Vercel bermasalah, web dan kodenya tetap aman di repo.

---

## 5. Push ke GitHub

Jauh lebih rapi kalau tiap orang punya folder sendiri (sesuai permintaan Rizki:
_"video, data, hasil prompting dll masukin ke folder masing²"_)。

```bash
git checkout -b feat/web-lombalink-kantinkilat
git add "TUGAS BERSAMA/web-lombalink" "TUGAS BERSAMA/web-kantinkilat"
git commit -m "feat(web): tambah landing page LombaLink & KantinKilat + GA4"
git push -u origin feat/web-lombalink-kantinkilat
```

---

## 6. Event GA4 yang sudah otomatis tercatat

Tidak perlu pasang apa pun — semua sudah aktif di dalam file.

| Event | Kapan terkirim | Gunanya |
|---|---|---|
| `nav_daftar`, `nav_daftar_mobile` | Klik tombol Daftar di navbar | Tahu CTA mana yang diklik |
| `hero_daftar` | Klik CTA utama di hero | Mengukur minat awal |
| `hero_lihat_fitur` / `hero_lihat_cara` | Klik tombol sekunder | Mengukur rasa penasaran |
| `cta_daftar` | Klik CTA di kotak besar | CTA paling bawah |
| `scroll_25/50/75/100` | Scroll 25%, 50%, 75%, 100% | Tahu sampai mana orang membaca |
| `lihat_seksi` | Seksi tertentu terlihat (params: `section`) | Tahu seksi mana yang menarik |
| `form_start` | Mulai mengetik di form | Mengukur niat isi form |
| `form_submit_attempt` | Klik tombol kirim | Mengukur percobaan kirim |
| `form_success` | Data berhasil terkirim | **Konversi sebenarnya** |
| `form_error` | Gagal kirim | Tahu kalau ada masalah teknis |

**Cara membaca hasilnya:**
- Buka Google Analytics → **Reports** → **Engagement** → **Events**
- Event yang paling penting: `form_success` — itu jumlah pendaftar nyata
- Bandingkan: berapa yang `form_start` vs `form_success` → kalau selisihnya
  jauh, berarti form-nya terlalu panjang atau membingungkan

**Cara tes GA4 bekerja (sebelum dibagikan):**
1. Buka web di browser
2. Klik beberapa tombol
3. Buka GA4 → **Reports** → **Realtime**
4. Harusnya muncul kunjunganmu dalam beberapa detik

> Kalau ingin data tes tidak mengotori laporan, pakai **Google Tag Assistant**
> atau mode **DebugView** di GA4.

---

## 7. Biaya

| Layanan | Biaya |
|---|---|
| Vercel (Hobby) | Gratis |
| GitHub (repo publik) | Gratis |
| GA4 | Gratis |
| Formspree | Gratis s/d 50 kiriman/bulan |

Total: **Rp 0**.

---

## 8. Catatan penting untuk tugas

- Web ini **landing page promosi**, bukan produk jadi. Fungsinya mengumpulkan
  **respons konsumen** — persis yang diminta Rizki untuk menentukan produk mana
  yang dilanjutkan.
- Statistik di web (80%, 48%) berasal dari riset kelompok yang ada di repo
  (`Rizki-1/sumber-30.csv`, jurnal IWIMA). **Jangan dihapus** — itu yang bikin
  klaimnya kredibel saat ditanya dosen.
- Sebelum dipakai serius, cek ulang angka tersebut dan sebutkan ukuran sampelnya
  kalau ditanya (350 mahasiswa FILKOM UB; 68 responden IWIMA).
