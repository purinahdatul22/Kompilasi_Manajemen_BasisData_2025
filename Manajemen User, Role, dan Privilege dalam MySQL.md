# Manajemen User, Role, dan Privilege dalam MySQL

## 1. Latar Belakang
Manajemen pengguna dalam MySQL berperan penting dalam menjaga keamanan serta mengontrol akses ke database. Dengan pengelolaan yang efektif,
administrator dapat menentukan siapa saja yang dapat mengakses database, menetapkan hak akses untuk setiap pengguna, serta memantau aktivitas mereka.

## 2. Problem
Beberapa tantangan dalam manajemen pengguna di MySQL antara lain:
- **Pengelolaan akun pengguna**: Bagaimana membuat, menghapus, dan mengamankan akun pengguna.
- **Hak akses pengguna**: Memberikan privilege yang sesuai dengan kebutuhan.
- **Manajemen role**: Mengelompokkan hak akses dalam role agar lebih mudah dikelola.
- **Monitoring aktivitas pengguna**: Melacak aktivitas query yang dijalankan untuk keamanan.

## 3. Solusi/Skenario Aktivitas
### a. Pembuatan dan Penghapusan User
- Membuat user baru:
  ```sql
  CREATE USER 'elsa'@'localhost' IDENTIFIED BY '12345678';
  CREATE USER 'puri'@'localhost' IDENTIFIED BY 'puri02';
  CREATE USER 'irfan'@'localhost' IDENTIFIED BY 'irfan03';
  CREATE USER 'rifky'@'localhost' IDENTIFIED BY 'rifky04';
  ```
- Menghapus user:
  ```sql
  DROP USER 'irfan'@'localhost';
  ```

### b. Manajemen Hak Akses dan Privilege
- Memberikan hak akses kepada user:
  ```sql
  GRANT SELECT, INSERT ON database_example.* TO 'user_example'@'localhost';
  ```
- Menampilkan hak akses user:
  ```sql
  SHOW GRANTS FOR 'user_example'@'localhost';
  ```

### c. Manajemen Role
Sejak MySQL 8.0, fitur **role** diperkenalkan untuk mempermudah pengelolaan hak akses:
- Membuat role:
  ```sql
  CREATE ROLE 'role_select_insert';
  ```
- Memberikan privilege ke role:
  ```sql
  GRANT SELECT, INSERT ON database_example.* TO 'role_select_insert';
  ```
- Memberikan role kepada user:
  ```sql
  GRANT 'role_select_insert' TO 'rifky'@'localhost';
  ```
- Menghapus role dari user:
  ```sql
  REVOKE 'role_select_insert' FROM 'rifky'@'localhost';
  ```

### d. Monitoring Akses dan Aktivitas Pengguna
Monitoring dilakukan untuk memeriksa aktivitas pengguna:
- Mengaktifkan general log:
  ```sql
  SET global general_log = 1;
  SET global log_output = 'table';
  ```
- Melihat log aktivitas pengguna:
  ```sql
  SELECT * FROM mysql.general_log;
  ```

## 4. Pembahasan
Dengan menerapkan manajemen user, role, dan privilege yang baik, database menjadi lebih aman dan terstruktur. Administrator dapat lebih mudah mengontrol akses pengguna, menghindari perubahan data yang tidak diinginkan, serta memonitor aktivitas yang mencurigakan.

## 5. Kesimpulan
Manajemen pengguna dalam MySQL sangat penting untuk menjaga integritas dan keamanan database. Dengan menggunakan user, role, privilege, dan monitoring, administrator dapat memastikan akses yang sesuai dan menghindari potensi ancaman keamanan.

## 6. Bukti Dukung Gambar
1.   Lakukan proses pembuatan username sebanyak jumlah kelompok anda. Tuliskan script
dan tampilkan hasilnya.
![1](https://github.com/user-attachments/assets/3be9bd51-b93d-465c-a0cd-199597862be0)

2.   Lakukan penghapusan username terhadap user yang sudah dibuat. Tuliskan script dan tampilkan hasilnya
![2](https://github.com/user-attachments/assets/d8d1ef51-ab90-4364-9e5a-e389efea1476)

3.   Buat role dengan “role_nama_anda_insert_select” → role_andi_select_insert.
 ![3](https://github.com/user-attachments/assets/540a5519-49c5-4879-ace9-a9dcca7ab343)
 
4.   Berikan privilege select, insert kedalam role diatas. 
5.   Buat role dengan “role_nama_anda_create_drop” → role_andi_create_drop.
![5](https://github.com/user-attachments/assets/2f1bbb5e-367c-4ce1-ac6f-7a31cd50ab92)


6.   Berikan privilege create, drop kedalam role diatas.
![6](https://github.com/user-attachments/assets/3bdea653-9f4d-40e9-8799-1f3ff26fd88a)


8.   Berikan 2 user kedalam masing-masing role diatas.
![7](https://github.com/user-attachments/assets/326a676a-faed-4855-ab7f-b78c2525af02)

9.   Lakukan pengujian sebelum dan sesudah user diberikan role.
- BEFORE : <br>
![8a](https://github.com/user-attachments/assets/e5458975-c92d-488e-bc2b-8382e4e51db2)
- AFTER : <br>
![8b](https://github.com/user-attachments/assets/2d8a8612-64e8-40c1-ad46-e96308ceccfd)

9.   Lepas role dari user diatas. Sehingga user menjadi tidak memiliki role.
![9](https://github.com/user-attachments/assets/8e9d5a58-48a8-41d2-a74f-37e6b55bd591)

10.  Lakukan  konfigurasi  untuk  proses  monitoring  proses  seperti  contoh  diatas,  dan  lakukan beberapa kali proses query. Kemudian lihat di log nya dan tampilkan hasilnya.  
- TABEL DI USER IRFAN <br>
![10a](https://github.com/user-attachments/assets/6c6d3d65-df2a-4395-b3d6-da4ab5f67ffc)
- TABEL DI USER ROOT <br>
![10b](https://github.com/user-attachments/assets/b9187698-069a-4c97-bade-fe04d1dbb0e9)

## 7. Sumber Referensi
- https://bif.telkomuniversity.ac.id/apa-itu-mysql/
