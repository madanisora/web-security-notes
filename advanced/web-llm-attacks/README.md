# Web LLM Attacks

## 📖 Overview
Catatan belajar tentang celah keamanan di aplikasi web yang mengintegrasikan LLM (chatbot/AI assistant), khususnya teknik **prompt injection** dan **excessive agency** — kondisi di mana LLM diberi akses API sensitif yang bisa disalahgunakan lewat manipulasi prompt.

## 📚 Konsep Utama
Lihat [concepts.md](concepts.md) untuk penjelasan lebih detail (apa itu prompt injection, cara kerja LLM-API integration, dan cara memetakan attack surface-nya).

## 🧪 Labs
| Lab | Status | Write-up |
|---|---|---|
| Exploiting LLM APIs with excessive agency | ✅ Solved | [lab01-excessive-agency-api.md](labs/lab01-excessive-agency-api.md) |

## 🛡️ Mitigasi
- Terapkan prinsip **least privilege**: LLM cuma dikasih akses ke API/fungsi yang benar-benar dia butuhkan, bukan API sensitif seperti raw SQL executor.
- Jangan pernah kasih LLM akses langsung untuk menjalankan query SQL mentah — selalu lewat fungsi terbatas dengan parameterized query.
- Tambahkan **human-in-the-loop confirmation** untuk aksi sensitif (reset password, hapus akun, ubah data) sebelum benar-benar dieksekusi.
- Validasi & sanitasi output LLM sebelum dipakai sebagai argumen API, jangan percaya mentah-mentah apa yang "diputuskan" LLM.
- Pisahkan konteks user (untrusted) dari instruksi sistem (trusted) sejelas mungkin supaya LLM tidak gampang tertukar mana perintah asli developer dan mana input dari user biasa.
- Audit & log semua pemanggilan API oleh LLM, supaya penyalahgunaan bisa terdeteksi lewat monitoring.

## 🔗 Referensi
- [PortSwigger Web Security Academy — Web LLM attacks](https://portswigger.net/web-security/llm-attacks)
