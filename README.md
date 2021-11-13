# Jarkom-Modul-3-D05-2021

- Andika Nugrahanto (0511194000031)
- Muhammad Rayhan Raffi Pratama (05111940000110)
- Fadhil Dimas Sucahyo (05111940000212)

## Pendahuluan

### Setting Topologi

![0.1](imgs/0.1.JPG)

### Edit Knofigurasi Network

#### Foosha

![0.2](imgs/0.2.JPG)
![0.3](imgs/0.3.JPG)

#### EniesLobby

![0.4](imgs/0.4.JPG)

#### Water7

![0.5](imgs/0.5.JPG)

#### Jipangu

![0.6](imgs/0.6.JPG)

#### Loguetown, Alabasta, Totoland, Skypie

![0.7](imgs/0.7.JPG)

## no. 1

Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server

### Jawab

#### Foosha

Menjalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.194.0.0/16` yang digunakan supaya dapat terhubung ke jaringan luar pada router `Foosha`
![1.1](imgs/1.1.JPG)

Setelah itu pada EniesLobby, Water7, Jipangu dijalankan command `echo "nameserver 192.168.122.1" > /etc/resolv.conf` untuk setting IP DNS agar dapat terhubung ke jaringan luar.

#### EniesLobby

pada EniesLobby jalankan command `apt-get update` dan `apt-get install bind9 -y` untuk menginstall bind9
![1.2](imgs/1.2.JPG)

#### Jipangu

pada Jipangu jalankan command `apt-get update` dan `apt-get install isc-dhcp-server -y` untuk menginstall isc-dhcp-server
![1.3](imgs/1.3.JPG)

Kemudian setting `INTERFACES` yang digunakan oleh Jipangu pada file `/etc/default/isc-dhcp-server` dengan menambahkan `eth0`
![1.5](imgs/1.5.JPG)

#### Water7

pada Water7 jalankan command `apt-get update` dan `apt-get install squid -y` untuk menginstall squid
![1.4](imgs/1.4.JPG)

## no. 2

Foosha sebagai DHCP Relay

### Jawab

#### Foosha

pada Foosha jalankan command `apt-get update` dan `apt-get install isc-dhcp-relay -y` untuk menginstall isc-dhcp-relay
![2.1](imgs/2.1.JPG)

Kemudian edit file `/etc/default/isc-dhcp-relay` dengan menambahkan `SERVER = "IP Jipangu"` dan `INTERFACES = "eth1 eth2 eth3"`
![2.2](imgs/2.2.JPG)

Lalu jalankan command `service isc-dhcp-relay restart`

## no. 3

Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169

### Jawab

#### Jipangu

Edit file `/etc/dhcp/dhcpd.conf` dengan menambahkan:

```bash
    subnet 192.194.1.0 netmask 255.255.255.0 {
        range 192.194.1.20 192.194.1.99;
        range 192.194.1.150 192.194.1.169;
        option routers 192.194.1.1;
        option broadcast-address 192.194.1.255;
        option domain-name-servers 192.194.2.2;
        default-lease-time 360;
        max-lease-time 7200;
    }
```

![3.1](imgs/3.1.JPG)

Lalu jalankan command `service isc-dhcp-server restart`

## no. 4

Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50

### Jawab

#### Jipangu

Edit file `/etc/dhcp/dhcpd.conf` dengan menambahkan:

```bash
    subnet 192.194.3.0 netmask 255.255.255.0 {
        range 192.194.3.30 192.194.3.50;
        option routers 192.194.3.1;
        option broadcast-address 192.194.3.255;
        option domain-name-servers 192.194.2.2;
        default-lease-time 720;
        max-lease-time 7200;
    }
```

![4.1](imgs/4.1.JPG)

Lalu jalankan command `service isc-dhcp-server restart`

## no. 5

Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut.

### Jawab

#### EniesLobby

Edit file `/etc/bind/named.conf.options` dengan menambahkan

```bash
    forwarders {
        "IP nameserver dari Foosha";
    };

    allow-query{any;};
```

dan mengkomen bagian

```bash
    // dnssec-validation auto;
```

![5.2](imgs/5.2.JPG)

kemudian jalankan command `service bind9 restart`

#### Jipangu

Edit file `/etc/dhcp/dhcpd.conf` dengan menambahkan baris `option domain-name-servers "IP EniesLobby"` pada `subnet 192.194.1.0` dan `subnet 192.194.3.0`

![5.1](imgs/5.1.JPG)
