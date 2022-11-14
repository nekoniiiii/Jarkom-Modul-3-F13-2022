# Jarkom-Modul-3-F13-2022

## Penyelesaian Soal Praktikum Modul 3 Jaringan Komputer

* Muhammad Andi Akbar Ramadhan (5025201264)
* Natya Madya Marciola	(5025201238)
* Neisa Hibatillah Alif	(5025201170)

## NOMOR 1

Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server.

### Penyelesaian

#### WISE

Masukkan line dibawah ke web console WISE :
```
apt-get update
apt-get install bind9
```

#### Westalis

Masukkan line dibawah ke web console Westalis :
```
apt-get update
apt-get install isc-dhcp-server
```

Kemudian, edit ```/etc/default/isc-dhcp-server```
```
INTERFACES="eth0"
```

Setelah itu, restart dhcp dengan ```service isc-dhcp-server restart```

#### Ostania

Masukkan line dibawah ke web console Ostania :
```
apt-get update
apt-get install squid
```

## NOMOR 2

Ostania sebagai DHCP Relay.

### Penyelesaian

#### Ostania

Masukkan line dibawah ke web console Ostania :
```
apt-get update
apt-get install isc-dhcp-relay
```

Kemudian, edit ```/etc/default/isc-dhcp-relay```
```
SERVERS="10.35.2.4"
INTERFACES="eth1 eth2 eth3"
OPTIONS=""
```

Restart dhcp relay dengan ```service isc-dhcp-relay restart```

## NOMOR 8
SSS, Garden, dan Eden digunakan sebagai client Proxy agar pertukaran informasi dapat terjamin keamanannya, juga untuk mencegah kebocoran data. </br>

Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut: </br>

### NOMOR 8.1
1. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)

#### Penyelesaian
- Pada Berlint, masukkan konfigurasi berikut pada file ```/etc/squid/acl.conf```
```
acl AVAILABLE_WORKING time MTWHF 00:00-08:00
acl AVAILABLE_WORKING1 time MTWHF 17:00-24:00
acl AVAILABLE_WORKING2 time AS 00:00-24:00
```

- Dan konfigurasi berikut pada file ```/etc/squid/squid.conf```
```
visible_hostname Berlint
http_access allow AVAILABLE_WORKING USERS
http_access allow AVAILABLE_WORKING1 USERS
http_access allow AVAILABLE_WORKING2 USERS
http_access deny all
```

- Kemudian restart squid
```
service squid restart
```

### NOMOR 8.2
2. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)

#### Penyelesaian
- Pada Berlint, tambahkan konfigurasi berikut pada file ```/etc/squid/squid.conf``` (ditulis sebelum http port)
```
acl whitelist dstdomain .loid-work.com .franky-work.com
http_access allow whitelist
```
![8 2](https://user-images.githubusercontent.com/72701806/201578157-67a8a83c-dafa-4aa5-b811-7da0d30a1cae.png)
</br>

- Kemudian restart squid
```
service squid restart
``` 

### NOMOR 8.3
3. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)

#### Penyelesaian
- Port https adalah 443. Maka, pada Berlint, tambahkan konfigurasi berikut pada file ```/etc/squid/acl.conf```
```
acl CONNECT method CONNECT
acl SSL_ports port 443
```
![8 3 (1)](https://user-images.githubusercontent.com/72701806/201578225-88b2793a-b04e-4e6c-9833-db9c09edf37d.png)
</br>

- Kemudian ubah beberapa baris sebelumnya menjadi konfigurasi berikut pada file ```/etc/squid/squid.conf```
```
http_access allow CONNECT AVAILABLE_WORKING SSL_ports
http_access allow CONNECT AVAILABLE_WORKING1 SSL_ports
http_access allow CONNECT AVAILABLE_WORKING2 SSL_ports
```
</br>

- Kemudian restart squid
```
service squid restart
```

### NOMOR 8.4
4. Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

#### Penyelesaian
- Untuk kecepatan 128kbps, maka angka yang perlu ditulis pada konfigurasi adalah (128/8)*1000 = 16000

- Pada Berlint, beri konfigurasi berikut pada file ```/etc/squid/acl-bandwidth.conf```
```
delay_pools 1
delay_class 1 1
delay_access 1 allow all
delay_parameters 1 16000/16000
```
![8 4 (1)](https://user-images.githubusercontent.com/72701806/201578286-cc499abd-c28b-4000-80cc-f6f389054903.png)
</br>

- Kemudian tambah konfigurasi berikut pada file ```/etc/squid/squid.conf```
```
include /etc/squid/acl-bandwidth.conf
```
![8 4 (2)](https://user-images.githubusercontent.com/72701806/201578318-70fa1eff-b273-4b6a-bd70-064f76c1bdab.png)
</br>

- Kemudian restart squid
```
service squid restart
```

- Pada client, jalankan beberapa perintah berikut untuk melihat kecepatan akses internet
```
apt-get update
apt install speedtest-cli
export PYTHONHTTPSVERIFY=0
speedtest
```

### NOMOR 8.5
5. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan
kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

#### Penyelesaian
- Pada Berlint, tambahkan konfigurasi berikut pada file ```/etc/squid/acl.conf```
![8 5 (1 1)](https://user-images.githubusercontent.com/72701806/201578759-4b1386ec-2c40-4ef5-aea9-694a9244bf86.png)
</br>

- Kemudian ubah konfigurasi delay_access pada file ```/etc/squid/acl-bandwidth.conf``` sehingga pembatasan bandwidth hanya dapat bekerja
```
delay_access 1 allow WEEKEND
```
![8 5 (2)](https://user-images.githubusercontent.com/72701806/201578393-7f09cafd-771a-4517-8c7a-c2e63c77e2e0.png)
</br>

- Kemudian restart squid
```
service squid restart
```


### Kendala Pengerjaan

