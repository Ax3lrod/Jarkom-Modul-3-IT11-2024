# Jarkom Modul 3

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image.png)

## **Konfigurasi Node**

- **Paradis (Router (DHCP Relay))**

```bash
auto eth0
iface eth0 inet dhcp
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.69.0.0/16

auto eth1
iface eth1 inet static
	address 10.69.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.69.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.69.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.69.4.1
	netmask 255.255.255.0
```

- **Armin (PHP Worker)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.1.2
	netmask 255.255.255.0
  gateway 10.69.1.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Eren (PHP Worker)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.1.3
	netmask 255.255.255.0
  gateway 10.69.1.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Mikasa (PHP Worker)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.1.4
	netmask 255.255.255.0
  gateway 10.69.1.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Erwin (Client)**

```bash
auto eth0
iface eth0 inet dhcp
```

- **Fritz (DNS Server)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.2.2
	netmask 255.255.255.0
  gateway 10.69.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Tybur  (DHCP Server)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.2.3
	netmask 255.255.255.0
  gateway 10.69.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Warhammer (Database Server)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.3.2
	netmask 255.255.255.0
  gateway 10.69.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Colossal (Load Balancer (PHP))**

```bash
auto eth0
iface eth0 inet static
	address 10.69.3.3
	netmask 255.255.255.0
  gateway 10.69.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Beast  (Load Balancer (Laravel))**

```bash
auto eth0
iface eth0 inet static
	address 10.69.3.4
	netmask 255.255.255.0
  gateway 10.69.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Zeke (Client)**

```bash
auto eth0
iface eth0 inet dhcp
```

- **Reiner (Laravel Worker)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.4.4
	netmask 255.255.255.0
  gateway 10.69.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Bertholdt (Laravel Worker)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.4.3
	netmask 255.255.255.0
  gateway 10.69.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- **Annie (Laravel Worker)**

```bash
auto eth0
iface eth0 inet static
	address 10.69.4.2
	netmask 255.255.255.0
  gateway 10.69.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

## Soal 0

Pulau Paradis telah menjadi tempat yang damai selama 1000 tahun, namun kedamaian tersebut tidak bertahan selamanya. Perang antara kaum Marley dan Eldia telah mencapai puncak. Kaum Marley yang dipimpin oleh Zeke**,** me-register domain name **marley.yyy.com** untuk worker Laravel mengarah pada **Annie**. Namun ternyata tidak hanya kaum Marley saja yang berinisiasi, kaum Eldia ternyata sudah mendaftarkan domain name **eldia.yyy.com** untuk worker PHP **(0)** mengarah pada **Armin.**

(1) Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

**Di Fritz (DNS Server):**

Jalankan:

```bash
apt-get update
apt-get install bind9 -y
```

```bash
service bind9 start
```

Menambahkan line berikut pada file `etc/bind/named.conf.local`

```bash
zone "marley.it11.com" {
 	type master;
 	file "/etc/bind/it11/marley.it11.com";
};

zone "eldia.it11.com" {
 	type master;
 	file "/etc/bind/it11/eldia.it11.com";
};
```

 Membuat DNS record pada `/etc/bind/it11/marley.it11.com`

```bash
$TTL    604800
@       IN      SOA     marley.it11.com. root.marley.it11.com. (
                        2				; Serial
                        604800			; Refresh
                        86400			; Retry
                        2419200         ; Expire
                        604800 )		; Negative Cache TTL
;
@		IN      NS      marley.it11.com.
@		IN      A       10.69.4.2 ; IP Annie
www		IN      CNAME   marley.it11.com.
```

Membuat DNS record pada `/etc/bind/it11/eldia.it11.com` 

```bash
$TTL    604800
@       IN      SOA     eldia.it11.com. root.eldia.it11.com. (
                        2				; Serial
                        604800			; Refresh
                        86400			; Retry
                        2419200         ; Expire
                        604800 )		; Negative Cache TTL
;
@		IN      NS      eldia.it11.com.
@		IN      A       10.69.1.2 ; IP Armin
www		IN      CNAME   eldia.it11.com.
```

Merestart service dari bind9

```
service bind9 restart
```

Atau bisa pake script `dnsserverno0.sh`:

```bash
#!/bin/bash

# Update sistem
apt-get update

# Install bind9
apt-get install bind9 -y

# Mulai layanan bind9
service bind9 start

# Tambahkan konfigurasi domain pada named.conf.local
cat <<EOT >> /etc/bind/named.conf.local
zone "marley.it11.com" {
    type master;
    file "/etc/bind/it11/marley.it11.com";
};

zone "eldia.it11.com" {
    type master;
    file "/etc/bind/it11/eldia.it11.com";
};
EOT

# Buat direktori jika belum ada
mkdir -p /etc/bind/it11

# Buat DNS record untuk marley.it11.com
cat <<EOT > /etc/bind/it11/marley.it11.com
\$TTL    604800
@       IN      SOA     marley.it11.com. root.marley.it11.com. (
                        2               ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@       IN      NS      marley.it11.com.
@       IN      A       10.69.4.2       ; IP Annie
www     IN      CNAME   marley.it11.com.
EOT

# Buat DNS record untuk eldia.it11.com
cat <<EOT > /etc/bind/it11/eldia.it11.com
\$TTL    604800
@       IN      SOA     eldia.it11.com. root.eldia.it11.com. (
                        2               ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@       IN      NS      eldia.it11.com.
@       IN      A       10.69.1.2       ; IP Armin
www     IN      CNAME   eldia.it11.com.
EOT

# Restart layanan bind9 untuk menerapkan perubahan
service bind9 restart

echo "Konfigurasi DNS untuk marley.it11.com dan eldia.it11.com selesai."
```

## Soal 1

Semua Client harus menggunakan konfigurasi ip address dari keluarga Tybur (dhcp).

**Di Tybur (DHCP Server):**

Install dependencies

```bash
apt-get update
apt-get install isc-dhcp-server -y
```

Menjalankan service dari isc-dhcp-server

```bash
service isc-dhcp-server start
```

Menambahkan line berikut pada file `/etc/default/isc-dhcp-server`

```bash
INTERFACES="eth0"
```

Menambahkan line berikut pada file `/etc/dhcp/dhcpd.conf`

```bash
subnet 10.69.1.0 netmask 255.255.255.0 {
	option routers 10.69.1.1;
	option broadcast-address 10.69.1.255;
	option domain-name-servers 10.69.2.2;
}

subnet 10.69.4.0 netmask 255.255.255.0 {
	option routers 10.69.4.1;
	option broadcast-address 10.69.4.255;
	option domain-name-servers 10.69.2.2;
}

subnet 10.69.2.0 netmask 255.255.255.0 {}

subnet 10.69.3.0 netmask 255.255.255.0 {}
```

Merestart service dari isc-dhcp-server

```bash
service isc-dhcp-server restart
```

Atau bisa pake shellscript `dhcpserverno1.sh`:

```bash
#!/bin/bash

# Update package list and install isc-dhcp-server
echo "Updating package list and installing isc-dhcp-server..."
apt-get update
apt-get install isc-dhcp-server -y

# Configure the DHCP server interface
echo "Configuring isc-dhcp-server interface..."
sed -i 's/^INTERFACES=.*/INTERFACES="eth0"/' /etc/default/isc-dhcp-server

# Configure the DHCP subnets
echo "Configuring DHCP subnets in /etc/dhcp/dhcpd.conf..."
cat <<EOT >> /etc/dhcp/dhcpd.conf
subnet 10.69.1.0 netmask 255.255.255.0 {
	option routers 10.69.1.1;
	option broadcast-address 10.69.1.255;
	option domain-name-servers 10.69.2.2;
}

subnet 10.69.4.0 netmask 255.255.255.0 {
	option routers 10.69.4.1;
	option broadcast-address 10.69.2.255;
	option domain-name-servers 10.69.2.2;
}

subnet 10.69.2.0 netmask 255.255.255.0 {}

subnet 10.69.3.0 netmask 255.255.255.0 {}
EOT

# Restart the DHCP server to apply the changes
echo "Restarting isc-dhcp-server service..."
service isc-dhcp-server restart

echo "DHCP server installation and configuration completed."

```

**Di Paradis (DHCP Relay):**

Instalasi dependencies yang diperlukan

```
apt-get update
apt-get install isc-dhcp-relay -y

```

Menjalankan service dari isc-dhcp-relay

```
service isc-dhcp-relay start
```

Menambahkan line berikut pada file `/etc/default/isc-dhcp-relay`

```
SERVERS="10.69.2.3"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
```

Menambahkan line berikut pada file `/etc/sysctl.conf`

```
net.ipv4.ip_forward=1
```

Merestart service dari isc-dhcp-relay

```
service isc-dhcp-relay restart
```

Menampilkan status dari isc-dhcp-relay

```bash
service isc-dhcp-relay status
```

Atau bisa menggunakan shell script:

```bash
#!/bin/bash

# Update package list and install isc-dhcp-relay
echo "Updating package list and installing isc-dhcp-relay..."
apt-get update
apt-get install isc-dhcp-relay -y

# Start the isc-dhcp-relay service
echo "Starting isc-dhcp-relay service..."
service isc-dhcp-relay start

# Configure isc-dhcp-relay settings
echo "Configuring isc-dhcp-relay in /etc/default/isc-dhcp-relay..."
cat <<EOT > /etc/default/isc-dhcp-relay
SERVERS="10.69.2.3"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
EOT

# Enable IP forwarding
echo "Enabling IP forwarding in /etc/sysctl.conf..."
if ! grep -q "net.ipv4.ip_forward=1" /etc/sysctl.conf; then
    echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
fi

# Apply the IP forwarding setting
echo "Applying IP forwarding setting..."
sysctl -p

# Restart the isc-dhcp-relay service to apply the changes
echo "Restarting isc-dhcp-relay service..."
service isc-dhcp-relay restart

# Display the status of isc-dhcp-relay
echo "Displaying isc-dhcp-relay status..."
service isc-dhcp-relay status
```

## Soal 2

**Client** yang melalui bangsa marley mendapatkan range IP dari [prefix IP].1.05 - [prefix IP].1.25 dan [prefix IP].1.50 - [prefix IP].1.100 **(2)**

**Di Tybur (DHCP Server):**

Edit konfigurasi `subnet 10.69.4.0` pada file `/etc/dhcp/dhcpd.conf` menjadi seperti berikut:

```bash
subnet 10.69.4.0 netmask 255.255.255.0 {
	range 10.69.4.14 10.69.4.28;
	range 10.69.4.49 10.69.4.70;
	option routers 10.69.4.1;
	option broadcast-address 10.69.4.255;
}
```

Merestart service dari isc-dhcp-server.

```bash
service isc-dhcp-server restart
```

## Soal 3

**Client** yang melalui bangsa eldia mendapatkan range IP dari [prefix IP].2.09 - [prefix IP].2.27 dan [prefix IP].2 .81 - [prefix IP].2.243 **(3)**

**Di Tybur (DHCP Server):**

Edit konfigurasi `subnet 10.69.1.0` pada file `/etc/dhcp/dhcpd.conf` menjadi seperti berikut:

```bash
subnet 10.69.1.0 netmask 255.255.255.0 {
	range 10.69.1.15 10.69.1.25;
	range 10.69.1.200 10.69.1.210;
	option routers 10.69.1.1;
	option broadcast-address 10.69.1.255;
}
```

Merestart service dari isc-dhcp-server.

```bash
service isc-dhcp-server restart
```

Tes di Zeke (Bangsa Marley):

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%201.png)

Tes di Erwin (Bangsa Eldia):

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%202.png)

## Soal 4

**Client** mendapatkan DNS dari keluarga **Fritz** dan dapat terhubung dengan internet melalui DNS tersebut.

**Di Tybur (DHCP Server):**

Edit konfigurasi `subnet 10.69.1.0` dan `subnet 10.69.4.0` pada file `/etc/dhcp/dhcpd.conf` menjadi seperti berikut:

```bash
subnet 10.69.1.0 netmask 255.255.255.0 {
	range 10.69.1.15 10.69.1.25;
	range 10.69.1.200 10.69.1.210;
	option routers 10.69.1.1;
	option broadcast-address 10.69.1.255;
	option domain-name-servers 10.69.2.2;
}

subnet 10.69.4.0 netmask 255.255.255.0 {
	range 10.69.4.14 10.69.4.28;
	range 10.69.4.49 10.69.4.70;
	option routers 10.69.4.1;
	option broadcast-address 10.69.4.255;
	option domain-name-servers 10.69.2.2;
}
```

**Di Fritz (DNS Server):**

Edit file `/etc/bind/named.conf.options` jadi seperti ini:

```bash
options {
    listen-on-v6 { none; };
    directory "/var/cache/bind";

    forwarders {
        192.168.122.1;
    };

    forward only;

    dnssec-validation no;

    auth-nxdomain no;
    allow-query { any; };
};
```

Jika ngga works coba restart service di Paradis:

```bash
service isc-dhcp-relay start
```

Tes dengan ping ke [`marley.it11.com`](http://marley.it11.com) dan `eldia.it11.com`  di Zeke (Client) dan Erwin (Client).

**Di Zeke:**

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%203.png)

**Di Erwin:**

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%204.png)

## Soal 5

Dikarenakan keluarga **Tybur** tidak menyukai kaum **eldia,** maka mereka hanya meminjamkan ip address ke kaum **eldia** selama 6 menit. Namun untuk kaum **marley**, keluarga **Tybur** meminjamkan ip address selama 30 menit. Waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit.

**Di Tybur (DHCP Server):**

Edit konfigurasi `subnet 10.69.1.0` dan `subnet 10.69.4.0` pada file `/etc/dhcp/dhcpd.conf` menjadi seperti berikut:

```bash
subnet 10.69.1.0 netmask 255.255.255.0 {
	range 10.69.1.15 10.69.1.25;
	range 10.69.1.200 10.69.1.210;
	option routers 10.69.1.1;
	option broadcast-address 10.69.1.255;
	option domain-name-servers 10.69.2.2;
	default-lease-time 360;
	max-lease-time 5220;
}

subnet 10.69.4.0 netmask 255.255.255.0 {
	range 10.69.4.14 10.69.4.28;
	range 10.69.4.49 10.69.4.70;
	option routers 10.69.4.1;
	option broadcast-address 10.69.4.255;
	option domain-name-servers 10.69.2.2;
	default-lease-time 1800;
	max-lease-time 5220;
}
```

Merestart service dari isc-dhcp-server.

```bash
service isc-dhcp-server restart
```

Tes di client (Zeke dan Erwin).

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%205.png)

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%206.png)

## Shortcut 1-5

**Di Tybur (DHCP Server):**

Jalankan script `dhcpserverno1-5.sh` :

```bash
#!/bin/bash

# Update package list and install isc-dhcp-server
echo "Updating package list and installing isc-dhcp-server..."
apt-get update
apt-get install isc-dhcp-server -y

# Configure the DHCP server interface
echo "Configuring isc-dhcp-server interface..."
sed -i 's/^INTERFACES=.*/INTERFACES="eth0"/' /etc/default/isc-dhcp-server

# Configure the DHCP subnets
echo "Configuring DHCP subnets in /etc/dhcp/dhcpd.conf..."
cat <<EOT >> /etc/dhcp/dhcpd.conf
subnet 10.69.1.0 netmask 255.255.255.0 {
	range 10.69.1.15 10.69.1.25;
	range 10.69.1.200 10.69.1.210;
	option routers 10.69.1.1;
	option broadcast-address 10.69.1.255;
	option domain-name-servers 10.69.2.2;
	default-lease-time 360;
	max-lease-time 5220;
}

subnet 10.69.2.0 netmask 255.255.255.0 {}

subnet 10.69.3.0 netmask 255.255.255.0 {}

subnet 10.69.4.0 netmask 255.255.255.0 {
	range 10.69.4.14 10.69.4.28;
	range 10.69.4.49 10.69.4.70;
	option routers 10.69.4.1;
	option broadcast-address 10.69.4.255;
	option domain-name-servers 10.69.2.2;
	default-lease-time 1800;
	max-lease-time 5220;
}
EOT

# Restart the DHCP server to apply the changes
echo "Restarting isc-dhcp-server service..."
service isc-dhcp-server restart

echo "DHCP server installation and configuration completed."
```

**Di Paradis (DHCP Relay):**

Jalankan script `dhcprelayno1.sh` :

```bash
#!/bin/bash

# Update package list and install isc-dhcp-relay
echo "Updating package list and installing isc-dhcp-relay..."
apt-get update
apt-get install isc-dhcp-relay -y

# Start the isc-dhcp-relay service
echo "Starting isc-dhcp-relay service..."
service isc-dhcp-relay start

# Configure isc-dhcp-relay settings
echo "Configuring isc-dhcp-relay in /etc/default/isc-dhcp-relay..."
cat <<EOT > /etc/default/isc-dhcp-relay
SERVERS="10.69.2.3"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
EOT

# Enable IP forwarding
echo "Enabling IP forwarding in /etc/sysctl.conf..."
if ! grep -q "net.ipv4.ip_forward=1" /etc/sysctl.conf; then
    echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
fi

# Apply the IP forwarding setting
echo "Applying IP forwarding setting..."
sysctl -p

# Restart the isc-dhcp-relay service to apply the changes
echo "Restarting isc-dhcp-relay service..."
service isc-dhcp-relay restart

# Display the status of isc-dhcp-relay
echo "Displaying isc-dhcp-relay status..."
service isc-dhcp-relay status
```

## Soal 6

**Armin** berinisiasi untuk memerintahkan setiap worker PHP untuk melakukan konfigurasi virtual host untuk website berikut [https://intip.in/BangsaEldia](https://intip.in/BangsaEldia) dengan menggunakan php 7.3

**Di PHP Worker (Armin, Eren, Mikasa):**

Instalasi dependencies yang diperlukan.

```bash
apt-get update
apt-get install lynx nginx wget unzip php7.3 php-fpm -y
```

Menjalankan service dari php-fpm dan nginx.

```bash
service php7.3-fpm start
service nginx start
```

Unduh file `index.php` , `info.php` , `styles.css` , `script.js` dan letakkan isinya pada directory `/var/www/html/eldia` .

```bash
mkdir -p /var/www/html/download/

# Download file dari Google Drive
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=1hGSy2l_9B149woArwgjSGoNeKPT1sRSQ' -O /var/www/html/download/eldia.zip

# Ekstrak file eldia.zip ke direktori tujuan
unzip /var/www/html/download/eldia.zip -d /var/www/html/download/

# Pindahkan file hasil ekstraksi ke direktori /var/www/html/
mv /var/www/html/download/eldia* /var/www/html/

# Hapus direktori download setelah selesai
rm -rf /var/www/html/download/
```

Menambahkan line berikut pada file `/etc/nginx/sites-available/jarkom-it11.conf` .

```bash
server {
	listen 80;

	root /var/www/html;

	index index.php index.html index.htm;

	server_name _;

	location / {
		try_files \$uri \$uri/ /index.php?\$query_string;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
	}

	error_log /var/log/nginx/jarkom-it11_error.log;
	access_log /var/log/nginx/jarkom-it11_access.log;
}
```

Buat symlink `jarkom-it11` pada `/etc/nginx/sites-available/` di `/etc/nginx/sites-enabled` .

```bash
ln -s /etc/nginx/sites-available/load-balancer-it11 /etc/nginx/sites-enabled
```

Hapus `default` pada `/etc/nginx/sites-enabled/` .

```bash
rm /etc/nginx/sites-enabled/default
```

Restart service nginx dan php-fpm.

```bash
service nginx restart
service php7.3-fpm restart
```

**Shortcut**

`phpworkerno6.sh`:

```bash
#!/bin/bash

# Install dependencies
apt-get update
apt-get install lynx nginx wget unzip php7.3 php-fpm -y

# Start services
service php7.3-fpm start
service nginx start

# Membuat direktori untuk download
mkdir -p /var/www/html/download/

# Mendownload file 'Bangsa Eldia.zip' dari Google Drive menggunakan wget
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=1aoymkLncLB26WjFxHUt9OKxAqR7_N1WS' -O /var/www/html/download/Bangsa_Eldia.zip

# Memastikan file terdownload dengan nama 'Bangsa Eldia.zip'
mv /var/www/html/download/*.zip /var/www/html/download/Bangsa_Eldia.zip

# Mengekstrak file Bangsa_Eldia.zip ke direktori download
unzip /var/www/html/download/Bangsa_Eldia.zip -d /var/www/html/download/

# Memindahkan file yang diekstrak ke direktori /var/www/html/
mv /var/www/html/download/'Bangsa Eldia'/modul-3/* /var/www/html/

# Menghapus direktori download
rm -rf /var/www/html/download/

# Nginx configuration for the site
echo 'server {
    listen 80;

    root /var/www/html;
    index index.php index.html index.htm;

    server_name _;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
    }

    error_log /var/log/nginx/jarkom-it11_error.log;
    access_log /var/log/nginx/jarkom-it11_access.log;
}' > /etc/nginx/sites-available/jarkom-it11.conf

# Enable the site by creating a symlink
ln -s /etc/nginx/sites-available/jarkom-it11.conf /etc/nginx/sites-enabled/

# Remove the default Nginx site
rm /etc/nginx/sites-enabled/default

# Restart services to apply changes
service nginx restart
service php7.3-fpm restart

echo "Setup complete!"
```

**Testing**

`lynx 10.69.1.2`

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%207.png)

`lynx 10.69.1.3` 

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%208.png)

`lynx 10.69.1.4` 

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%209.png)

## Soal 7

Dikarenakan Armin sudah mendapatkan kekuatan titan colossal, maka bantulah kaum **eldia** menggunakan **colossal** agar dapat bekerja sama dengan baik. Kemudian lakukan testing dengan 6000 request dan 200 request/second.

**Di Colossal (Load Balancer):**

Instalasi dependencies yang diperlukan

```
apt-get update
apt-get install lynx nginx php7.3 php-fpm apache2-utils -y

```

Menjalankan service dari php-fpm dan nginx

```bash
service php7.3-fpm start
service nginx start
```

Menambahkan line berikut pada file `/etc/nginx/sites-available/load-balancer-it11.conf` :

```bash
upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		proxy_pass http://worker;  # Mengarahkan ke grup upstream worker
		proxy_http_version 1.1;
		proxy_set_header Upgrade \$http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_set_header Host \$host;
		proxy_cache_bypass \$http_upgrade;
	}
}
```

Buat symlink `load-balancer-it11` pada `/etc/nginx/sites-available/` di `/etc/nginx/sites-enabled` 

```bash
ln -s /etc/nginx/sites-available/load-balancer-it11 /etc/nginx/sites-enabled
```

Hapus `default` pada `/etc/nginx/sites-enabled/` .

```bash
rm /etc/nginx/sites-enabled/default
```

Restart service nginx dan php-fpm.

```bash
service nginx restart
service php7.3-fpm restart
```

Shortcut:

`lbno7.sh` :

```bash
#!/bin/bash

# Update repositories
echo "Updating package repositories..."
apt-get update

# Install dependencies
echo "Installing lynx, nginx, php7.3, and php-fpm..."
apt-get install -y lynx nginx php7.3 php7.3-fpm apache2-utils

# Start php-fpm and nginx services
echo "Starting php7.3-fpm and nginx services..."
service php7.3-fpm start
service nginx start

# Define NGINX load balancer configuration
NGINX_CONF="/etc/nginx/sites-available/load-balancer-it11.conf"
echo "Creating nginx load balancer configuration at $NGINX_CONF..."

cat <<EOL > $NGINX_CONF
upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		proxy_pass http://worker;  # Mengarahkan ke grup upstream worker
		proxy_http_version 1.1;
		proxy_set_header Upgrade \$http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_set_header Host \$host;
		proxy_cache_bypass \$http_upgrade;
	}
}
EOL

# Enable the new configuration
echo "Creating symlink for load balancer configuration..."
ln -s $NGINX_CONF /etc/nginx/sites-enabled/load-balancer-it11

# Remove default configuration
echo "Removing default nginx configuration..."
rm /etc/nginx/sites-enabled/default

# Restart services
echo "Restarting nginx and php7.3-fpm services..."
service nginx restart
service php7.3-fpm restart

echo "Load balancer setup complete."

```

Lakukan testing dengan command:

```bash
ab -n 6000 -c 200 http://10.69.3.3/
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2010.png)

## Soal 8

Karena Erwin meminta “laporan kerja Armin”, maka dari itu buatlah analisis hasil testing dengan 1000 request dan 75 request/second untuk masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:

1. Nama Algoritma Load Balancer
2. Report hasil testing pada Apache Benchmark
3. Grafik request per second untuk masing masing algoritma.
4. Analisis

Testing dengan command:

```bash
ab -n 1000 -c 75 http://10.69.3.3/
```

1. **Round Robin (Default)**

```bash
upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2011.png)

2. **Weighted Round Robin**

```bash
upstream worker {
    server 10.69.1.2 weight=3; # Server ini akan menerima lebih banyak request
    server 10.69.1.3 weight=1;
    server 10.69.1.4 weight=2;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2012.png)

3. **Least Connections**

```bash
upstream worker {
    least_conn;
    server 10.69.1.2;
    server 10.69.1.3;
    server 10.69.1.4;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2013.png)

4. **Weighted Least Connections**

```bash
upstream worker {
    least_conn;
    server 10.69.1.2 weight=3;
    server 10.69.1.3 weight=1;
    server 10.69.1.4 weight=2;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2014.png)

5. **IP Hash**

```bash
upstream worker {
    ip_hash;
    server 10.69.1.2;
    server 10.69.1.3;
    server 10.69.1.4;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2015.png)

6. **Hash (Custom Hash)**

```bash
upstream worker {
    hash $request_uri consistent; # Menggunakan URI sebagai hash
    server 10.69.1.2;
    server 10.69.1.3;
    server 10.69.1.4;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2016.png)

## Soal 9

Dengan menggunakan algoritma Least-Connection, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 1000 request dengan 10 request/second, kemudian tambahkan grafiknya pada “laporan kerja Armin”.

Testing gunakan command:

```bash
ab -n 1000 -c 10 http://10.69.3.3/
```

1 Worker:

```bash
upstream worker {
    least_conn;
    server 10.69.1.2;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2017.png)

2 Worker:

```bash
upstream worker {
    least_conn;
    server 10.69.1.2;
    server 10.69.1.3;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2018.png)

3 Worker:

```bash
upstream worker {
    least_conn;
    server 10.69.1.2;
    server 10.69.1.3;
    server 10.69.1.4;
}
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2019.png)

## Soal 10

Selanjutnya coba tambahkan keamanan dengan konfigurasi autentikasi di **Colossal** dengan dengan kombinasi username: “arminannie” dan password: “jrkmyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/supersecret/

**Di Colossal (Load Balancer):**

Membuat folder supersecret

```bash
mkdir -p /etc/nginx/supersecret
```

Buat file `htpasswd` dengan username dan password yang telah ditentukan.

```bash
htpasswd -cb /etc/nginx/supersecret/htpasswd arminannie jrkmit11
```

Menjalankan service dari php-fpm dan nginx.

```bash
service php7.3-fpm start
service nginx start
```

Edit konfigurasi `server` pada file `/etc/nginx/sites-available/load-balancer-it11.conf` menjadi seperti berikut:

```bash
server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
				auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/supersecret/htpasswd;
        proxy_pass http://worker;
	}
}
```

Restart service nginx dan php-fpm.

```bash
service nginx restart
service php7.3-fpm restart
```

Shortcut:

`lbno10.sh` :

```bash
#!/bin/bash

# Membuat folder supersecret
echo "Membuat folder /etc/nginx/supersecret..."
mkdir -p /etc/nginx/supersecret

# Membuat file htpasswd dengan username dan password
echo "Menambahkan user 'arminannie' dengan password ke htpasswd..."
htpasswd -cb /etc/nginx/supersecret/htpasswd arminannie jrkmit11

# Menjalankan service php-fpm dan nginx
echo "Menjalankan php7.3-fpm dan nginx..."
service php7.3-fpm start
service nginx start

# Menambahkan konfigurasi autentikasi pada file load balancer
NGINX_CONF="/etc/nginx/sites-available/load-balancer-it11.conf"
echo "Mengedit konfigurasi server pada $NGINX_CONF..."

cat <<EOL > $NGINX_CONF
upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass http://worker;
		proxy_http_version 1.1;
		proxy_set_header Upgrade \$http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_set_header Host \$host;
		proxy_cache_bypass \$http_upgrade;
	}
}
EOL

# Restart nginx dan php-fpm
echo "Me-restart nginx dan php7.3-fpm..."
service nginx restart
service php7.3-fpm restart

echo "Konfigurasi selesai. Load balancer dengan autentikasi telah diatur."
```

Testing dengan command:

```bash
lynx 10.69.3.3
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2020.png)

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2021.png)

## Soal 11

Lalu buat untuk setiap request yang mengandung /titan akan di proxy passing menuju halaman [https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki](https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki) 

**Di Colossal (Load Balancer):**

Edit konfigurasi `server` pada file `/etc/nginx/sites-available/load-balancer-it11.conf` menjadi seperti berikut:

```bash
upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass http://worker;
	}
	
	location /titan {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki;
		proxy_set_header X-Real-IP \$remote_addr;
		proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto \$scheme;
	}

}
```

Restart service nginx dan php-fpm.

```bash
service nginx restart
service php7.3-fpm restart
```

Shortcut:

`lbno11.sh` :

```bash
#!/bin/bash

# Menambahkan konfigurasi autentikasi pada file load balancer
NGINX_CONF="/etc/nginx/sites-available/load-balancer-it11.conf"
echo "Mengedit konfigurasi server pada $NGINX_CONF..."

cat <<EOL > $NGINX_CONF
upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass http://worker;
	}
	
	location /titan {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki;
		proxy_set_header X-Real-IP \$remote_addr;
		proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto \$scheme;
	}

}

EOL

# Restart nginx dan php-fpm untuk menerapkan konfigurasi baru
echo "Me-restart nginx dan php7.3-fpm..."
service nginx restart
service php7.3-fpm restart

echo "Konfigurasi selesai. Load balancer dengan autentikasi telah diatur."

```

Testing dengan command:

```bash
lynx 10.69.3.3/titan
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2022.png)

## Soal 12

Selanjutnya **Colossal** ini hanya boleh diakses oleh client dengan IP [Prefix IP].1.77, [Prefix IP].1.88, [Prefix IP].2.144, dan [Prefix IP].2.156.

**Di Colossal (Load Balancer):**

Edit konfigurasi `server` pada file `/etc/nginx/sites-available/load-balancer-it11.conf` menjadi seperti berikut:

```bash

upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		allow 10.69.1.77;
		allow 10.69.1.88;
		allow 10.69.2.144;
		allow 10.69.2.156;
		deny all;
	
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass http://worker;
	}
	
	location /titan {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki;
		proxy_set_header X-Real-IP \$remote_addr;
		proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto \$scheme;
	}

}
```

Shortcut:

`lbno12.sh` :

```bash
#!/bin/bash

# Menambahkan konfigurasi autentikasi pada file load balancer
NGINX_CONF="/etc/nginx/sites-available/load-balancer-it11.conf"
echo "Mengedit konfigurasi server pada $NGINX_CONF..."

cat <<EOL > $NGINX_CONF

upstream worker {
	server 10.69.1.2; # IP Armin
	server 10.69.1.3; # IP Eren
	server 10.69.1.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		allow 10.69.1.77;
		allow 10.69.1.88;
		allow 10.69.2.144;
		allow 10.69.2.156;
		deny all;
	
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass http://worker;
	}
	
	location /titan {
		auth_basic "Restricted Content";
		auth_basic_user_file /etc/nginx/supersecret/htpasswd;
		proxy_pass https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki;
		proxy_set_header X-Real-IP \$remote_addr;
		proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto \$scheme;
	}

}

EOL

# Restart nginx dan php-fpm untuk menerapkan konfigurasi baru
echo "Me-restart nginx dan php7.3-fpm..."
service nginx restart
service php7.3-fpm restart

echo "Konfigurasi selesai. Load balancer dengan autentikasi telah diatur."

```

Testing di:

`10.69.3.3`

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2023.png)

## Soal 13

Karena mengetahui bahwa ada keturunan marley yang mewarisi kekuatan titan, Zeke pun berinisiatif untuk menyimpan data data penting di **Warhammer**, dan semua data tersebut harus dapat diakses oleh anak buah kesayangannya, **Annie**, **Reiner**, dan **Berthold**. 

**Di Warhammer (Database Server):**

Instalasi dependencies yang diperlukan.

```bash
apt-get update
apt-get install mariadb-server -y
```

Nyalakan mysql.

```bash
service mysql start
```

Membuat konfigurasi `mysql` sebagai berikut:

```bash
CREATE USER 'it11'@'%' IDENTIFIED BY 'passwordit11';
CREATE USER 'it11'@'localhost' IDENTIFIED BY 'passwordit11';
CREATE DATABASE dbkelompokit11;
GRANT ALL PRIVILEGES ON *.* TO 'it11'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'it11'@'localhost';
FLUSH PRIVILEGES;
```

Akses ke database mysql dengan username `it11` dan `passwordit11` lalu tampilkan semua database dengan command:

```bash
SHOW DATABASES;
```

Tambahkan line berikut pada file `/etc/mysql/my.cnf` 

```bash
[mysqld]
skip-networking=0
skip-bind-address
```

Restart service dari mysql.

```bash
service mysql restart
```

Shortcut:

`dbserverno13.sh` :

```bash
#!/bin/bash

# Update repositories
apt-get update

# Install MariaDB server
apt-get install mariadb-server -y

# Start the MySQL service
service mysql start

# Configure MySQL
mysql -u root <<EOF
CREATE USER 'it11'@'%' IDENTIFIED BY 'passwordit11';
CREATE USER 'it11'@'localhost' IDENTIFIED BY 'passwordit11';
CREATE DATABASE dbkelompokit11;
GRANT ALL PRIVILEGES ON *.* TO 'it11'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'it11'@'localhost';
FLUSH PRIVILEGES;
EOF

# Add configuration to allow external connections
cat <<EOT >> /etc/mysql/my.cnf

[mysqld]
skip-networking=0
skip-bind-address
EOT

# Restart MySQL service to apply changes
service mysql restart

# Login to MySQL as the 'it11' user and display databases
mysql -u it11 -p'passwordit11' -e "SHOW DATABASES;"
```

**Di Laravel Worker (Annie, Bertholdt, Reiner):**

Instalasi dependencies yang diperlukan:

```bash
apt-get update
apt-get install mariadb-server -y
```

Testing dengan command:

```bash
mariadb --host=10.69.3.2 --port=3306 --user=it11 --password==passwordit11
```

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2024.png)

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2025.png)

![image.png](Jarkom%20Modul%203%2012765c4d222080c79612ddde981a9707/image%2026.png)