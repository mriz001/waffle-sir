# Waffle Sir

Sistem order waffle mobile-first menggunakan Cloudflare Workers, D1, Durable Objects (WebSocket) dan GitHub.

## Fungsi versi 1
- Pelanggan order tanpa akaun.
- Enam menu permulaan.
- Harga dan jumlah dikira di server.
- Nombor order automatik.
- Admin/staf login.
- Alert pesanan baharu realtime ketika dashboard dibuka.
- Status: BAHARU, DITERIMA, SEDANG DISEDIAKAN, SIAP, SELESAI, DIBATALKAN.
- Pautan WhatsApp Click-to-Chat apabila status SIAP.
- Admin boleh tambah menu dan akaun staf/admin.
- Tandakan pembayaran manual.

## Keperluan
- Akaun GitHub
- Akaun Cloudflare
- Node.js LTS dan npm (untuk ujian/deploy melalui komputer)
- Wrangler CLI (dipasang melalui `npm install`)

## 1. Cipta database D1
Di terminal dalam folder projek:

```bash
npm install
npx wrangler d1 create waffle-sir-db
```

Salin `database_id` yang diberi dan gantikan `REPLACE_WITH_D1_DATABASE_ID` dalam `wrangler.jsonc`.

Jalankan migration:

```bash
npx wrangler d1 migrations apply waffle-sir-db --remote
```

## 2. Tetapkan setup key
Pilih satu rahsia panjang dan simpan dengan selamat:

```bash
npx wrangler secret put ADMIN_SETUP_KEY
```

Masukkan nilai rahsia apabila diminta. Jangan masukkan rahsia ini ke GitHub.

## 3. Deploy
```bash
npx wrangler deploy
```

Cloudflare akan memberikan URL `*.workers.dev`.

## 4. Cipta admin pertama
Gunakan terminal (ganti URL dan nilai). Jalankan sekali sahaja selepas database migration dan deploy:

```bash
curl -X POST "https://NAMA-PROJEK.NAMA-AKAUN.workers.dev/api/setup-admin" \
  -H "content-type: application/json" \
  -H "x-setup-key: NILAI_ADMIN_SETUP_KEY" \
  -d '{"username":"admin","display_name":"Waffle Sir Admin","password":"GANTIKAN_DENGAN_KATA_LALUAN_PANJANG"}'
```

Kata laluan minimum 12 aksara. Simpan kata laluan dengan selamat. Endpoint setup akan menolak permintaan selepas akaun pertama wujud.

## 5. Login dan tambah staf
Buka `https://URL-ANDA/login.html`, login sebagai admin, kemudian tambah akaun staf dari bahagian bawah dashboard.

## 6. GitHub
1. Cipta repository `waffle-sir`.
2. Upload semua fail projek kecuali `node_modules`.
3. Jangan commit token, kata laluan, atau fail `.dev.vars`.
4. Untuk deploy automatik, sambungkan repository di Cloudflare Workers & Pages > Builds > Connect to Git.

## Ujian tempatan
```bash
npm run dev
```

Untuk ujian penuh tempatan, buat database tempatan dan jalankan migration:
```bash
npx wrangler d1 migrations apply waffle-sir-db --local
```

## Penting sebelum operasi sebenar
- Tukar harga menu di dashboard admin.
- Uji pesanan, status, WhatsApp dan login dengan akaun ujian.
- Pastikan staf benarkan bunyi/notifikasi browser dan kekalkan dashboard terbuka untuk alert realtime.
- WhatsApp menggunakan Click-to-Chat: staf perlu tekan Send dalam WhatsApp. Tiada WhatsApp API berbayar digunakan.
- Nombor telefon pelanggan dipaparkan kepada pengguna yang login. Lindungi akaun staf dan jangan kongsi login.
- Kuota percuma Cloudflare tertakluk pada had semasa akaun. Pantau penggunaan di dashboard Cloudflare.
- Ini starter release untuk ujian. Sebelum operasi awam, tambah rate limiting/anti-spam pada endpoint order, polisi privasi, backup, dan semakan keselamatan bebas.
