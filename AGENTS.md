# AGENTS.md — Panduan Kerja Project

File ini dibaca sebelum menulis kode apa pun — oleh junior programmer maupun AI agent.
Spesifikasi lengkap ada di `PRD.md`. File ini hanya berisi aturan kerja dan hal yang sering salah dikerjakan.

## Aturan emas

1. `PRD.md` adalah sumber kebenaran. Kode dan PRD berbeda → ubah kode, atau catat perubahan keputusan di PRD §Assumptions & Decisions.
2. Kerjakan per fase sesuai PRD §U (Development Roadmap), berurutan. Jangan mulai fase berikutnya sebelum Definition of Done fase sekarang terpenuhi.
3. Saat mulai satu fase, pecah dulu jadi daftar task konkret (file yang disentuh, endpoint, test) sebelum coding.
4. Harga, stock, dan quantity **selalu** diambil ulang dari database backend saat checkout — jangan pernah percaya angka dari frontend (PRD §R.3).
5. Skema database hanya berubah lewat file migrasi (`golang-migrate`). Jangan pernah edit tabel langsung dari SQL client.
6. Secret (`JWT_SECRET`, API key, password) hanya di `.env` (permission 600). Tidak boleh masuk repo, tidak boleh di-log (PRD §O).
7. Response API mengikuti format PRD §L: sukses `{success, data, meta}`; error `{success:false, message, code, errors}`. Status code mapping ada di tabel PRD §L.
8. Struktur backend ikut PRD §M.2: `handler → service → repository`, tanpa layer tambahan.
9. Uang disimpan sebagai `NUMERIC` di PostgreSQL, dan `decimal`/integer di kode Go. Jangan pernah pakai `float`.
10. Perubahan status order hanya lewat alur yang didefinisikan PRD §F (payment flow). Webhook Midtrans wajib idempotent (PRD §R.11).
11. Sebelum mengerjakan fitur, baca edge case terkait di PRD §S.
12. Setiap kode non-trivial (ada branch/loop/money path) harus punya minimal 1 test yang bisa dijalankan. Domain kritis (checkout, payment, auth) ikut strategi PRD §P, termasuk 1 concurrency test checkout stock=1.

## Stack (sudah diputuskan di PRD — jangan diganti tanpa update PRD)

| Layer | Teknologi |
|---|---|
| Backend | Go, Gin/Echo/chi, `pgx`, `golang-migrate`, `golangci-lint` |
| Database | PostgreSQL 16 + Redis 7 (cache & rate limit, bukan source of truth) |
| Frontend | Next.js (App Router), TanStack Query, React Hook Form + Zod, React Context untuk auth. Zustand/Redux hanya jika benar-benar perlu (PRD §K) |
| Integrasi | Midtrans Snap (sandbox), Resend (email), Supabase/Cloudinary (storage) |
| Testing | Go stdlib `testing` + `httptest`, test DB terpisah/testcontainers; E2E Playwright/Cypress (PRD §P) |
| Deploy | FE ke Vercel, BE di VPS dengan Docker Compose + Nginx + HTTPS (PRD §N) |

## Perintah standar

| Aksi | Command |
|---|---|
| Infra lokal (DB + Redis) | `docker compose up -d` |
| Migration up | `migrate -path migrations -database "$DATABASE_URL" up` |
| Jalankan backend | `go run ./cmd/api` |
| Test backend | `go test ./...` |
| Lint backend | `golangci-lint run` |
| Jalankan frontend | `npm run dev` |
| Lint frontend | `npm run lint` |

## Environment variables (daftar lengkap: PRD §N.2)

`DATABASE_URL`, `REDIS_URL`, `JWT_SECRET`, `MIDTRANS_SERVER_KEY`, `MIDTRANS_CLIENT_KEY`, `MIDTRANS_IS_PRODUCTION=false`, `STORAGE_*`, `RESEND_API_KEY`, `RESEND_FROM_EMAIL`, `FRONTEND_URL`, `CORS_ALLOWED_ORIGIN`

## Gotchas — sering salah dikerjakan junior/AI

- **Refresh token dipakai ulang (reuse)** → revoke seluruh sesi user, bukan cuma 401 (PRD §C.1).
- Access token disimpan di memory state frontend, **bukan** localStorage (PRD §C.1). Refresh token hanya di httpOnly cookie.
- Endpoint `/auth/refresh` butuh CSRF double-submit token (PRD Assumptions #12).
- Stock berkurang saat order `PENDING` dibuat, dikembalikan saat `EXPIRED`/`CANCELLED` — bukan saat payment sukses (PRD §R.14, §G).
- `/auth/forgot-password` selalu merespons sukses generic, email dikirim async, tidak boleh membocorkan apakah email terdaftar (PRD §C.1).
- Redis down → aplikasi tetap jalan, cache fallback ke DB. Jangan bikin Redis jadi titik gagal (PRD §H, §S.10).
- Webhook Midtrans: wajib verifikasi signature + status check sebelum ubah status order (PRD §F.2, §R.5).
- Endpoint baru wajib dicatat di PRD §E; tabel baru wajib dicatat di PRD §D. PRD tidak boleh ketinggalan dari kode.
- Perubahan requirement di tengah jalan dicatat di PRD §Assumptions & Decisions.

## Alur kerja fase

1. Baca PRD §U, ambil fase berikutnya yang belum selesai.
2. Pecah jadi task list, kerjakan task per task.
3. Tiap task selesai: test + lint jalan.
4. DoD fase terpenuhi → tandai selesai, baru lanjut fase berikutnya.
