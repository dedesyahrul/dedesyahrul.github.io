# Menjalankan Portfolio dengan Docker

Konfigurasi ini menjalankan website statis menggunakan Caddy langsung pada port standar HTTP/HTTPS untuk domain `dedesyahrul.dev`.

## Persiapan server

1. Arahkan DNS domain ke IP publik server:
   - `A` record: `dedesyahrul.dev` -> `IP_SERVER`
   - `CNAME` record: `www.dedesyahrul.dev` -> `dedesyahrul.dev`
   - Tambahkan `AAAA` record hanya jika IPv6 server benar-benar aktif dan port `80`/`443` dapat diakses melalui IPv6.
2. Pastikan port TCP `80` dan `443` terbuka pada firewall dan tidak dipakai service lain.
3. Install Docker Engine dan Docker Compose Plugin.

## SSL Cloudflare

Konfigurasi memakai Cloudflare Origin Certificate berikut:

```text
ssl/origin-cert.pem
ssl/origin-key.pem
```

Keduanya di-mount read-only ke container dan digunakan langsung oleh Caddy. Jangan commit atau membagikan `origin-key.pem`.

Di Cloudflare, buka **SSL/TLS > Overview** dan pilih **Full (strict)**. Pastikan record DNS untuk domain utama dan `www` berstatus **Proxied** (awan oranye). Origin Certificate Cloudflare hanya dipercaya oleh Cloudflare, bukan browser saat DNS diset **DNS only**.

## Menjalankan

```bash
docker compose up -d --build
```

Caddy menggunakan Origin Certificate yang disediakan pada folder `ssl`. Akses website melalui:

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

Pastikan container berhasil `Up` dan tidak ada service lain yang menggunakan port `80` atau `443`:

```bash
docker compose ps
docker compose logs --tail=100 portfolio
curl -I http://127.0.0.1
curl -Ik https://dedesyahrul.dev
```

Volume `caddy_data` dan `caddy_config` dipertahankan untuk data Caddy. Sertifikat TLS dikelola dari file pada folder `ssl`.

## Catatan form kontak

Halaman utama adalah static site. File `assets/php/contact-form.php` tidak dijalankan oleh image Caddy, dan form pada `index.html` saat ini belum memiliki `action` ke endpoint tersebut. Jika form kontak ingin dibuat aktif, diperlukan service backend/mail provider terpisah.
