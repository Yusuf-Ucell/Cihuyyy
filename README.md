# Analisis Risiko SOP + AI (GitHub + Vercel)

Paket ini memakai HTML yang diberikan pengguna dan menambahkan backend AI di `api/analyze.js`.

## Cara kerja

- `Demo (aturan lokal)` menganalisis di browser menggunakan aturan yang sudah ada di HTML. Mode ini tidak memerlukan API key.
- `Claude`, `OpenAI`, dan `Gemini` mengirim teks SOP ke fungsi backend di `/api/analyze`.
- API key disimpan sebagai Environment Variables di Vercel, bukan di `index.html` atau repository GitHub.
- Aplikasi menerima teks yang ditempel atau file `.txt`. Upload PDF/DOCX belum disediakan di paket ini.
- Backend tidak melakukan pencarian web. Semua sitasi hukum atau sumber yang diusulkan AI tetap harus diverifikasi langsung dari sumber resmi.

## Deployment yang direkomendasikan

GitHub menyimpan kode. Vercel menjalankan frontend DAN backend. Jangan gunakan GitHub Pages sebagai satu-satunya hosting jika ingin API AI aman, karena GitHub Pages hanya menyajikan situs statis dan tidak menjalankan endpoint Node.js.

### 1. Masukkan file ke repository GitHub

Repository yang disarankan: `sop-risk-analyzer`.

Di GitHub, buka repository tersebut:

1. Unggah file `index.html` ini dan pilih untuk mengganti `index.html` lama.
2. Klik **Add file → Create new file**, ketik `api/analyze.js` sebagai nama file, lalu salin seluruh isi `api/analyze.js` dari paket ini dan commit.
3. Buat file baru `package.json` di root dan salin isinya dari paket ini.
4. Buat file baru `vercel.json` di root dan salin isinya dari paket ini.
5. (Opsional) Tambahkan `README.md` dan `.gitignore`.

Lebih mudah jika memakai GitHub Desktop atau VS Code: salin semua file dan folder dari paket ini ke folder repository lokal, commit, lalu push ke GitHub.

### 2. Hubungkan repository ke Vercel

1. Buka https://vercel.com dan masuk/daftar.
2. Pilih **Add New → Project**.
3. Hubungkan akun GitHub dan pilih repository `sop-risk-analyzer`.
4. Pada konfigurasi project, gunakan root folder repository. Untuk HTML statis, tidak diperlukan build command khusus.
5. Klik **Deploy** dan tunggu sampai deployment selesai.
6. Setelah berhasil, Vercel akan memberi URL seperti `https://nama-project.vercel.app`.

Setiap `commit` atau `push` baru ke branch production yang dipilih dapat memicu deployment baru.

### 3. Siapkan satu penyedia AI

Pilih salah satu dulu. Paket ini mendukung tiga penyedia, tetapi kamu harus memiliki API key dari penyedia yang dipilih.

- Claude: https://console.anthropic.com/
- OpenAI API: https://platform.openai.com/api-keys
- Gemini API: https://aistudio.google.com/apikey

Catatan: langganan chat konsumen seperti Claude Pro atau ChatGPT Plus tidak otomatis berarti API key atau kredit API tersedia. Ketersediaan, batas gratis, dan biaya mengikuti akun/API provider.

### 4. Masukkan Environment Variables di Vercel

Di dashboard Vercel:

1. Pilih project hasil deployment.
2. Buka **Settings → Environment Variables**.
3. Tambahkan `ACCESS_CODE` dengan kata sandi acak yang panjang. Jangan pakai contoh literal dari dokumentasi ini.
4. Jika menggunakan Claude, tambahkan `ANTHROPIC_API_KEY` dan isi dengan key dari Claude Console.
5. Jika menggunakan OpenAI, tambahkan `OPENAI_API_KEY`.
6. Jika menggunakan Gemini, tambahkan `GEMINI_API_KEY`.
7. Klik **Save**.
8. Buka tab **Deployments** dan lakukan **Redeploy** pada deployment terbaru agar variabel baru terbaca.

Jangan pernah menaruh API key di HTML, JavaScript browser, README publik, pesan commit, atau screenshot. Jangan commit file `.env`.

### 5. Uji di website

1. Buka URL Vercel.
2. Tempel SOP uji yang tidak rahasia dan klik **Analisis SOP**.
3. Untuk pemeriksaan lokal, pilih **Demo (aturan lokal)**.
4. Untuk AI, pilih penyedia yang API key-nya sudah disetel, misalnya **Claude**, lalu klik **Analisis SOP**.
5. Ketika diminta, masukkan nilai `ACCESS_CODE` yang kamu buat di Vercel. Setelah itu klik **Analisis SOP** sekali lagi.
6. Pastikan halaman **Hasil analisis**, **SOP revisi**, **Dashboard**, dan **Laporan** terisi.

## Jika terjadi error

- `ACCESS_CODE belum diatur`: tambahkan variabel itu di Vercel lalu redeploy.
- `Kode akses salah`: masukkan kode persis seperti yang ada di Vercel.
- `ANTHROPIC_API_KEY belum diatur`: tambahkan key Claude; pesan serupa berlaku untuk OpenAI atau Gemini.
- `HTTP 401` atau `HTTP 403`: periksa key, akses akun, dan billing di penyedia AI.
- `HTTP 429`: kuota atau rate limit provider mungkin tercapai.
- `HTTP 400`: model mungkin tidak tersedia untuk akun atau konfigurasi provider. Periksa model di dokumentasi resmi.
- `404` pada `/api/analyze`: pastikan file berada tepat di `api/analyze.js` pada root repository, bukan di dalam folder lain.
- `FUNCTION_INVOCATION_TIMEOUT`: SOP mungkin terlalu panjang atau model merespons terlalu lambat. Uji dengan SOP yang lebih pendek dan periksa batas durasi plan Vercel.

## Keamanan dan batasan

- `ACCESS_CODE` adalah penghalang sederhana untuk uji/pilot, bukan sistem login per pengguna. Jangan menganggapnya sebagai autentikasi tingkat perusahaan.
- Aplikasi ini tidak menerapkan rate limit per pengguna. Jangan membagikan URL dan kode akses ke publik tanpa proteksi tambahan.
- Jangan mengirim SOP internal, data nasabah, nomor rekening, data pribadi, atau informasi rahasia ke penyedia AI tanpa persetujuan perusahaan dan pemeriksaan kebijakan data yang berlaku. Uji pertama harus memakai teks contoh yang sudah dianonimkan.
- AI dapat membuat kesalahan atau menyarankan kontrol yang tidak sesuai. Rujukan hukum tidak diverifikasi otomatis dan hasil harus diperiksa petugas yang berwenang.
- File ini hanya membaca teks yang ditempel atau `.txt`; pembacaan PDF/DOCX dan penelusuran sumber web belum diimplementasikan.
