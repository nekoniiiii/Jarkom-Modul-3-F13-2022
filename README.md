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

#### Berlint

Masukkan line dibawah ke web console Berlint :
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

## NOMOR 3
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155
### Penyelesaian
[Westalis]
- Pada Westalis, edit file ```/etc/dhcp/dhcpd.conf``` dan tambahkan line berikut
```
subnet 10.35.2.0 netmask 255.255.255.0 {
        option routers 10.35.2.1;
}

subnet 10.35.1.0 netmask 255.255.255.0 {
    range 10.35.1.50 10.35.1.88;
    range 10.35.1.120 10.35.1.155;
    option routers 10.35.1.1;
    option broadcast-address 10.35.1.255;
    option domain-name-servers 10.35.2.2;
}
```
- Restart
```
service isc-dhcp-server restart
```
## NOMOR 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

### Penyelesaian
- Pada Westalis, edit file ```/etc/dhcp/dhcpd.conf``` dan tambahkan line berikut
```
subnet 10.35.3.0 netmask 255.255.255.0 {
    range 10.35.3.10 10.35.3.30;
    range 10.35.3.60 10.35.3.85;
    option routers 10.35.3.1;
    option broadcast-address 10.35.3.255;
    option domain-name-servers 10.35.2.2;
}
```
- Restart
```
service isc-dhcp-server restart
```
Testing pada soal no 3 dan 4 apakah masing-masing client telah mendapatkan IP setelah semua client di setting IP dinamisnya dari DHCP Server dengan command ip a pada masing-masing client. <br>

SSS <br>
![image](https://user-images.githubusercontent.com/91374949/200805913-07510418-7d71-46d3-9e0a-528014c998df.png)
<br>Garden<br>
![image](https://user-images.githubusercontent.com/91374949/200806002-0106a6c5-582c-48ea-abf4-41f0ef904b44.png)
<br>Eden<br>
![image](https://user-images.githubusercontent.com/91374949/200806212-b4740cef-3c7d-4aa5-a01e-c05cdadbce74.png)
<br>NewstonCastle<br>
![image](https://user-images.githubusercontent.com/91374949/200806329-24fa3e68-9e08-4114-949d-fc5104b086ee.png)
<br>KemonoPark<br>
![image](https://user-images.githubusercontent.com/91374949/200806424-b0d21a48-e063-43b6-9b7d-10f7349ed719.png) 

## NOMOR 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut
### Penyelesaian
[WISE]
- Edit file ```/etc/bind/named.conf.options``` pada WISE sebagai berikut
```
forwarders {
  192.168.122.1;
};
// dnssec-validation auto;
allow-query{any;};
```
![image](https://user-images.githubusercontent.com/91374949/200804114-466f392c-3b92-4d2e-8df8-3dd64915eca3.png)

- Restart bind9 ```service bind9 restart```

Untuk mengeceknya, maka dapat menjalankan command cat /etc/resolv.conf pada masing-masing client. <br>
SSS<br>
![image](https://user-images.githubusercontent.com/91374949/200807044-a42ac087-3f69-4c8e-b85b-ed6c0fe8f980.png)
<br>Garden<br>
![image](https://user-images.githubusercontent.com/91374949/200807121-19f2cb8f-f726-4512-8450-0658e039a1bc.png)
<br>Eden<br>
![image](https://user-images.githubusercontent.com/91374949/200806970-4c356eef-706d-4e66-a24b-dcf5af0c1f31.png)
<br>NewstonCastle<br>
![image](https://user-images.githubusercontent.com/91374949/200806908-eec0774d-15cd-4e51-91d2-13cc436e3ad3.png)
<br>KemonoPark<br>
![image](https://user-images.githubusercontent.com/91374949/200806842-aeb1991e-165a-4f7f-b68e-b482a323aa36.png)

## NOMOR 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

### Penyelesaian
[Westalis]
- Edit file``` /etc/dhcp/dhcpd.conf``` pada Westalis sebagai berikut
```
# Pada subnet Switch 1 tambahkan
    default-lease-time 300;
    max-lease-time 6900;
    
# Pada subnet Switch 3 tambahkan
    default-lease-time 600;
    max-lease-time 6900;
```
- Restart
```
service isc-dhcp-server restart
```
## NOMOR 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13

### Penyelesaian
[Westalis]
- Pada Westalis, edit file ```/etc/dhcp/dhcpd.conf``` dan tambahkan line berikut
```
host Eden { 
        hardware ethernet 82:f8:21:de:6b:8f;
        fixed-address 10.35.3.13;
}
```
- Restart ```service isc-dhcp-server restart```
- Kemudian cek alamat IP pada Eden
![image](https://user-images.githubusercontent.com/91374949/200806212-b4740cef-3c7d-4aa5-a01e-c05cdadbce74.png)<br>

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

