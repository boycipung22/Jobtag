# JOBTAG — Job Tracking & Reporting System

> Aplikasi mobile manajemen pekerja lapangan berbasis React Native (Expo) yang mengintegrasikan Axios untuk konsumsi API dan Firebase sebagai Backend-as-a-Service.

---

## 👥 Anggota Tim & Pembagian Tugas

| No | Nama | NRP | Peran | Tanggung Jawab |
|----|------|-----|-------|----------------|
| 1 | [Muarofatussa'diyah] | [0923040067] | **Frontend & Axios Specialist** | Merancang seluruh UI/UX aplikasi (LoginScreen, DashboardScreen, navigasi) dan bertanggung jawab penuh atas integrasi Axios untuk mengambil data dari API eksternal |
| 2 | [M. Zazul Romadhoni] | [0923040059] | **Backend, State & Firebase Specialist** | Mengelola state aplikasi, konfigurasi Firebase SDK, implementasi Firebase Authentication, penyimpanan data di Cloud Firestore (absensi, laporan, data user) |

### Rincian Tugas per Anggota

**Muarofatussa'diyah — Frontend & Axios Specialist**
- Merancang layout dan komponen UI: `LoginScreen.js`, `DashboardScreen.js`
- Mengimplementasikan navigasi antar halaman dengan React Navigation (Stack Navigator)
- Mengintegrasikan Axios pada fitur **Info Keselamatan Kerja** (request ke `httpbin.org` sebagai API trigger + rotasi dataset berita K3)
- Mengelola state lokal form absensi: foto wajah (kamera), GPS, form laporan kerja, peminjaman alat, pengaduan

**M. Zazul Romadhoni — Backend, State & Firebase Specialist**
- Konfigurasi Firebase SDK (`firebaseConfig.js`) dengan platform-aware persistence (AsyncStorage untuk Native, browserLocalPersistence untuk Web)
- Implementasi Firebase Authentication: Login dan Register (`signInWithEmailAndPassword`, `createUserWithEmailAndPassword`, `updateProfile`)
- Integrasi Cloud Firestore: menyimpan data user ke koleksi `users`, data absensi ke `attendance`, dan laporan kerja ke `reports`
- Membangun `ReportScreen.js` dan `SalaryScreen.js` termasuk pengambilan data dari Firestore dan manajemen sesi pengguna

---

## 📱 Deskripsi Aplikasi

**JOBTAG** adalah sistem pelacakan dan pelaporan kerja digital untuk pekerja lapangan. Aplikasi ini memungkinkan pekerja untuk melakukan absensi berbasis foto dan GPS, mengirim laporan hasil kerja harian, melihat slip gaji elektronik, serta mendapatkan informasi keselamatan kerja (K3) terkini — semuanya dalam satu platform mobile.

**Tech Stack:**
- React Native (Expo ~52.0.0)
- Firebase v11 (Authentication + Cloud Firestore)
- Axios v1.18.1
- React Navigation v6 (Native Stack)
- expo-image-picker, expo-location, AsyncStorage

---

## 🔌 Daftar API yang Digunakan

| API | Endpoint | Kegunaan |
|-----|----------|----------|
| HTTPBin | `https://httpbin.org/get?refresh={timestamp}` | API trigger untuk fitur refresh berita K3 via Axios (membuktikan HTTP request berhasil dilakukan) |
| Firebase Authentication | Firebase SDK | Login, Register, sesi pengguna |
| Cloud Firestore | Firebase SDK | CRUD data absensi (`attendance`), laporan kerja (`reports`), profil user (`users`) |

---

## ✅ 3 Fitur Utama 

### Fitur 1 — Autentikasi Pengguna (Login & Register)
**File:** `src/screens/LoginScreen.js`

Fitur autentikasi lengkap menggunakan Firebase Authentication. Pengguna dapat membuat akun baru dengan mengisi nama lengkap, posisi jabatan, email, dan password. Data profil tambahan (nama dan posisi) disimpan ke koleksi `users` di Firestore. Setelah login, sistem otomatis mendeteksi sesi aktif via `onAuthStateChanged` dan mengarahkan pengguna ke Dashboard tanpa perlu login ulang.

- **Firebase Auth:** `createUserWithEmailAndPassword`, `signInWithEmailAndPassword`, `updateProfile`
- **Firestore:** `setDoc` ke koleksi `users/{uid}`
- **State Management:** mode login/register dinamis, validasi form, loading indicator

---

### Fitur 2 — Absensi & Laporan Kerja Harian
**File:** `src/screens/DashboardScreen.js`

Pekerja melakukan absensi dengan mengambil foto selfie menggunakan kamera perangkat (via `expo-image-picker`) dan mengunci koordinat GPS (`expo-location`). Data foto disimpan sebagai Base64. Selain absensi, pekerja juga mengisi laporan kerja harian mencakup deskripsi pekerjaan, progres (%), foto hasil kerja, peminjaman alat, dan pengaduan/kendala. Semua data dikirim ke Cloud Firestore dengan key unik `{uid}_{tanggal}`.

- **Axios:** request ke `httpbin.org` sebagai trigger refresh data berita K3 (bukti integrasi HTTP)
- **Firestore:** `setDoc` ke koleksi `attendance` dan `reports`
- **Kamera & GPS:** `expo-image-picker`, `expo-location`

---

### Fitur 3 — Ringkasan Kerja & Slip Gaji Elektronik
**File:** `src/screens/ReportScreen.js`, `src/screens/SalaryScreen.js`

Halaman Ringkasan Kerja (`ReportScreen`) menampilkan rekap data absensi dan laporan kerja hari ini yang diambil dari Firestore, termasuk foto absensi, koordinat GPS, progres pekerjaan, peminjaman alat, dan pengaduan. Dilengkapi fitur pull-to-refresh. Dari halaman ini, pekerja dapat mengakses Slip Gaji Elektronik (`SalaryScreen`) yang menampilkan rincian gaji (gaji pokok, tunjangan, potongan BPJS, take home pay) beserta nama dan posisi jabatan yang diambil dari Firestore.

- **Firestore:** `getDoc` dari koleksi `attendance`, `reports`, dan `users`
- **Firebase Auth:** `signOut`, `auth.currentUser`
- **State:** data lokal sementara dari Firestore, format mata uang IDR

---

## 🏗️ Struktur Proyek

```
jobtag-app/
├── App.js
├── src/
│   ├── navigation/
│   │   └── AppNavigator.js       # Stack navigator + auth state listener
│   ├── screens/
│   │   ├── LoginScreen.js        # Fitur 1: Auth (Login & Register)
│   │   ├── DashboardScreen.js    # Fitur 2: Absensi + Laporan + Axios berita K3
│   │   ├── ReportScreen.js       # Fitur 3: Ringkasan kerja harian
│   │   └── SalaryScreen.js       # Fitur 3: Slip gaji elektronik
│   ├── services/
│   │   └── firebaseConfig.js     # Inisialisasi Firebase SDK
│   ├── components/
│   │   ├── LoadingOverlay.js
│   │   └── StatusBadge.js
│   └── utils/
│       ├── helpers.js            # Fungsi getDateKey()
│       └── theme.js
├── package.json
└── FIREBASE_SETUP.md
```

---

## 🚀 Cara Menjalankan Aplikasi

```bash
# 1. Clone repositori
git clone https://github.com/[username]/jobtag-app.git
cd jobtag-app

# 2. Install dependencies
npm install

# 3. Jalankan aplikasi
npx expo start
```

> Scan QR code menggunakan aplikasi **Expo Go** di perangkat Android/iOS, atau tekan `w` untuk membuka di browser.

---

## 🔥 Struktur Firestore Database

```
Firestore/
├── users/
│   └── {uid}                    # Data profil user (nama, email, posisi, createdAt)
├── attendance/
│   └── {uid}_{YYYY-MM-DD}       # Data absensi harian (fotoBase64, latitude, longitude, status)
└── reports/
    └── {uid}_{YYYY-MM-DD}       # Laporan kerja harian (deskripsiKerja, progres, fotoPekerjaan, namaAlat, pengaduan)
```

---

## 📋 Firestore Security Rules (Contoh)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // User hanya bisa baca/tulis data miliknya sendiri
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    match /attendance/{docId} {
      allow read, write: if request.auth != null && docId.matches(request.auth.uid + '_.*');
    }
    match /reports/{docId} {
      allow read, write: if request.auth != null && docId.matches(request.auth.uid + '_.*');
    }
  }
}
```

---

*© 2026 JOBTAG System — Ujian Praktikum Mobile Computing Semester Genap 2025/2026*
