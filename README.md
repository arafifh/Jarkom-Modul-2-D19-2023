# Jarkom-Modul-2-D19-2023

## Anggota Kelompok
| Nama | NRP |
|---------------------------|------------|
|Adrian Ismu Arifianto | 5025211116 |
| | 5025 |

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
  ```
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.31.0.0/16
  ```
- **Yudhistira & Werkudara**
  ```
  echo 'nameserver 192.168.122.1' > /etc/resolv.conf
  apt-get update
  apt-get install bind9 -y      
  ```
- **Nakula & Sadewa**
  ```
  echo '
  nameserver 192.168.122.1
  nameserver 10.31.2.2 # IP Yudhistira
  nameserver 10.31.1.2 # IP Werkudara
  ' > /etc/resolv.conf
  apt-get update
  apt-get install dnsutils -y
  apt-get install lynx -y
  ```

