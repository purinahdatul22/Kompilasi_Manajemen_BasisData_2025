# Optimasi Bottleneck dalam MySQL

## 1. Latar Belakang
Bottleneck dalam MySQL terjadi ketika performa database menurun karena keterbatasan sumber daya atau eksekusi query yang tidak efisien.
Penyebab utama bottleneck meliputi query yang lambat, kurangnya indeks, konfigurasi server yang tidak optimal, serta penggunaan sumber daya yang tidak efisien.
Identifikasi dan optimasi bottleneck sangat penting untuk memastikan MySQL dapat beroperasi dengan efisien.

## 2. Penyebab Umum Bottleneck
1. **Full Table Scan** karena tidak adanya indeks.
2. **Jumlah koneksi yang terlalu banyak** menyebabkan overload server.
3. **Masalah locking dalam transaksi**, terutama pada operasi UPDATE atau DELETE.
4. **Ukuran buffer pool yang terlalu kecil**, mengakibatkan MySQL sering membaca dari disk.
5. **Penggunaan SELECT * tanpa spesifikasi kolom**, yang mengambil data lebih banyak dari yang diperlukan.
6. **Replikasi database yang tidak terkelola dengan baik**, menyebabkan bottleneck saat sinkronisasi data.

## 3. Contoh Bottleneck dalam MySQL
### 3.1 Full Table Scan Karena Tidak Ada Indeks
**Query Bermasalah:**
```sql
SELECT * FROM orders WHERE customer_id = 1001;
```
Jika kolom `customer_id` tidak memiliki indeks, MySQL akan melakukan full table scan.

**Solusi:**
```sql
CREATE INDEX idx_customer_id ON orders(customer_id);
```

### 3.2 Banyak Koneksi Database yang Tidak Dikelola dengan Baik
Terlalu banyak koneksi terbuka yang tidak ditutup dapat menyebabkan error:
```
ERROR 1040: Too many connections
```
**Solusi:** Batasi jumlah koneksi dan optimalkan pool koneksi aplikasi.

### 3.3 Deadlock Karena Transaksi yang Berjalan Bersamaan
**Query Bermasalah:**
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```
**Solusi:** Gunakan **LOCK TABLES** atau **optimalkan urutan transaksi**.

### 3.4 Ukuran InnoDB Buffer Pool yang Kecil
Jika buffer pool kecil, MySQL akan sering membaca dari disk, memperlambat akses data.

**Solusi:**
```sql
SET GLOBAL innodb_buffer_pool_size = 4G;
```

### 3.5 Penggunaan SELECT * yang Tidak Efisien
**Query Bermasalah:**
```sql
SELECT * FROM users;
```
**Solusi:**
```sql
SELECT id, nama, email FROM users;
```

## 4. Teknik Optimasi Bottleneck
### 4.1 Menambahkan Indeks
Indeks membantu mempercepat pencarian data dengan mengurangi jumlah baris yang dipindai.

**Cara Mengecek Indeks:**
```sql
SHOW INDEX FROM nama_tabel;
```

**Menambahkan Indeks:**
```sql
CREATE INDEX idx_nama ON users(nama);
```

**Menggunakan EXPLAIN untuk Evaluasi Indeks:**
```sql
EXPLAIN SELECT * FROM users WHERE nama = 'John';
```
Jika `type = ALL`, artinya MySQL masih melakukan full table scan.

### 4.2 Menggunakan Query Cache
Query cache menyimpan hasil query agar tidak perlu dihitung ulang.

**Aktifkan Query Cache:**
```ini
[mysqld]
query_cache_type = 1
query_cache_size = 64M
query_cache_limit = 2M
```

### 4.3 Meningkatkan Buffer Pool untuk InnoDB
Buffer Pool digunakan untuk menyimpan data dan indeks yang sering diakses.

**Cek Ukuran Saat Ini:**
```sql
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
```

**Atur Buffer Pool:**
```ini
[mysqld]
innodb_buffer_pool_size = 5G
```

### 4.4 Menghindari SELECT *
Selalu ambil hanya kolom yang diperlukan untuk menghemat sumber daya.

## 5. Studi Kasus dan Soal Latihan
### 5.1 Optimasi pada Database Minimarket
Optimalkan tabel `tr_penjualan_raw` dengan indeks dan query cache.

**Menambahkan Indeks:**
```sql
CREATE INDEX idx_tgl_transaksi ON tr_penjualan_raw(YEAR(tgl_transaksi));
```
Hasil <br>
![1](https://github.com/user-attachments/assets/ea9f1aa0-d361-4e91-ae7f-2d49720f9918)

Melihat status
```sql
SHOW ENGINE INNODB STATUS;
```
![2](https://github.com/user-attachments/assets/18d2a085-b34c-4f3d-9f4b-78dbf7edc5aa)

### 5.2 Evaluasi Query
#### a) Query dengan Filter Tahun
**Query Bermasalah:**
```sql
SELECT * FROM tr_penjualan_raw WHERE YEAR(tgl_transaksi) = 2024;
```
Hasil <br>
![3](https://github.com/user-attachments/assets/a06851ab-0b0f-4788-8f1c-4c5f915da0b1)

**Solusi:**
```sql
CREATE INDEX idx_tgl_transaksi ON tr_penjualan_raw(tgl_transaksi);
SELECT * FROM tr_penjualan_raw WHERE tgl_transaksi BETWEEN '2024-01-01' AND '2024-12-31';
```
Hasil <br>
![4](https://github.com/user-attachments/assets/9b31e5fc-4da9-409e-9bdb-567baf1251d9)

#### b) Query dengan Banyak Item dalam IN
**Query Bermasalah:**
```sql
SELECT * FROM tr_penjualan_raw 
WHERE kode_item IN (
    'ITM-038', 'ITM-020', 'ITM-017', 'ITM-007', 'ITM-015', 
    'ITM-006', 'ITM-035', 'ITM-034', 'ITM-022', 'ITM-021', 
    'ITM-005', 'ITM-012', 'ITM-009', 'ITM-031'
);
```
Hasil<br>
![5](https://github.com/user-attachments/assets/780caa61-d59e-4228-92c6-cce26874383e)

**Solusi:**
```sql
SELECT tgl_transaksi, kode_cabang, kode_kasir, kode_item FROM tr_penjualan_raw 
WHERE kode_item IN ('ITM-038', 'ITM-020', 'ITM-017', 'ITM-007', 'ITM-015', 
    'ITM-006', 'ITM-035', 'ITM-034', 'ITM-022', 'ITM-021', 
    'ITM-005', 'ITM-012', 'ITM-009', 'ITM-031'
);

```
Hasil<br>
![6](https://github.com/user-attachments/assets/95a99da1-4f99-43b6-9fff-3dd8cb4600a8)

#### c) Query dengan LIKE
**Query Bermasalah:**
```sql
SELECT * FROM tr_penjualan_raw WHERE nama_kasir LIKE '%Galang%';
```
Hasil <br>
![7](https://github.com/user-attachments/assets/7ac86ecf-c5af-467f-ba5d-9f29d73cbab4)

**Solusi:**
Tambahkan indeks **FULLTEXT**:
Untuk Menambahkan Full-Text Index pada kolom nama_kasir.Full-Text Index digunakan untuk pencarian teks yang lebih kompleks dibandingkan dengan pencarian biasa menggunakan LIKE.
```sql
ALTER TABLE tr_penjualan_raw ADD FULLTEXT(nama_kasir);
UPDATE tr_penjualan_raw SET nama_kasir = 'Sari' WHERE kode_kasir = '039-212';
```
```sql
SELECT * FROM tr_penjualan_raw WHERE nama_kasir LIKE 'Sari';
```
Hasil <br>
![8](https://github.com/user-attachments/assets/9e63132d-addd-40aa-9716-610a3b3c1c57)

#### d) Query MAX(harga) Tanpa Indeks
**Query Bermasalah:**
```sql
SELECT MAX(harga) FROM tr_penjualan_raw WHERE kode_cabang = 'CB001';
```
Hasil<br>
![9](https://github.com/user-attachments/assets/2cc6f18a-0502-412a-8095-8d22b2655d57)

**Solusi 1:**
```sql
UPDATE tr_penjualan_raw SET harga = 20000 WHERE kode_kasir = '039-212';
```
Hasil<br>
![10](https://github.com/user-attachments/assets/9aebffcf-d41a-44d4-b8e4-0954e2564253)

**Solusi 2:**
```sql
SELECT * FROM tr_penjualan_raw WHERE nama_kasir LIKE '%Sari%';
```
Hasil<br>
![11](https://github.com/user-attachments/assets/bba56b59-a533-405d-b632-065091f940c0)

## 6. Kesimpulan
Bottleneck dalam MySQL dapat diatasi dengan optimasi indeks, konfigurasi buffer pool, penggunaan query cache, dan penulisan query yang lebih efisien.
Dengan teknik ini, performa database dapat ditingkatkan secara signifikan, mengurangi waktu eksekusi query, serta mengoptimalkan penggunaan sumber daya server.

---

**Referensi:**
- [Praktikum Sistem Manajemen Basis Data](https://drive.google.com/file/d/1BNdP1PwjGzMLrUwQHjMxg4IuHGvK0i8f/view?usp=sharing)
