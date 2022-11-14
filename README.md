# Jarkom-Modul-3-F06-2022

## Kelompok F06

|               Nama               |      NRP      |
| -------------------------------- | ------------- |
| Benedictus Bimo Cahyo Wicaksono  |  5025201097   |  
| Andhika Ditya Bagaskara D.       |  5025201096   |
| Theresia Nawangsih               |  5025201144   |

# Soal 1
### Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server.

Pertama, dilakukan konfigurasi untuk masing-masing node.

WISE -> DNS Server
```
auto eth0
iface eth0 inet static
  address 192.202.2.2
  netmask 255.255.255.0
  gateway 192.202.2.1
```

Westalis -> DHCP Server
```
auto eth0
iface eth0 inet static
  address 192.202.2.4
  netmask 255.255.255.0
  gateway 192.202.2.1
```

Berlint -> Proxy Server
```
auto eth0
iface eth0 inet static
  address 192.202.2.3
  netmask 255.255.255.0
  gateway 192.202.2.1
```

Ostania -> DHCP Relay
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
address 192.202.1.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 192.202.2.1
netmask 255.255.255.0

auto eth3
iface eth3 inet static
address 192.202.3.1
netmask 255.255.255.0
```

SSS & Garden
```
auto eth0
iface eth0 inet dhcp
```

EDEN
```
auto eth0
iface eth0 inet static
  address 192.202.3.2
  netmask 255.255.255.0
  gateway 192.202.3.1
```

NewstonCastle
```
auto eth0
iface eth0 inet static
  address 192.202.3.3
  netmask 255.255.255.0
  gateway 192.202.3.1
```

KemonoPark
```
auto eth0
iface eth0 inet static
  address 192.202.3.4
  netmask 255.255.255.0
  gateway 192.202.3.1
```

Kemudian di Ostania, jalankan
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.202.0.0/16
```

Kemudian jalankan pada semua node
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

Pada WISE sebagai DNS Server, dilakukan instalasi bind9
```
apt-get update
apt-get install bind9 -y
service bind9 start
service bind9 status
```

Pada Westalis sebagai DHCP Server, dilakukan instalasi isc-dhcp-server
```
apt-get update
apt-get install isc-dhcp-server -y
```

Dan untuk Berlint sebagai Proxy Server, dilakukan instalasi squid.
```
apt-get update
apt-get install squid -y
service squid restart
service squid status
```

# Soal 2
### Dan Ostania sebagai DHCP Relay.

Pada Ostania sebagai DHCP Relay, dilakukan instalasi isc-dhcp-relay
```
apt-get update
apt-get install isc-dhcp-relay -y
```

Kemudian dilakukan konfigurasi pada `/etc/default/isc-dhcp-relay`
```
nano /etc/default/isc-dhcp-relay
```

dan tambahkan
```
SERVERS = "192.202.2.4"
INTERFACES = "eth1 eth2 eth3"
```

setelah itu, restart isc-dhcp-relay.
```
service isc-dhcp-relay restart
```

# Soal 3
### Loid dan Franky menyusun peta tersebut dengan hati-hati dan teliti. Ada beberapa kriteria yang ingin dibuat oleh Loid dan Franky, yaitu:
### 1. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
### 2. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155

Karena Westalis adalah DHCP Server, maka dilakukan konfigurasi pada file `/etc/default/isc-dhcp-server`
```
INTERFACES = "eth0"
```

Kemudian tambahkan konfugirasi berikut pada file `/etc/dhcp/dhcpd.conf`
```
subnet 192.202.2.0 netmask 255.255.255.0 {
  option routers 192.202.2.1;
}

subnet 10.8.1.0 netmask 255.255.255.0 {
  range 192.202.1.50 192.202.1.88;
  range 192.202.1.120 192.202.1.155;
  option routers 192.202.1.1;
  option broadcast-address 192.202.1.255;
  option domain-name-servers 192.202.2.2;
  default-lease-time 360;
  max-lease-time 7200;
}
```

Setelah itu, restart isc-dhcp-server.
```
service isc-dhcp-server restart
service isc-dhcp-server status
```













