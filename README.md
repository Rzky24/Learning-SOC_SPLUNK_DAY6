# Learning-SOC_SPLUNK_DAY6

SOAL, QUERY, FUNGSI QUERY, BUKTI, & JAWABAN
❓ SOAL 1
Event apa yang sedang dianalisis?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security
| stats count by EventCode

🧠 PENJELASAN QUERY (BARIS DEMI BARIS)

index=windows_logs
➜ Ambil log Windows

sourcetype=wineventlog:security
➜ Fokus ke Security Event Log

stats count by EventCode
➜ Hitung jumlah event berdasarkan ID

✅ HASIL (SESUAI FILE)
4625   4
4624   1

✅ JAWABAN

➡️ EventCode 4625 (Failed Logon) dominan
➡️ Ada 1 event 4624 (Successful Logon) sebagai pembanding

❓ SOAL 2
User mana yang menjadi target login gagal?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by user

🧠 PENJELASAN QUERY

EventCode=4625
➜ Filter hanya login gagal

stats count by user
➜ Lihat akun yang paling sering diserang

✅ HASIL
administrator   4

✅ JAWABAN

➡️ administrator adalah target utama

📌 Akun administrator = high value target

❓ SOAL 3
IP sumber mana yang melakukan percobaan login gagal?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by src_ip

🧠 PENJELASAN QUERY

Mengelompokkan login gagal berdasarkan IP

Digunakan SOC untuk block / blacklist

✅ HASIL
185.220.101.45   4

✅ JAWABAN

➡️ 185.220.101.45 melakukan brute force

❓ SOAL 4
Jenis logon apa yang digunakan penyerang?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by logon_type

🧠 PENJELASAN QUERY

logon_type menjelaskan cara login

SOC pakai ini untuk menentukan remote / lokal

✅ HASIL
3   4

✅ JAWABAN

➡️ Logon Type 3 (Network Logon)

📌 Biasanya via:

SMB

Remote authentication

Serangan dari jaringan

❓ SOAL 5
Apakah ada login yang berhasil? Dan apakah normal?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4624
| table user src_ip logon_type status

🧠 PENJELASAN QUERY

EventCode=4624
➜ Login berhasil

table ...
➜ Tampilkan detail bukti

✅ HASIL
user=rizky  src_ip=192.168.1.10  logon_type=2  status=0x0

✅ JAWABAN

➡️ Login normal

User sah

IP internal

Logon Type 2 (Interactive)

Status sukses

🧪 QUERY KESIMPULAN SOC (DAY 6)
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by src_ip user logon_type

🧠 KEGUNAAN QUERY

Menjawab siapa menyerang

Menyerang akun apa

Menggunakan metode login apa

HASIL SESUAI FILE
185.220.101.45  administrator  3   4

🧾 ATOMIC DOCUMENTATION (FINAL & JUJUR)
Context : Windows Security Log mencatat beberapa failed logon ke server
Action  : Analisis Event ID 4625 menggunakan Splunk berdasarkan IP, user, dan logon type
Result  : IP 185.220.101.45 melakukan brute force network logon ke akun administrator

🏁 STATUS DAY 6

✅ Semua query langsung bisa dijalankan
✅ Semua jawaban keluar dari Splunk
✅ Tidak ada field palsu
