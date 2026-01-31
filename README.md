# Learning-SOC_SPLUNK_DAY6

# SOAL, QUERY, FUNGSI QUERY, BUKTI, & JAWABAN
 # SOAL 1
Event apa yang sedang dianalisis?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security
| stats count by EventCode

<img width="1357" height="562" alt="image" src="https://github.com/user-attachments/assets/28327e8c-7182-4d0a-9f01-47a44ed9a454" />


# PENJELASAN QUERY (BARIS DEMI BARIS)

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

# SOAL 2
User mana yang menjadi target login gagal?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by user

<img width="1364" height="542" alt="image" src="https://github.com/user-attachments/assets/58da3a03-f1ba-4e15-8c8f-7ab7f3f6f3ed" />


 PENJELASAN QUERY

EventCode=4625
➜ Filter hanya login gagal

stats count by user
➜ Lihat akun yang paling sering diserang

✅ HASIL
administrator   4

✅ JAWABAN

➡️ administrator adalah target utama

📌 Akun administrator = high value target

# SOAL 3
IP sumber mana yang melakukan percobaan login gagal?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by src_ip

<img width="1364" height="542" alt="image" src="https://github.com/user-attachments/assets/3c90f0b0-972f-4b3c-b195-b083b79afa0c" />


 PENJELASAN QUERY

Mengelompokkan login gagal berdasarkan IP

Digunakan SOC untuk block / blacklist

✅ HASIL
185.220.101.45   4

✅ JAWABAN

➡️ 185.220.101.45 melakukan brute force

# SOAL 4
Jenis logon apa yang digunakan penyerang?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by logon_type

<img width="1351" height="480" alt="image" src="https://github.com/user-attachments/assets/6690b233-8da6-40a5-b5a3-d51ed7882c48" />


 PENJELASAN QUERY

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

# SOAL 5
Apakah ada login yang berhasil? Dan apakah normal?
🔹 QUERY
index=windows_logs sourcetype=wineventlog:security EventCode=4624
| table user src_ip logon_type status

<img width="1358" height="485" alt="image" src="https://github.com/user-attachments/assets/3d8e8898-f38a-413d-810b-2137d801bd76" />


 PENJELASAN QUERY

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

# QUERY KESIMPULAN SOC (DAY 6)
index=windows_logs sourcetype=wineventlog:security EventCode=4625
| stats count by src_ip user logon_type

<img width="1358" height="511" alt="image" src="https://github.com/user-attachments/assets/77cca808-c066-4bd1-b411-8bbf965a9b62" />


 KEGUNAAN QUERY

Menjawab siapa menyerang

Menyerang akun apa

Menggunakan metode login apa

HASIL SESUAI FILE
185.220.101.45  administrator  3   4

🧾 # ATOMIC DOCUMENTATION (FINAL & JUJUR)
Context : Windows Security Log mencatat beberapa failed logon ke server
Action  : Analisis Event ID 4625 menggunakan Splunk berdasarkan IP, user, dan logon type
Result  : IP 185.220.101.45 melakukan brute force network logon ke akun administrator

🏁 STATUS DAY 6

✅ Semua query langsung bisa dijalankan
✅ Semua jawaban keluar dari Splunk
✅ Tidak ada field palsu

contoh file latihan 
FILE LOG (SUMBER DATA)

⬇️ UPLOAD DATA INI KE SPLUNK

2025-01-25 09:15:01 EventCode=4625 host=WIN-SERVER user=administrator src_ip=185.220.101.45 logon_type=3 status=0xC000006A
2025-01-25 09:15:05 EventCode=4625 host=WIN-SERVER user=administrator src_ip=185.220.101.45 logon_type=3 status=0xC000006A
2025-01-25 09:15:09 EventCode=4625 host=WIN-SERVER user=administrator src_ip=185.220.101.45 logon_type=3 status=0xC000006A
2025-01-25 09:15:12 EventCode=4625 host=WIN-SERVER user=administrator src_ip=185.220.101.45 logon_type=3 status=0xC000006A

2025-01-25 09:20:44 EventCode=4624 host=WIN-SERVER user=rizky src_ip=192.168.1.10 logon_type=2 status=0x0

Nama file latihan

SOC_SPLUNK_DAY6_Windows_Failed_Logon.txt


Index

windows_logs


Sourcetype

wineventlog:security

