# **Optimasi Database dengan Partisi Tabel di MySQL**  

## **Latar Belakang**  
Optimasi database merupakan cara untuk memperbaiki kinerja dan meningkatkannya. Upaya ini dilakukan dengan memaksimalkan struktur data, indeks, dan query.
Dalam proses optimasi, kecepatan dari transaksi menjadi fokus perbaikan. Oleh sebab itu, optimasi sangatlah penting karena hal itu berkaitan dengan waktu yang
dibutuhkan oleh database untuk menangani semua permintaan yang ditujukan kepadanya.

## **Problem**  
Seiring pertumbuhan data dalam tabel yang besar, beberapa masalah umum yang sering muncul antara lain:  
1. **Query Lambat** – Saat jumlah data meningkat, pencarian berdasarkan kondisi tertentu menjadi lebih lambat.  
2. **Pengelolaan Data Sulit** – Pemeliharaan dan backup data menjadi lebih kompleks.  
3. **Beban Server Tinggi** – Karena semua data disimpan dalam satu tabel besar, penggunaan sumber daya server meningkat drastis.  

## **Solusi: Menggunakan Partisi Tabel**  
Partisi tabel memungkinkan kita untuk membagi data menjadi beberapa bagian berdasarkan kondisi tertentu, seperti:  
- **Range Partitioning** (berdasarkan rentang nilai)  
- **List Partitioning** (berdasarkan nilai yang spesifik)  
- **Hash Partitioning** (berdasarkan hash function)  
- **Key Partitioning** (serupa dengan hash tetapi berbasis primary key)  

### **1. Range Partitioning**  
Digunakan ketika data dapat dibagi ke dalam rentang tertentu. Misalnya, mempartisi tabel transaksi berdasarkan tahun:  

```sql
CREATE TABLE transaksi (
    id INT NOT NULL,
    tanggal DATE NOT NULL,
    jumlah DECIMAL(10,2),
    PRIMARY KEY (id, tanggal)
) PARTITION BY RANGE (YEAR(tanggal)) (
    PARTITION p2019 VALUES LESS THAN (2020),
    PARTITION p2020 VALUES LESS THAN (2021),
    PARTITION p2021 VALUES LESS THAN (2022)
);
```

### **2. List Partitioning**  
Digunakan untuk membagi data berdasarkan nilai yang spesifik, misalnya berdasarkan kode wilayah:  

```sql
CREATE TABLE pelanggan (
    id INT NOT NULL,
    nama VARCHAR(100) NOT NULL,
    kode_wilayah INT NOT NULL,
    PRIMARY KEY (id, kode_wilayah)
) PARTITION BY LIST (kode_wilayah) (
    PARTITION pJawa VALUES IN (1, 2, 3),
    PARTITION pSumatra VALUES IN (4, 5, 6)
);
```

### **3. Hash Partitioning**  
Jika data tidak dapat dibagi secara logis berdasarkan rentang atau daftar, hash partitioning bisa digunakan:  

```sql
CREATE TABLE orders (
    id INT NOT NULL,
    customer_id INT NOT NULL,
    total DECIMAL(10,2),
    PRIMARY KEY (id, customer_id)
) PARTITION BY HASH(customer_id) PARTITIONS 4;
```

### **4. Key Partitioning**  
Digunakan ketika ingin membagi data berdasarkan primary key secara otomatis oleh MySQL:  

```sql
CREATE TABLE users (
    id INT NOT NULL,
    username VARCHAR(50) NOT NULL,
    PRIMARY KEY (id)
) PARTITION BY KEY() PARTITIONS 3;
```

## **Pembahasan**  
Dengan menerapkan partisi tabel, kita bisa mendapatkan keuntungan berikut:  
- **Peningkatan Performa Query** – Query yang mengakses hanya satu partisi akan lebih cepat dibandingkan mengakses keseluruhan tabel.
- **Efisiensi Penyimpanan** – Data dapat disimpan lebih terorganisir, misalnya dalam storage yang berbeda.  
- **Manajemen Data Lebih Mudah** – Partisi dapat dihapus atau ditambahkan secara independen tanpa mempengaruhi seluruh tabel.  

Namun, ada beberapa hal yang perlu diperhatikan dalam penggunaan partisi:  
- **Tidak Semua Query Mendukung Partisi** – Beberapa operasi seperti `JOIN` dan `FOREIGN KEY` bisa menjadi lebih kompleks.
- **Overhead Administrasi** – Memerlukan perencanaan awal yang matang untuk menentukan strategi partisi yang optimal.  

## **Kesimpulan**  
Partisi tabel adalah salah satu teknik optimasi database yang sangat berguna dalam menangani data dalam jumlah besar. Dengan memilih strategi partisi yang tepat, kita dapat meningkatkan performa query, mengurangi beban server, dan membuat pengelolaan data lebih efisien.  

## **Bukti Dukung**  
1. Jalankan semua contoh script diatas sampai anda benar-benar memahami konsep  partisi, cara kerja partisi, manfaat partisi tabel.
- Partisi Range <br>
![1](https://github.com/user-attachments/assets/cad16f80-786c-4876-a114-af2cbfdddcc6)

- Partisi List <br>
Hasil Cek partisi yang digunakan
![2](https://github.com/user-attachments/assets/bef26d7e-76c7-4164-a66c-541eaeff1150)

Menjalankan Query dengan Optimasi Partisi 
```sql
SELECT * FROM transactions WHERE region_id = 2; 
```
![3](https://github.com/user-attachments/assets/5cfec6cf-ceb8-4d35-800c-6158b93685c3)

2. Pada tabel tr_penjualan, lakukan scenario sebagai berikut: 
a. Redesaian tabel tr_penjualan, tambahkan partisi pada tabel tersebut. Sehingga  ada tabel baru tr_penjualan_partisi
```sql
CREATE TABLE tr_penjualan_partisi ( 
 tgl_transaksi DATETIME DEFAULT NULL, 
 kode_cabang VARCHAR(10) DEFAULT NULL, 
 kode_kasir VARCHAR(10) DEFAULT NULL, 
 kode_item VARCHAR(7) DEFAULT NULL, 
 kode_produk VARCHAR(12) DEFAULT NULL, 
 jumlah_pembelian INT(11) DEFAULT NULL,  
 harga INT(6) DEFAULT NULL 
) 
PARTITION BY RANGE (YEAR(tgl_transaksi)) ( 
 PARTITION p0 VALUES LESS THAN (2008), 
 PARTITION p1 VALUES LESS THAN (2009), 
 PARTITION p2 VALUES LESS THAN (2010), 
 PARTITION p3 VALUES LESS THAN (2011), 
 PARTITION p4 VALUES LESS THAN (2012), 
 PARTITION p5 VALUES LESS THAN (2013), 
 PARTITION p6 VALUES LESS THAN (2014), 
 PARTITION p7 VALUES LESS THAN (2015) 
);
```
Menambahkan data ke table tr_penjualan_partisi dari tahun 2008 - 2014
```sql
INSERT INTO tr_penjualan_partisi (tgl_transaksi, kode_cabang, kode_kasir, kode_item,  kode_produk, jumlah_pembelian, harga) ( 
SELECT tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, harga_produk FROM tr_penjualan )
```
Query record sesuai partisi akan ditampilkan seperti  berikut:
```sql
SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS 
FROM INFORMATION_SCHEMA.PARTITIONS 
WHERE TABLE_NAME = 'tr_penjualan_partisi'; 
```
Hasil <br>
![4](https://github.com/user-attachments/assets/a20b4820-0354-4490-beb2-8f406f979319)

b. Redesaian tabel tr_penjualan, tambahkan partisi pada tabel tersebut. Sehingga  ada tabel baru tr_penjualan_raw
```sql
CREATE TABLE tr_penjualan_raw ( 
 tgl_transaksi DATETIME DEFAULT NULL, 
 kode_cabang VARCHAR(10) DEFAULT NULL, 
 kode_kasir VARCHAR(10) DEFAULT NULL, 
 kode_item VARCHAR(7) DEFAULT NULL, 
 kode_produk VARCHAR(12) DEFAULT NULL, 
 jumlah_pembelian INT(11) DEFAULT NULL,  
 harga INT(6) DEFAULT NULL 
)

```
Menambahkan data ke table tr_penjualan_raw dari tahun 2008 - 2014
```sql
INSERT INTO tr_penjualan_raw (tgl_transaksi, kode_cabang, kode_kasir, kode_item,  kode_produk, jumlah_pembelian, harga) ( 
SELECT tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, harga_produk FROM tr_penjualan )
```
Query record sesuai partisi akan ditampilkan seperti  berikut:
```sql
SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS 
FROM INFORMATION_SCHEMA.PARTITIONS 
WHERE TABLE_NAME = 'tr_penjualan_raw'; 
```
Hasil <br>
![5](https://github.com/user-attachments/assets/b9e74ee1-4c94-42e2-971e-9fec9bfd6126)

c. Pengujian tabel 
Jalankan query berikut dengan perulangan 10x. lakukan pencatatan waktu running setiap query. Dan ambil rata-ratanya.

Table Raw
```sql
SELECT * FROM tr_penjualan_raw WHERE tgl_transaksi > DATE('2010-08-01') AND tgl_transaksi < DATE('2011-07-31') 
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 16,253   |
| 2         | 16,168   |
| 3         | 14,856   |
| 4         | 16,659   |
| 5         | 16,012   |
| 6         | 16,566   |
| 7         | 14,881   |
| 8         | 16,557   |
| 9         | 16,261   |
| 10        | 16,348   |
**Rata-rata: 16,056 s**


Table Partisi
```sql
SELECT * FROM tr_penjualan_partisi WHERE tgl_transaksi > DATE('2010-08-01') AND tgl_transaksi < DATE('2011-07-31')
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 15,058   |
| 2         | 15,505   |
| 3         | 15,406   |
| 4         | 16,135   |
| 5         | 15,097   |
| 6         | 14,751   |
| 7         | 14,552   |
| 8         | 14,253   |
| 9         | 16,221   |
| 10        | 14,403   |
**Rata-rata: 15,138 s**


Table Raw
```sql
SELECT * FROM tr_penjualan_raw WHERE kode_cabang = 'CABANG-039'; 
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 38,963   |
| 2         | 37,89    |
| 3         | 36,847   |
| 4         | 37,66    |
| 5         | 37,598   |
| 6         | 36,761   |
| 7         | 37,051   |
| 8         | 37,106   |
| 9         | 37,106   |
| 10        | 37,559   |
**Rata-rata: 37,454 s**


Table Partisi
```sql
SELECT * FROM tr_penjualan_partisi WHERE kode_cabang = 'CABANG-039';
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 31,764   |
| 2         | 32,551   |
| 3         | 33,321   |
| 4         | 34,761   |
| 5         | 30,781   |
| 6         | 30,841   |
| 7         | 33,831   |
| 8         | 34,971   |
| 9         | 35,821   |
| 10        | 36,009   |
**Rata-rata: 33,465 s**

## **Referensi**  
- https://i-3.co.id/query-sql-terasa-lama-implementasikan-partisi-pada-tabel-anda/
