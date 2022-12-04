# Task Week 1

Pada Task kali ini, Saya akan menjelaskan langkah-langkah dalam mendeploy sebuah aplikasi. Dimulai dengan Setup SSH, Konfigurasi Reverse Proxy, dan lain-lain.

## A. Setup SSH

Setup SSH dilakukan agar memudahkan kita ketika akan mengakses server.

Berikut adalah langkahnya.

1. Step pertama, adalah men-generate keygen SSH. Baik dilokal maupun pada server. Kegunaanya berbeda, untuk lokal kita gunakan untuk kuncinya, sementara yang ada pada server digunakan untuk meletakkan kunci yang di generate dilokal kita. Karena saya sudah men-genrate dilokal, maka saya melakukan generate hanya pada server saya.

```bash
ssh-keygen
```

<img src="Dokumentasi/Dokumentasi-SSH/1.png">
<img src="Dokumentasi/Dokumentasi-SSH/3.png">

2. Jika sudah, langkah berikutnya adalah mencopy ```id_rsa.pub``` yang ada pada lokal kita ke file ```authorized_keys``` yang ada diserver berlokasi di ```/home/.ssh/athurized_keys```.

<img src="Dokumentasi/Dokumentasi-SSH/7.png">
<img src="Dokumentasi/Dokumentasi-SSH/5.png">
<img src="Dokumentasi/Dokumentasi-SSH/2.png">
<img src="Dokumentasi/Dokumentasi-SSH/4.png">

3. Maka, berikutnya kita bisa mengakses Server kita tanpa harus memasukkan password.

<img src="Dokumentasi/Dokumentasi-SSH/6.png">

## B. Konfigurasi Reverse Proxy

Konfigurasi Reverse Proxy dilakukan agar aplikasi yang kita deploy mempunyai domain atau alamat website. Disini kita akan menggunakan Nginx untuk Webserver.

1. Sebelum menginstall Nginx, kita lakukan update dan upgrade.

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/1.png">

2. Lalu Install Nginx.

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/2.png">
<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/3.png">

3. Jika sudah, kita membuat domain terlebih dahulu di cloudflare. Ini yang akan kita input dikonfigurasi Reverse Proxy.

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/4.png">
<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/5.png">

4. Berikutnya, kita membuat directory yang berlokasi di```/etc/nginx/```yang nantinya akan kita masukkan konfigurasi Reverse Proxy.

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/6.png">

5. Kemudian, buat file konfigurasi pada directory tersebut, dan masukkan konfigurasi seperti pada contoh.

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/7.png">
<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/8.png">
<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/13.png">

6. Lalu tambahkan Directory yang berisi konfigurasi Reverse Proxy sebagai Virtual Host pada konfigurasi ```nginx.conf```

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/9.png">
<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/10.png">

7. Check Syntax yang sudah kita buat dan restart Nginx.

<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/11.png">
<img src="Dokumentasi/Dokumentasi-Reverse-Proxy/12.png">

## C. Setup Aplikasi

Langkah berikutnya adalah men-setup konfigurasi aplikasi yang akan kita deploy, disini kita akan menggunakan aplikasi Dumbflix.

1. Yang pertama adalah, Clone aplikasi dari Repository Github, baik Frontend maupun Backend.

<img src="Dokumentasi/Dokumentasi-Aplikasi/1.png">
<img src="Dokumentasi/Dokumentasi-Aplikasi/2.png">
<img src="Dokumentasi/Dokumentasi-Aplikasi/3.png">

2. Step berikutnya, dikarenakan aplikasi Dumbflix menggunakan NodeJS, maka disini kita perlu mendownload engine dari NodeJS yaitu NVM yang nantinya akan me-manage versi dari NodeJS yang harus digunakan.

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

<img src="Dokumentasi/Dokumentasi-Aplikasi/4.png">

3. Sesuai yang ada pada arahan README pada aplikasi, kita akan menggunakan Node versi 10.

<img src="Dokumentasi/Dokumentasi-Aplikasi/5.png">

4. Jika sudah, kita lanjut untuk melakukan beberapa konfigurasi pada Frontend. Kita perlu melakukan konfigurasi pada ```api.js``` yang terletak pada ```src/config/api.js```. Disini kita masukkan domain dari Backend sesuai dengan yang kita buat di Cloudflare.

<img src="Dokumentasi/Dokumentasi-Aplikasi/6.png">
<img src="Dokumentasi/Dokumentasi-Aplikasi/7.png">

5. Lanjut ke Backend, disini kita perlu meng-copy file .env.example menjadi .env, hal ini menyesuaikan dengan apa yang ada pada README.

<img src="Dokumentasi/Dokumentasi-Aplikasi/8.png">

6. Setelah itu, kita perlu melakukan setting pada ```config.json``` untuk menentukan relasi Database yang akan di migrasi dari Backend. Kita akan menggunakan environment ```development```.

<img src="Dokumentasi/Dokumentasi-Aplikasi/9.png">

## D. Install MYSQL dan Migrasi Database

Pada kesempatan ini, kita akan menggunakan tools yaitu MYSQL sebagai tools untuk mengakses Database. Berikut adalah Step untuk menginstall dan melakukan beberapa pengaturan untuk MYSQL.

1. Kita install MYSQL dengan command
```bash
sudo apt install mysql-server
```
<img src="Dokumentasi/Dokumentasi-DB/1.png">

2. Kemudian, kita masuk ke MYSQL sebagai user root. Tahap ini kita lakukan untuk membuat password dari user root, hal ini dilakukan karena akan berkaitan dengan tahap ketika kita akan men-setting MYSQL.

```bash
sudo mysql -u root
```
```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by '(password bebas)';
```

<img src="Dokumentasi/Dokumentasi-DB/2.png">

3. Jika sudah, keluar dan kita akan melakukan secure installation untuk MYSQL, ini dilakukan sebagai pertahanan dan keamanan database.

```bash
sudo mysql_secure_installation
```
<img src="Dokumentasi/Dokumentasi-DB/3.png">

* Ikuti arahan dan pilih Setting sesuai dengan kebutuhan.

4. Berikutnya, kita tidak akan menggunakan user root untuk migrasi database, kita akan sesuaikan dengan yang ada pada ```config.json``` Backend dan akan memberikan privileges kepada user yang kita buat.

<img src="Dokumentasi/Dokumentasi-DB/4.png">

```bash
CREATE USER '(namauser)'@'%' IDENTIFIED BY '(Passwordbebas)';
```

* Keterangan : "%" yang di maksud adalah setting agar user dapat di akses secara remote atau melalui server lain.

```bash
GRANT ALL PRIVILEGES ON *.* TO '(namauser)'@'%'
```
<img src="Dokumentasi/Dokumentasi-DB/5.png">

5. Kita kembali ke Backend untuk mempersiapkan migrasi database, yang harus dilakukan pertama adalah Install NPM kemudian Install Sequelize.

<img src="Dokumentasi/Dokumentasi-DB/6.png">

```bash
npm install
```
<img src="Dokumentasi/Dokumentasi-DB/7.png">

```bash
npm i -g sequelize-cli
```

6. Kemudian, kita buat database pada user yang sudah kita buat.

<img src="Dokumentasi/Dokumentasi-DB/8.png">

```bash
sequelize-cli db:create
```
<img src="Dokumentasi/Dokumentasi-DB/9.png">

7. Terakhir kita migrasi data Backend ke database yang sudah kita buat.

<img src="Dokumentasi/Dokumentasi-DB/10.png">

```bash
sequelize-cli db:migrate
```

<img src="Dokumentasi/Dokumentasi-DB/11.png">

## E. Lauch Application

Setelah melakukan semua setting sebelum melakukan peluncuran aplikasi, berikutnya kita akan melakukan peluncuran aplikasi. Disini kita akan menggunakan PM2 sebagai peluncur aplikasi, agar aplikasi dapat berjalan secara background.

1. Install aplikasi PM2 secara global agar PM2 ter-install secara keseluruhan pada server.

<img src="Dokumentasi/Dokumentasi-Launch-App/1.png">

```bash
npm i -g pm2
```

2. Lalu kita inisiasi sebuah file yaitu ```ecosystem.config.js```, file ini digunakan untuk menjalankan aplikasi menggunakan PM2.

<img src="Dokumentasi/Dokumentasi-Launch-App/2.png">
<img src="Dokumentasi/Dokumentasi-Launch-App/4.png">

```bash
pm2 ecosystem simple
```

3. Setelah itu, kita edit isi file dari ```ecosystem.config.js```, sesuaikan dengan aplikasinya, baik itu Frontend maupun Backend.

<img src="Dokumentasi/Dokumentasi-Launch-App/3.png">
<img src="Dokumentasi/Dokumentasi-Launch-App/6.png">

4. Lalu jalankan pada masing-masing aplikasi.

```bash
pm2 start ecosystem.config.js
```

Check status PM2. pastikan aplikasi bertuliskan keterangan ```online```

<img src="Dokumentasi/Dokumentasi-Launch-App/7.png">

```bash
pm2 status
```

5. Kita jalankan aplikasi pada browser kita menggunakan domain yang kita setting pada Reverse Proxy. Sekaligus kita melakukan registrasi untuk memastikan bahwa database juga sudah terkoneksi dengan baik.

<img src="Dokumentasi/Dokumentasi-Launch-App/8.png">
<img src="Dokumentasi/Dokumentasi-Launch-App/9.png">

6. Masuk ke database untuk lebih memastikan bahwa data yang kita registrasi tadi sudah benar-benar terdaftar.

<img src="Dokumentasi/Dokumentasi-Launch-App/10.png">

```bash
SELECT * FROM (nama tabel)
```

Demikian rangkuman tugas Stage-2 di Week-1. Kurang lebihnya saya memohon maaf.  Terimakasih.