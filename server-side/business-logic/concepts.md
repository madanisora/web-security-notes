# Konsep: Business Logic Vulnerabilities

## Apa itu Business Logic Vulnerabilities?

Business logic vulnerabilities adalah celah yang muncul dari kesalahan desain atau implementasi alur kerja (workflow) sebuah aplikasi, bukan dari bug teknis seperti SQL injection atau XSS. Celah ini memungkinkan penyerang memicu perilaku aplikasi yang tidak diinginkan oleh developer, lalu memanfaatkannya untuk tujuan tertentu (misalnya penipuan, bypass otorisasi, atau manipulasi transaksi).

Istilah "business logic" di sini merujuk pada kumpulan aturan yang mengatur bagaimana aplikasi seharusnya beroperasi — aturan itu tidak selalu terkait langsung dengan "bisnis" dalam arti komersial, sehingga celah ini juga sering disebut *application logic vulnerabilities* atau *logic flaws*.

Karena tidak muncul dari penggunaan normal aplikasi, celah jenis ini sulit ditemukan oleh scanner otomatis. Untuk menemukannya dibutuhkan pemahaman manusia tentang domain bisnis aplikasi dan cara berpikir seorang penyerang — sehingga logic flaws menjadi target favorit bug bounty hunter dan penguji manual.

## Bagaimana Cara Kerjanya?

Business logic vulnerabilities umumnya muncul karena tim desain dan development membuat asumsi yang keliru tentang bagaimana pengguna akan berinteraksi dengan aplikasi. Asumsi yang salah ini sering berujung pada validasi input yang lemah. Contoh klasik: developer berasumsi input hanya akan datang lewat browser, sehingga validasi hanya diletakkan di sisi client — padahal validasi semacam itu mudah dilewati dengan intercepting proxy seperti Burp Suite.

Ketika penyerang menyimpang dari alur penggunaan yang "diharapkan", aplikasi gagal mengantisipasi skenario tersebut dan gagal menanganinya dengan aman. Masalah ini makin parah pada sistem yang kompleks, di mana satu developer mengerjakan satu komponen tanpa benar-benar memahami cara kerja komponen lain — sehingga muncul asumsi yang saling bertabrakan dan tidak terdokumentasi.

Beberapa pola umum eksploitasi business logic:
- Melewati (skip) tahapan tertentu dalam alur transaksi yang seharusnya wajib dilalui secara berurutan.
- Mengirim nilai yang tidak masuk akal (negatif, nol, sangat besar) pada parameter kritikal seperti harga atau kuantitas.
- Memanfaatkan kombinasi fitur yang sah namun tidak pernah dipikirkan bersamaan oleh developer.
- Mengulang suatu aksi berkali-kali (race condition atau abuse dari fitur yang seharusnya sekali pakai).

## Dampak

Dampak dari business logic vulnerabilities sangat bervariasi, mulai dari yang tergolong sepele hingga yang berdampak sangat serius, tergantung fungsionalitas mana yang terkena.

- Jika celah berada pada mekanisme autentikasi, penyerang berpotensi melakukan bypass login atau eskalasi privilege, yang selanjutnya membuka akses ke data dan fungsi sensitif — sekaligus memperluas permukaan serangan (attack surface) untuk eksploitasi lain.
- Jika celah berada pada logika transaksi finansial, dampaknya bisa berupa kerugian besar bagi bisnis lewat pencurian dana atau penipuan.
- Bahkan jika penyerang tidak mendapat keuntungan langsung, logic flaw tetap bisa dipakai untuk merugikan bisnis dengan cara lain (misalnya merusak reputasi atau mengganggu operasional).

Karena dampaknya sulit diprediksi di awal, celah logika yang terlihat "aneh" sebaiknya tetap diperbaiki meskipun cara eksploitasinya belum ditemukan — ada risiko pihak lain akan menemukannya kemudian.

## Contoh Skenario

- **Melewati tahap pembayaran**: alur checkout multi-step yang bergantung pada asumsi client akan selalu mengikuti urutan step 1 → 2 → 3, sehingga penyerang bisa langsung memanggil endpoint step akhir tanpa membayar.
- **Manipulasi harga/kuantitas**: parameter harga atau jumlah barang dikirim dari client tanpa validasi ulang di server, memungkinkan nilai negatif atau desimal yang menghasilkan total harga yang salah.
- **Abuse fitur diskon/kupon**: kupon yang seharusnya sekali pakai bisa dipakai berulang kali karena tidak ada pengecekan status penggunaan di server.
- **Reset password/registrasi yang lemah logikanya**: alur multi-step yang bisa "diloncati" karena setiap step tidak benar-benar memverifikasi bahwa step sebelumnya sudah selesai dengan sah.

## Pencegahan (Best Practice)

- Pastikan developer dan tester benar-benar memahami domain bisnis aplikasi, bukan hanya sisi teknisnya.
- Hindari asumsi implisit tentang perilaku pengguna maupun perilaku komponen lain dalam sistem.
- Validasi ulang semua input di sisi server — jangan pernah percaya validasi client-side saja.
- Dokumentasikan alur data dan transaksi secara jelas, termasuk asumsi yang diambil di setiap tahap.
- Tulis kode sejelas mungkin agar mudah diaudit; pada bagian yang kompleks, lengkapi dengan dokumentasi agar asumsi dan perilaku yang diharapkan mudah dipahami tim lain.
- Perhatikan efek samping (side effects) dari ketergantungan antar komponen jika salah satu di antaranya dimanipulasi dengan cara yang tidak wajar.

