# Menjalankan Portfolio dengan Docker

Konfigurasi ini menjalankan website statis menggunakan Caddy pada domain `dedesyahrul.dev`.

## Persiapan server

1. Arahkan DNS domain ke IP publik server:
   - `A` record: `dedesyahrul.dev` -> `IP_SERVER`
   - `CNAME` record: `www.dedesyahrul.dev` -> `dedesyahrul.dev`
   - `AAAA` record untuk domain utama, jika server menggunakan IPv6 -> `IPV6_SERVER`
2. Pastikan port TCP `80` dan `443` terbuka pada firewall dan tidak dipakai service lain.
3. Install Docker Engine dan Docker Compose Plugin.

## Menjalankan

```bash
docker compose up -d --build
```

Caddy akan meminta dan memperbarui sertifikat HTTPS Let's Encrypt secara otomatis. Akses website melalui:

```text
https://dedesyahrul.dev
```

## Operasional

```bash
# Melihat status
docker compose ps

# Melihat log web server
docker compose logs -f portfolio

# Menghentikan container
docker compose down

# Deploy ulang setelah perubahan file website
docker compose up -d --build
```

Volume `caddy_data` menyimpan sertifikat HTTPS. Jangan menghapus volume tersebut kecuali memang ingin meminta sertifikat baru.

## Catatan form kontak

Halaman utama adalah static site. File `assets/php/contact-form.php` tidak dijalankan oleh image Caddy, dan form pada `index.html` saat ini belum memiliki `action` ke endpoint tersebut. Jika form kontak ingin dibuat aktif, diperlukan service backend/mail provider terpisah.
