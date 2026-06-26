# Panduan Deploy Kukus.In

Aplikasi ini terdiri dari dua bagian:
- **Frontend** (React) → deploy ke **Vercel**
- **Backend** (FastAPI + MongoDB) → deploy ke **Railway**

---

## Langkah 1 — Siapkan MongoDB Atlas (Database)

1. Buka [mongodb.com/atlas](https://www.mongodb.com/atlas) → buat akun gratis
2. Buat cluster baru (pilih **M0 Free**)
3. Buat **Database User**: catat username & password
4. Di **Network Access**, tambahkan IP: `0.0.0.0/0` (allow from anywhere)
5. Klik **Connect** → **Drivers** → copy connection string
   - Contoh: `mongodb+srv://user:password@cluster.mongodb.net/`

---

## Langkah 2 — Deploy Backend ke Railway

1. Buka [railway.app](https://railway.app) → login dengan GitHub
2. Klik **New Project** → **Deploy from GitHub repo**
3. Pilih repo ini, lalu set **Root Directory** ke `backend`
4. Setelah deploy mulai, masuk ke tab **Variables** dan tambahkan:

   | Key | Value |
   |-----|-------|
   | `MONGO_URL` | `mongodb+srv://user:pass@cluster.mongodb.net/` |
   | `DB_NAME` | `kukusin` |
   | `CORS_ORIGINS` | *(isi nanti setelah dapat URL Vercel)* |
   | `PORT` | `8000` |

5. Masuk ke tab **Settings** → **Networking** → klik **Generate Domain**
6. Catat URL backend kamu, contoh: `https://kukusin-backend.up.railway.app`

---

## Langkah 3 — Deploy Frontend ke Vercel

### Cara A: Via Vercel CLI (recommended)

```bash
cd frontend
npm install -g vercel
vercel login
vercel --prod
```

Saat ditanya:
- Set up and deploy? → **Y**
- Which scope? → pilih akun kamu
- Link to existing project? → **N**
- Project name → terserah (misal `kukusin`)
- Root directory → tekan Enter (sudah di folder frontend)

Setelah deploy, Vercel akan tanya environment variables. Tambahkan:
```
REACT_APP_BACKEND_URL = https://kukusin-backend.up.railway.app
```

### Cara B: Via Dashboard Vercel

1. Buka [vercel.com](https://vercel.com) → **Add New Project**
2. Import repo GitHub kamu
3. Set **Root Directory** ke `frontend`
4. Di bagian **Environment Variables**, tambahkan:
   - `REACT_APP_BACKEND_URL` = URL Railway backend kamu
5. Klik **Deploy**

Setelah selesai, kamu akan dapat URL Vercel seperti: `https://kukusin.vercel.app`

---

## Langkah 4 — Update CORS di Railway

Sekarang kamu sudah punya URL Vercel. Kembali ke Railway:

1. Buka project backend → tab **Variables**
2. Update `CORS_ORIGINS` dengan URL Vercel kamu:
   ```
   CORS_ORIGINS=https://kukusin.vercel.app
   ```
3. Railway akan otomatis redeploy

---

## Selesai! ✅

Buka URL Vercel kamu di browser — aplikasi Kukus.In sudah live.

### Troubleshooting

**Frontend tampil kosong / error API:**
- Cek apakah `REACT_APP_BACKEND_URL` sudah di-set di Vercel (tanpa trailing slash)
- Pastikan backend Railway sudah running (cek logs di Railway)

**Backend error saat start:**
- Cek Railway logs: pastikan `MONGO_URL` dan `DB_NAME` sudah benar
- Pastikan MongoDB Atlas Network Access sudah allow `0.0.0.0/0`

**CORS error di browser:**
- Pastikan `CORS_ORIGINS` di Railway sudah diisi URL Vercel yang benar (tanpa trailing slash)
