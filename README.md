# Jarkom-Modul-5-E07-2021
Laporan Resmi Soal Shift Jarkom Modul 5
## (A) Buatlah topologi seperti instruksi soal
![alt text](https://github.com/migellamp/Jarkom-Modul-5-E07-2021/blob/main/Screenshot/awal.png) <br />

## (B) Konfigurasi IP dengan VLSM (Variable Length Subnet Masking) :
## VLSM :
![alt text](https://github.com/migellamp/Jarkom-Modul-5-E07-2021/blob/main/Screenshot/vlsm.png) <br />

## TREE : 
![alt text](https://github.com/migellamp/Jarkom-Modul-5-E07-2021/blob/main/Screenshot/tree.png) <br />

## Pembagian IP : 
| Subnet | Jumlah | Netmask | subnetmask |	nid |
| ------ | ------ | ------- | ---------- | --- |
| A1 | 2 | /30 |	255.255.255.252 |	192.203.0.0 |
| A2 | 2 | /30 |	255.255.255.252 |	192.203.0.4 |
| A7 | 4 | /29 |	255.255.255.248 |	192.203.0.16 |
| A8 | 4 | /29 |	255.255.255.248 |	192.203.0.24 |
| A5 | 101 | /25 |	255.255.255.128 |	192.203.0.128 |
| A3 | 201 | /24 |	255.255.255.0 |	192.203.1.0 |
| A4 | 301 | /23 |	255.255.254.0 |	192.203.2.0 |
| A6 | 701 | /22 | 	255.255.252.0 |	192.203.4.0 |
| JUMLAH |	1316 |	/21 |	255.255.248.0 |	\- |

## Setting network interface
**FOOSHA**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.203.0.5
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 192.203.0.1
	netmask 255.255.255.252
```
**WATER7**
```
auto eth0
iface eth0 inet static
	address 192.203.0.2
	netmask 255.255.255.252

auto eth1
iface eth1 inet static
	address 192.203.4.1
	netmask 255.255.252.0
	
auto eth2
iface eth2 inet static
	address 192.203.0.129
	netmask 255.255.255.128

auto eth3
iface eth3 inet static
	address 192.203.0.17
	netmask 255.255.255.248
```
**GUANHAO**
```
auto eth0
iface eth0 inet static
	address 192.203.0.6
	netmask 255.255.255.252
	 
auto eth1
iface eth1 inet static
	address 192.203.1.1
	netmask 255.255.255.0
	
auto eth2
iface eth2 inet static
	address 192.203.2.1
	netmask 255.255.254.0

auto eth3
iface eth3 inet static
	address 192.203.0.25
	netmask 255.255.255.248
```
**DORIKI**
```
auto eth0
iface eth0 inet static
	address 192.203.0.19
	netmask 255.255.255.248
	gateway 192.203.0.17
```
**JIPANGU**
```
auto eth0
iface eth0 inet static
	address 192.203.0.18
	netmask 255.255.255.248
	gateway 192.203.0.17
```
**MAINGATE**
```
auto eth0
iface eth0 inet static
	address 192.203.0.27
	netmask 255.255.255.248
	gateway 192.203.0.25
```
**JORGE**
```
auto eth0
iface eth0 inet static
	address 192.203.0.26
	netmask 255.255.255.248
	gateway 192.203.0.25
```
**Pada masing-masing host/router, setting ke dhcp**
```
auto eth0
iface eth0 inet dhcp
```

## (C) Routing
**FOOSHA**
```
route add -net 192.203.1.0 netmask 255.255.255.0 gw 192.203.0.6
route add -net 192.203.2.0 netmask 255.255.254.0 gw 192.203.0.6
route add -net 192.203.0.24 netmask 255.255.255.248 gw 192.203.0.6
route add -net 192.203.4.0 netmask 255.255.252.0 gw 192.203.0.2
route add -net 192.203.0.128 netmask 255.255.255.128 gw 192.203.0.2
route add -net 192.203.0.16 netmask 255.255.255.248 gw 192.203.0.2
```
**WATER7**
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.203.0.1
```
**GUANHAO**
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.203.0.5
```

(D) Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

1. Install `apt-get install isc-dhcp-relay -y` pada foosha, water7, dan guanhao, `apt-get install isc-dhcp-server` pada Jipangu. Jangan lupa start semuanya setelah proses instalasi selesai.
2. Install bind9 pada DNS Server DORIKI. Lalu konfigurasikan forwarder menuju ke `192.168.122.1` dengan meng-edit file `nano /etc/bind/named.conf.options`. Setelah itu, restart bind9.
```
apt-get update
apt-get install bind9 -y
```
```
options {
        directory "/var/cache/bind";

        forwarders{
                192.168.122.1;
        };

        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;     # conform to RFC1035
        listen-on-v6 { any; };
};
```
3. Pada Router (foosha, water7 dan guanhao) Edit file ```/etc/sysctl.conf``` di line berikut :
```
net.ipv4.ip_forward=1
net.ipv4.conf.all.accept_source_route = 1
```
4. Buka file `nano /etc/default/isc-dhcp-relay` dan edit server dengan mengarahkan dchp-relay menuju Jipangu(192.203.0.18) lalu `service isc-dhcp-relay restart` pada foosha, water7, dan guanhao
5. Pada Jipangu edit file `nano /etc/default/isc-dhcp-server` dengan menambahkan:
```
INTERFACES="eth0"
```
6. Pada dhcp-server(Jipangu) isikan data pada `nano /etc/dhcp/dhcpd.conf` di line paling bawah yang kosong :
```
subnet 192.203.1.0 netmask 255.255.255.0 {
	range 192.203.1.2 192.203.1.255;
	option routers 192.203.1.1;
	option broadcast-address 192.203.1.255;
	option domain-name-servers 192.203.0.19;
	default-lease-time 600;
	max-lease-time 7200;
}
subnet 192.203.0.128 netmask 255.255.255.128 {
	range 192.203.0.130 192.203.0.255;
	option routers 192.203.0.129;
	option broadcast-address 192.203.0.255;
	option domain-name-servers 192.203.0.19;
	default-lease-time 600;
	max-lease-time 7200;
}
subnet 192.203.4.0 netmask 255.255.252.0 {
	range 192.203.4.2 192.203.7.255;
	option routers 192.203.4.1;
	option broadcast-address 192.203.7.255;
	option domain-name-servers 192.203.0.19;
	default-lease-time 600;
	max-lease-time 7200;
}
subnet 192.203.2.0 netmask 255.255.254.0 {
	range 192.203.2.2 192.203.3.255;
	option routers 192.203.2.1;
	option broadcast-address 192.203.3.255;
	option domain-name-servers 192.203.0.19;
	default-lease-time 600;
	max-lease-time 7200;
}
subnet 192.203.0.16 netmask 255.255.255.248{
	option routers 192.203.0.17;		#relay
        default-lease-time 360;
        max-lease-time 7200;
}
```
7. Restart dhcp server.
8. Restart masing-masing client. Lalu cek ip masing-masing dengan command `ip a`. Tes dengan cara ping ke salah satu server/client lain, atau ping ke google.
