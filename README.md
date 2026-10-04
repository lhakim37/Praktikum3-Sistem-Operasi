# Laporan Praktikum 3: Sistem File (File System) Linux
**Mata Kuliah:** Sistem Operasi  
**Fakultas:** Ilmu Komputer, Universitas Sriwijaya  

---

## 📋 Deskripsi
Repository ini berisi jawaban dan penyelesaian langkah-demi-langkah untuk **Tugas Praktikum 3: Sistem File (File System)**. Praktikum ini mencakup pemahaman struktur direktori Linux, manajemen hak akses file/direktori, penggunaan link, umask, serta manipulasi proses dan direktori.

---

## 🛠️ Penyelesaian Tugas (Jawaban Soal)

Berikut adalah perintah Terminal Linux (Ubuntu) beserta penjelasan lengkap untuk menyelesaikan 10 soal latihan tugas.

### 1. Melihat peralatan I/O (Character Device) pada sistem komputer
Peralatan *character device* mengirimkan data karakter per karakter (seperti terminal, console, modem) dan biasanya berada di direktori `/dev`.

**Perintah:**
```bash
ls -l /dev | grep "^c"
```
**Penjelasan:**
- `ls -l /dev` mendaftar seluruh isi direktori `/dev` dalam format rinci.
- `grep "^c"` menyaring baris yang diawali dengan karakter `c`, yang menandakan tipe file **Character Device**.

---

### 2. Membuat sub-direktori `januari`, `februari`, dan `maret` sekaligus pada direktori `latihan5`

**Perintah:**
```bash
mkdir -p latihan5/{januari,februari,maret}
```
**Penjelasan:**
- Opsi `-p` memastikan direktori induk `latihan5` dibuat jika belum ada.
- Sintaks `{januari,februari,maret}` membuat ketiga sub-direktori tersebut secara bersamaan dalam satu perintah.

---

### 3. Membuat file `dataku` (berisi Nama, NIM, Alamat) di sub-direktori `januari`, lalu meng-copy file tersebut ke `februari` dan `maret`

**Perintah:**
```bash
# Membuat file dataku pada direktori januari
cat << 'EOF' > latihan5/januari/dataku
Nama   : [Nama Anda]
NIM    : [NIM Anda]
Alamat : [Alamat Anda]
EOF

# Menyalin file dataku ke direktori februari dan maret
cp latihan5/januari/dataku latihan5/februari/
cp latihan5/januari/dataku latihan5/maret/
```
**Penjelasan:**
- `cat << 'EOF' > ...` digunakan untuk mengisi teks ke dalam file `dataku`.
- `cp` digunakan untuk mengandakan/menyalin file dari lokasi asal ke direktori tujuan.

---

### 4. Mengubah izin akses file `dataku` pada sub-direktori `januari` agar `group` dan `others` dapat melakukan *write*

**Perintah:**
```bash
chmod go+w latihan5/januari/dataku
```
**Penjelasan:**
- `g` (*group*) dan `o` (*others*) ditambahkan hak akses `+w` (*write*).

---

### 5. Mengubah izin akses file `dataku` pada sub-direktori `februari` (User: `rwx`, Group & Others: `r-x`)

**Perintah:**
```bash
chmod 755 latihan5/februari/dataku
```
**Penjelasan:**
- Format oktal `755`:
  - `7` (`rwx`) untuk Owner/User.
  - `5` (`r-x`) untuk Group.
  - `5` (`r-x`) untuk Others.

---

### 6. Mengubah izin akses file `dataku` pada sub-direktori `maret` agar semua (`user`, `group`, `others`) dapat melakukan *write*, *read*, dan *execute*

**Perintah:**
```bash
chmod 777 latihan5/maret/dataku
# Atau menggunakan sintaks simbolik:
# chmod ugo+rwx latihan5/maret/dataku
```
**Penjelasan:**
- Nilai oktal `777` memberikan hak akses penuh (`rwx`) kepada seluruh kategori pengguna (*all/ugo*).

---

### 7. Menghapus direktori `maret`

**Perintah:**
```bash
rm -rf latihan5/maret
```
**Penjelasan:**
- Karena direktori `maret` tidak kosong (berisi file `dataku`), perintah `rmdir` biasa akan menolak/gagal. Maka dari itu digunakan `rm -rf` untuk menghapus direktori beserta seluruh isinya secara rekursif dan paksa.

---

### 8. Mengubah hak akses sub-direktori `februari` (User & Group hanya `read`), serta pengujian pembuatan direktori baru `haha` di dalamnya

**Perintah:**
```bash
# Mengubah hak akses direktori februari menjadi Read Only (r--) untuk User dan Group
chmod ug=r,o= latihan5/februari

# Mencoba membuat direktori baru 'haha' di dalam sub-direktori februari
mkdir latihan5/februari/haha
```

**Hasil & Analisis Pengujian:**
Saat menjalankan perintah `mkdir latihan5/februari/haha`, sistem akan menampilkan pesan kesalahan:
```text
mkdir: cannot create directory ‘latihan5/februari/haha’: Permission denied
```
**Alasan:**
Untuk membuat direktori atau berkas baru di dalam suatu direktori pada Linux, pengguna memerlukan izin **Write (`w`)** untuk membuat file/folder, serta izin **Execute (`x`)** untuk memasuki dan mengakses struktur direktori tersebut. Karena izin direktori `februari` diubah menjadi *read-only* (`r--`), pembuatan direktori `haha` ditolak (*Permission Denied*).

---

### 9. Memodifikasi `umask` menjadi `027` dan Penjelasan Nilai Default

**Perintah Memodifikasi Umask:**
```bash
umask 027
```

**Penjelasan & Kalkulasi Nilai Default:**
`umask` (User Mask) digunakan untuk menetapkan hak akses awal (*default permission*) saat berkas atau direktori baru dibuat.

1. **Nilai Default Umum di Linux:**
   - Default izin dasar (*Base Permission*) untuk **File biasa** = `666` (`rw-rw-rw-`)
   - Default izin dasar (*Base Permission*) untuk **Direktori** = `777` (`rwxrwxrwx`)
   - Default nilai `umask` bawaan sistem biasanya = `022`

2. **Kalkulasi Hak Akses dengan `umask 027`:**
   - **Untuk File:**
     $$\text{Base Permission (666)} - \text{Umask (027)} = \text{Izin Akhir (640)}$$
     - User: $6 - 0 = 6$ (`rw-`)
     - Group: $6 - 2 = 4$ (`r--`)
     - Others: $6 - 7 = 0$ (`---` / tidak ada akses)
     - *Hasil izin file akhir:* `-rw-r-----` (640)

   - **Untuk Direktori:**
     $$\text{Base Permission (777)} - \text{Umask (027)} = \text{Izin Akhir (750)}$$
     - User: $7 - 0 = 7$ (`rwx`)
     - Group: $7 - 2 = 5$ (`r-x`)
     - Others: $7 - 7 = 0$ (`---` / tidak ada akses)
     - *Hasil izin direktori akhir:* `drwxr-x---` (750)

---

### 10. Membuat *link* dari file `dataku` ke `dataku.ini` dan `dataku.juga`, serta memeriksa jumlah link

**Perintah:**
```bash
# Berpindah ke direktori januari
cd latihan5/januari

# Membuat Hard Link pertama (dataku.ini)
ln dataku dataku.ini

# Membuat Hard Link kedua (dataku.juga)
ln dataku dataku.juga

# Memeriksa jumlah link yang terbentuk
ls -l dataku
```

**Hasil Output Perintah `ls -l`:**
```text
-rw-rw-r-- 3 mahasiswa mahasiswa 45 Oct  5 06:30 dataku
```

**Penjelasan:**
- Kolom kedua pada output `ls -l` menunjukkan angka **`3`**.
- Hal ini menandakan terdapat **3 hard link** yang menunjuk ke *inode* data yang sama, yaitu: `dataku`, `dataku.ini`, dan `dataku.juga`.

---

## 📌 Kesimpulan
Dari praktikum ini dapat disimpulkan bahwa:
1. Linux mengorganisir seluruh komponennya (termasuk perangkat keras/IO) dalam bentuk file di bawah hirarki *root* (`/`).
2. Perizinan file (*file permissions*) sangat krusial dalam menjaga keamanan data, di mana hak akses `r`, `w`, dan `x` memiliki perilaku yang berbeda jika diterapkan pada file vs direktori.
3. `umask` menentukan hak akses default saat pembuatan file/folder baru.
4. Perintah `ln` memungkinkan pembuatan referensi (*hard link* atau *symbolic link*) yang menambah jumlah penunjuk (*link count*) ke suatu file pada sistem berkas.
