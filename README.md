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

