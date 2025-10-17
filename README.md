# Project Hosting KDJK YAFFA [Kelompok 5/Paralel 1] 
<p align="center">
  <img src="https://github.com/user-attachments/assets/f3b129fb-c24b-4c29-ba28-b6c0a04ba1dc" 
       width="200" height="200" alt="YAFFA Logo">
</p>

<h2 align="center">YAFFA - Your Personal Finance App</h2>

<p align="center"><em>A Free Personal Finance & Budgeting Tool for Everyone.</em></p>

<div align="center">

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Konfigurasi Lanjutan](#konfigurasi-lanjutan) | [Pembahasan](#pembahasan) | [Referensi](#referensi)
:---:|:---:|:---:|:---:|:---:|:---:

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

# Konfigurasi Lanjutan

[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

## 1. **Alpha Vantage (API Harga Investasi)**

* **Status:** *Sudah Diterapkan*
* **Jenis Layanan:** Gratis (dengan batasan pemakaian harian)

### Fungsi

YAFFA menggunakan **Alpha Vantage API** untuk mengambil data harga **historis** dan **real-time** dari berbagai aset seperti **saham**, **forex**, dan **mata uang kripto**.
Dengan fitur ini, pengguna tidak perlu lagi memperbarui harga investasi secara manual.

### Konfigurasi

Tambahkan kunci API ke file `.env`:

```env
ALPHA_VANTAGE_KEY=your_api_key_here
```

---

## 2. **Web Scraping & Penjadwalan Otomatis (Cron Job)**

* **Status:** *Sudah Diterapkan*
* **Jenis Layanan:** Fitur bawaan (konfigurasi sistem)

### Fungsi

Fitur ini memungkinkan YAFFA mengambil harga investasi langsung dari **halaman web sumber**, misalnya situs reksa dana lokal yang tidak terdaftar di API publik.
Agar proses ini berjalan otomatis setiap hari, digunakan **Cron Job** yang memanggil perintah Laravel scheduler (`artisan schedule:run`) di latar belakang.

### Konfigurasi Cron Job

1. Buka terminal dan masuk ke server via SSH.
2. Jalankan perintah berikut untuk mengedit `crontab`:

   ```bash
   crontab -e
   ```
3. Tambahkan baris berikut:

   ```crontab
   * * * * * cd /home/azureuser/yaffa && /usr/bin/php artisan schedule:run >> /dev/null 2>&1
   ```
4. Simpan dan keluar (`Ctrl + O`, `Enter`, `Ctrl + X`).
5. Periksa konfigurasi aktif:

   ```bash
   crontab -l
   ```

### Catatan

* Cron job berjalan **setiap menit** untuk memeriksa jadwal scraping.
* Jika struktur situs berubah, **CSS Selector** sumber harga perlu diperbarui agar data tetap valid.

---

## 3. **Konfigurasi Pengiriman Email (SMTP)**

* **Status:** *Belum Diterapkan*
* **Jenis Layanan:** *Freemium* (mayoritas berbayar)

### Fungsi

Menambahkan kemampuan YAFFA untuk **mengirim email otomatis**, misalnya untuk:

* Verifikasi pendaftaran pengguna
* Reset kata sandi

### Alasan Belum Diterapkan

Diperlukan layanan SMTP pihak ketiga seperti **SendGrid**, **Mailgun**, atau **Postmark**, yang umumnya memiliki batasan kuota untuk paket gratis.

### Konfigurasi (Rencana)

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.example.com
MAIL_PORT=587
MAIL_USERNAME=your_username
MAIL_PASSWORD=your_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=no-reply@yourdomain.com
MAIL_FROM_NAME="YAFFA System"
```

---

## 4. **Pemrosesan Struk Otomatis (OpenAI)**

* **Status:** *Belum Diterapkan*
* **Jenis Layanan:** Berbayar dan kompleks untuk diatur

### Fungsi

Fitur ini dirancang agar YAFFA dapat memproses **struk pembelian otomatis** yang dikirim melalui email.
Sistem menggunakan **OpenAI API** untuk membaca isi struk dan menghasilkan **draf transaksi otomatis**.

### Alasan Belum Diterapkan

* Membutuhkan **domain pribadi** dengan pengaturan **MX Record** (tidak dapat menggunakan DuckDNS)
* Memerlukan **API OpenAI berbayar**
* Bergantung pada layanan email masuk (seperti SendGrid)

### Konfigurasi (Rencana)

```env
OPENAI_API_KEY=your_api_key_here
SENDGRID_API_KEY=your_sendgrid_key
```

# Cara Pemakaian
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

[gambar YAFFA_information architecture]
YAFFA membantu pengguna mengelola keuangan harian sekaligus pertumbuhan jangka panjang. Fitur-fiturnya terbagi menjadi dua fokus utama:
### A. Pengelolaan tabungan dan pengeluaran harian

1. Currencies (Mata Uang)
Tambahkan dan kelola mata uang yang digunakan untuk transaksi. Tentukan **base currency** agar semua saldo dan laporan ditampilkan dengan nilai yang konsisten.
[gambar currencies]

2. Account Groups dan Account
Buat **Account Groups** untuk mengelompokkan akun seperti rekening Bank, dompet, atau investasi. Lalu tambahkan akun yang digunakan.
[gambar Account Groups dan Account, atau kalo bisa gambarnya yang 1 1 tapi jadi sebaris gak sekolom]

3. Payees & Categories
Gunakan **Categories** untuk mengelompokkan pengeluaran dan pemasukan (misal: Makanan, Transportasi).  
Tambah **Payees** untuk mencatat pihak transaksi seperti toko atau aplikasi.
[gambar categories dan payees]

4. New Transaction
Tambah transaksi baru, pilih jenis transaksi (**Withdraw**, **Deposit**, atau **Transfer**), menentukan akun dan payee, lalu menambahkan rincian item transaksi. 
[gambar new transaction]

### B. Pemantauan investasi dan kekayaan.

1. Investment Groups dan Investments
Kelompokkan jenis investasi (misalnya Saham, Reksa Dana, Emas).
[gambar Investment Groups dan Investments]

2. New Investment dan Investment Details
Menambahkan instrumen investasi yang kredibel untuk memantau nilainya otomatis.
[gambar New Investment dan Investment Details]

3. Dashboard
Dashboard menampilkan ringkasan keuangan keseluruhan dari seluruh akun dan aktivitas keuangan.
[gamabr dashboard]



# Pembahasan
[`^ kembali ke atas ^`](#project-hosting-kdjk-yaffa-kelompok-5paralel-1)

## Kelebihan
- **Antarmuka Web Modern**  
  YAFFA memiliki tampilan yang sederhana, bersih, dan mudah digunakan. Karena berbasis web, pengguna dapat mengaksesnya dari berbagai perangkat melalui browser tanpa perlu instalasi tambahan.
  
- **Multi-Currency Support**  
  Mendukung berbagai mata uang dengan konversi otomatis berdasarkan kurs terkini. Fitur ini sangat berguna bagi pengguna yang memiliki rekening dalam mata uang berbeda (USD, IDR, EUR, dll).

- **Manajemen Akun yang Fleksibel**  
  Pengguna dapat menambahkan berbagai jenis akun seperti bank, e-wallet, kas, maupun akun investasi. Setiap akun bisa diberi saldo awal dan dikelompokkan sesuai kebutuhan.

- **Pelacakan Investasi dan Aset**  
  Tidak hanya untuk pengeluaran harian, YAFFA juga mampu melacak investasi seperti saham, obligasi, atau emas, memberikan gambaran lengkap tentang kekayaan bersih.

- **Scheduled Transactions**  
  Mendukung pencatatan transaksi rutin seperti gaji atau cicilan, sehingga tidak perlu melakukan input manual secara berulang.

- **Open Source & Self-Hosted**  
  YAFFA bersifat open source dan bisa diinstal di server pribadi, memberikan kontrol penuh terhadap data dan meningkatkan keamanan serta privasi.

---

## Kekurangan
- **Komunitas & Dokumentasi Terbatas**  
  Karena masih relatif baru dibanding aplikasi lain seperti Firefly III, panduan dan komunitas pengguna YAFFA belum terlalu besar.

- **Integrasi Eksternal Minim**  
  Belum tersedia koneksi langsung dengan API bank atau aplikasi keuangan lain, sehingga transaksi harus diimpor atau dicatat secara manual.

- **Laporan Visual Sederhana**  
  Grafik dan analisis keuangan masih tergolong dasar, belum sekomprehensif aplikasi lain yang berfokus pada budgeting dan analitik visual.

- **Kurang Cocok untuk Bisnis/UKM**  
  YAFFA lebih ditujukan untuk personal finance dan tidak mendukung sistem akuntansi double-entry seperti GnuCash.

- **Membutuhkan Setup Server**  
  Karena bersifat self-hosted, pengguna perlu memahami dasar-dasar hosting atau Docker untuk menginstalnya.

---
## Perbandingan dengan Aplikasi Sejenis

| Aspek | **YAFFA** | **Firefly III** | **MMEX (Money Manager Ex)** | **GnuCash** |
|:--|:--|:--|:--|:--|
| **Platform** | Web (self-hosted) | Web (self-hosted) | Desktop | Desktop |
| **Kemudahan Instalasi** | Mudah & ringan | Kompleks untuk pemula | Mudah | Cukup kompleks |
| **Antarmuka (UI/UX)** | Modern & responsif | Menengah, banyak fitur | Klasik | Kurang ramah pengguna |
| **Akses Offline** | Tidak | Tidak | Ya | Ya |
| **Multi-Currency** | Ya | Ya | Terbatas | Ya |
| **Investasi & Aset** | Ada | Ada | Terbatas | Ada |
| **Integrasi API Bank** | Tidak | Ya (melalui plugin) | Tidak | Tidak |
| **Automasi Transaksi** | Ada | Ada | Terbatas | Terbatas |
| **Laporan Visual** | Sederhana | Lengkap & interaktif | Dasar | Lengkap tapi kaku |
| **Fokus Utama** | Keuangan pribadi | Keuangan pribadi & semi bisnis | Personal finance offline | Bisnis kecil (double-entry) |
| **Komunitas & Dukungan** | Kecil | Besar, aktif | Besar | Sangat besar |
| **Kebutuhan Server** | Ringan | Lebih berat | Tidak perlu | Tidak perlu |
| **Lisensi** | Open Source | Open Source | Open Source | Open Source |


# Referensi
[`^ kembali ke atas ^`](#top)

https://yaffa.cc/documentation/
