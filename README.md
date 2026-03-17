# 📦 D&L Ekspedisi — Warehouse Management System

Aplikasi web manajemen paket ekspedisi berbasis **Python Flask** dan **Flask-SQLAlchemy (ORM)**.
Dibangun sebagai Tugas Kelompok 4 mata kuliah Pemrograman Web.

---

## 🔗 Links

**Live URL** | https://your-app.onrender.com *(update setelah deploy)* |
**GitHub** | https://github.com/yourusername/dl-ekspedisi *(update setelah push)* |

---

## 🛠 Tech Stack

| Layer | Teknologi |
|-------|-----------|
| Backend | Python 3, Flask |
| ORM | Flask-SQLAlchemy (`db.Model`, `db.session`) |
| Database (deploy) | SQLite — `sqlite:///db_gudang_ekspedisi.db` |
| Database (local) | MySQL — `mysql+pymysql://...` *(opsional, lihat bagian MySQL)* |
| Frontend | HTML, CSS, Jinja2 Templates |
| Production Server | Gunicorn |
| Deploy Platform | Render (free tier) |

---

## ✨ Fitur Aplikasi

### 🔐 Autentikasi
- **Login** — cek username & password ke database, simpan ke Flask `session`
- **Register** — form lengkap dengan validasi penuh (lihat bagian Validasi)
- **Logout** — hapus session
- **Session Guard** — semua halaman protected redirect ke login jika belum masuk

### 📦 Manajemen Paket (CRUD)
- **Create** — tambah paket baru via form (resi, pengirim, penerima, berat, jenis, dll)
- **Read** — tampilkan semua paket dalam tabel di dashboard
- **Update** — edit data paket yang sudah ada
- **Delete** — hapus paket dengan konfirmasi

### 🔍 Pencarian & Filter
- Cari paket berdasarkan **resi**, **nama pengirim**, atau **nama penerima**
- Filter berdasarkan **kategori** (Elektronik, Dokumen, dll)
- Filter berdasarkan **jenis pengiriman** (Reguler, Kilat, dll)

### 💰 Kalkulasi Otomatis
- Tarif dihitung otomatis: `berat (kg) × tarif per kg`
- Estimasi pengiriman otomatis berdasarkan jenis pengiriman
- Total tarif semua paket ditampilkan di dashboard

### 👤 Profil User
- Tampilkan semua data user yang login (nama, email, alamat, hobi, dll)

---

## ✅ Aturan Validasi

### Validasi Registrasi (`validation_registration.py`)

| Field | Aturan |
|-------|--------|
| **Username** | 6–20 karakter, kombinasi huruf dan angka, tidak boleh sudah terdaftar |
| **Password** | Min. 8 karakter, harus ada huruf besar + huruf kecil + angka + karakter khusus (`/.,@#$%`) |
| **Email** | Format `user@hosting.ekstensi`, ekstensi maks 5 huruf, maks 2 dot setelah `@` |
| **Nama** | Hanya huruf (boleh spasi) |
| **Gender** | Harus `Male` atau `Female` |
| **Usia** | Angka antara 17–80 |
| **Pekerjaan** | Hanya huruf |
| **Hobi** | Minimal 3 hobi, dipisah koma, hanya huruf |
| **Kota** | Hanya huruf |
| **RT / RW** | Harus angka |
| **Zip Code** | Tepat 5 digit angka |
| **Latitude / Longitude** | Angka desimal (contoh: `-6.9175`) |
| **No. HP** | 11–13 digit angka |

### Validasi Resi Paket

| Aturan |
|--------|
| Tepat 6 karakter |
| Harus diawali `EXP` (huruf kapital) |
| 3 karakter terakhir harus angka (contoh: `EXP001`) |
| Resi tidak boleh sudah terdaftar di database |

---

## 🗃 Struktur Database

### Tabel `users`

| Kolom | Tipe | Keterangan |
|-------|------|------------|
| `id` | INTEGER | Primary Key, auto increment |
| `username` | TEXT | Unique, not null |
| `password` | TEXT | Not null |
| `email` | TEXT | Unique, not null |
| `nama` | TEXT | Nama lengkap |
| `gender` | TEXT | `Male` / `Female` |
| `usia` | INTEGER | Usia user |
| `pekerjaan` | TEXT | Pekerjaan |
| `hobi` | TEXT | Disimpan sebagai string dipisah koma |
| `kota` | TEXT | Nama kota |
| `rt` | INTEGER | Nomor RT |
| `rw` | INTEGER | Nomor RW |
| `zipcode` | INTEGER | Kode pos 5 digit |
| `lat` | REAL | Latitude koordinat |
| `longitude` | REAL | Longitude koordinat |
| `nohp` | BIGINTEGER | Nomor handphone |

### Tabel `paket`

| Kolom | Tipe | Keterangan |
|-------|------|------------|
| `id` | INTEGER | Primary Key, auto increment |
| `resi` | TEXT | Unique, format `EXPxxx` |
| `pengirim` | TEXT | Nama pengirim |
| `no_hp_pengirim` | TEXT | No HP pengirim |
| `penerima` | TEXT | Nama penerima |
| `no_hp_penerima` | TEXT | No HP penerima |
| `alamat_tujuan` | TEXT | Alamat lengkap tujuan |
| `kategori` | TEXT | Lihat daftar kategori |
| `berat` | REAL | Berat dalam kg |
| `tanggal_pengiriman` | TEXT | Format `DD-MM-YYYY` |
| `jenis_pengiriman` | TEXT | Lihat daftar jenis |
| `estimasi` | TEXT | Estimasi tiba otomatis |
| `tarif` | REAL | Tarif otomatis (berat × rate) |

---

## 💡 Konsep Flask-SQLAlchemy (ORM) yang Digunakan

Aplikasi ini menggunakan **Flask-SQLAlchemy** dengan pola ORM (Object Relational Mapping),
bukan raw SQL. Setiap tabel direpresentasikan sebagai **class Python**:

```
class  →  tabel database
object →  baris (row) dalam tabel
atribut → kolom (column) dalam tabel
```

Contoh operasi CRUD yang dipakai:

```python
# CREATE — INSERT INTO paket VALUES (...)
paket_baru = Paket(resi='EXP004', pengirim='Andi', ...)
db.session.add(paket_baru)
db.session.commit()

# READ — SELECT * FROM paket
semua = Paket.query.all()
satu  = db.session.get(Paket, id)

# UPDATE — UPDATE paket SET ... WHERE id = ?
paket = db.session.get(Paket, id)
paket.berat = 3.0
db.session.commit()

# DELETE — DELETE FROM paket WHERE id = ?
paket = db.session.get(Paket, id)
db.session.delete(paket)
db.session.commit()
```

---

## 📁 Struktur Project

```
dl-ekspedisi/
├── app.py                       # Flask routes (main entry point)
│                                # Konfigurasi db, semua @app.route()
├── database.py                  # Flask-SQLAlchemy setup
│                                # class User, class Paket (db.Model)
│                                # Semua fungsi CRUD (ORM style)
├── validation_registration.py   # Validasi input form registrasi & resi
├── requirements.txt             # Package: Flask, Flask-SQLAlchemy, gunicorn
├── Procfile                     # Perintah deploy: web: gunicorn app:app
├── README.md                    # Dokumentasi ini
└── templates/
    ├── base.html                # Layout utama (navbar, flash message)
    ├── login.html               # Halaman login
    ├── register.html            # Halaman registrasi (form lengkap)
    ├── dashboard.html           # Daftar paket + form tambah + filter
    ├── edit_paket.html          # Form edit paket
    └── profile.html             # Halaman profil user
```

---

## 🚀 Cara Menjalankan Secara Lokal

### Prasyarat
- Python 3.8 atau lebih baru
- pip (package manager Python)

### Langkah-langkah

```bash
# 1. Clone repository
git clone https://github.com/yourusername/dl-ekspedisi.git
cd dl-ekspedisi

# 2. (Opsional) Buat virtual environment
python -m venv venv
source venv/bin/activate        # Mac/Linux
venv\Scripts\activate           # Windows

# 3. Install semua package
pip install -r requirements.txt

# 4. Jalankan aplikasi
python app.py

# 5. Buka browser
# http://127.0.0.1:5000
```

> **Catatan:** Database SQLite (`db_gudang_ekspedisi.db`) akan dibuat otomatis
> beserta data awal (4 user + 3 paket) saat pertama kali `python app.py` dijalankan.

---

## 🐬 Beralih ke MySQL (Opsional — untuk Development Lokal)

Di `app.py`, ganti baris konfigurasi database:

```python
# SQLite — default untuk deploy (aktif sekarang):
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db_gudang_ekspedisi.db'

# MySQL — untuk local dev (uncomment baris ini, comment baris SQLite):
# app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://DB_Admin:CRUDsql@localhost/Tugas_Project'
```

Jika pakai MySQL, tambahkan `pymysql` ke `requirements.txt` dan buat database dulu:

```sql
CREATE DATABASE Tugas_Project;
CREATE USER 'DB_Admin'@'localhost' IDENTIFIED BY 'CRUDsql';
GRANT ALL PRIVILEGES ON Tugas_Project.* TO 'DB_Admin'@'localhost';
FLUSH PRIVILEGES;
```

---

## 🌐 Cara Deploy ke Render

1. **Push project ke GitHub** (pastikan versi SQLite yang aktif)
2. Buka [render.com](https://render.com) → klik **New Web Service**
3. Hubungkan repository GitHub kamu
4. Isi konfigurasi berikut:

| Setting | Value |
|---------|-------|
| **Build Command** | `pip install -r requirements.txt` |
| **Start Command** | `gunicorn app:app` |
| **Environment** | Python 3 |

5. Klik **Deploy** — URL live akan digenerate otomatis

> **Kenapa Gunicorn?**
> `app.run(debug=True)` hanya untuk development lokal.
> Di production (Render), yang digunakan adalah **Gunicorn** sebagai web server yang lebih stabil dan efisien.
> Perintahnya sudah ada di `Procfile`: `web: gunicorn app:app`

---

## 🧪 Akun Demo

Akun berikut sudah tersedia saat pertama kali aplikasi dijalankan:

| Username | Password | Pekerjaan |
|----------|----------|-----------|
| `admin123` | `Admin@123` | Manager |
| `dea2026` | `Dea#2026` | Staff Gudang |
| `lauzia99` | `Lauzia@99` | Admin |
| `user456` | `User@456` | Kurir |

---

## 📦 Tarif Pengiriman

| Jenis Pengiriman | Tarif per kg | Estimasi |
|------------------|-------------|---------|
| D&L Reguler | Rp 10.000 | 2–5 hari |
| D&L Eco (Ekonomis) | Rp 7.000 | 3–7 hari |
| D&L Super (Kilat) | Rp 15.000 | 1–2 hari |
| D&L Cargo | Rp 8.000 | Sesuai jadwal |

> Tarif dihitung otomatis: `berat (kg) × tarif per kg`

---

## 📂 Kategori Paket

- Alat Olahraga
- Kebutuhan Rumah Tangga
- ATK (Alat Tulis Kantor)
- Dokumen
- Elektronik
- Pakaian/Tekstil
- Sparepart
# tugas-kel-4
