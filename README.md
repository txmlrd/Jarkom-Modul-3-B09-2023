# Laporan Resmi Praktikum Jarkom Modul 3 Kelompok B09

Anggota Kelompok B09:
| Nama | NRP |
| ---------------------- | ---------- |
| Melanie Sayyidina Sabrina Refman | 5025211029 |
| I Gusti Agung Ngurah Adhi Sanjaya | 5025211056 |

----
## Topologi
![image](https://github.com/txmlrd/Jarkom-Modul-3-B09-2023/assets/87845735/d6872ecb-b353-4e6d-af97-89a95fbd10d0)



## Soal 0
Sebagai Raja iblis Muzan mulai mempersiapkan pertempuran melawan hashira dengan mempersiapkan prajuritnya, kalian diminta untuk melakukan register domain berupa muzan.uppermoon.yyy.com untuk Load balancer

## Solusi
Configurasi domain muzan.uppermoon.b09.com pada DNS server

```
apt-get update
apt-get install bind9 -y
mkdir /etc/bind/jarkom
echo '
zone "muzan.uppermoon.b09.com" {
        type master;
        file "/etc/bind/jarkom/muzan.uppermoon.b09.com";
};

' > /etc/bind/named.conf.local

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     muzan.uppermoon.b09.com. root.muzan.uppermoon.b09.com. (
                 2023122301 ; Serial (YYYYMMDDnn)
                         604800 ; Refresh
                          86400 ; Retry
                        2419200 ; Expire
                         604800 ) ; Negative Cache TTL
;
@       IN      NS      muzan.uppermoon.b09.com.
@       IN      A       10.13.2.11 ; IP Load Balancer
@       IN      AAAA    ::1

' > /etc/bind/jarkom/muzan.uppermoon.b09.com
service bind9 restart
```

Lalu setelah mengkonfigurasi, coba untuk test ping dari client lain ke domain tersebut 
![image](https://github.com/txmlrd/Jarkom-Modul-3-B09-2023/assets/87845735/1a3c84bf-d881-4d47-8961-f2f23e8c0504)

## Soal 1
Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

## Solusi
Mengkonfigurasi masing masing node dari topologi yang sudah di buat
- Gotoge
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.13.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.13.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.13.3.0
        netmask 255.255.255.0

```
- Tanjiro
```
auto eth0
iface eth0 inet static
	address 10.13.1.1
	netmask 255.255.255.0
	gateway 10.13.1.0
```
- Nezuko
```
auto eth0
iface eth0 inet static
	address 10.13.1.2
	netmask 255.255.255.0
	gateway 10.13.1.0
```
- Shinobu
```
auto eth0
iface eth0 inet static
	address 10.13.3.1
	netmask 255.255.255.0
	gateway 10.13.3.0
```
- Uzui
```
auto eth0
iface eth0 inet static
	address 10.13.3.2
	netmask 255.255.255.0
	gateway 10.13.3.0
```
- Muichiro
```
#auto eth0
#iface eth0 inet static
	#address 10.13.3.3
	#netmask 255.255.255.0
	#gateway 10.13.3.0
auto eth0
iface eth0 inet dhcp
gateway 10.13.3.0

```
- Akaza
```
#auto eth0
#iface eth0 inet static
#	address 10.13.2.4
#	netmask 255.255.255.0
#	gateway 10.13.2.0

auto eth0
iface eth0 inet dhcp
gateway 10.13.2.0
hwaddress ether b6:29:7b:d4:f2:60

```
- Kokushibo
```
#auto eth0
#iface eth0 inet static
	#address 10.13.2.3
	#netmask 255.255.255.0
	#gateway 10.13.2.0

auto eth0
iface eth0 inet dhcp
hwaddress ether 7e:0c:4e:f5:1f:8f
```
- Doma
```
#auto eth0
#iface eth0 inet static
#	address 10.13.2.2
#	netmask 255.255.255.0
#	gateway 10.13.2.0

auto eth0
iface eth0 inet dhcp
gateway 10.13.2.0
hwaddress ether da:ad:a8:7c:20:4c
```
- Muzan
```
#auto eth0
#iface eth0 inet static
	#address 10.13.2.1
	#netmask 255.255.255.0
	#gateway 10.13.2.0
auto eth0
iface eth0 inet dhcp
hwaddress ether 1a:07:1b:37:1e:77
```

## Soal 2
Client yang melalui Switch 2 mendapatkan range IP dari [prefix IP].2.11 - [prefix IP].2.22 dan [prefix IP].2.44 - [prefix IP].2.88 (2)

## Solusi
Jadi kita akan mengkonfigurasi Node yang berada pada Switch 2 untuk mendapatkan IP dari range tersebut, Dilihat pada topologi node Muzan, Doma, Kokushibo, dan Akaza adalah nodenya.
Karena pada soal, node node tersebut mendapatkan Fixed DHCP, maka konfigurasinya akan seperti berikut
## Tanjiro
```
host muzan {
    hardware ethernet 1a:07:1b:37:1e:77;
    fixed-address 10.13.2.11;
    option domain-name-servers 192.168.122.1;
    option routers 10.13.2.0;
}

host doma {
    hardware ethernet da:ad:a8:7c:20:4c;
    fixed-address 10.13.2.12;
    option domain-name-servers 192.168.122.1;
option routers 10.13.2.0;
}

host akaza {
    hardware ethernet b6:29:7b:d4:f2:60;
    fixed-address 10.13.2.13;
    option domain-name-servers 192.168.122.1;
option routers 10.13.2.0;
}

host kukishibo {
    hardware ethernet 7e:0c:4e:f5:1f:8f;
    fixed-address 10.13.2.14;
    option domain-name-servers 192.168.122.1;
option routers 10.13.2.0;
} > /etc/dhcp/dhcpd.conf
```
Jadi pada tanjiro, akan di lakukan konfigurasi DHCP dimana pada soal 2 ini dari masing2 node tersebut akan mendapatkan IP Fixed melalui DHCP yang di mulai dari 10.13.2.11 - 10.13.2.14

## Soal 3
Client yang melalui Switch 3 mendapatkan range IP dari [prefix IP].3.11 - [prefix IP].3.22 dan [prefix IP].3.44 - [prefix IP].3.88 (3)

## Solusi
Lalu pada no 3, kita lihat pada keterangan soal node yang kebagian untuk mendapatkan IP DHCP hanya dari Muichiro, maka konfigurasi DHCP pada Tanjiro sebagai berikut :

## Tanjiro
```
subnet 10.13.2.0 netmask 255.255.255.0 {

}

subnet 10.13.1.0 netmask 255.255.255.0 {

}

#muichiro3
subnet 10.13.3.0 netmask 255.255.255.0 {
    range 10.13.3.11 10.13.3.22;
    range 10.13.3.44 10.13.3.88;
    option routers 10.13.3.0;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.3.2;
    default-lease-time 180;
    max-lease-time 5760;
}

subnet 10.13.2.0 netmask 255.255.255.0 {
    range 10.13.2.15 10.13.2.22;
    range 10.13.2.44 10.13.2.88;
    option routers 10.13.2.0;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.3.2;
    default-lease-time 660;
    max-lease-time 5760;
} > /etc/dhcp/dhcpd.conf
```

Maka dari itu Dari Tanjiro sendiri memiliki configurasi DHCP seperti berikut

```
apt-get update
apt-get install isc-dhcp-server -y
echo 'INTERFACESv4="eth0"' > /etc/default/isc-dhcp-server
echo "
subnet 10.13.2.0 netmask 255.255.255.0 {

}

subnet 10.13.1.0 netmask 255.255.255.0 {

}

#muichiro3
subnet 10.13.3.0 netmask 255.255.255.0 {
    range 10.13.3.11 10.13.3.22;
    range 10.13.3.44 10.13.3.88;
    option routers 10.13.3.0;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.3.2;
    default-lease-time 180;
    max-lease-time 5760;
}

subnet 10.13.2.0 netmask 255.255.255.0 {
    range 10.13.2.15 10.13.2.22;
    range 10.13.2.44 10.13.2.88;
    option routers 10.13.2.0;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.3.2;
    default-lease-time 660;
    max-lease-time 5760;
}


host muzan {
    hardware ethernet 1a:07:1b:37:1e:77;
    fixed-address 10.13.2.11;
    option domain-name-servers 192.168.122.1;
    option routers 10.13.2.0;
}

host doma {
    hardware ethernet da:ad:a8:7c:20:4c;
    fixed-address 10.13.2.12;
    option domain-name-servers 192.168.122.1;
option routers 10.13.2.0;
}

host akaza {
    hardware ethernet b6:29:7b:d4:f2:60;
    fixed-address 10.13.2.13;
    option domain-name-servers 192.168.122.1;
option routers 10.13.2.0;
}

host kukishibo {
    hardware ethernet 7e:0c:4e:f5:1f:8f;
    fixed-address 10.13.2.14;
    option domain-name-servers 192.168.122.1;
option routers 10.13.2.0;
}
" > /etc/dhcp/dhcpd.conf

service isc-dhcp-server start
```

## Soal 4
Client mendapatkan DNS dari Shinobu dan dapat terhubung dengan internet melalui DNS tersebut (4)

## Solusi
Kita akan setting DNS pada Shinobu dengan command berikut
```
apt update
apt install bind9
echo "options {
        directory \"/var/cache/bind\";
        forwarders {
                192.168.122.1;
        };
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
" > /etc/bind/named.conf.options
```
Kita membuat forwarders ke 192.168.122.1 supaya client dapat terhubung dengan internet jika mendapatkan DNS dari Shinobu

Kita akan test ping pada salah satu client (Doma)
![image](https://github.com/txmlrd/Jarkom-Modul-3-B09-2023/assets/87845735/86a2115f-6d6a-4763-bd1b-d730e28de3fd)
Kita lihat bahwa nameserver pada Doma sudah mengarah pada IP dari Tanjiro sebagai DNS Server
![image](https://github.com/txmlrd/Jarkom-Modul-3-B09-2023/assets/87845735/b2c1651f-95ef-4d81-a890-ef4b5cc8d93d)
Lalu test ping dibuktikan dan Doma bisa mengakses internet keluar menggunakan forwarders IP dari Tanjiro

## Soal 5
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch 2 selama 11 menit sedangkan pada client yang melalui Switch 3 selama 3 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit (5)

## Solusi
Pada saat configurasi DHCP, kita akan menetapkan Waktu lease timenya sebagai berikut :
```
subnet 10.13.2.0 netmask 255.255.255.0 {
    range 10.13.2.15 10.13.2.22;
    range 10.13.2.44 10.13.2.88;
    option routers 10.13.2.0;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.3.2;
    default-lease-time 660;
    max-lease-time 5760;
}
```
Dilihat bahwa default lease timenya 660 detik/11 menit pada switch2

```
#muichiro3
subnet 10.13.3.0 netmask 255.255.255.0 {
    range 10.13.3.11 10.13.3.22;
    range 10.13.3.44 10.13.3.88;
    option routers 10.13.3.0;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.3.2;
    default-lease-time 180;
    max-lease-time 5760;
}
```
Dilihat bahwa default lease timenya 180 detik/3 menit pada switch3

Lalu diminta untuk waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit tertera pada masing masing konfigurasi 5760 detik/ 96 menit

## Soal 6
Pada masing-masing worker, lakukan deployment laravel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP 8.0 dan Composer dengan mengambil data pada database Uzui (6)

## Solusi
Kita akan menginstall PHP 8.0 dan Composer pada masing masing Laravel Worker dengan database dari Uzui melalui command berikut.
```
apt update
apt-get install mariadb-client -y

apt-get update
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get update
apt-get install php8.0-mbstring -y
apt-get install php8.0-xml -y
apt-get install php8.0-cli -y
apt-get install php8.0-common -y
apt-get install php8.0-intl -y
apt-get install php8.0-opcache -y
apt-get install php8.0-readline -y
apt-get install php8.0-mysql -y
apt-get install php8.0-fpm -y
apt-get install php8.0-curl -y
apt-get install unzip -y
apt-get install wget -y

      
apt-get install nginx -y

wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer

apt-get install git -y

cd /var/www/
git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git
cd laravel-praktikum-jarkom
composer update

echo "APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.13.3.2
DB_PORT=3306
DB_DATABASE=dbkelompokb09
DB_USERNAME=kelompokb09
DB_PASSWORD=passwordb09

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=\"hello@example.com\"
MAIL_FROM_NAME=\"\${APP_NAME}\"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY=\"\${PUSHER_APP_KEY}\"
VITE_PUSHER_HOST=\"\${PUSHER_HOST}\"
VITE_PUSHER_PORT=\"\${PUSHER_PORT}\"
VITE_PUSHER_SCHEME=\"\${PUSHER_SCHEME}\"
VITE_PUSHER_APP_CLUSTER=\"\${PUSHER_APP_CLUSTER}\"
" > .env

php artisan migrate:fresh
php artisan db:seed --class=AiringsTableSeeder
php artisan key:generate
php artisan jwt:secret

echo "
server {

    listen 80;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files \$uri \$uri/ /index.php?\$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php\$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/implementasi_error.log;
    access_log /var/log/nginx/implementasi_access.log;
}" > /etc/nginx/sites-available/implementasi

ln -s /etc/nginx/sites-available/implementasi /etc/nginx/sites-enabled/
rm -rf /etc/nginx/sites-enabled/default
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
service php8.0-fpm start
service nginx restart
```

## Kendala
- Untuk No 6, kami sudah mendapatkan solusi kenapa tidak works pada Laravel Worker kami. Kami mengganti image docker yang baru dan ternyata baru bisa jalan, tetapi solusi ini works saat waktu pengumpulan sudah sangat dekat jadi kami tidak bisa melanjutkannya.
- Karena waktu untuk mengerjakan Praktikum Susulan 3 berbarengan dengan EAS mata kuliah lain, maka kami baru dapat mengerjakannya full dari hari terakhir, hari kedua malam kami hanya menyicil sedikit. Sehingga kami hanya mendapatkan menyelsaikan sampai no 5
