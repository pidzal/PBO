📘 README.md — Analisis Refactoring Sistem Notifikasi
1. Deskripsi Singkat Studi Kasus

Sistem notifikasi digunakan untuk mengirim pesan melalui berbagai kanal seperti Email, SMS, dan WhatsApp.
Sebelum dilakukan refactoring, seluruh logika notifikasi ditangani di dalam satu kelas bernama NotificationManager.
Kondisi ini menyebabkan banyak pelanggaran prinsip SOLID.

## ❌ Analisis Pelanggaran SOLID
### 1. Pelanggaran SRP (Single Responsibility Principle)

Masalah:
Class NotificationManager menangani terlalu banyak tanggung jawab sekaligus, yaitu:
- Menentukan jenis notifikasi (email/sms/whatsapp)
- Menjalankan logika pengiriman pesan
- Menampilkan hasil notifikasi
- Melakukan validasi tipe notifikasi
Kenapa melanggar?
SRP mengatakan class hanya boleh memiliki satu alasan untuk berubah.
Pada kasus ini:
- Jika format email berubah → class harus diubah
- Jika format SMS berubah → class harus diubah
- Jika WhatsApp ditambah fitur baru → class harus diubah
Class menjadi besar, sulit dirawat, dan rawan bug.

### 2. Pelanggaran OCP (Open/Closed Principle)

Masalah:
- Untuk menambah jenis notifikasi baru (misalnya Telegram), programmer harus:
- Membuka file NotificationManager
- Menambah elif baru di dalam send_notification()
- Menambahkan logika baru di class yang sama

Contoh kode buruk:

if notif_type == "email": ...
elif notif_type == "sms": ...
elif notif_type == "whatsapp": ...


Kenapa melanggar?
OCP menyatakan bahwa:
Sistem harus terbuka untuk ditambah, tetapi tertutup terhadap modifikasi.

Artinya jika ingin menambah fitur, kita tidak boleh mengubah kode lama.
Pada kode awal, menambahkan satu metode baru = harus mengedit bagian if/else.

Ini membuktikan class tidak memenuhi OCP.

### 3. Pelanggaran DIP (Dependency Inversion Principle)

Masalah:
NotificationManager bergantung secara langsung pada class konkret seperti:
- EmailNotification
- SMSNotification
- WhatsAppNotification
Semua implementasi hardcoded ke dalam satu fungsi.

Kenapa melanggar?
DIP mensyaratkan:
High-level module harus bergantung pada abstraksi, bukan implementasi konkret.
Namun pada kode buruk:
- NotificationManager mengatur detail pengiriman email/sms
- Tidak ada interface/abstraksi sebagai perantara
- Perubahan implementasi notifikasi akan mempengaruhi kode utama

Akibatnya sistem sulit diperluas dan diuji.

## ✔️ Kesimpulan

Kode awal melanggar 3 prinsip SOLID utama:

Prinsip	Status	Penyebab
SRP	❌ Dilanggar	Satu class mengerjakan terlalu banyak hal
OCP	❌ Dilanggar	Penambahan fitur baru harus mengubah kode lama
DIP	❌ Dilanggar	Bergantung pada implementasi, bukan abstraksi
## ✔️ Solusi (Hasil Refactoring)

Setelah direfactor:

- Dibuat interface INotificationSender → DIP terpenuhi
- Dibuat class terpisah: EmailNotification, SMSNotification, WhatsAppNotification → SRP terpenuhi
- NotificationService hanya menerima interface dan tidak diubah saat menambah Telegram → OCP terpenuhi

Dengan refactoring ini:
- Kode lebih bersih
- Mudah diperluas (plugin style)
- Lebih mudah diuji (mock interface)
- Lebih stabil dan scalable