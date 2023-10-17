# Jarkom-Modul2-D02-2023

Nama Anggota | NRP
------------------- | --------------		
Ryan Abinugraha | 5025211178
Abdurrohim Usaamah Khafizduddin | 5025201255

## Soal 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut

Pertama, buat topologi sesuai yang diminta pada soal.

Kemudian kita setting konfigurasi pada Router yaitu Pandudewanata.

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.192.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.192.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.192.3.1
	netmask 255.255.255.0
```
Setelah itu lakukan testing untuk mengecek apakah sudah terkoneksi dengan internet dengan melakukan `ping` google.com

## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

Pada node Yudhistira, lakukan perintah berikut:
```
apt-get update
apt-get install bind9 -y
cp -r -f /root/prak1/bind /etc/
service bind9 restart

nano /etc/bind/named.conf.local

zone "arjuna.D02.com" {
    type master;
    file "/etc/bind/jarkom/arjuna.D02.com";
};

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/arjuna.D02.com
nano /etc/bind/jarkom/arjuna.D02.com  

(ganti menjadi arjuna.02.com)
((ganti ip (192.192.2.2)))
((Tambahkan www	IN	CNAME	arjuna.D02.com.))

service bind9 restart
```

Kemudian pada node Sadewa, lakukan testing dengan menambahkan `nameserver 192.192.2.2` (IP Yudhistira) kemudian lakukan ping arjuna.D02.com.

## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

Untuk soal nomor 3, langkah-langkah yang dilakukan sama persis seperti pada soal nomor 2. Hanya saja ganti arjuna.D02.com menjadi abimanyu.D02.com.

```
apt-get update
apt-get install bind9 -y
cp -r -f /root/prak1/bind /etc/
service bind9 restart

nano /etc/bind/named.conf.local

zone "abimanyu.D02.com" {
    type master;
    file "/etc/bind/jarkom/abimanyu.D02.com";
};

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.D02.com
nano /etc/bind/jarkom/abimanyu.D02.com  

(ganti menjadi abimanyu.02.com)
((ganti ip (192.192.2.2)))
((Tambahkan www	IN	CNAME	abimanyu.D02.com.))

service bind9 restart
```

## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu. 

Lakukan perintah berikut pada node Yudhistira

```
nano /etc/bind/jarkom/abimanyu.D02.com

((Tambahkan))
parikesit	IN	A	192.192.3.3
service bind9 restart
```
Kemudian pada node Sadewa, lakukan testing untuk mengecek apakah abimanyu.D02.com atau www.abimanyu.D02.com dapat diakses. Lakukan ping parikesit.abimanyu.D02.com.

## Soal 5 
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

Lakukan perintah ini pada node Yudhistira.
```nano /etc/bind/named.conf.local

zone "3.188.192.in-addr.arpa" {
  type master;
  file "/etc/bind/jarkom/3.188.192.in-addr.arpa";
};

cp /etc/bind/db.local /etc/bind/jarkom/3.188.192.in-addr.arpa

nano /etc/bind/jarkom/3.188.192.in-addr.arpa

((Tambahkan))
3.188.192.in-addr.arpa.	IN	NS	abimanyu.D02.com.
3			            IN	PTR	abimanyu.D02.com.

service bind9 restart
```
Kemudian pada node `Sadewa`, lakukan perintah berikut.
```
// Install package dnsutils, ubah nameserver ke 192.192.122.1
apt-get update
apt-get install dnsutils -y

// Kembalikan nameserver agar tersambung dengan Yudhistira
host -t PTR 192.192.2.2
```
Setelah itu lakukan testing dengan menulis perintah berikut pada node Sadewa.
```
host -t PTR 192.192.3.3
```

## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

Lakukan perintah berikut pada node Yudhistira
```
nano /etc/bind/named.conf.local

((Pada setiap zone (kecuali reverse), tambahkan))
also-notify { 192.192.2.3; }; // IP Werkudara
allow-transfer { 192.192.2.3; }; // IP Werkudara

service bind9 restart
```
Kemudian pada node Werkudara, lakukan perintah berikut
```
apt-get update
apt-get install bind9 -y
nano /etc/bind/named.conf.local

zone "arjuna.D02.com" {
  type slave;
  masters { 192.192.2.2; };
  file "/var/lib/bind/arjuna.D02.com";
};

zone "abimanyu.D02.com" {
  type slave;
  masters { 192.192.2.2; }; 
  file "/var/lib/bind/abimanyu.D02.com";
};

service bind9 restart
```

Sebelum melakukan testing pada node Sadewa, stop service bind9 terlebih dahulu pada node Yudhistira.
``` 
service bind9 stop
```

Setelah itu lakukan testing pada node Sadewa untuk mengecek apakah DNS Slave berhasil dibuat pada Werkudara.

```
nano /etc/resolv.conf
nameserver 192.192.2.2
nameserver 192.192.2.3

ping www.arjuna.D02.com
ping www.abimanyu.D02.com
```
## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Lakukan perintah berikut pada node `Yudhistira`

```
  nano /etc/bind/jarkom/abimanyu.D02.com

  ((Tambahkan))
  ns1	        IN	A	192.192.2.3
  baratayuda	IN	NS	ns1

  nano /etc/bind/named.conf.options

  comment //dnsec
  allow-query{any;};

  service bind9 restart
```

Pada node `Werkudara`, lakukan perintah berikut.

```
nano /etc/bind/named.conf.options

comment //dnsec
allow-query{any;};

  nano /etc/bind/named.conf.local
  
zone "baratayuda.abimanyu.D02.com" {
  type master;
  file "/etc/bind/delegasi/baratayuda.abimanyu.D02.com";
};

  mkdir /etc/bind/delegasi

cp /etc/bind/db.local /etc/bind/Baratayuda/baratayuda.abimanyu.D02.com

  nano  /etc/bind/delegasi/baratayuda.abimanyu.D02.com

  ((ganti nama menjadi baratayuda.abimanyu.D02.com))
  ((ganti IP abimanyu))
  ((ganti AAAA jadi www	IN	A	192.192.3.3))

  service bind9 restart
```

Pada node Sadewa, lakukan testing untuk mengecek apakah baratayuda.abimanyu.D02.com atau www.baratayuda.abimanyu.D02.com dapat diakses.

```
ping baratayuda.abimanyu.D02.com

ping www.baratayuda.abimanyu.D02.com
```

## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

Lakukan perintah berikut pada node Werkudara

```
nano /etc/bind/delegasi/baratayuda.abimanyu.D02.com

((Tambahkan))
rjp	    IN	A	    192.192.3.3
www.rjp	IN	CNAME	rjp.baratayuda.abimanyu.D02.com.

service bind9 restart
```

Pada node Sadewa, lakukan testing untuk mengecek apakah rjp.baratayuda.abimanyu.yyy.com atau www.rjp.baratayuda.abimanyu.yyy.com dapat diakses.

```
ping www.rjp.baratayuda.abimanyu.D02.com
```

## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

Lakukan perintah berikut pada node `Pandudewanata`

```
apt-get update
apt-get install nginx -y
service nginx start
```

Lakukan perintah berikut pada node `Yudhistira`

```
apt-get install bind9 nginx -y
service nginx start
```

Lakukan perintah berikut pada node `Prabukusuma/Wisangeni/Abimanyu`

```
apt-get update && apt install nginx php php-fpm -y
mkdir /var/www/jarkom

  nano /var/www/jarkom/index.php
```

```
 <?php
echo "Halo, Kamu berada di Prabukusuma";
 	?>

	<?php
echo "Halo, Kamu berada di Abimanyu";
 	?>

	<?php
echo "Halo, Kamu berada di Wisanggeni";
 	?>
```

```
ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled

service nginx start
  service php7.0-fpm start

nginx -t
```



