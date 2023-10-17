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
