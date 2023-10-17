# Laporan Resmi Praktikum Jaringan Komputer - Modul 2 DNS dan Web Server - IT20

### Annisa Rahmapuri - 5027211018
### Abdul Zaki Syarul Rahmat - 502721120

## Nomor 11
**Soal** : Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

**Cara Pengerjaan** : 
Sebelum memulai untuk konfigurasi web server kita perlu melakukan instalasi Apache terlebih dahulu di node AbimanyuWebServer. Instalasi pada pengerjaan ini sudah ditambahkan ke dalam script .bashrc dalam bentuk shell script.

```
apt-get install apache2 -y
service apache2 start
```
Setelah itu kita akan lanjut untuk melakukan konfigurasi halaman yang akan digunakan. Pergi ke alamat /var/www/ lalu buat direktori baru bernama abimanyu.it20

```
cd /var/www/
mkdir abimanyu.it20
```

Untuk mengisi direktori tersebut dengan resource yang disediakan, gunakan wget dan unzip. Install unzip terlebih dahulu jika di node saat ini belum terinstall 

```
wget -O '/var/www/abimanyu.yyy.com.zip' 'https://drive.usercontent.google.com/download?id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc'
unzip /var/www/abimanyu.yyy.com.zip -d /var/www/
mv /var/www/abimanyu.yyy.com /var/www/abimanyu.it20
rm /var/www/abimanyu.yyy.com.zip
```

Dari sini kita dapat melanjutkan untuk konfigurasi apachenya. Pergi ke folder /etc/apache2/sites-available kemudian copy file 000-default.conf sebagai basis file yang akan digunakan

```
cd /etc/apache2/sites-available
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.it20.conf
nano abimanyu.it20.conf
```
Langsung saja kita konfigurasikan file tersebut sebagai berikut

```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/abimanyu.it20
ServerName abimanyu.it20.com
ServerAlias www.abimanyu.it20.com
```
Save file tersebut, kemudian jalankan command berikut untuk menjalankan situs Abimanyu
```
a2ensite abimanyu.it20.conf
service apache2 reload
```
Pergi ke node Sadewa atau client. Jalankan command lynx ke www.abimanyu.it20.com melalui port 8080, jika berhasil maka akan tampil halaman berikut
```
lynx www.abimanyu.it20.com:8080/
```
![Foto](./img/11_1.PNG)

## Nomor 12
**Soal** : Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

**Cara Pengerjaan** : Pada soal nomor 12 ini kita diperintahkan untuk melakukan Directory Rewrite dari /index.php/home menjadi /home. Disini kita akan menggunakan .htaccess maka sebelum itu kita perlu menyalakan modul rewrite
```
a2enmod rewrite
```
Setelah itu kita dapat langsung pergi ke folder web Abimanyu dan menambahkan file .htaccess di direktori tersebut
```
cd /var/www/abimanyu.it20
nano .htaccess
```
Untuk isi dari file .htaccess diisi dengan script berikut
```
RewriteEngine On
RewriteRule ^home$ /index.php/home [L]
```
Script ini digunakan untuk mengaktifkan RewriteEngine pada domain web server Abimanyu dan mengalihkan /index.php/home menjadi /home saja. Untuk testing dapat dilakukan kembali di node Sadewa
```
lynx www.abimanyu.it20.com:8080/home
```
Jika berhasil maka akan tampil halaman yang sama seperti sebelumnya

![Foto](./img/11_1.PNG)

## Nomor 13
**Soal** : Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

**Cara Pengerjaan** : Pada nomor 13 ini kita akan melakukan penambahan subdomain yaitu parikesit.abimanyu.it20.com. Lakukan step yang sama seperti nomor 11 sebelumnya yaitu membuat direktori pada /var/www/ dan mendownload resource yang diperlukan
```
cd /var/www/
mkdir parikesit.abimanyu.it20
```
```
wget -O '/var/www/parikesit.abimanyu.yyy.com.zip' 'https://drive.usercontent.google.com/download?id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS'
unzip /var/www/parikesit.abimanyu.yyy.com.zip -d /var/www/
mv /var/www/parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.it20
rm /var/www/parikesit.abimanyu.yyy.com.zip
mkdir /var/www/parikesit.abimanyu.it20/secret
mkdir /var/www/parikesit.abimanyu.it20/secret/html
```
Setelah itu kita dapat lanjut untuk menkonfigurasikan subdomain parikesit dengan mengikuti langkah-langkah pada nomor 11
```
cd /etc/apache2/sites-available
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/parikesit.abimanyu.it20.conf
nano parikesit.abimanyu.it20.conf
```
Untuk isi dari file konfigurasinya adalah sebagai berikut
```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/parikesit.abimanyu.it20
ServerName parikesit.abimanyu.it20.com
ServerAlias www.parikesit.abimanyu.it20.com
```
Lakukan aktivasi untuk subdomain parikesit
```
a2ensite parikesit.abimanyu.it20.conf
service apache2 reload
```
Kembali lagi ke node Sadewa dan lakukan testing
```
lynx www.parikesit.abimanyu.it20.com:8080/
```
Jika berhasil akan tampil halaman berikut

![Foto](./img/13.PNG)

## Nomor 14
**Soal** : Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

**Cara Pengerjaan** : Di nomor ini kita diperintahkan untuk mengkonfigurasi Directory Listing untuk folder /public dan /secret. Kita hanya perlu mengubah konfigurasi dari subdomain parikesit. Tambahkan code berikut ke file .conf dari parikesit
```
cd /etc/apache2/sites-available
nano parikesit.abimanyu.it20.conf
```
```
<Directory /var/www/parikesit.abimanyu.it20/public>
    Options +Indexes
</Directory>

<Directory /var/www/parikesit.abimanyu.it20/secret>
    Options -Indexes
</Directory>
```
Save kemudian restart subdomain parikesit dengan perintah berikut
```
a2dissite parikesit.abimanyu.it20.conf
a2ensite parikesit.abimanyu.it20.conf
service apache2 reload
```
Kembali lagi ke node Sadewa atau client dan lakukanlah testing dengan command lynx sebagai berikut
```
lynx www.parikesit.abimanyu.it20.com:8080/public
lynx www.parikesit.abimanyu.it20.com:8080/secret
```
- Tampilan halaman /public

![Foto](./img/14_1.PNG)
- Tampilan halaman /secret

![Foto](./img/14_2.PNG)