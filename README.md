# Project Hosting KDJK YAFFA [Kelompok 5/Paralel 1] 
<p align="center">
  <img src="https://github.com/user-attachments/assets/f3b129fb-c24b-4c29-ba28-b6c0a04ba1dc" 
       width="200" height="200" alt="YAFFA Logo">
</p>

<h2 align="center">YAFFA - Your Personal Finance App</h2>

<p align="center"><em>A Free Personal Finance & Budgeting Tool for Everyone.</em></p>

<div align="center">

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Otomatisasi](#otomatisasi) | [Cara Pemakaian](#cara-pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi)
:---:|:---:|:---:|:---:|:---:|:---:|:---:

</div>



# Sekilas Tentang
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)
<div align="justify">
<p>
YAFFA adalah aplikasi web manajemen keuangan pribadi yang bersifat open-source (kode sumber terbuka) dan dapat diinstal secara mandiri (self-hosted). Aplikasi ini dirancang untuk memberikan kendali penuh kepada pengguna dalam mengelola seluruh aspek keuangan mereka, mulai dari pelacakan pengeluaran harian, penetapan anggaran bulanan, hingga pemantauan kinerja investasi. YAFFA sangat berfokus pada dukungan perencanaan keuangan jangka panjang, memungkinkan pengguna untuk melakukan peramalan dan analisis berdasarkan data historis mereka.
</p>

<p>
Keistimewaan YAFFA terletak pada model self-hosted dan statusnya yang gratis (di bawah lisensi MIT). Ini berarti pengguna menjalankan aplikasi di server pribadi mereka sendiri. Keuntungan utamanya adalah privasi data maksimal, karena informasi keuangan yang sensitif tidak disimpan di layanan cloud pihak ketiga, melainkan tetap berada di bawah kendali pengguna sepenuhnya. YAFFA hadir sebagai alternatif kuat bagi mereka yang mencari alat budgeting canggih tanpa mengorbankan keamanan data atau harus membayar biaya berlangganan.
</p>
</div>


# Instalasi
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

## Tutorial Instalasi & Setup Azure Virtual Machine
### Prasyarat

Sebelum memulai instalasi, pastikan sudah menyiapkan:
- Akun Azure aktif → [https://portal.azure.com](https://portal.azure.com)
- Koneksi internet yang stabil
- Terminal/PowerShell untuk akses SSH
- Domain opsional (DuckDNS atau custom domain)

---

### Langkah Instalasi

#### 1. Login ke Azure Portal

1. Buka browser dan navigasi ke [https://portal.azure.com](https://portal.azure.com)
2. Login menggunakan akun Microsoft Anda
3. Tunggu hingga dashboard Azure Portal terbuka

#### 2. Buat Resource Group

1. Di Azure Portal, klik **Resource Groups** → **Create**
2. Isi parameter berikut:
   - **Subscription**: pilih subscription aktif Anda
   - **Resource group name**: `yaffa-rg` (atau nama sesuai keinginan)
   - **Region**: `Indonesia Central` (pilih yang terdekat)
3. Klik **Review + Create** → **Create**
4. Tunggu hingga deployment selesai

#### 3. Buat Virtual Machine (VM)

1. Klik **Virtual Machines** → **Create** → **Azure Virtual Machine**
2. Pada tab **Basics**, isi parameter:

| Parameter | Value | Keterangan |
|-----------|-------|------------|
| **Subscription** | Pilih subscription aktif | - |
| **Resource group** | `yaffa-rg` | Yang sudah dibuat sebelumnya |
| **Virtual machine name** | `yaffa-vm` | Nama VM Anda |
| **Region** | `Indonesia Central` | Sesuai dengan resource group |
| **Availability options** | `No infrastructure redundancy required` | Untuk testing/development |
| **Security type** | `Standard` | Default option |
| **Image** | `Ubuntu Server 22.04 LTS - x64 Gen2` | OS Linux yang stabil |
| **VM architecture** | `x64` | Arsitektur processor |
| **Size** | `Standard_B1s` atau `Standard_B2s` | B1s: hemat, B2s: lebih stabil |

3. Pada bagian **Administrator account**:
   - **Authentication type**: pilih **SSH public key**
   - **Username**: `azureuser` (default)
   - **SSH public key source**: `Generate new key pair`
   - **Key pair name**: `yaffa-key`

---

### Konfigurasi Networking VM

Pada tab **Networking**, konfigurasikan sesuai tabel berikut:

| Parameter | Setting | Keterangan |
|-----------|---------|------------|
| **Virtual network** | `Create new` → `yaffa-vnet` | Address space: `10.0.0.0/16` |
| **Subnet** | `default (10.0.0.0/24)` | Subnet untuk VM |
| **Public IP** | `Create new` → `yaffa-ip` | IP publik untuk akses eksternal |
| **NIC network security group** | `Basic` | Kontrol firewall dasar |
| **Public inbound ports** | `Allow selected ports` | Hanya buka port tertentu |
| **Select inbound ports** | `SSH (22)`, `HTTP (80)`, `HTTPS (443)` | Port untuk SSH dan web server |
| **Delete public IP and NIC when VM is deleted** | Opsional | Biasanya tidak dicentang |

#### 4. Review dan Deploy VM

1. Klik **Review + Create**
2. Periksa kembali semua konfigurasi
3. Klik **Create**
4. **PENTING**: Saat popup muncul, klik **Download private key and create resource**
5. Simpan file `.pem` yang di-download (akan digunakan untuk SSH)
6. Tunggu deployment selesai (biasanya 3-5 menit)

---

### Menyimpan File .pem

#### Lokasi Penyimpanan SSH Key

Setelah download file `.pem`, simpan di lokasi yang aman:

##### Windows
```
C:\Users\<username>\.ssh\yaffa-key.pem
```

##### Linux / macOS
```
~/.ssh/yaffa-key.pem
```

### Akses Terminal VM

#### Mendapatkan Public IP Address

1. Buka **Azure Portal** → **Virtual Machines** → **yaffa-vm**
2. Di bagian **Overview**, copy **Public IP address**
3. Contoh IP: `20.120.55.10`

#### Koneksi SSH

##### Windows (PowerShell)

```powershell
ssh -i C:\Users\<username>\.ssh\yaffa-key.pem azureuser@<Public-IP>
```

**Contoh:**
```powershell
ssh -i C:\Users\Fauzan\.ssh\yaffa-key.pem azureuser@20.120.55.10
```

##### Linux / macOS / WSL

```bash
ssh -i ~/.ssh/yaffa-key.pem azureuser@<Public-IP>
```

### Setup DuckDNS (Opsional)

DuckDNS adalah layanan DNS dinamis gratis yang memungkinkan Anda mengakses server dengan nama domain yang mudah diingat, misalnya `yaffadomain.duckdns.org` alih-alih menggunakan alamat IP.

#### 1. Daftar di DuckDNS

1. Buka [https://www.duckdns.org](https://www.duckdns.org)
2. Login menggunakan salah satu opsi:
   - Google Account
   - GitHub Account  
   - Twitter Account
   - Reddit Account

#### 2. Buat Subdomain

1. Setelah login, Anda akan melihat dashboard DuckDNS
2. Di bagian **subdomains**, masukkan nama subdomain yang diinginkan
   - Contoh: `yaffadomain` (akan menjadi `yaffadomain.duckdns.org`)
3. Klik **add domain**

#### 3. Update IP Address Manual

Di dashboard DuckDNS:
1. Paste **Public IP** Azure VM Anda di kolom **current ip**
2. Klik **update ip** 
3. Status akan berubah menjadi **OK** jika berhasil

##### Test SSH dengan Domain
Sekarang Anda bisa SSH menggunakan domain:

```bash
# Alih-alih menggunakan IP
ssh -i C:\Users\<username>\.ssh\yaffa-key.pem azureuser@20.120.55.10

# Sekarang bisa menggunakan domain
ssh -i C:\Users\<username>\.ssh\yaffa-key.pem azureuser@yaffadomain.duckdns.org
```

---

## Install YAFFA + HTTPS di Azure VM (Ubuntu + Docker + Caddy + DuckDNS)

---

### 1. Update & Install Tools Dasar

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl unzip docker.io docker-compose
```

---

### 2. Buat Folder Proyek

```bash
mkdir ~/yaffa && cd ~/yaffa
```

---

### 3. Download File Konfigurasi

```bash
curl -o docker-compose.yml https://raw.githubusercontent.com/kantorge/yaffa/refs/heads/main/docker/docker-compose.yml
curl -o .env https://raw.githubusercontent.com/kantorge/yaffa/refs/heads/main/.env.example
curl -o Caddyfile https://raw.githubusercontent.com/kantorge/yaffa/refs/heads/main/docker/Caddyfile
```

---

### 4. Edit File `.env`

```bash
nano .env
```

Ubah jadi pakai domain kamu (`yaffadomain.duckdns.org`):

```
APP_URL=https://yaffadomain.duckdns.org
SANCTUM_STATEFUL_DOMAINS=yaffadomain.duckdns.org
SESSION_DOMAIN=yaffadomain.duckdns.org
TRUSTED_PROXIES=*
```

Simpan (`CTRL+O`, lalu `CTRL+X`).

---

### 5. Generate APP_KEY

```bash
grep -q '^APP_KEY=' .env && \
  sed -i 's|^APP_KEY=.*|APP_KEY=base64:'"$(openssl rand -base64 32)"'|' .env || \
  echo "APP_KEY=base64:$(openssl rand -base64 32)" >> .env
```

---

### 6. Edit `docker-compose.yml`

```bash
nano docker-compose.yml
```

Ubah service **app** → mapping ke port 8080 (biar diproxy lewat Caddy):

```yaml
app:
  image: kantorge/yaffa:latest
  container_name: yaffa_app
  ports:
    - "8080:80"
  env_file:
    - .env
  environment:
    API_URL: "https://yaffadomain.duckdns.org"
    RUNS_SCHEDULER: FALSE
  volumes:
    - yaffa_storage:/var/www/html/storage
  depends_on:
    db:
      condition: service_healthy
    redis:
      condition: service_started
  restart: unless-stopped
  networks:
    - yaffa-network
```

Tambahkan **caddy** service:

```yaml
caddy:
  image: caddy:latest
  container_name: yaffa_caddy
  restart: unless-stopped
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - ./Caddyfile:/etc/caddy/Caddyfile
    - caddy_data:/data
    - caddy_config:/config
  networks:
    - yaffa-network
  depends_on:
    - app
```

---

### 7. Edit `Caddyfile`

```bash
nano Caddyfile
```

Isi:

```caddy
yaffadomain.duckdns.org {
    reverse_proxy app:80

    header {
        Content-Security-Policy "upgrade-insecure-requests"
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
    }
}
```

---

#### 8. Jalankan Docker

```bash
sudo docker compose up -d
```

Cek:

```bash
sudo docker ps
```

---

### 10. Akses di Browser

```
https://yaffadomain.duckdns.org
```




# Konfigurasi
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

## Konfigurasi YAFFA

### Environment Variables (.env)

File `.env` berisi konfigurasi utama YAFFA. Berikut adalah setting penting:

| Variable | Deskripsi | Contoh Value | Wajib |
|----------|-----------|--------------|-------|
| `APP_URL` | URL aplikasi | `https://yaffadomain.duckdns.org` | Ya |
| `SANCTUM_STATEFUL_DOMAINS` | Domain untuk authentication | `yaffadomain.duckdns.org` | Ya |
| `SESSION_DOMAIN` | Domain untuk session cookies | `yaffadomain.duckdns.org` | Ya |
| `TRUSTED_PROXIES` | Proxy yang terpercaya | `*` (untuk Caddy reverse proxy) | Ya |
| `APP_KEY` | Encryption key | Auto-generated dengan `base64:` | Ya |
| `REGISTERED_USER_LIMIT` | Batas maksimal user registrasi | `100` (kosong = unlimited) | Tidak |

### Pengaturan User Registration

**REGISTERED_USER_LIMIT** adalah setting penting untuk mengontrol jumlah user yang bisa mendaftar:

```bash
# Edit file .env
nano .env

# Tambahkan/ubah setting ini:
REGISTERED_USER_LIMIT=50    # Maksimal 50 user
REGISTERED_USER_LIMIT=      # Unlimited (hapus nilai)
REGISTERED_USER_LIMIT=1000  # Maksimal 1000 user
```

### Menerapkan Perubahan Konfigurasi

Setelah mengubah `.env`, restart container:

```bash
sudo docker compose down
sudo docker compose up -d
```

# Maintenance
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

Setting tambahan untuk maintenance secara periodik, misalnya:
- buat backup database tiap pekan
- hapus direktori sampah tiap hari
- dll



# Otomatisasi
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

Skrip shell untuk otomatisasi instalasi, konfigurasi, dan maintenance.



# Cara Pemakaian
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

- Tampilan aplikasi web
- Fungsi-fungsi utama
- Isi dengan data real/dummy (jangan kosongan) dan sertakan beberapa screenshot



# Pembahasan
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

- Pendapat anda tentang aplikasi web ini
  - kelebihan
  - kekurangan
- Bandingkan dengan aplikasi web lain yang sejenis



# Referensi
[`^ kembali ke atas ^`](#top)

https://yaffa.cc/documentation/
