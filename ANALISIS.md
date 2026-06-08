<img width="430" height="233" alt="web 3" src="https://github.com/user-attachments/assets/44359656-cff6-41dd-a168-6d614fabd8a1" />
<img width="452" height="219" alt="web 2" src="https://github.com/user-attachments/assets/3f0bae3c-ca59-400b-bf3c-f72487e0d657" />
<img width="561" height="237" alt="web 1" src="https://github.com/user-attachments/assets/bbb4821c-dfd6-462e-a3f4-e49ae13f9cc4" />
# Analisis Perbaikan

## Permasalahan 1

### Gejala
Docker Compose gagal dijalankan dan menampilkan error:

```bash
yaml: line 3: could not find expected ':'
```

### Penyebab
Pada file `docker-compose.yml`, bagian deklarasi service ditulis:

```yaml
services
```

tanpa tanda titik dua (`:`).

### Solusi
Mengubah konfigurasi menjadi:

```yaml
services:
```

---

## Permasalahan 2

### Gejala
Docker Compose menampilkan error:

```bash
service "db" refers to undefined volume db-data
```

### Penyebab
Service database menggunakan volume:

```yaml
- db-data:/var/lib/mysql
```

sedangkan pada bagian volumes didefinisikan:

```yaml
volumes:
  database-data:
```

Nama volume tidak sesuai.

### Solusi
Menyamakan nama volume menjadi:

```yaml
volumes:
  db-data:
```

---

## Permasalahan 3

### Gejala
Proses build gagal dengan pesan:

```bash
unable to prepare context: path ".../web33" not found
```

### Penyebab
Konfigurasi service `web3` menggunakan path yang salah:

```yaml
context: ./web33
```

Padahal folder yang tersedia adalah `web3`.

### Solusi
Mengubah konfigurasi menjadi:

```yaml
context: ./web3
```

---

## Permasalahan 4

### Gejala
Build image web1 gagal dengan pesan:

```bash
php:8.2-apach: not found
```

### Penyebab
Terdapat kesalahan penulisan image pada Dockerfile web1:

```dockerfile
FROM php:8.2-apach
```

### Solusi
Mengubah menjadi:

```dockerfile
FROM php:8.2-apache
```

---

## Permasalahan 5

### Gejala
Build image web3 gagal dengan pesan:

```bash
php:8.2-apche: not found
```

### Penyebab
Terdapat kesalahan penulisan image pada Dockerfile web3:

```dockerfile
FROM php:8.2-apche
```

### Solusi
Mengubah menjadi:

```dockerfile
FROM php:8.2-apache
```

---

## Permasalahan 6

### Gejala
Container MySQL berhenti saat startup dan menampilkan error SQL syntax.

### Penyebab
File `init.sql` masih mengandung markdown code block:

```sql
```sql
CREATE TABLE ...
```
```

Sintaks tersebut tidak valid untuk dijalankan oleh MySQL.

### Solusi
Menghapus seluruh tanda markdown sehingga file hanya berisi perintah SQL.

---

## Permasalahan 7

### Gejala
Container Nginx gagal dijalankan dan menampilkan error:

```bash
unknown directive "```nginx"
```

### Penyebab
File `nginx.conf` masih mengandung markdown code block:

```nginx
```nginx
...
```
```

### Solusi
Menghapus seluruh tanda markdown sehingga file hanya berisi konfigurasi Nginx.

---

## Permasalahan 8

### Gejala
Container Nginx gagal dijalankan dan menampilkan error:

```bash
host not found in upstream "web11"
```

### Penyebab
Nama host pada upstream salah:

```nginx
server web11:80;
```

Sedangkan nama service yang tersedia adalah `web1`.

### Solusi
Mengubah konfigurasi menjadi:

```nginx
server web1:80;
```

---

## Permasalahan 9

### Gejala
Nginx tidak dapat meneruskan request ke web3.

### Penyebab
Konfigurasi upstream menggunakan:

```nginx
server web3:8080;
```

Padahal Apache di dalam container berjalan pada port 80.

### Solusi
Mengubah konfigurasi menjadi:

```nginx
server web3:80;
```

---

## Permasalahan 10

### Gejala
Nginx tidak dapat mengakses service web3.

### Penyebab
Service `web3` hanya terhubung ke network backend:

```yaml
networks:
  - backend
```

Sedangkan Nginx berada pada network frontend.

### Solusi
Menambahkan network frontend:

```yaml
networks:
  - frontend
  - backend
```

---

## Permasalahan 11

### Gejala
Konfigurasi web1 tidak dapat menemukan host database yang benar.

### Penyebab
Environment variable menggunakan:

```yaml
DB_HOST: mysql
```

Padahal nama service database adalah:

```yaml
db
```

### Solusi
Mengubah konfigurasi menjadi:

```yaml
DB_HOST: db
```

---

## Permasalahan 12

### Gejala
Konfigurasi web2 menggunakan kredensial database yang salah.

### Penyebab
Password database ditulis:

```yaml
DB_PASS: wrongpassword
```

### Solusi
Mengubah password menjadi:

```yaml
DB_PASS: student123
```

---

## Permasalahan 13

### Gejala
Identitas praktikan belum ditampilkan pada aplikasi.

### Penyebab
File PHP masih menggunakan placeholder:

```php
$nama = "ganti ke namamu";
$nim  = "ganti ke nimmu";
```

### Solusi
Mengganti placeholder dengan data praktikan yang sesuai.

---

## Permasalahan 14

### Gejala
Informasi container pada web2 dan web3 tidak sesuai.

### Penyebab
Terdapat kesalahan penulisan:

```html
WEB-WEB
```

dan

```html
WEB-WOB
```

### Solusi
Mengubah menjadi:

```html
WEB-2
```

dan

```html
WEB-3
```

---

## Permasalahan 15

### Gejala
Data identitas praktikan ditampilkan secara statis (hardcoded) pada masing-masing web server dan tidak berasal dari database.

Contoh:

```php
$nama = "ganti ke namamu";
$nim  = "ganti ke nimmu";
```

### Penyebab
File `index.php` pada web1, web2, dan web3 hanya menampilkan data yang ditulis langsung pada source code sehingga tidak memanfaatkan database yang telah disediakan.

### Solusi
Mengubah file `index.php` pada seluruh web server agar melakukan koneksi ke database MySQL dan mengambil data dari tabel `students`.

Kode yang ditambahkan:

```php
$conn = new mysqli("db", "student", "student123", "responsi");

if ($conn->connect_error) {
    die("Koneksi database gagal: " . $conn->connect_error);
}

$result = $conn->query("SELECT * FROM students LIMIT 1");
$data = $result->fetch_assoc();
```

Kemudian mengganti tampilan identitas menjadi:

```php
<strong><?= $data['nama'] ?></strong>

<strong><?= $data['nim'] ?></strong>
```

Dengan perubahan ini, seluruh web server mengambil data identitas dari database yang sama sehingga memenuhi kebutuhan integrasi database pada sistem.

## Permasalahan 16

### Gejala
Saat menggunakan mysqli muncul error:

```bash
Fatal error: Class "mysqli" not found
```

### Penyebab
Image PHP belum memiliki ekstensi mysqli.

### Solusi
Menambahkan instalasi mysqli pada Dockerfile:

```dockerfile
RUN docker-php-ext-install mysqli
```

Kemudian melakukan rebuild container menggunakan:

```bash
docker compose build --no-cache
docker compose up -d
```

---

## Hasil Akhir

Setelah seluruh perbaikan dilakukan:

- Seluruh container berhasil dijalankan menggunakan Docker Compose.
- Nginx dapat diakses melalui `http://localhost:8080`.
- Nginx berfungsi sebagai load balancer dengan metode Round Robin.
- Ketiga web server dapat diakses melalui Nginx.
- Ketiga web server berhasil mengakses database MySQL yang sama.
- Data identitas praktikan ditampilkan dari tabel database `students`.
- Informasi WEB-1, WEB-2, dan WEB-3 tampil bergantian sesuai mekanisme load balancing.
