# Dashboard Evaluasi Proyek JO

Dashboard web satu file (`index.html`) untuk memantau evaluasi proyek Joint Operation (JO) — bertema konstruksi, minimalis, dan mengambil data langsung dari Google Sheets. Mendukung banyak proyek (didesain untuk ~100 proyek di 3 divisi: EPCC, Infra, Building) dengan pemilih Divisi + Proyek yang mengganti seluruh isi dashboard secara instan.

Dashboard ini dibangun mengikuti struktur data pada file `Dashboard EPCC Juli 26.xlsx` yang Anda kirim (sheet `EPCC` sebagai data master proyek, sheet `PAJAK EPCC` sebagai data restitusi PPN).

## 1. Siapkan Google Sheet

1. Struktur setiap divisi mengikuti pola sheet `EPCC` yang sudah Anda punya:
   - Baris 8–11: header (boleh dibiarkan seperti aslinya).
   - Baris 12 dst: satu baris = satu proyek. Kolom `NO` (kolom B) **harus berisi angka** — inilah penanda "ini baris proyek", bukan baris subtotal/total.
   - Kolom mengikuti posisi yang sama seperti sheet `EPCC` asli (Nama Proyek di kolom E, Lokasi di G, Nilai Kontrak di Q, dst). Jangan menyisipkan/menghapus kolom di tengah, karena dashboard membaca berdasarkan posisi kolom.
2. Untuk 3 divisi, buat 3 sheet/tab data master — beri nama bebas (default dashboard: `EPCC`, `INFRA`, `BUILDING`), dan 3 sheet pajak/PPN dengan struktur seperti `PAJAK EPCC` (default: `PAJAK EPCC`, `PAJAK INFRA`, `PAJAK BUILDING`).
3. **Bagikan spreadsheet**: klik **Share**, ubah menjadi **"Anyone with the link" → Viewer**. Tidak perlu "Publish to web" — cukup share link saja.
4. Salin **Spreadsheet ID** dari URL:
   `https://docs.google.com/spreadsheets/d/`**`INI_SPREADSHEET_ID_NYA`**`/edit`
5. **(Opsional) Foto proyek di banner atas** — tambahkan satu kolom baru di sheet dengan header mengandung kata "Foto" (bebas di posisi mana saja, mis. `Foto Proyek`), isi selnya dengan **URL gambar publik** (mis. link Google Drive yang sudah di-share "Anyone with link", diubah ke format `https://drive.google.com/uc?export=view&id=ID_FILE_NYA`, atau URL gambar publik lainnya). Dashboard otomatis mendeteksi kolom ini lewat nama headernya — tidak perlu edit kode. Foto ini akan tampil untuk **semua orang** yang buka dashboard. Kalau belum sempat isi kolom ini, ada juga kolom/kotak khusus "Upload Foto" di pojok kiri banner atas untuk unggah foto langsung dari browser (klik kotaknya) — tapi itu **hanya tersimpan di perangkat/browser itu saja**, tidak ikut terlihat oleh anggota tim lain.

## 2. Hubungkan ke Dashboard

Buka `index.html` dengan teks editor, cari bagian `DEFAULT_CONFIG` di dekat awal tag `<script>`, lalu isi:

```js
const DEFAULT_CONFIG = {
  spreadsheetId: 'TEMPEL_SPREADSHEET_ID_ANDA_DI_SINI',
  reportMonth: '', // contoh '2026-08' — kosongkan agar dashboard pakai bulan berjalan otomatis
  divisions: [
    { key:'EPCC',     label:'EPCC',     masterSheet:'EPCC',     taxSheet:'PAJAK EPCC' },
    { key:'INFRA',    label:'Infra',    masterSheet:'INFRA',    taxSheet:'PAJAK INFRA' },
    { key:'BUILDING', label:'Building', masterSheet:'BUILDING', taxSheet:'PAJAK BUILDING' },
  ],
  dataStartRow: 12,
};
```

Sesuaikan `masterSheet` / `taxSheet` dengan nama tab asli di spreadsheet Anda. Simpan file.

Anda juga bisa mengubah pengaturan ini **tanpa edit kode**, langsung dari dashboard: klik ikon gerigi (⚙) di kanan atas → isi form → **Tes Koneksi** untuk memastikan setiap divisi berhasil terbaca → **Terapkan & Muat Ulang**. Perubahan lewat panel ini hanya berlaku untuk sesi browser saat itu (hilang saat halaman di-refresh) — untuk perubahan permanen, edit `DEFAULT_CONFIG` di file seperti di atas.

## 3. Coba Lokal

Cukup buka `index.html` langsung di browser (double-click). Karena dashboard mengambil data lewat internet (Google Sheets), pastikan komputer terhubung ke internet saat membuka file.

## 4. Deploy ke GitHub Pages (akses tim, online)

Tidak perlu install apa pun atau paham `git` — cukup lewat website GitHub (drag & drop file).

**A. Buat repository baru**
1. Login ke [github.com](https://github.com), klik tombol **+** di kanan atas → **New repository**.
2. Isi **Repository name** bebas, misalnya `dashboard-jo`.
3. Pilih **Public** (GitHub Pages gratis hanya jalan otomatis di repo Public; repo Private butuh paket GitHub Team/Enterprise). Karena dashboard ini sudah dilindungi **gerbang kata sandi** (lihat bagian D di bawah), repo Public tetap aman dipakai — kode sumbernya publik tapi halaman yang sudah jadi tidak bisa dibuka orang tanpa tahu kata sandinya.
4. Klik **Create repository**.

**B. Upload file**
1. Di halaman repo yang baru dibuat, klik **uploading an existing file** (atau menu **Add file → Upload files**).
2. Drag & drop (atau pilih) file berikut dari komputer Anda: `index.html` dan `README.md`. Folder `apps-script/Code.gs` boleh ikut diupload sebagai arsip/dokumentasi, tapi tidak wajib — dashboard tidak memanggilnya langsung.
3. Scroll ke bawah, klik **Commit changes**.

**C. Nyalakan GitHub Pages**
1. Di repo, buka **Settings → Pages** (menu kiri).
2. Di **Build and deployment → Source**, pilih **Deploy from a branch**.
3. Di **Branch**, pilih `main` dan folder `/ (root)` → klik **Save**.
4. Tunggu 1–2 menit, refresh halaman Settings → Pages sampai muncul link hijau:
   `https://USERNAME.github.io/NAMA_REPO/`
   Itulah alamat dashboard online Anda — bagikan link ini ke tim.

Tidak ada proses build — file `index.html` ini murni HTML/CSS/JS dan langsung berjalan begitu dibuka di browser mana pun, termasuk dari GitHub Pages.

**D. Gerbang kata sandi (akses gerbang sebelum data dimuat)**

Karena GitHub Pages bersifat publik (siapa pun yang punya link bisa membuka halamannya), dashboard ini sudah dilengkapi layar "masukkan kata sandi" sebelum data proyek dimuat. Ini **bukan keamanan penuh** — hanya penyaring dasar terhadap orang yang kebetulan menemukan link, karena kata sandinya tetap terlihat siapa pun yang membuka "View Page Source" di browser. Jangan taruh data sangat rahasia di dashboard ini kalau linknya akan disebar luas.

- Kata sandi default: **`wika2026`**. Untuk menggantinya, buka `index.html`, cari baris:
  ```js
  const ACCESS_GATE = {
    enabled: true,
    password: 'wika2026',
  };
  ```
  ganti `'wika2026'` dengan kata sandi pilihan Anda, simpan, lalu upload ulang file `index.html` ke GitHub (Add file → Upload files, timpa file lama).
- Untuk mematikan gerbang ini sama sekali (misalnya kalau dashboard hanya dipakai di jaringan internal), ubah `enabled: true` menjadi `enabled: false`.
- Centang "Ingat perangkat ini" membuat browser tidak perlu minta kata sandi lagi di kunjungan berikutnya pada perangkat yang sama.

## 5. Menambah / Mengubah Proyek

Karena dashboard membaca langsung dari Google Sheets setiap kali dibuka (tombol refresh ↻ di kanan atas untuk memuat ulang manual), Anda cukup **menambah baris baru di sheet** — proyek baru otomatis muncul di daftar pilihan, tanpa perlu mengubah kode dashboard. Batas praktis: ratusan proyek per divisi berjalan lancar karena data diambil sekali per divisi per pemuatan.

## 6. Catatan Data & Formula

Dashboard mereplikasi logika perhitungan yang ada di sheet `DASHBOARD` pada file Excel asli (INDEX/MATCH berdasarkan nama proyek), termasuk:

- Info umum proyek (lokasi, porsi WIKA, status, progress, tanggal, nilai kontrak).
- Rencana Cash In 3 bulan ke depan (dari `reportMonth` + 1/2/3 bulan) dan Rencana Cash In RKAP (RA/RI/% Capaian).
- KPI: Kas & Bank, Persediaan, Piutang Usaha & Retensi, Tagihan Bruto, PDP, PPN M, Hutang Usaha & Pajak.
- Investasi/Piutang/Utang Ventura Bersama, Biaya Langsung (Material/Upah/Alat/Subkont), Aging Piutang Usaha & Tagihan Bruto.
- Posisi Restitusi PPN per tahun & per tahap proses (dari sheet Pajak), dengan pencocokan nama proyek melalui kolom bantu (kolom `V` pada sheet Pajak — **jangan diubah/dihapus**, formula/isian di kolom ini dipakai sebagai kunci pencocokan proyek).
- Masalah Potensial (teks bebas per proyek) — sumber utamanya kolom di Google Sheet, tapi kartu **"Masalah Potensial"** di dashboard juga punya kotak teks yang bisa langsung ditulis/diedit dari browser (tombol **Simpan Catatan**). Sama seperti unggah foto lokal (lihat bagian 1), catatan yang ditulis lewat kotak ini **hanya tersimpan di browser/perangkat itu saja** (belum tampil untuk anggota tim lain) — untuk catatan yang perlu terlihat semua orang, tetap isikan lewat kolom Masalah Potensial di Google Sheet. Tombol **Kembalikan ke data sheet** muncul kalau ada catatan lokal, untuk menghapusnya dan kembali memakai isi dari sheet.

Jika struktur kolom di sheet Anda berbeda dari template asli, sesuaikan konstanta `COL` dan `TAXCOL` di bagian atas skrip (`<script>`) pada `index.html` — setiap konstanta merujuk ke indeks kolom (A=0, B=1, C=2, dst).

## 7. Tema & Aksesibilitas

- Tema default adalah **terang, putih-biru gradien** dan minim warna (aksen biru satu warna untuk elemen dekoratif seperti ikon KPI); mode gelap tetap tersedia lewat ikon matahari di kanan atas, tersimpan hanya untuk sesi berjalan.
- Font memakai **Consolas** (jatuh ke font monospace lain kalau Consolas tidak terpasang di perangkat).
- Tampilan dibuat sepadat mungkin (kartu, tabel, dan grafik dibuat ringkas) supaya isi dashboard bisa terlihat dengan sesedikit mungkin scroll — meski karena banyaknya data keuangan per proyek, muat-tidaknya persis dalam satu layar tetap tergantung ukuran/resolusi monitor Anda.
- Setiap grafik punya tombol "Tabel" untuk melihat data mentah dalam bentuk tabel angka, dan tooltip saat hover.
- Palet warna kategori sudah divalidasi untuk keterbacaan bagi pengguna buta warna (mengikuti panduan data-viz internal); kartu komposisi (donut) sengaja memakai satu keluarga warna biru saja agar tidak terlalu ramai warna.

## 8. Kalau grafik tidak muncul (kartu kosong padahal angka judul sudah benar)

Dashboard menggambar grafik memakai library **Chart.js** yang diambil dari internet (CDN publik). Kalau kartu grafik terlihat kosong/putih padahal angka ringkasan di atasnya sudah benar (mis. "Investasi Ventura Bersama: 28.881 Rp Juta" tapi area di bawahnya kosong), itu tandanya jaringan yang Anda pakai (biasanya jaringan kantor/VPN perusahaan) **memblokir domain CDN** tersebut — bukan masalah data, karena data dari Google Sheets tetap berhasil terbaca.

Dashboard sudah dibuat mencoba 3 sumber CDN berbeda secara otomatis (cdnjs, jsdelivr, unpkg). Kalau ketiganya tetap gagal, akan muncul pesan peringatan kuning di atas dashboard. Solusinya, pilih salah satu:

1. **Coba jaringan lain** — misalnya hotspot HP — untuk memastikan ini memang soal jaringan, bukan soal dashboard.
2. **Minta admin IT mengizinkan (whitelist)** domain berikut di jaringan kantor: `cdnjs.cloudflare.com`, `cdn.jsdelivr.net`, `unpkg.com`.
3. **Simpan Chart.js secara lokal** (paling pasti, tidak bergantung internet sama sekali untuk bagian grafik):
   - Dari komputer yang jaringannya tidak diblokir, unduh file ini: `https://cdn.jsdelivr.net/npm/chart.js@4.4.4/dist/chart.umd.min.js`
   - Simpan dengan nama persis `chart.umd.min.js`, taruh di **folder yang sama** dengan `index.html` (dan upload juga ke repo GitHub Pages Anda kalau sudah online).
   - Dashboard otomatis akan memakainya sebagai cadangan terakhir jika ketiga CDN di atas gagal — tidak perlu edit kode apa pun.

## 9. (Opsional/"go-live") Simpan Catatan Masalah Potensial ke Google Sheet

Secara default, kotak catatan pada kartu **"Masalah Potensial"** hanya tersimpan di browser/perangkat yang dipakai menulis (localStorage) — belum tampil untuk anggota tim lain. Supaya catatan itu **otomatis ikut tertulis ke Google Sheet** (dan tampil untuk semua orang yang buka dashboard setelah refresh), Anda perlu membuat satu **Google Apps Script Web App** kecil — ini karena dashboard murni file statis (HTML/JS di browser) tanpa server sendiri, sedangkan Google Sheets API baca-saja (`gviz`) yang dipakai dashboard ini tidak bisa dipakai untuk menulis.

Langkah setup (sekali saja, dilakukan di akun Google Anda sendiri):

1. Buka Google Sheet yang sama dipakai dashboard ini.
2. Menu **Extensions → Apps Script**.
3. Hapus isi default `Code.gs`, lalu salin-tempel **seluruh isi file `apps-script/Code.gs`** yang disertakan bersama dashboard ini.
4. Di baris `var SCRIPT_SECRET = 'GANTI_DENGAN_TOKEN_RAHASIA_ANDA';`, ganti dengan kata sandi bebas pilihan Anda sendiri (atau kosongkan `''` kalau tidak mau pakai token sama sekali — tidak disarankan untuk spreadsheet berisi data sensitif).
5. Klik **Deploy → New deployment** → pilih tipe **"Web app"**:
   - **Execute as**: Me (akun Anda)
   - **Who has access**: Anyone
6. Klik **Deploy**, lalu salin **URL Web App** yang muncul (diakhiri `/exec`).
7. Di dashboard, klik ikon gerigi (⚙) → bagian **"Simpan Catatan Masalah Potensial ke Google Sheet"** → tempel URL tadi ke kolom **URL Web App**, dan token rahasia yang sama ke kolom **Token Rahasia** → **Terapkan & Muat Ulang**.
8. Coba tulis catatan di kartu Masalah Potensial pada sebuah proyek → klik **Simpan Catatan** → kalau berhasil akan muncul status hijau "Tersimpan ke Google Sheet ✓", dan isi kolom Masalah Potensial di baris proyek tsb di Google Sheet ikut berubah.

Catatan penting soal keamanan: karena dashboard adalah halaman publik/statis, URL Web App di atas bisa terlihat oleh siapa saja yang membuka source code halaman (mis. lewat DevTools browser). Token rahasia di langkah 4 hanyalah **penyaring dasar** (mencegah panggilan acak/bot), bukan keamanan penuh setingkat login — jangan andalkan ini untuk data yang sangat sensitif. Kalau koneksi ke Web App gagal (mis. sedang offline, atau URL/token salah), dashboard otomatis tetap menyimpan catatan secara lokal di perangkat itu sebagai cadangan, dan menampilkan pesan error di bawah tombol Simpan.
