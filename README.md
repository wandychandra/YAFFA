# YAFFA

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Otomatisasi](#otomatisasi) | [Cara Pemakaian](#cara-pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi)
:---:|:---:|:---:|:---:|:---:|:---:|:---:



# Sekilas Tentang
[`^ kembali ke atas ^`](#)

Deskripsi singkat tentang aplikasi tsb.



# Instalasi
[`^ kembali ke atas ^`](#)

## Tutorial Instalasi & Setup Azure Virtual Machine

### Daftar Isi
- [Prasyarat](#prasyarat)
- [Langkah Instalasi](#langkah-instalasi)
- [Konfigurasi Networking](#konfigurasi-networking-vm)
- [Menyimpan SSH Key](#menyimpan-file-pem)
- [Akses Terminal VM](#akses-terminal-vm)
- [Setup DuckDNS (Opsional)](#setup-duckdns-opsional)

---

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
| **Region** | `Southeast Asia` | Sesuai dengan resource group |
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
| **Select inbound ports** | ☑️ `SSH (22)`, ☑️ `HTTP (80)`, ☑️ `HTTPS (443)` | Port untuk SSH dan web server |
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

DuckDNS adalah layanan DNS dinamis gratis yang memungkinkan Anda mengakses server dengan nama domain yang mudah diingat, misalnya `yaffa.duckdns.org` alih-alih menggunakan alamat IP.

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



# Konfigurasi
[`^ kembali ke atas ^`](#)

Setting server tambahan yang diperlukan untuk meningkatkan fungsi dan kinerja aplikasi, misalnya:
- batas upload file
- batas memori
- dll

Plugin untuk fungsi tambahan
- login dengan Google/Facebook
- editor Markdown
- dll



# Maintenance
[`^ kembali ke atas ^`](#)

Setting tambahan untuk maintenance secara periodik, misalnya:
- buat backup database tiap pekan
- hapus direktori sampah tiap hari
- dll



# Otomatisasi
[`^ kembali ke atas ^`](#)

Skrip shell untuk otomatisasi instalasi, konfigurasi, dan maintenance.



# Cara Pemakaian
[`^ kembali ke atas ^`](#)

- Tampilan aplikasi web
- Fungsi-fungsi utama
- Isi dengan data real/dummy (jangan kosongan) dan sertakan beberapa screenshot



# Pembahasan
[`^ kembali ke atas ^`](#)

- Pendapat anda tentang aplikasi web ini
  - kelebihan
  - kekurangan
- Bandingkan dengan aplikasi web lain yang sejenis



# Referensi
[`^ kembali ke atas ^`](#)

Cantumkan tiap sumber informasi yang anda pakai.
