# Software Requirements Specification (SRS)
## Aplikasi SaaS Pemesanan Lapangan Olahraga "Gsports" (MVP)

**Versi:** 1.1
**Tanggal:** 14 Juli 2025 (Diperbarui)
**Penyusun:** 
[M. Gilang M.W. Sabdokafi - 221240001248]
[Arrayyan Rizqy Fawwaz - 221240001274]
[Umi Nurul Latifah - 221240001226]
---

## 1. Pendahuluan

### 1.1. Tujuan (TUJ001)
Dokumen ini bertujuan untuk mendefinisikan kebutuhan fungsional dan non-fungsional untuk Minimum Viable Product (MVP) dari aplikasi SaaS pemesanan dan manajemen ketersediaan lapangan olahraga. Aplikasi ini akan memungkinkan pengguna (pemain) untuk mencari, melihat ketersediaan, dan memesan lapangan secara real-time, serta memungkinkan admin Sports Center (SC) untuk mengelola lapangan dan pemesanan mereka secara terisolasi (multi-tenant).

### 1.2. Ruang Lingkup MVP (RLG001)
MVP akan fokus pada fungsionalitas inti berikut:
*   **(F001)** Registrasi dan login untuk pemain dan admin SC.
*   **(F002)** Pencarian lapangan berdasarkan jenis olahraga dan kota.
*   **(F003)** Tampilan detail SC dan lapangan (termasuk harga dan foto).
*   **(F004)** Tampilan ketersediaan slot lapangan secara real-time.
*   **(F005)** Proses pemesanan dasar oleh pemain (opsi pembayaran awal: konfirmasi tanpa pembayaran online / transfer bank manual).
*   **(F006)** Manajemen profil SC dasar oleh admin SC.
*   **(F007)** Manajemen lapangan (tambah/edit/hapus) oleh admin SC.
*   **(F008)** Manajemen jadwal dan ketersediaan manual oleh admin SC (blokir slot, tambah booking manual).
*   **(F009)** Tampilan daftar pemesanan untuk pemain dan admin SC.
*   **(F010)** (Jika transfer manual) Konfirmasi pembayaran oleh admin SC.
*   **(F011)** Manajemen dasar tenant (Sports Center) oleh Admin Platform.

### 1.3. Definisi, Akronim, dan Singkatan (DEF001)
*   **SC:** Sports Center (Pusat Olahraga)
*   **MVP:** Minimum Viable Product
*   **SRS:** Software Requirements Specification
*   **UI:** User Interface
*   **UX:** User Experience
*   **BaaS:** Backend as a Service
*   **API:** Application Programming Interface
*   **SDK:** Software Development Kit
*   **PK:** Primary Key
*   **FK:** Foreign Key

---

## 2. Deskripsi Umum

### 2.1. Perspektif Produk (DPP001)
Aplikasi ini adalah platform SaaS multi-tenant yang menghubungkan pemain dengan penyedia lapangan olahraga. Pemain dapat dengan mudah menemukan dan memesan lapangan, sementara SC mendapatkan alat untuk mengelola operasional pemesanan mereka secara digital dengan data yang terisolasi untuk setiap SC.

### 2.2. Pengguna Target (DPT001)
1.  **Pemain/Penyewa Lapangan (USR_PLAYER):** Individu atau grup yang ingin mencari dan menyewa lapangan olahraga.
2.  **Admin Sports Center (USR_ADMIN_SC):** Pemilik atau staf yang bertanggung jawab mengelola operasional dan pemesanan lapangan di SC mereka.
3.  **Admin Platform (USR_SUPER_ADMIN):** Anda/tim Anda, yang mengelola SC terdaftar, pengguna, dan operasional platform secara keseluruhan.

### 2.3. Teknologi yang Digunakan (TEK001)
*   **Frontend (Aplikasi Mobile):**
    *   **Framework:** Flutter (Target: Android & iOS)
    *   **Bahasa:** Dart
    *   **Manajemen State:** (Pilih salah satu, misal: BLoC/Cubit atau Riverpod untuk skalabilitas)
    *   **Routing:** `go_router` (Untuk routing berbasis state dan deep linking)
    *   **HTTP Client:** `dio` (Untuk penanganan request/response yang lebih advanced)
    *   **SDK Appwrite:** `appwrite` package untuk Flutter
*   **Backend (BaaS):**
    *   **Platform:** Appwrite (Self-hosted atau Cloud)
    *   **Database:** Appwrite Database (NoSQL, berbasis dokumen)
    *   **Otentikasi:** Appwrite Authentication (Email/Password)
    *   **Penyimpanan File:** Appwrite Storage (Untuk gambar, bukti bayar)
    *   **Realtime:** Appwrite Realtime (Untuk update ketersediaan jadwal)
    *   **Serverless Functions:** Appwrite Functions (Dart/Node.js untuk logika backend, validasi, dan otomatisasi)
*   **Desain & Prototyping (Opsional):** Figma, Adobe XD
*   **Version Control:** Git (GitHub, GitLab)
*   **Project Management:** Trello, Jira, atau Notion

---

## 3. Kebutuhan Fungsional (MVP)

### 3.1. Fitur Untuk Pengguna (Pemain & Admin SC)

**3.1.1. (F001) Registrasi & Login Pengguna**
*   **ID Fitur:** F001
*   **Peran:** USR_PLAYER, USR_ADMIN_SC (calon)
*   **User Story:**
    *   (US001) "Sebagai pengguna baru, saya ingin bisa mendaftar menggunakan nama, email, dan password agar dapat menggunakan fitur aplikasi sesuai peran saya."
    *   (US002) "Sebagai pengguna terdaftar, saya ingin bisa login ke akun saya menggunakan email dan password."
    *   (US003) "Sebagai pengguna, saya ingin bisa logout dari akun saya."
*   **Alur Sistem & Pengguna:**

    **Alur Registrasi (AS001):**
    ```mermaid
    sequenceDiagram
        participant P as Pengguna
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Buka Aplikasi, Pilih "Daftar"
        App->>P: Tampilkan Form Registrasi (Nama, Email, Password, Pilih Peran [Pemain / Ingin jadi Admin SC])
        P->>App: Isi Form, Submit
        App->>API: Request `account.create()` dengan data & peran (misal: 'player' atau 'pending_sc_admin')
        API-->>App: Respons Sukses (user object) / Error
        alt Sukses
            App->>P: Tampilkan Pesan Sukses, Arahkan ke Login
        else Error
            App->>P: Tampilkan Pesan Error (misal: email sudah ada)
        end
    ```

    **Alur Login (AS002):**
    ```mermaid
    sequenceDiagram
        participant P as Pengguna
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Buka Aplikasi, Pilih "Login"
        App->>P: Tampilkan Form Login (Email, Password)
        P->>App: Isi Form, Submit
        App->>API: Request `account.createEmailSession()`
        API-->>App: Respons Sukses (session object) / Error
        alt Sukses
            App->>API: Request `account.get()` (untuk data user detail, termasuk peran dari prefs)
            API-->>App: User data
            App->>P: Arahkan ke Dashboard sesuai peran (Player/Admin SC)
        else Error
            App->>P: Tampilkan Pesan Error (misal: email/password salah)
        end
    ```

    **Alur Logout (AS003):**
    ```mermaid
    sequenceDiagram
        participant P as Pengguna
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Pilih Opsi "Logout" di Profil
        App->>API: Request `account.deleteSession('current')`
        API-->>App: Respons Sukses / Error
        App->>P: Arahkan ke Halaman Login
    ```
*   **Kriteria Penerimaan (KP001):**
    *   Pengguna berhasil membuat akun baru dan data tersimpan di collection `users`.
    *   Peran pengguna disimpan di `prefs.role`. Calon Admin SC diberi peran `pending_sc_admin`.
    *   Pengguna berhasil login dan diarahkan ke dashboard yang benar.
    *   Pengguna berhasil logout dan sesi dihapus.
    *   Notifikasi error jelas ditampilkan jika registrasi/login gagal.
    *   Password disimpan terenkripsi (dikelola oleh Appwrite).

---

**3.1.2. (F002) Pencarian Lapangan Dasar**
*   **ID Fitur:** F002
*   **Peran:** USR_PLAYER
*   **User Story:**
    *   (US004) "Sebagai pemain, saya ingin mencari SC berdasarkan jenis olahraga (misal: futsal, badminton) dan kota agar menemukan pilihan yang relevan."
*   **Alur Sistem & Pengguna (AS004):**
    ```mermaid
    sequenceDiagram
        participant P as Pemain
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Di halaman utama, input/pilih jenis olahraga & input kota
        App->>API: Request `databases.listDocuments('sport_centers')` dengan Query filter `sc_city`
        API-->>App: Daftar SC yang cocok dengan kota
        App->>App: Filter hasil di sisi klien berdasarkan jenis olahraga yang tersedia di SC tersebut (atau query ke `fields` jika diperlukan performa lebih baik)
        App->>P: Tampilkan daftar SC yang relevan (Nama, Alamat singkat, foto utama)
    ```
*   **Kriteria Penerimaan (KP002):**
    *   Hasil pencarian menampilkan daftar SC yang berstatus `active` dan sesuai dengan kriteria kota.
    *   Aplikasi dapat menampilkan SC yang memiliki minimal satu lapangan dengan jenis olahraga yang dicari.
    *   Jika tidak ada hasil, pesan informatif "Tidak ada hasil ditemukan" ditampilkan.

---

**3.1.3. (F003) Tampilan Detail Sports Center & Lapangan**
*   **ID Fitur:** F003
*   **Peran:** USR_PLAYER
*   **User Story:**
    *   (US006) "Sebagai pemain, setelah memilih SC dari hasil pencarian, saya ingin melihat detailnya (alamat, foto, jam buka, daftar lapangan)."
    *   (US007) "Sebagai pemain, setelah memilih lapangan, saya ingin melihat detail spesifiknya (harga, foto, tipe)."
*   **Alur Sistem & Pengguna (AS005):**
    ```mermaid
    sequenceDiagram
        participant P as Pemain
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Pilih satu SC dari daftar hasil pencarian
        App->>API: Request `databases.getDocument('sport_centers', centerId)`
        App->>API: Request `databases.listDocuments('fields')` dengan filter `centerId`
        API-->>App: Detail SC dan Daftar Lapangan
        App->>P: Tampilkan halaman detail SC (info, foto, jam buka, daftar lapangan)

        P->>App: Pilih satu lapangan dari daftar
        App->>API: Request `databases.getDocument('fields', fieldId)`
        API-->>App: Detail lapangan
        App->>P: Tampilkan halaman detail lapangan (info lengkap, harga, foto, deskripsi)
    ```
*   **Kriteria Penerimaan (KP003):**
    *   Informasi detail SC dan lapangan ditampilkan dengan akurat.
    *   Galeri foto untuk SC dan lapangan dapat dilihat.
    *   Harga per jam untuk setiap lapangan jelas terlihat.

---

**3.1.4. (F004) Tampilan Ketersediaan Real-time Sederhana**
*   **ID Fitur:** F004
*   **Peran:** USR_PLAYER
*   **User Story:**
    *   (US008) "Sebagai pemain, di halaman detail lapangan, saya ingin memilih tanggal dan melihat jadwal ketersediaan slot waktu secara real-time (tersedia/dipesan)."
*   **Alur Sistem & Pengguna (AS006):**
    ```mermaid
    sequenceDiagram
        participant P as Pemain
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API (DB & Realtime)

        P->>App: Di halaman detail lapangan, pilih tanggal
        App->>API: Request `databases.listDocuments('bookings')` dengan filter `fieldId` & `bookingDate`
        App->>API: Request `databases.listDocuments('blocked_slots')` dengan filter `fieldId` & `block_date`
        API-->>App: Daftar booking & slot diblokir yang ada
        App->>App: Proses data, buat list slot per jam (dari jam buka-tutup), tandai yang sudah dipesan/diblokir
        App->>P: Tampilkan jadwal slot waktu dengan statusnya
        
        Note right of App: Subscribe ke perubahan di `bookings` & `blocked_slots`
        App->>API: Subscribe ke event Realtime untuk collection `bookings` & `blocked_slots` dengan filter `fieldId`
        API->>App: (Jika ada perubahan) Kirim update Realtime
        App->>App: Update UI jadwal secara otomatis
    ```
*   **Kriteria Penerimaan (KP004):**
    *   Jadwal ketersediaan akurat, menggabungkan data dari `bookings` (status 'confirmed' dan 'pending_payment') dan `blocked_slots`.
    *   Perubahan ketersediaan tercermin di UI pemain dengan cepat (< 5 detik) tanpa refresh manual.
    *   Slot di luar jam operasional SC tidak dapat dipilih.

---

**3.1.5. (F005) Proses Pemesanan Dasar oleh Pemain**
*   **ID Fitur:** F005
*   **Peran:** USR_PLAYER
*   **User Story:**
    *   (US009) "Sebagai pemain, saya ingin memilih slot waktu yang tersedia, mengisi detail, dan menyelesaikan pemesanan dengan status 'Menunggu Pembayaran'."
*   **Alur Sistem & Pengguna (AS007):**
    ```mermaid
    sequenceDiagram
        participant P as Pemain
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Pilih slot waktu yang tersedia, klik "Pesan"
        App->>P: Tampilkan halaman ringkasan pemesanan (lapangan, jadwal, total harga)
        P->>App: Konfirmasi pemesanan
        App->>API: Request `databases.createDocument('bookings', data)`
        Note over API: Data: `playerId`, `fieldId`, `centerId`, jadwal, harga, `status: 'pending_payment'`
        API-->>App: Respons Sukses (dokumen booking baru) / Error
        alt Sukses
            App->>P: Tampilkan halaman sukses pemesanan dengan instruksi pembayaran (transfer manual)
        else Error
            App->>P: Tampilkan pesan error (misal: "Slot sudah tidak tersedia")
        end
    ```
*   **Kriteria Penerimaan (KP005):**
    *   Pemesanan baru berhasil dibuat di collection `bookings` dengan status `pending_payment`.
    *   Pemain melihat halaman konfirmasi dengan detail booking dan instruksi pembayaran.
    *   Jika slot sudah diambil pengguna lain saat proses, pemesanan gagal dengan pesan yang jelas.

---

**3.1.6. (F006 & F007) Manajemen Profil SC dan Lapangan oleh Admin SC**
*   **ID Fitur:** F006, F007
*   **Peran:** USR_ADMIN_SC
*   **User Story:**
    *   (US010) "Sebagai admin SC, saya ingin bisa mengedit informasi dasar SC saya (nama, alamat, jam operasional, foto)."
    *   (US011) "Sebagai admin SC, saya ingin bisa menambah, mengedit, dan menghapus (menonaktifkan) lapangan di SC saya."
*   **Alur Sistem & Pengguna (AS008):**
    ```mermaid
    sequenceDiagram
        participant Admin as Admin SC
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        Admin->>App: Login, masuk ke Dashboard Admin
        
        opt Edit Profil SC
            Admin->>App: Pilih "Profil SC"
            App->>API: Ambil data `sport_centers` miliknya
            API-->>App: Detail SC
            App->>Admin: Tampilkan form edit profil SC
            Admin->>App: Ubah data, Simpan
            App->>API: Request `databases.updateDocument('sport_centers', ...)`
            API-->>App: Respons Sukses
        end

        opt Manajemen Lapangan
            Admin->>App: Pilih "Manajemen Lapangan"
            App->>API: Ambil daftar `fields` miliknya
            API-->>App: Daftar Lapangan
            App->>Admin: Tampilkan daftar lapangan (opsi Tambah/Edit/Hapus)
            Admin->>App: Lakukan aksi (misal: Edit Lapangan X)
            App->>API: Request `databases.updateDocument('fields', ...)` atau `createDocument` / `deleteDocument`
            API-->>App: Respons Sukses
        end
    ```
*   **Kriteria Penerimaan (KP006, KP007):**
    *   Admin SC hanya dapat melihat dan mengelola data SC dan lapangan yang terkait dengannya.
    *   Perubahan pada profil SC dan data lapangan berhasil disimpan ke database.
    *   Admin dapat menambah lapangan baru, mengedit yang sudah ada, atau mengubah statusnya menjadi tidak aktif.

---

**3.1.7. (F008) Manajemen Jadwal Manual oleh Admin SC**
*   **ID Fitur:** F008
*   **Peran:** USR_ADMIN_SC
*   **User Story:**
    *   (US012) "Sebagai admin SC, saya ingin bisa memblokir slot waktu tertentu untuk maintenance atau acara internal."
    *   (US013) "Sebagai admin SC, saya ingin bisa menambahkan booking secara manual untuk pelanggan offline (walk-in)."
*   **Alur Sistem & Pengguna (AS009):**
    ```mermaid
    sequenceDiagram
        participant Admin as Admin SC
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        Admin->>App: Buka Tampilan Jadwal Lapangan
        
        opt Blokir Slot
            Admin->>App: Pilih slot waktu, pilih "Blokir Slot"
            App->>Admin: Tampilkan form blokir (alasan, dll)
            Admin->>App: Konfirmasi blokir
            App->>API: Request `databases.createDocument('blocked_slots', data)`
            API-->>App: Sukses
        end

        opt Tambah Booking Manual
            Admin->>App: Pilih slot waktu, pilih "Tambah Booking"
            App->>Admin: Tampilkan form booking (nama pelanggan, dll)
            Admin->>App: Konfirmasi booking
            App->>API: Request `databases.createDocument('bookings', data)`
            Note over API: `booked_by_role: 'sc_admin'`, `status: 'confirmed'`
            API-->>App: Sukses
        end
    ```
*   **Kriteria Penerimaan (KP008):**
    *   Admin SC berhasil membuat entri baru di `blocked_slots`.
    *   Admin SC berhasil membuat entri baru di `bookings` untuk pelanggan offline.
    *   Slot yang diblokir atau di-booking manual akan langsung tidak tersedia di tampilan pemain (via Realtime).

---

**3.1.8. (F009 & F010) Daftar Pemesanan dan Konfirmasi Pembayaran**
*   **ID Fitur:** F009, F010
*   **Peran:** USR_PLAYER, USR_ADMIN_SC
*   **User Story:**
    *   (US014) "Sebagai pemain, saya ingin melihat riwayat pemesanan saya dan mengunggah bukti bayar untuk pesanan yang 'Menunggu Pembayaran'."
    *   (US015) "Sebagai admin SC, saya ingin melihat daftar semua pemesanan dan bisa mengkonfirmasi atau menolak pembayaran yang masuk."
*   **Alur Sistem & Pengguna (AS010):**
    ```mermaid
    sequenceDiagram
        participant P as Pemain
        participant Admin as Admin SC
        participant App as Aplikasi (Flutter)
        participant API as Appwrite API

        P->>App: Buka "Riwayat Pesanan"
        App->>API: Ambil `bookings` dengan filter `player_user_id`
        API-->>App: Daftar pesanan pemain
        P->>App: Pilih pesanan `pending_payment`, unggah bukti bayar
        App->>API: Request `storage.createFile()` untuk upload gambar
        API-->>App: File ID/URL
        App->>API: Request `databases.updateDocument('bookings', bookingId)` dengan `payment_proof_url` dan ubah status ke `waiting_for_sc_confirmation`

        Admin->>App: Buka "Daftar Pesanan"
        App->>API: Ambil `bookings` dengan filter `centerId` miliknya
        API-->>App: Daftar semua pesanan di SC
        Admin->>App: Filter/pilih pesanan dengan status `waiting_for_sc_confirmation`
        Admin->>App: Lihat bukti bayar, pilih "Konfirmasi" atau "Tolak"
        App->>API: Request `databases.updateDocument('bookings', bookingId)` ubah status ke `confirmed` atau `payment_rejected`
    ```
*   **Kriteria Penerimaan (KP009, KP010):**
    *   Pemain dapat melihat daftar pesanannya dan mengunggah bukti bayar.
    *   Admin SC dapat melihat semua pesanan di tempatnya.
    *   Admin SC dapat mengubah status pemesanan dari `waiting_for_sc_confirmation` menjadi `confirmed` atau `payment_rejected`.
    *   Perubahan status booking dapat dilihat oleh pemain di riwayat pesanannya.

---

**3.1.9. (F011) Manajemen Tenant oleh Admin Platform**
*   **ID Fitur:** F011
*   **Peran:** USR_SUPER_ADMIN
*   **User Story:**
    *   (US016) "Sebagai admin platform, saya ingin melihat daftar pengguna yang mendaftar sebagai admin SC dan bisa menyetujui atau menolak pendaftaran mereka."
    *   (US017) "Sebagai admin platform, saat menyetujui, saya ingin bisa membuat entri SC baru untuk mereka dan menetapkan mereka sebagai admin."
*   **Alur Sistem & Pengguna (AS011):**
    ```mermaid
    sequenceDiagram
        participant SuperAdmin as Admin Platform
        participant App as Aplikasi (Web/Mobile)
        participant API as Appwrite API
        participant Func as Appwrite Function

        SuperAdmin->>App: Login, masuk ke Panel Super Admin
        App->>API: Ambil `users` dengan filter `prefs.role: 'pending_sc_admin'`
        API-->>App: Daftar calon admin SC
        App->>SuperAdmin: Tampilkan daftar calon admin
        
        SuperAdmin->>App: Pilih calon admin, klik "Setujui"
        App->>SuperAdmin: Tampilkan form untuk membuat SC baru (nama SC, kota, dll)
        SuperAdmin->>App: Isi form, submit
        App->>Func: Panggil Function 'approveScAdmin' dengan `userId` & data SC
        
        Func->>API: 1. Buat `sport_centers` baru, dapatkan `centerId`
        Func->>API: 2. Buat Team baru `sc_[centerId]_admins`, dapatkan `teamId`
        Func->>API: 3. Update `sport_centers` dengan `sc_admin_team_id`
        Func->>API: 4. Tambah `userId` ke Team baru
        Func->>API: 5. Update user `prefs` -> `role: 'sc_admin'`, `assignedCenterId: centerId`
        Func-->>App: Respons Sukses
        App->>SuperAdmin: Tampilkan pesan "Admin SC berhasil disetujui"
    ```
*   **Kriteria Penerimaan (KP011):**
    *   Super Admin dapat melihat daftar pengguna dengan peran `pending_sc_admin`.
    *   Setelah disetujui melalui Function:
        *   Sebuah dokumen baru dibuat di collection `sport_centers`.
        *   Sebuah Team baru dibuat di Appwrite untuk admin SC tersebut.
        *   Pengguna ditambahkan ke Team tersebut.
        *   Peran pengguna di `users.prefs.role` diubah menjadi `sc_admin`.
        *   `users.prefs.assignedCenterId` diisi dengan ID SC yang baru dibuat.

---
## 4. Kebutuhan Non-Fungsional (KNF)

*   **KNF001: Kinerja (Performance)**
    *   **KNF001.1:** Waktu respons aplikasi untuk aksi pengguna umum (navigasi, buka halaman) harus < 2 detik.
    *   **KNF001.2:** Waktu muat daftar SC (hasil pencarian) harus < 3 detik untuk 20 hasil pertama.
    *   **KNF001.3:** Waktu muat tampilan ketersediaan lapangan (setelah pilih tanggal) harus < 3 detik.
    *   **KNF001.4:** Penggunaan CPU dan memori aplikasi di perangkat mobile harus efisien.
*   **KNF002: Ketersediaan Real-time (Real-time Availability)**
    *   **KNF002.1:** Update perubahan status ketersediaan slot lapangan di semua klien yang relevan harus terjadi dalam < 5 detik (ideal < 2 detik) setelah perubahan di backend.
*   **KNF003: Keamanan (Security)**
    *   **KNF003.1:** Otentikasi pengguna menggunakan mekanisme aman yang disediakan Appwrite (password hashing, session management).
    *   **KNF003.2:** Otorisasi: Data hanya dapat diakses dan dimodifikasi oleh pengguna dengan peran dan hak yang sesuai (Pemain, Admin SC spesifik, Super Admin). Isolasi data tenant (SC) harus ketat.
    *   **KNF003.3:** Perlindungan terhadap serangan umum (misal: XSS, SQL Injection – sebagian besar ditangani BaaS, namun validasi input tetap penting).
    *   **KNF003.4:** Komunikasi antara aplikasi klien dan Appwrite harus melalui HTTPS.
    *   **KNF003.5:** API Key (jika digunakan untuk Functions atau server-to-server) harus disimpan aman dan memiliki scope terbatas.
*   **KNF004: Usability (Kemudahan Penggunaan)**
    *   **KNF004.1:** Antarmuka pengguna (UI) harus intuitif, konsisten, dan mudah dipahami oleh pengguna target tanpa memerlukan pelatihan khusus.
    *   **KNF004.2:** Alur kerja utama (pencarian, pemesanan, manajemen SC) harus efisien dan minim langkah yang tidak perlu.
    *   **KNF004.3:** Pesan error dan feedback sistem harus jelas dan informatif.
    *   **KNF004.4:** Aplikasi harus mendukung orientasi potret.
*   **KNF005: Stabilitas & Keandalan (Stability & Reliability)**
    *   **KNF005.1:** Aplikasi harus stabil dan memiliki Mean Time Between Failures (MTBF) yang tinggi. Target crash rate < 0.1%.
    *   **KNF005.2:** Data yang disimpan di backend harus persisten dan tidak hilang akibat error aplikasi.
    *   **KNF005.3:** Proses pemesanan harus transaksional (atau setidaknya mendekati, memastikan slot terblokir dengan benar saat dipesan).
*   **KNF006: Skalabilitas (Awal) (Scalability - Initial)**
    *   **KNF006.1:** Arsitektur backend (Appwrite) harus mampu menangani pertumbuhan pengguna dan SC hingga (misalnya) 100 SC dan 1000 pengguna aktif harian untuk fase MVP tanpa degradasi performa signifikan.
    *   **KNF006.2:** Desain database harus mempertimbangkan query yang efisien seiring pertumbuhan data.
*   **KNF007: Maintainability (Kemudahan Pemeliharaan)**
    *   **KNF007.1:** Kode sumber (Flutter & Appwrite Functions) harus terstruktur dengan baik, mudah dipahami, dan mengikuti best practices untuk memudahkan modifikasi dan penambahan fitur di masa depan.
    *   **KNF007.2:** Dokumentasi kode (komentar) yang memadai.
*   **KNF008: Kompatibilitas (Compatibility)**
    *   **KNF008.1:** Aplikasi Flutter harus kompatibel dengan Android versi 6.0 (Marshmallow) ke atas.
    *   **KNF008.2:** Aplikasi Flutter harus kompatibel dengan iOS versi 12.0 ke atas.
    *   **KNF008.3:** Tampilan harus responsif terhadap berbagai ukuran layar ponsel umum.
*   **KNF009: Lokalizasi (Localization - Awal)**
    *   **KNF009.1:** Teks antarmuka pengguna utama akan menggunakan Bahasa Indonesia. (Dukungan multi-bahasa bisa jadi fitur masa depan).
*   **KNF010: Data Management & Backup**
    *   **KNF010.1:** Admin Platform harus memiliki mekanisme untuk backup data penting dari Appwrite secara periodik (jika self-hosted, atau memahami kebijakan backup penyedia cloud Appwrite).
    *   **KNF010.2:** Kebijakan retensi data (jika diperlukan) harus didefinisikan.

---
## 5. Skema Database Detail (Appwrite Collections) (DB001)
```mermaid
erDiagram
    USERS ||--o{ BOOKINGS : "makes"
    USERS ||--o{ BLOCKED_SLOTS : "blocks"
    USERS ||--|{ SPORT_CENTERS : "manages"

    SPORT_CENTERS ||--|{ USERS : "has_admin"
    SPORT_CENTERS ||--o{ FIELDS : "has"
    SPORT_CENTERS ||--o{ BOOKINGS : "receives_for"
    SPORT_CENTERS ||--o{ BLOCKED_SLOTS : "has_blocked_slots_for"

    FIELDS ||--|{ SPORT_CENTERS : "belongs_to"
    FIELDS ||--o{ BOOKINGS : "is_booked_in"
    FIELDS ||--o{ BLOCKED_SLOTS : "has_blocked_slots"

    BOOKINGS ||--|| USERS : "booked_by_player"
    BOOKINGS ||--|| FIELDS : "for_field"
    BOOKINGS ||--|| SPORT_CENTERS : "at_center"

    BLOCKED_SLOTS ||--|| USERS : "blocked_by_admin"
    BLOCKED_SLOTS ||--|| FIELDS : "for_field"
    BLOCKED_SLOTS ||--|| SPORT_CENTERS : "at_center"

    USERS {
        string user_id PK "User ID (Appwrite $id)"
        string name "Nama Lengkap"
        string email "Email (unique)"
        string phone "Nomor Telepon (ops)"
        object prefs "Preferensi Pengguna"
        string prefs_role "Peran (player, sc_admin, super_admin)"
        string prefs_assignedCenterId "FK ke SPORT_CENTERS.$id (jika sc_admin)"
        boolean emailVerification "Status Verifikasi Email"
        boolean status "Status Akun (aktif/tidak aktif)"
        datetime createdAt "Waktu Pembuatan"
        datetime updatedAt "Waktu Update"
    }

    SPORT_CENTERS {
        string center_id PK "SC ID (Appwrite $id)"
        string sc_name "Nama SC"
        string sc_address "Alamat SC"
        string sc_city "Kota SC (indexed)"
        string sc_contact_phone "Kontak Telepon SC (ops)"
        string sc_contact_email "Kontak Email SC (ops)"
        string sc_description "Deskripsi SC (ops)"
        string sc_operating_hours_open "Jam Buka (HH:MM)"
        string sc_operating_hours_close "Jam Tutup (HH:MM)"
        string sc_main_photo_url "URL Foto Utama (ops)"
        string_array sc_additional_photos_urls "URL Foto Tambahan (ops)"
        string_array sc_facilities "Fasilitas (ops)"
        string sc_status "Status SC (pending, active, inactive)"
        string sc_admin_team_id "ID Team Admin SC di Appwrite"
        datetime createdAt "Waktu Pembuatan"
        datetime updatedAt "Waktu Update"
    }

    FIELDS {
        string field_id PK "Field ID (Appwrite $id)"
        string center_id FK "FK ke SPORT_CENTERS.$id (indexed)"
        string field_name "Nama Lapangan"
        string sport_type "Jenis Olahraga (indexed)"
        string field_type "Tipe Lapangan (indoor/outdoor, ops)"
        string floor_type "Jenis Lantai (ops)"
        float price_per_hour "Harga per Jam"
        string field_description "Deskripsi Lapangan (ops)"
        string_array field_photos_urls "URL Foto Lapangan (ops)"
        boolean is_active "Status Aktif Lapangan"
        datetime createdAt "Waktu Pembuatan"
        datetime updatedAt "Waktu Update"
    }

    BOOKINGS {
        string booking_id PK "Booking ID (Appwrite $id)"
        string player_user_id FK "FK ke USERS.$id (indexed)"
        string center_id FK "FK ke SPORT_CENTERS.$id (indexed)"
        string field_id FK "FK ke FIELDS.$id (indexed)"
        string booking_date "Tanggal Main (YYYY-MM-DD, indexed)"
        string start_time "Waktu Mulai (HH:MM, indexed)"
        string end_time "Waktu Selesai (HH:MM, indexed)"
        float duration_hours "Durasi (jam)"
        float total_price "Total Harga"
        string booking_status "Status Booking (pending_payment, confirmed, etc., indexed)"
        string payment_method "Metode Bayar (ops)"
        string payment_proof_url "URL Bukti Bayar (ops)"
        string player_notes "Catatan Pemain (ops)"
        string sc_notes "Catatan SC (ops)"
        string booked_by_role "Dipesan oleh (player/sc_admin)"
        datetime createdAt "Waktu Pembuatan Booking"
        datetime updatedAt "Waktu Update Booking"
    }

    BLOCKED_SLOTS {
        string blocked_slot_id PK "Blocked Slot ID (Appwrite $id)"
        string center_id FK "FK ke SPORT_CENTERS.$id (indexed)"
        string field_id FK "FK ke FIELDS.$id (indexed)"
        string block_date "Tanggal Blokir (YYYY-MM-DD, indexed)"
        string start_time "Waktu Mulai Blokir (HH:MM, indexed)"
        string end_time "Waktu Selesai Blokir (HH:MM, indexed)"
        string reason "Alasan Blokir (ops)"
        string blocked_by_user_id FK "FK ke USERS.$id"
        datetime createdAt "Waktu Pembuatan"
        datetime updatedAt "Waktu Update"
    }
```

**Aturan Umum Permissions:**
*   **`role:super_admin`**: Biasanya memiliki CRUD penuh pada semua collection untuk keperluan administrasi.
*   **`team:sc_[centerId]_admins`**: Tim yang berisi ID user admin dari SC tertentu. Digunakan untuk memberikan akses CRUD ke data milik SC tersebut.
*   **`user:[userId]`**: Memberikan akses kepada pengguna spesifik (pemilik dokumen).
*   **`role:player`**: Memberikan akses baca umum atau hak buat terbatas.

---

**5.1. Collection: `users`**
*   **Tujuan:** Menyimpan data semua pengguna (Pemain, Admin SC, Super Admin).
*   **Atribut:**
    *   `$id` (string, PK, otomatis oleh Appwrite): ID unik pengguna.
    *   `$createdAt` (datetime, otomatis): Waktu pembuatan.
    *   `$updatedAt` (datetime, otomatis): Waktu update terakhir.
    *   `name` (string, wajib): Nama lengkap pengguna.
    *   `email` (string, wajib, unik, indexed): Alamat email pengguna (untuk login).
    *   `emailVerification` (boolean, otomatis): Status verifikasi email.
    *   `password` (string, otomatis, hashed): Password pengguna (dikelola Appwrite).
    *   `phone` (string, opsional): Nomor telepon pengguna.
    *   `prefs` (object, otomatis): Preferensi pengguna (bisa berisi `role` dan `assignedCenterId`).
        *   `role` (string, wajib, enum: `player`, `pending_sc_admin`, `sc_admin`, `super_admin`): Peran pengguna dalam sistem.
        *   `assignedCenterId` (string, opsional, FK ke `sport_centers.$id`): Diisi jika `role` adalah `sc_admin`, menunjukkan SC yang dikelola.
    *   `status` (boolean, otomatis): Status akun (aktif/tidak aktif).
*   **Permissions Dokumen Default:**
    *   Read: `role:super_admin`, `user:$id`
    *   Update: `role:super_admin`, `user:$id` (Pengguna bisa update profil sendiri)
    *   Delete: `role:super_admin`
*   **Relasi:**
    *   `prefs.assignedCenterId` -> `sport_centers.$id` (Satu Admin SC mengelola satu SC - untuk MVP).

---

**5.2. Collection: `sport_centers` (SC)**
*   **Tujuan:** Menyimpan data detail setiap Sports Center (tenant).
*   **Atribut:**
    *   `$id` (string, PK, otomatis): ID unik SC.
    *   `$createdAt` (datetime, otomatis): Waktu pembuatan.
    *   `$updatedAt` (datetime, otomatis): Waktu update terakhir.
    *   `sc_name` (string, wajib, indexed): Nama resmi Sports Center.
    *   `sc_address` (string, wajib): Alamat lengkap SC.
    *   `sc_city` (string, wajib, indexed): Kota lokasi SC (untuk pencarian).
    *   `sc_contact_phone` (string, opsional): Nomor telepon kontak SC.
    *   `sc_contact_email` (string, opsional): Email kontak SC.
    *   `sc_description` (string, opsional, teks panjang): Deskripsi tentang SC.
    *   `sc_operating_hours_open` (string, wajib, format "HH:MM"): Jam buka SC.
    *   `sc_operating_hours_close` (string, wajib, format "HH:MM"): Jam tutup SC.
    *   `sc_main_photo_url` (string, opsional): URL ke foto utama SC (dari Appwrite Storage).
    *   `sc_additional_photos_urls` (array of strings, opsional): Daftar URL ke foto tambahan SC.
    *   `sc_facilities` (array of strings, opsional): Daftar fasilitas (misal: "Parkir", "Toilet", "Kantin").
    *   `sc_status` (string, wajib, enum: `pending_approval`, `active`, `inactive`, `suspended`, indexed): Status SC.
    *   `sc_admin_team_id` (string, wajib): ID Team Appwrite (`sc_[$id]_admins`) yang berisi admin(s) untuk SC ini.
*   **Permissions Dokumen Default:**
    *   Read: `role:super_admin`, `role:player`, `team:[sc_admin_team_id]`
    *   Create: `role:super_admin` (atau Function yang dipicu oleh `pending_sc_admin` yang disetujui)
    *   Update: `role:super_admin`, `team:[sc_admin_team_id]`
    *   Delete: `role:super_admin`
*   **Relasi:**
    *   Memiliki banyak `fields`.
    *   Dikelola oleh pengguna dalam `team:[sc_admin_team_id]`.

---

**5.3. Collection: `fields` (Lapangan)**
*   **Tujuan:** Menyimpan data detail setiap lapangan milik Sports Center.
*   **Atribut:**
    *   `$id` (string, PK, otomatis): ID unik lapangan.
    *   `$createdAt` (datetime, otomatis): Waktu pembuatan.
    *   `$updatedAt` (datetime, otomatis): Waktu update terakhir.
    *   `center_id` (string, wajib, FK ke `sport_centers.$id`, indexed): ID SC pemilik lapangan ini.
    *   `field_name` (string, wajib): Nama lapangan (misal: "Lapangan Futsal A", "Badminton Indoor 1").
    *   `sport_type` (string, wajib, enum: `futsal`, `badminton`, `basketball`, `volleyball`, `tennis`, indexed): Jenis olahraga utama lapangan.
    *   `field_type` (string, opsional, enum: `indoor`, `outdoor`): Jenis lapangan (indoor/outdoor).
    *   `floor_type` (string, opsional): Jenis lantai (misal: "Sintetis", "Vinyl", "Semen").
    *   `price_per_hour` (number, wajib, float): Harga sewa per jam.
    *   `field_description` (string, opsional): Deskripsi singkat lapangan.
    *   `field_photos_urls` (array of strings, opsional): Daftar URL ke foto-foto lapangan.
    *   `is_active` (boolean, wajib, default: true): Status aktif/tidak aktif lapangan.
*   **Permissions Dokumen Default:**
    *   Read: `role:super_admin`, `role:player`, `team:[center_id]_admins` (Asumsi Team ID mengikuti pola `sc_[center_id]_admins`)
    *   Create: `role:super_admin`, `team:[center_id]_admins`
    *   Update: `role:super_admin`, `team:[center_id]_admins`
    *   Delete: `role:super_admin`, `team:[center_id]_admins`
*   **Relasi:**
    *   Dimiliki oleh satu `sport_centers` (via `center_id`).
    *   Memiliki banyak `bookings`.
    *   Memiliki banyak `blocked_slots`.

---

**5.4. Collection: `bookings` (Pemesanan)**
*   **Tujuan:** Menyimpan data setiap transaksi pemesanan lapangan.
*   **Atribut:**
    *   `$id` (string, PK, otomatis): ID unik pemesanan.
    *   `$createdAt` (datetime, otomatis): Waktu pemesanan dibuat.
    *   `$updatedAt` (datetime, otomatis): Waktu update terakhir.
    *   `player_user_id` (string, wajib, FK ke `users.$id`, indexed): ID pengguna (pemain) yang memesan.
    *   `center_id` (string, wajib, FK ke `sport_centers.$id`, indexed): ID SC tempat lapangan dipesan (denormalisasi untuk query).
    *   `field_id` (string, wajib, FK ke `fields.$id`, indexed): ID lapangan yang dipesan.
    *   `booking_date` (string, wajib, format "YYYY-MM-DD", indexed): Tanggal bermain.
    *   `start_time` (string, wajib, format "HH:MM", indexed): Waktu mulai bermain.
    *   `end_time` (string, wajib, format "HH:MM", indexed): Waktu selesai bermain.
    *   `duration_hours` (number, wajib, float): Durasi sewa dalam jam.
    *   `total_price` (number, wajib, float): Total harga pemesanan.
    *   `booking_status` (string, wajib, enum: `pending_payment`, `confirmed`, `cancelled_by_player`, `cancelled_by_sc`, `completed`, `payment_rejected`, `waiting_for_sc_confirmation`, indexed): Status pemesanan.
    *   `payment_method` (string, opsional, enum: `on_the_spot`, `bank_transfer`): Metode pembayaran (untuk MVP).
    *   `payment_proof_url` (string, opsional): URL ke bukti pembayaran jika `bank_transfer`.
    *   `player_notes` (string, opsional): Catatan dari pemain.
    *   `sc_notes` (string, opsional): Catatan dari admin SC terkait booking ini.
    *   `booked_by_role` (string, wajib, enum: `player`, `sc_admin`): Siapa yang membuat booking (pemain atau admin SC untuk booking manual).
*   **Permissions Dokumen Default:**
    *   Read: `role:super_admin`, `user:[player_user_id]`, `team:[center_id]_admins`
    *   Create: `role:player`, `team:[center_id]_admins`
    *   Update: `role:super_admin`, `user:[player_user_id]` (hanya untuk status tertentu seperti `cancelled_by_player`), `team:[center_id]_admins` (untuk konfirmasi, pembatalan oleh SC)
    *   Delete: `role:super_admin` (pembatalan keras, jarang digunakan)
*   **Relasi:**
    *   Dibuat oleh satu `users` (pemain).
    *   Merujuk ke satu `fields` dan satu `sport_centers`.

---

**5.5. Collection: `blocked_slots` (Slot Waktu Diblokir Admin SC)**
*   **Tujuan:** Menyimpan informasi slot waktu yang diblokir oleh Admin SC (misal untuk maintenance, acara internal).
*   **Atribut:**
    *   `$id` (string, PK, otomatis): ID unik slot diblokir.
    *   `$createdAt` (datetime, otomatis): Waktu pembuatan.
    *   `$updatedAt` (datetime, otomatis): Waktu update terakhir.
    *   `center_id` (string, wajib, FK ke `sport_centers.$id`, indexed): ID SC.
    *   `field_id` (string, wajib, FK ke `fields.$id`, indexed): ID lapangan yang slotnya diblokir.
    *   `block_date` (string, wajib, format "YYYY-MM-DD", indexed): Tanggal slot diblokir.
    *   `start_time` (string, wajib, format "HH:MM", indexed): Waktu mulai blokir.
    *   `end_time` (string, wajib, format "HH:MM", indexed): Waktu selesai blokir.
    *   `reason` (string, opsional): Alasan pemblokiran slot.
    *   `blocked_by_user_id` (string, wajib, FK ke `users.$id`): ID Admin SC yang melakukan blokir.
*   **Permissions Dokumen Default:**
    *   Read: `role:super_admin`, `role:player` (aplikasi akan query ini untuk menentukan ketersediaan), `team:[center_id]_admins`
    *   Create: `role:super_admin`, `team:[center_id]_admins`
    *   Update: `role:super_admin`, `team:[center_id]_admins`
    *   Delete: `role:super_admin`, `team:[center_id]_admins`
*   **Relasi:**
    *   Merujuk ke satu `fields` dan satu `sport_centers`.
    *   Dibuat oleh satu `users` (Admin SC).

---

## 6. Pertimbangan Desain Antarmuka Pengguna (UI/UX) (UIX001)

*   **UIX001.1 Desain Bersih & Intuitif:** Mengutamakan kemudahan navigasi dan pemahaman. Tata letak yang jelas, penggunaan ikon yang familiar.
*   **UIX001.2 Mobile-First:** Desain dioptimalkan untuk pengalaman pengguna di perangkat mobile (Android & iOS).
*   **UIX001.3 Konsistensi:** Elemen UI (tombol, form, warna) dan alur kerja konsisten di seluruh aplikasi.
*   **UIX001.4 Feedback Visual:** Pengguna harus mendapatkan feedback visual yang jelas atas aksi mereka (misal: loading indicator, pesan sukses/error, animasi transisi halus).
*   **UIX001.5 Aksesibilitas Dasar:** Kontras warna yang baik (WCAG AA), ukuran font yang dapat disesuaikan (jika memungkinkan oleh OS), label yang jelas untuk elemen interaktif.
*   **UIX001.6 Alur Pemesanan Efisien:** Proses dari pencarian hingga konfirmasi booking harus sependek dan sejelas mungkin.
*   **UIX001.7 Tampilan Jadwal Interaktif:** Kalender atau tampilan slot waktu yang mudah digunakan untuk memilih jadwal.

---

## 7. Rencana Rilis (MVP) (RLS001)

Fokus rilis MVP adalah untuk meluncurkan fungsionalitas inti (F001-F011) dan kebutuhan non-fungsional dasar yang telah dijabarkan di atas. Setelah peluncuran, feedback dari pengguna awal (pemain dan SC) akan dikumpulkan secara aktif untuk iterasi dan pengembangan fitur selanjutnya (misal: pembayaran online terintegrasi, fitur membership, ulasan & rating, notifikasi push lebih canggih).

---
*Dokumen ini bersifat hidup dan dapat diperbarui seiring dengan perkembangan proyek.*

