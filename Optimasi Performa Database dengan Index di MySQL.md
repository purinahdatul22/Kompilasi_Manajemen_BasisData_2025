# **Optimasi Performa Database dengan Index di MySQL**

## **1. Latar Belakang**
Optimasi database merupakan cara untuk memperbaiki kinerja dan meningkatkannya. Upaya ini dilakukan dengan memaksimalkan struktur data, indeks, dan query.
Dalam proses optimasi, kecepatan dari transaksi menjadi fokus perbaikan. Oleh sebab itu, optimasi sangatlah penting karena hal itu berkaitan dengan waktu yang
dibutuhkan oleh database untuk menangani semua permintaan yang ditujukan kepadanya.

## **2. Problem**
Ketika jumlah data dalam tabel semakin besar, proses pencarian menggunakan `SELECT` tanpa index menjadi lambat. Hal ini terjadi karena MySQL harus melakukan **full table scan**, yaitu memeriksa seluruh baris dalam tabel satu per satu. Oleh karena itu, perlu dilakukan pengujian untuk melihat bagaimana penggunaan index dapat mempercepat eksekusi query.

## **3. Solusi**
Untuk mengatasi masalah tersebut, dilakukan beberapa langkah:
1. **Import database** `employee.sql` sebagai dataset uji coba.
2. **Menggunakan EXPLAIN** untuk melihat bagaimana query dijalankan oleh MySQL.
3. **Menambahkan Index** pada kolom `first_name` dan `last_name` untuk mengoptimalkan pencarian.
4. **Menguji kecepatan query** sebelum dan sesudah menggunakan index.
5. **Menguji jenis index lain**, seperti **foreign key index** dan **composite index**.

## **4. Pembahasan**
Berikut adalah langkah-langkah implementasi yang telah dilakukan:

### **4.1. Melakukan Query Tanpa Index**
Sebelum menambahkan index, dilakukan pencarian berdasarkan `first_name` menggunakan perintah berikut:

```sql
EXPLAIN SELECT * FROM employee WHERE first_name = 'Georgi';
```

Hasil `EXPLAIN` menunjukkan bahwa query menggunakan **full table scan**, yang berarti semua baris dalam tabel diperiksa satu per satu.

### **4.2. Menambahkan Index**
Untuk meningkatkan performa, ditambahkan index pada kolom `first_name` dan `last_name`:

```sql
ALTER TABLE employee ADD INDEX idx_full_name (first_name, last_name);
```

Setelah index ditambahkan, query yang sama dijalankan kembali menggunakan `EXPLAIN`. Hasilnya menunjukkan bahwa query kini menggunakan **index scan**, yang jauh lebih efisien dibandingkan full table scan.

### **4.3. Pengujian Sebelum dan Sesudah Menggunakan Index**
Untuk mengukur efektivitas index, dilakukan pengujian dengan menjalankan query sebanyak 10 kali, lalu mencatat waktu eksekusinya.

#### **Sebelum Menggunakan Index:**
```sql
SELECT * FROM employee WHERE first_name = 'Georgi' AND last_name = 'Bahr';
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 0.15     |
| 2         | 0.15     |
| 3         | 0.15     |
| 4         | 0.15     |
| 5         | 0.15     |
| 6         | 0.14     |
| 7         | 0.15     |
| 8         | 0.14     |
| 9         | 0.14     |
| 10        | 0.15     |
**Rata-rata: 0.147 s**

#### **Setelah Menggunakan Index:**
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 0.00     |
| 2         | 0.00     |
| 3         | 0.00     |
| 4         | 0.00     |
| 5         | 0.00     |
| 6         | 0.00     |
| 7         | 0.00     |
| 8         | 0.00     |
| 9         | 0.00     |
| 10        | 0.00     |
**Rata-rata: 0.00 s**

### **4.4. Menambahkan Kolom Baru dan Mengupdate Data**
Selain pengujian index, dilakukan juga beberapa modifikasi tabel:
- **Menambahkan kolom `nama_departemen`** pada tabel `dept_manager` dan `dept_emp`, lalu mengupdate nilainya.
- **Menambahkan kolom `umur`** pada tabel `employee`, lalu menghitung umurnya berdasarkan tanggal lahir.

```sql
ALTER TABLE dept_manager ADD COLUMN nama_departemen VARCHAR(255);
UPDATE dept_manager dm 
JOIN department d ON dm.dept_no = d.dept_no 
SET dm.nama_departemen = d.dept_name;

ALTER TABLE employee ADD COLUMN umur INT;
UPDATE employee 
SET umur = TIMESTAMPDIFF(YEAR, birth_date, CURDATE());
```

### **4.5. Menambahkan Foreign Key Index**
Foreign key index ditambahkan untuk menjaga integritas referensial antara tabel `dept_emp` dan `dept_manager` dengan tabel `employee`:

```sql
ALTER TABLE dept_emp 
ADD CONSTRAINT fk_dept_emp FOREIGN KEY (emp_no) REFERENCES employee(emp_no);

ALTER TABLE dept_manager 
ADD CONSTRAINT fk_dept_manager FOREIGN KEY (emp_no) REFERENCES employee(emp_no);
```

### **4.6. Mencari Gaji Tertinggi dalam Departemen Tertentu**
Untuk menemukan karyawan dengan gaji tertinggi di departemen `d006`, digunakan query berikut:

```sql
SELECT e.first_name, e.last_name, s.amount AS salary
FROM employee e
JOIN salary s ON e.emp_no = s.emp_no
JOIN dept_emp de ON e.emp_no = de.emp_no
WHERE de.dept_no = 'd006'
AND s.amount = (
    SELECT MAX(s2.amount)
    FROM salary s2
    JOIN dept_emp de2 ON s2.emp_no = de2.emp_no
    WHERE de2.dept_no = 'd006'
);
```

Hasilnya menunjukkan nama karyawan dengan gaji tertinggi dalam departemen tersebut.

## **5. Kesimpulan**
Dari serangkaian pengujian yang telah dilakukan, didapatkan beberapa kesimpulan:
1. **Penggunaan index dapat meningkatkan performa query secara signifikan**, dari rata-rata eksekusi **0.147 detik** menjadi **0 detik**.
2. **Composite index lebih efektif dibandingkan index tunggal** saat mencari berdasarkan lebih dari satu kolom.
3. **Foreign key index berguna untuk menjaga referensial integritas antar tabel** dan dapat mempercepat join antar tabel.
4. **Menambahkan kolom baru dan memperbarui data** merupakan bagian dari optimasi database agar lebih informatif dan mudah digunakan.

## **6. Bukti Pendukung**
- Hasil query explain sebelum menggunakan index
```sql
EXPLAIN SELECT * FROM employee WHERE first_name = 'Georgi';
```
![1](https://github.com/user-attachments/assets/8a1b7071-f8b2-4684-9dae-8b64a9d1537b)
- Tambahkan index <br>
![2](https://github.com/user-attachments/assets/8912b479-d6ac-46c1-951a-ffff68839b9b)

- Hasil query explain setelah menggunakan index
![3](https://github.com/user-attachments/assets/f077f2bb-366f-4f6f-badf-b06cfb4d3968)

- Tambahkan kolom nama departemen pada table dept_manager. Dan lakukan update terhadap  kolom tersebut. Tambahkan screenshot hasilnya
![4](https://github.com/user-attachments/assets/0b9e13b3-e533-48c1-9da1-a23548db60d2)

- Tambahkan kolom nama departemen pada table dept_emp. Dan lakukan update terhadap kolom  tersebut. Tambahkan screenshot hasilnya <br>
![5](https://github.com/user-attachments/assets/f5a02957-5e2e-4114-aa1c-8f86f26ab8ee)

- Buat query untuk menampilkan gaji yang tertinggi pada departemen d006. Siapa Namanya?screenshot hasilnya
![6](https://github.com/user-attachments/assets/59d51eb0-2300-4440-983d-52d8e7140d5f)

- Dari database employe yang sudah diimport, tambahkan kolom umur pada table employee.  Kemudian lakukan update terhadap kolom umur tersbut. Nilai umur, dhitung dari tanggal lahir s.d  sekarang. Tambahkan screenshot hasilnya
![7](https://github.com/user-attachments/assets/0ecac802-6a2a-4e38-b17d-893e64ee3d4a)

- Tambahkan masing-masing jenis index diatas composite index dan foreign key index pada table  employee 
![8](https://github.com/user-attachments/assets/e2c80355-ccf9-4f75-9355-99dd3ba22af9)

- Lakukan pengujian terhadap query berikut, apakah sudah mengakses index atau belum.
```sql
EXPLAIN SELECT * FROM employee WHERE first_name = 'Georgi' 
```
![9](https://github.com/user-attachments/assets/4e263131-3cd4-4f55-a9c6-f409936f267c)


## **7. Sumber Referensi**
- https://www.cloudeka.id/id/berita/cloud/optimasi-database/
