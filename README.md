# Jarkom-Modul-2-D19-2023

## Anggota Kelompok
| Nama | NRP |
|---------------------------|------------|
|Adrian Ismu Arifianto | 5025211116 |
|Ahmad Rafif Hikmatiar | 5025211247 |

## Topologi
![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/1c398fec-0f05-4f6c-83e3-006ab2313971)

## Config 
- **Pandudewanata**
  ```
  auto eth0
  iface eth0 inet dhcp
  
  auto eth1
  iface eth1 inet static
  	address 10.31.1.1
  	netmask 255.255.255.0
  
  auto eth2
  iface eth2 inet static
  	address 10.31.2.1
  	netmask 255.255.255.0
  
  auto eth3
  iface eth3 inet static
  	address 10.31.3.1
  	netmask 255.255.255.0
  
  auto eth4
  iface eth4 inet static
  	address 10.31.4.1
  	netmask 255.255.255.0
  ```
- **Yudhistira**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.2.2
  	netmask 255.255.255.0
  	gateway 10.31.2.1
  ```
- **Werkudara**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.1.2
  	netmask 255.255.255.0
  	gateway 10.31.1.1
  ```
- **Nakula**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.3.3
  	netmask 255.255.255.0
  	gateway 10.31.3.1
  ```
- **Sadewa**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.3.2
  	netmask 255.255.255.0
  	gateway 10.31.3.1
  ```
- **Arjuna-LB**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.4.2
  	netmask 255.255.255.0
  	gateway 10.31.4.1
  ```
- **Prabukusuma**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.4.3
  	netmask 255.255.255.0
  	gateway 10.31.4.1
  ```
- **Abimanyu**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.4.5
  	netmask 255.255.255.0
  	gateway 10.31.4.1
  ```
- **Wisanggeni**
  ```
  auto eth0
  iface eth0 inet static
  	address 10.31.4.5
  	netmask 255.255.255.0
  	gateway 10.31.4.1
    ```
## Sebelum Memulai
Di dalam setiap node, eksekusi perintah berikut atau tambahkan perintah ini ke dalam file `.bashrc` agar perintah tersebut akan tetap berjalan saat kita membuka dan menutup proyek.

- **Pandudewanata**
  ```sh
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.31.0.0/16
  ```
- **Yudhistira & Werkudara**
  ```sh
  echo 'nameserver 192.168.122.1' > /etc/resolv.conf
  apt-get update
  apt-get install bind9 -y      
  ```
- **Nakula & Sadewa**
  ```sh
  echo '
  nameserver 192.168.122.1
  nameserver 10.31.2.2 # IP Yudhistira
  nameserver 10.31.1.2 # IP Werkudara
  ' > /etc/resolv.conf
  apt-get update
  apt-get install dnsutils -y
  apt-get install lynx -y
  ```

## Soal 1 
> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut
### Script

Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client *Nakula* dan *Sadewa*.
```
ping google.com -c 5
```

### Hasil


## Soal 2
> Buatlah website utama dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

### Script

```sh
echo 'zone "arjuna.d19.com" {
    type master;
    notify yes;
    also-notify { 10.31.1.2; };
    allow-transfer { 10.31.1.2; };
    file "/etc/bind/jarkom/arjuna.d19.com";
};

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/arjuna.d19.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.d19.com. root.arjuna.d19.com. (
                            100         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.d19.com.
@       IN      A       10.31.2.2     ; IP Yudhistira
www     IN      CNAME   arjuna.d19.com.
@       IN      AAAA    ::1' > /etc/bind/jarkom/arjuna.d19.com

service bind9 restart
```

### Hasil

Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client *Nakula* dan *Sadewa*.

```
ping arjuna.d19.com -c 5
ping www.arjuna.d19.com -c 5
```

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/73297d52-7ae1-4b31-be1a-a27f0c1583c8)


![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/3cb3a8ef-8652-4f70-8ed7-74740ca41ffd)

## Soal 3
> Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

### Script
**Yudhistira**
```sh
echo 'zone "arjuna.d19.com" {
    type master;
    notify yes;
    also-notify { 10.31.1.2; };
    allow-transfer { 10.31.1.2; };
    file "/etc/bind/jarkom/arjuna.d19.com";
};
zone "abimanyu.d19.com" {
    type master;
    notify yes;
    also-notify { 10.31.1.2; };
    allow-transfer { 10.31.1.2; };
    file "/etc/bind/jarkom/abimanyu.d19.com";
};

cp /etc/bind/db.local /etc/bind/jarkom/arjuna.d19.com

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.d19.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d19.com. root.abimanyu.d19.com. (
                            100         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.d19.com.
@               IN      A       10.31.2.2     ; IP Yudhistira
www             IN      CNAME   abimanyu.d19.com.
' > /etc/bind/jarkom/abimanyu.d19.com

service bind9 restart
```
### Hasil

Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client *Nakula* dan *Sadewa*.

```
ping abimanyu.d19.com -c 5
ping www.abimanyu.d19.com -c 5
```

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/d5f4a49c-a4d2-4d5a-ad16-06c9ce34c511)

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/672dc75b-603c-4e7e-a9e8-ccff953dbf90)

## Soal 4
> Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

### Script

**Yudhistira**

```sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d19.com. root.abimanyu.d19.com. (
                            100         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.d19.com.
@               IN      A       10.31.2.2     ; IP Yudhistira
www             IN      CNAME   abimanyu.d19.com.
parikesit       IN      A       10.31.4.4       ; IP Abimanyu
' > /etc/bind/jarkom/abimanyu.d19.com

service bind9 restart
```

### Hasil

Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client *Nakula* dan *Sadewa*.

```
ping parikesit.abimanyu.d19.com -c 5
```

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/4be627ab-f477-4abe-a65e-ccb86339c268)

## Soal 5
> Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

Untuk membuat reverse domain `abimanyu.d19.com`, gunakan script berikut di DNS Master.
```sh
zone "4.31.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/4.31.10.in-addr.arpa";
};
' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/4.31.10.in-addr.arpa

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d19.com. root.abimanyu.d19.com. (
                            100         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

;
4.31.10.in-addr.arpa.    IN      NS      abimanyu.d19.com.
4                        IN      PTR     abimanyu.d19.com. ;
' > /etc/bind/jarkom/4.31.10.in-addr.arpa
```
### Hasil

Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client Nakula atau Sadewa.
```
host -t PTR 10.31.2.2
```

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/a3a5804e-4d9c-471d-be1a-b36ca641a5ab)

## Soal 6
> Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

### Script
**Yudhistira**
```sh
echo 'zone "arjuna.d19.com" {
    type master;
    notify yes;
    also-notify { 10.31.1.2; };
    allow-transfer { 10.31.1.2; };
    file "/etc/bind/jarkom/arjuna.d19.com";
};
zone "abimanyu.d19.com" {
    type master;
    notify yes;
    also-notify { 10.31.1.2; };
    allow-transfer { 10.31.1.2; };
    file "/etc/bind/jarkom/abimanyu.d19.com";
};
zone "4.31.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/4.31.10.in-addr.arpa";
};
' > /etc/bind/named.conf.local

service bind9 restart
service bind9 stop
```

**Werkudara**
```sh
echo 'zone "arjuna.d19.com" {
    type slave;
    masters { 10.31.2.2; };
    file "/var/lib/bind/arjuna.d19.com";
};
zone "abimanyu.d19.com" {
    type slave;
    masters { 10.31.2.2; };
    file "/var/lib/bind/abimanyu.d19.com";
};

service bind9 restart
```

### Hasil

Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client Nakula atau Sadewa.
```
ping abimanyu.d19.com -c 5
ping www.abimanyu.d19.com -c 5
```

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/1ec7e402-a8c6-48fc-a3d6-3f7feae9028a)

## Soal 7
> Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda

### Script
```sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d19.com. root.abimanyu.d19.com. (
                            100         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.d19.com.
@               IN      A       10.31.2.2     ; IP Yudhistira
www             IN      CNAME   abimanyu.d19.com.
parikesit       IN      A       10.31.4.4       ; IP Abimanyu
ns1             IN      A       10.31.1.2       ; IP Werkudara
baratayuda      IN      NS      ns1
@               IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.d19.com

echo 'options {
        directory "/var/cache/bind";

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

service bind9 restart
```
**Werkudara**
```sh
zone "baratayuda.abimanyu.d19.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.d19.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/baratayuda

cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.d19.com

echo 'options {
        directory "/var/cache/bind";
        forwarders {
            192.168.122.1;
        };
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
```

### Hasil
Untuk melakukan pengujian, kita dapat mengeksekusi perintah berikut pada node client Nakula atau Sadewa.
```
ping www.baratayuda.abimanyu.d19.com -c 5
ping baratayuda.abimanyu.d19.com -c 5
```

![image](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/71255346/841bb3f2-d218-49ff-bcaa-12270ae2e5c0)


## Soal 8
> Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

### File baratayuda.abimanyu.d19.com
![WhatsApp Image 2023-10-17 at 18 38 00](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/4962274b-df6b-4d83-a166-12b597e03a13)

### Hasil
Untuk testing, kita ping rjp.baratayuda.abimanyu.d19.com dan www.rjp.baratayuda.abimanyu.d19.com di client yaitu node Sadewa.
![WhatsApp Image 2023-10-17 at 18 39 17](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/cd7b44a3-be7a-40ec-9fda-fcec627a2a0a)

## Soal 9
> Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### File /etc/nginx/sites-enabled/lb-jarkom dalam node Arjuna-LB sebagai Load Balancer
![WhatsApp Image 2023-10-17 at 18 48 29](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/454ff603-9220-4043-83f7-058406b43195)
Setelah itu kita buat symlink dengan menjalankan perintah
```
ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom
```

### File /etc/nginx/sites-enabled/jarkom
#### Prabukusuma
![WhatsApp Image 2023-10-17 at 18 47 01](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/fd4a6848-ee07-4874-85ec-196e062f76c8)
#### Abimanyu
![WhatsApp Image 2023-10-17 at 18 47 38](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/bba94167-8e9f-47d3-a623-dc3df51805a7)
#### Wisanggeni
![WhatsApp Image 2023-10-17 at 18 47 56](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/849c934b-38a1-4e49-a397-8eba4578cc37)

### Hasil
Untuk testing, kita lakukan lynx di node Sadewa sebagai client dengan perintah
```
lynx [ip web server]:[port]
```
![WhatsApp Image 2023-10-17 at 18 53 52](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/b3f2abf1-de9d-4d8b-83a2-63ea1834b42f)
![WhatsApp Image 2023-10-17 at 18 53 38](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/e4f5cee6-0f41-4082-825f-95c2c39ed729)
![WhatsApp Image 2023-10-17 at 18 54 09](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/3b019064-25a4-4290-8243-1590e5ce00fb)


## Soal 10
> Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

### Hasil
Sudah langsung saya aplikasikan di nomor 9, namun bedanya untuk melakukan testing di Sadewa sebagai client gunakan perintah
```
lynx arjuna.d19.com
```
dan lakukan secara berulang maka halaman akan otomatis terganti

## Soal 11
> Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy.

### File /var/www/abimanyu.d19/index.php
![WhatsApp Image 2023-10-17 at 19 06 58](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/5c7208da-b2da-4592-ae03-db09d21f2ae1)

### File /etc/apache2/sites-available/abimanyu.d19.com.conf
![WhatsApp Image 2023-10-17 at 19 04 18](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/dad66bfb-5723-48f6-8a9d-67ddb967ada8)

### Hasil
Untuk testing, kita lakukan lynx di node Sadewa sebagai client dengan perintah
```
lynx abimanyu.d19.com
```
![WhatsApp Image 2023-10-17 at 19 07 29](https://github.com/arafifh/Jarkom-Modul-2-D19-2023/assets/89500557/e9da8208-0f6b-440f-9904-87939556fb1e)

## Soal 12
## Soal 13
## Soal 14
## Soal 15
## Soal 16
## Soal 17
## Soal 18
## Soal 19
## Soal 20
