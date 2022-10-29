# Jarkom-Modul-2-E13-2022
Nama Anggota :<br>
- Rycahaya Sri Hutomo (5025201046)
- Agnesfia Anggraeni (5025201059)

## Nomor 1
WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet 
```
#!/bin/bash
Ostania(){
    apt-get update
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.28.0.0/16

}
```
```
Wise() {
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
echo 'zone "wise.e13.com" {
type master;
file "/etc/bind/wise/wise.e13.com";
};'
```

## NOMOR 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise
```
echo 'zone "wise.e13.com" {
        type master;
        file "/etc/bind/wise/wise.e13.com";
};' > /etc/bind/named.conf.local
mkdir /etc/bind/wise
echo "
\$TTL    604800
@       IN      SOA     wise.e13.com. root.wise.e13.com. (
                                2       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      wise.e13.com.
@               IN      A       10.28.2.2 ; IP Wise
www             IN      CNAME   wise.e13.com.
" > /etc/bind/wise/wise.e13.com
service bind9 restart
```

## NOMOR 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden

```
echo "
\$TTL    604800
@       IN      SOA     wise.e13.com. root.wise.e13.com. (
                                2       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      wise.e13.com.
@               IN      A       10.28.2.2 ; IP Wise
www             IN      CNAME   wise.e13.com.
eden           IN      A       10.28.2.3 ; IP Eden
www.eden       IN      CNAME   eden.wise.e13.com.
" > /etc/bind/wise/wise.e13.com
service bind9 restart
```

## NOMOR 4
Buat juga reverse domain untuk domain utama
```
echo '
zone "wise.e13.com" {
        type master;
        file "/etc/bind/wise/wise.e13.com";
};

zone "3.28.10.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/3.28.10.in-addr.arpa";
};' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/wise/3.28.10.in-addr.arpa

echo "
\$TTL    604800
@       IN      SOA     wise.e13.com. root.wise.e13.com. (
                                2022102601       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
3.28.10.in-addr.arpa.   IN      NS      wise.e13.com.
2                       IN      PTR     wise.e13.com.
"> /etc/bind/wise/3.28.10.in-addr.arpa
service bind9 restart
```

Kemudian, testing
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install dnsutils

echo 'nameserver 10.28.3.2' > /etc/resolv.conf

host -t PTR 10.28.3.2
```
