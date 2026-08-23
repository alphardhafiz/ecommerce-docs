# SEED_DATA.md — Data Awal Produk (Fase 3)

Niche: **"Ledger & Tag — Alat tulis kertas & kayu"** (lihat DESIGN.md §0 di repo client).
Sumber kebenaran desain produk: `../docs/PRD.md` §C.3/C.4, schema di `server/migrations/0004–0006`.

Dipakai oleh halaman client Fase 3: Home (hero + kategori + featured), Product Listing (filter/sort/pagination), Product Detail (galeri).

## A. Kategori (tabel `categories`)

`is_active=true`, `deleted_at=NULL`. `slug` unik.

| name | slug |
|---|---|
| Buku Tulis & Notebook | `buku-tulis-notebook` |
| Pena & Pensil | `pena-pensil` |
| Kertas & Kartu | `kertas-kartu` |
| Aksesoris Meja | `aksesoris-meja` |

## B. Produk (tabel `products`) — 18

Aturan: `price` integer rupiah (API kirim `bigint`; `89000` = Rp 89.000), `stock >= 0`,
`is_active=true`, `deleted_at=NULL`, `category_id` FK ke kategori di atas.
Deskripsi singkat, fokus material (DESIGN.md §8).

⭐ = produk hero (Home). 🔴 = sold-out (demo `is_available:false`).

| # | Nama | Deskripsi | Harga | Stok | Kategori |
|---|---|---|---|---|---|
| 1 ⭐ | Buku Kas "Ledger & Tag" A5 | Buku kas jahit tangan, sampul kraft tebal, 192 halaman. Baris & kolom tercetak seperti buku besar sungguhan — untuk yang suka membukukan dengan tulisan tangan. | 89.000 | 12 | Buku Tulis & Notebook |
| 2 | Notebook jahit tangan A5 | Kertas daur ulang 100%, 128 halaman kosong. Jahitan terbuka yang terlihat, sampul kraft. | 65.000 | 20 | Buku Tulis & Notebook |
| 3 | Notebook saku A6 | Sampul kayu tipis natural, kertas krem 96 halaman. Cukup kecil untuk kantong. | 55.000 | 15 | Buku Tulis & Notebook |
| 4 | Sketchbook A4 | Kertas tebal 200 gsm, 60 halaman — tahan basah untuk tinta, pensil, dan cat ringan. | 75.000 | 18 | Buku Tulis & Notebook |
| 5 | Jurnal garis tahunan | Sampul linen abu batu, 320 halaman bergaris. Jilid datar yang bisa dibuka 180°. | 95.000 | 10 | Buku Tulis & Notebook |
| 6 | Pensil kayu set 6 | Graphite 2B, body kayu utuh tanpa cat. Diasah siap pakai, dalam kotak karton daur ulang. | 35.000 | 30 | Pena & Pensil |
| 7 | Pena kayu bulat | Body kayu bulat digenggam nyaman, tinta gel hitam 0,5 mm. Setiap pena punya serat kayu berbeda. | 45.000 | 25 | Pena & Pensil |
| 8 | Pensil mekanik kayu | Body kayu, mekanisme 0,5 mm. Berat seimbang untuk menulis lama. | 68.000 | 14 | Pena & Pensil |
| 9 | Set gambar komplit | 12 pensil graphite (2H–8B) + rautan kayu + penghapus. Kotak kayu dengan sekat. | 110.000 | 8 | Pena & Pensil |
| 10 | Kartu ucapan letterpress set 4 | Cetak letterpress motif cap gudang di atas kertas tebal 300 gsm. Set 4 motif + amplop kraft. | 40.000 | 24 | Kertas & Kartu |
| 11 | Kertas kraft A4 isi 25 | Lembaran kertas kraft 120 gsm, cocok untuk cetak, bungkus, dan surat. | 25.000 | 40 | Kertas & Kartu |
| 12 | Washi tape motif cap | Pita kertas motif cap, 15 mm × 10 m. Menempel rapi dan mudah dilepas tanpa bekas. | 28.000 | 35 | Kertas & Kartu |
| 13 | Kertas memo blok | Blok memo 400 lembar kertas krem, perekat di sisi atas. Perforasi sobek rapi. | 32.000 | 22 | Kertas & Kartu |
| 14 | Pembatas buku kayu | Kayu tipis dengan ukiran cap. Tidak melengkung, tahan lama di antara halaman. | 20.000 | 50 | Aksesoris Meja |
| 15 | Penggaris kayu 30 cm | Kayu jati, skala cetak tahan aus. Tepi rata untuk menarik garis. | 30.000 | 28 | Aksesoris Meja |
| 16 | Rautan pensil kayu | Body kayu, pisau baja. Buang serutan lewat tutup geser. | 25.000 | 18 | Aksesoris Meja |
| 17 | Kotak pensil kayu | Kotak kayu tutup geser, muat ±15 alat tulis. Finis tanpa cat — serat kayu tetap terlihat. | 85.000 | 6 | Aksesoris Meja |
| 18 🔴 | Buku Kas edisi terbatas | Buku kas nomor seri, cap edisi terbatas di sampul. **Stok 0** — contoh produk habis. | 120.000 | 0 | Buku Tulis & Notebook |

## C. Gambar (tabel `product_images` + hero)

- **Wajib:** ≥1 gambar per produk dengan `is_primary=true` (API listing membaca `primary_image` dari subquery). Total **18 gambar primary**.
- **Galeri detail (disarankan):** +2 gambar per produk untuk #1, 2, 4, 6, 7, 9, 10, 17 (`is_primary=false`, `display_order` 1, 2). Total keseluruhan ± 45–50 gambar.
- **Hero Home (1 gambar):** flat-lay hangat — buku kas + notebook + pensil kayu di atas meja kayu, natural light. Tidak masuk DB (dipakai client sebagai aset statis, atau ambil gambar primary produk #1 resolusi tinggi).

Subjek foto per kategori (DESIGN.md §7 — natural light, background hangat kayu/linen/kraft):

| Kategori | Subjek foto |
|---|---|
| Buku Tulis & Notebook | Tutup buku di meja kayu; halaman terbuka; close-up sampul kraft/linen; buku ditumpuk |
| Pena & Pensil | Sekelompok pensil di atas kertas kraft; close-up grain kayu; pena di tangan menulis |
| Kertas & Kartu | Tumpukan kertas; kartu letterpress dengan cahaya miring; gulungan washi tape |
| Aksesoris Meja | Pembatas/penggaris kayu di atas kertas; kotak pensil terbuka; rautan + serutan |

Sumber gambar: upload ke Supabase storage (env `STORAGE_*`) lalu pakai URL-nya, atau URL langsung dari Unsplash (`images.unsplash.com/...`). URL tidak ditentukan di dokumen ini — isi sendiri.

## D. Template SQL (isi URL gambar & sesuaikan)

```sql
-- Kategori
INSERT INTO categories (name, slug) VALUES
  ('Buku Tulis & Notebook', 'buku-tulis-notebook'),
  ('Pena & Pensil',           'pena-pensil'),
  ('Kertas & Kartu',          'kertas-kartu'),
  ('Aksesoris Meja',          'aksesoris-meja');

-- Produk (price integer rupiah)
INSERT INTO products (category_id, name, description, price, stock) VALUES
  ((SELECT id FROM categories WHERE slug = 'buku-tulis-notebook'), 'Buku Kas "Ledger & Tag" A5', 'Buku kas jahit tangan, sampul kraft tebal, 192 halaman.', 89000, 12),
  ((SELECT id FROM categories WHERE slug = 'buku-tulis-notebook'), 'Notebook jahit tangan A5', 'Kertas daur ulang 100%, 128 halaman kosong.', 65000, 20),
  ((SELECT id FROM categories WHERE slug = 'buku-tulis-notebook'), 'Notebook saku A6', 'Sampul kayu tipis natural, kertas krem 96 halaman.', 55000, 15),
  ((SELECT id FROM categories WHERE slug = 'buku-tulis-notebook'), 'Sketchbook A4', 'Kertas tebal 200 gsm, 60 halaman.', 75000, 18),
  ((SELECT id FROM categories WHERE slug = 'buku-tulis-notebook'), 'Jurnal garis tahunan', 'Sampul linen abu batu, 320 halaman bergaris.', 95000, 10),
  ((SELECT id FROM categories WHERE slug = 'pena-pensil'), 'Pensil kayu set 6', 'Graphite 2B, body kayu utuh tanpa cat.', 35000, 30),
  ((SELECT id FROM categories WHERE slug = 'pena-pensil'), 'Pena kayu bulat', 'Body kayu bulat, tinta gel hitam 0,5 mm.', 45000, 25),
  ((SELECT id FROM categories WHERE slug = 'pena-pensil'), 'Pensil mekanik kayu', 'Body kayu, mekanisme 0,5 mm.', 68000, 14),
  ((SELECT id FROM categories WHERE slug = 'pena-pensil'), 'Set gambar komplit', '12 pensil graphite + rautan kayu + penghapus.', 110000, 8),
  ((SELECT id FROM categories WHERE slug = 'kertas-kartu'), 'Kartu ucapan letterpress set 4', 'Cetak letterpress motif cap gudang, 300 gsm.', 40000, 24),
  ((SELECT id FROM categories WHERE slug = 'kertas-kartu'), 'Kertas kraft A4 isi 25', 'Kertas kraft 120 gsm, 25 lembar.', 25000, 40),
  ((SELECT id FROM categories WHERE slug = 'kertas-kartu'), 'Washi tape motif cap', 'Pita kertas 15 mm x 10 m.', 28000, 35),
  ((SELECT id FROM categories WHERE slug = 'kertas-kartu'), 'Kertas memo blok', 'Blok 400 lembar, perekat atas.', 32000, 22),
  ((SELECT id FROM categories WHERE slug = 'aksesoris-meja'), 'Pembatas buku kayu', 'Kayu tipis dengan ukiran cap.', 20000, 50),
  ((SELECT id FROM categories WHERE slug = 'aksesoris-meja'), 'Penggaris kayu 30 cm', 'Kayu jati, skala cetak.', 30000, 28),
  ((SELECT id FROM categories WHERE slug = 'aksesoris-meja'), 'Rautan pensil kayu', 'Body kayu, pisau baja.', 25000, 18),
  ((SELECT id FROM categories WHERE slug = 'aksesoris-meja'), 'Kotak pensil kayu', 'Tutup geser, muat 15 alat tulis.', 85000, 6),
  ((SELECT id FROM categories WHERE slug = 'buku-tulis-notebook'), 'Buku Kas edisi terbatas', 'Nomor seri, cap edisi terbatas. Stok habis.', 120000, 0);

-- Gambar primary (wajib, 1 per produk) — ganti <URL> dengan URL gambar asli
INSERT INTO product_images (product_id, url, is_primary, display_order) VALUES
  ((SELECT id FROM products WHERE name = 'Buku Kas "Ledger & Tag" A5'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Notebook jahit tangan A5'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Notebook saku A6'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Sketchbook A4'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Jurnal garis tahunan'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Pensil kayu set 6'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Pena kayu bulat'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Pensil mekanik kayu'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Set gambar komplit'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Kartu ucapan letterpress set 4'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Kertas kraft A4 isi 25'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Washi tape motif cap'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Kertas memo blok'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Pembatas buku kayu'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Penggaris kayu 30 cm'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Rautan pensil kayu'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Kotak pensil kayu'), '<URL>', true, 0),
  ((SELECT id FROM products WHERE name = 'Buku Kas edisi terbatas'), '<URL>', true, 0);

-- Galeri tambahan (opsional, is_primary=false, display_order 1..2)
-- Contoh untuk produk hero #1:
-- INSERT INTO product_images (product_id, url, is_primary, display_order) VALUES
--   ((SELECT id FROM products WHERE name = 'Buku Kas "Ledger & Tag" A5'), '<URL>', false, 1),
--   ((SELECT id FROM products WHERE name = 'Buku Kas "Ledger & Tag" A5'), '<URL>', false, 2);
```
