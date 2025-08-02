# 📦 StreamDev - Panduan Instalasi Lengkap di Ubuntu

Proyek ini adalah aplikasi streaming berbasis Node.js. Gunakan panduan ini untuk instalasi lengkap dari awal di server Ubuntu (20.04 atau 22.04).

---

## ✅ Prasyarat

Sebelum mulai, pastikan kamu punya:

* VPS Ubuntu dengan akses root/SSH
* Port 3000 atau domain aktif
* Git & Node.js

---

## 🚀 Langkah Instalasi

### 1. Update Sistem

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Dependensi Umum

```bash
sudo apt install -y git curl wget build-essential ufw nginx
```

### 3. Clone Repository

```bash
cd /var/www/
sudo git clone https://github.com/afkarxyz/streamdev.git
cd streamdev
```

### 4. Install Node.js (LTS)

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

Cek versi:

```bash
node -v
npm -v
```

### 5. Install Dependency Proyek

```bash
npm install
```

### 6. Konfigurasi `.env`

Buat file `.env` jika belum ada:

```bash
nano .env
```

Contoh isi:

```
PORT=3000
SESSION_SECRET=streamdev123
```

### 7. Install & Konfigurasi FFmpeg (untuk video)

```bash
sudo apt install ffmpeg -y
```

Cek apakah `ffprobe` tersedia:

```bash
which ffprobe
```

### 8. Jalankan Aplikasi

Untuk development:

```bash
npm run dev
```

Untuk production pakai PM2:

```bash
sudo npm install -g pm2
pm2 start npm --name streamdev -- run start
```

Otomatis start saat reboot:

```bash
pm2 startup
pm2 save
```

### 9. Buka Port di Firewall

```bash
sudo ufw allow 3000
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

---

## 🌐 (Opsional) Konfigurasi Nginx (Reverse Proxy)

### Buat file konfigurasi:

```bash
sudo nano /etc/nginx/sites-available/streamdev
```

Isi:

```
server {
    listen 80;
    server_name 139.180.211.8;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Aktifkan:

```bash
sudo ln -s /etc/nginx/sites-available/streamdev /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### (Opsional) HTTPS dengan Certbot

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com
```

---

## 🕒 Atur Timezone ke WITA

```bash
sudo timedatectl set-timezone Asia/Makassar
```

---

## ✅ Cek Aplikasi

Akses aplikasi:

```
http://IP-VPS:3000
atau jika pakai domain:
http://your-domain.com
```

Cek log jika ada error:

```bash
pm2 logs streamdev
```

---

## ⚠️ Masalah Umum & Solusi

* ❌ `Cannot find ffprobe` → Install `ffmpeg`
* ❌ `secret option required for sessions` → Tambahkan `SESSION_SECRET` di `.env`
* ❌ `ERR_CONNECTION_TIMED_OUT` → Buka port 3000 di UFW & bind ke `0.0.0.0`

---

## 💪 Selesai!

Kalau masih mentok, kirim error log dan kita gas bareng sampai sukses deploy! 🚀
