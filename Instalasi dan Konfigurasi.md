# Instalasi dan Konfigurasi MySQL: Panduan Lengkap

## 1. Latar Belakang
MySQL adalah sistem manajemen basis data berbasis SQL (Structured Query Language) yang memungkinkan pengguna untuk menyimpan, mengelola, dan mengambil data dengan cara yang terstruktur.
Sebagai perangkat lunak open-source, MySQL memberikan kebebasan kepada pengembang untuk memodifikasi dan mendistribusikan perangkat lunak ini sesuai kebutuhan.

## 2. Problem
Beberapa tantangan yang sering muncul saat menginstal dan mengonfigurasi MySQL:
- Kesalahan instalasi akibat dependensi yang tidak terpenuhi.
- Penggunaan port default (3306) yang mungkin konflik dengan layanan lain.
- Konfigurasi yang tidak optimal, seperti ukuran buffer yang terlalu kecil.
- Keamanan MySQL yang lemah jika password root tidak diperbarui.

## 3. Solusi/Skenario Aktivitas
Berikut langkah-langkah untuk menginstal dan mengonfigurasi MySQL secara optimal:

### a. Instalasi MySQL
1. Buka browser dan cari "Download MySQL".
2. Pilih "MySQL Community (GPL) Downloads".
3. Unduh "MySQL Installer for Windows".
4. Pilih opsi **Server Only** dan klik **Next**.
5. Klik **Execute** untuk memulai instalasi.
6. Setelah instalasi selesai, lanjutkan dengan konfigurasi awal.

### b. Mengubah Port MySQL dari 3306 ke 3309
1. Masuk ke MySQL menggunakan **Command Prompt**.
2. Cek port saat ini dengan perintah:
   ```sql
   SHOW VARIABLES LIKE 'port';
   ```
3. Cari file `my.ini` menggunakan `%PROGRAMDATA%`.
4. Buka file `my.ini` dan ubah nilai `port` dari `3306` ke `3309`.
5. Stop MySQL sebelum menyimpan perubahan.
6. Restart MySQL dan cek kembali port dengan perintah yang sama.

### c. Mengubah `innodb_buffer_pool_size`
1. Buka file `my.ini`.
2. Cari parameter `innodb_buffer_pool_size`.
3. Ubah dari `16M` menjadi **25% dari total RAM** yang tersedia.
4. Restart MySQL untuk menerapkan perubahan.
5. Cek perubahan dengan perintah:
   ```sql
   SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
   ```

### d. Mengubah Password Root MySQL
1. Masuk ke MySQL sebagai root dengan:
   ```sh
   mysql -u root -p
   ```
2. Ubah password dengan perintah:
   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'password_baru';
   ```
3. Verifikasi perubahan dengan login ulang.

### e. Membuat Database Baru
1. Buat database dengan format `kelompok_AB_nama_mhs`:
   ```sql
   CREATE DATABASE kelompok_A1_irfan;
   ```
2. Cek apakah database berhasil dibuat:
   ```sql
   SHOW DATABASES;
   ```

## 4. Pembahasan
Konfigurasi MySQL yang tepat memastikan kinerja dan keamanan database tetap optimal. Mengubah port server menghindari konflik dengan layanan lain,
sementara penyesuaian innodb_buffer_pool_size meningkatkan performa query. Selain itu, memperbarui password root sangat penting untuk mencegah akses tidak sah.
Pembuatan database dengan format terstruktur juga mempermudah pengelolaan data.

## 5. Kesimpulan
Dengan konfigurasi yang sesuai, MySQL dapat berjalan lebih stabil dan aman. Langkah-langkah seperti mengganti port, menyesuaikan buffer,
dan mengamankan akses root membantu mencegah masalah umum serta mempermudah troubleshooting di masa depan.

## 6. Bukti Dukung Gambar
Konfigurasi pada MySQL
a.   port dari default 3306 menjadi 3309
−    Masuk ke mysql menggunakan command prompt
 ![image](https://github.com/user-attachments/assets/ea42ffe9-8834-4923-8a72-c3616e1bd85d)
 
−    Cek port menggunakan show variables like ‘port’; (before)
![image](https://github.com/user-attachments/assets/62666f6b-c523-4133-95f4-7767ab6d8a9e)

−    Letak my.ini <br>
![image](https://github.com/user-attachments/assets/e9cf1d75-ef37-46b7-88cd-dddbe7d95b53)

![image](https://github.com/user-attachments/assets/4b249dd7-6245-475a-a377-4c67045ad228)

−    Buka my.ini lalu cari port 3306

![image](https://github.com/user-attachments/assets/3473fe24-e81c-4725-8904-fbc72d2fc246)

−    Sebelum ganti port menjadi 3309, stop mysql terlebih dahulu di Terminal Admin
![image](https://github.com/user-attachments/assets/edcbb942-14ac-4b71-a83e-bd1a8b7269e4)

- Setelah itu ubah port <br>
![image](https://github.com/user-attachments/assets/d9fd463e-d68d-4389-a670-e653730fd56a)

-    Setelah port diubah, jalan kan lagi mysql di Terminal Admin
![image](https://github.com/user-attachments/assets/186b6a41-3e25-40e2-87a2-c2c45d09921c)

−    Kemudian jalankan lagi untuk cek port yang telah diubah (After)
![image](https://github.com/user-attachments/assets/9002d413-90a8-4dff-867e-f6730bdcaf0c)

b.   innodb_buffer_pool_size dr default 16M (menjadi 25% dari RAM )
−    Cara untuk mengubah innodb sama seperti mengubah port, jadi cari innodb

![image](https://github.com/user-attachments/assets/9a0794ee-7c76-411a-8f11-d7542c26e34a)


-  Kemudian sesuai dengan kapasitas RAM Laptop masing masing
![image](https://github.com/user-attachments/assets/643e9482-158d-4d5e-967d-53a2c159daff)


-	Cek innodb dengan SHOW VARIABLES LIKE 'innodb_buffer_pool_size'; di command prompt (Before)
![image](https://github.com/user-attachments/assets/fe9743e3-046d-4b2e-b662-daa61b8c5c37)

−    Cek innodb setelah innodb diubah (Ater)
![image](https://github.com/user-attachments/assets/d64d5710-913e-473e-9091-bab2e4a635fa)

6.   lakukan perubahan terhadap password root.
![image](https://github.com/user-attachments/assets/7a9d5875-a1ae-4072-b80f-9f2dab064877)

7.   Buat database dengan nama: kelompok_AB_nama_mhs
![image](https://github.com/user-attachments/assets/400e36d4-d116-4d49-a488-941a88ec2ece)


## 7. Sumber Referensi
- https://bif.telkomuniversity.ac.id/apa-itu-mysql/
