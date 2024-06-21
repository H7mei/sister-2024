# UAS Sister - README

## Informasi Kelompok

**Kelompok:** 16  
**Anggota:**

- Muhammad Hanafi Choirulloh - NIM: 1203210141
- Farel Ilyas Harianto - NIM: 1203210151

## Panduan Pengaturan

Panduan ini akan memandu Anda melalui proses pengaturan lingkungan UAS Sister menggunakan kontainer LXC, mengonfigurasi Nginx, dan menerapkan beberapa framework. Harap ikuti langkah-langkah berikut untuk memastikan semuanya berjalan dengan sukses.

### Langkah 1: Membuat Kontainer LXC

Untuk membuat kontainer LXC yang diperlukan, jalankan skrip Ansible yang disediakan. Ini akan mengatur semua kontainer yang diperlukan untuk proyek.

- **Lokasi:** Arahkan ke direktori berikut dan jalankan skrip Ansible yang ada di sana:

  ```bash
  /container/README.md
  ```

- **Verifikasi:** Pastikan setiap kontainer berjalan dengan sukses sebelum melanjutkan. Periksa status kontainer menggunakan perintah:

  ```bash
  lxc-ls --f
  ```

#### Foto:

![instal.png](/assets/install_container.png)

**Catatan Penting:**

- Pastikan lxc container sudah kosong untuk menjalankan script

### Langkah 2: Pengaturan Framework

Setelah kontainer berjalan, Anda perlu mengatur framework di setiap kontainer.

- **Lokasi Skrip:** Anda akan menemukan skrip Ansible yang diperlukan untuk pengaturan ini di:

  ```bash
  /frameworks/README.md
  ```

- **Eksekusi:** Jalankan setiap skrip secara berurutan. Pastikan setiap skrip selesai dengan sukses sebelum beralih ke yang berikutnya.

**Catatan Penting:**

- Jika ada masalah dalam pembuatan frameworks secara otomatis, pertimbangkan untuk melakukan instalasi secara manual satu per satu.

### Langkah 3: Konfigurasi Nginx

#### Pengaturan Hostname

Pertama, konfigurasikan hostname di file host Anda (`/etc/hosts`). Berikut adalah entri yang perlu Anda tambahkan atau modifikasi:

```bash
10.0.3.140    lxc_mariadb.dev
10.0.3.1      lxc_php5.dev
10.0.3.2      lxc_php5_2.dev
10.0.3.3      lxc_php7_2.dev
10.0.3.4      lxc_php7_3.dev
10.0.3.5      lxc_php7_4.dev
10.0.3.6      lxc_php7_5.dev
10.0.3.7      lxc_php7_6.dev
```

_Catatan:_ Pastikan alamat IP ini sesuai dengan yang terdaftar di LXC API Anda.

#### Konfigurasi Situs Nginx

Buat dan konfigurasikan situs Nginx yang tersedia di bawah `/etc/nginx/sites-available`. Berikut adalah konfigurasi untuk berbagai upstream dan blok server:

```bash
upstream laravel {
    least_conn;
    server Lxc_php7_2.dev;
    server Lxc_php7_3.dev;
    server Lxc_php7_4.dev;
    server Lxc_php7_6.dev;
}

upstream blog {
    ip_hash;
    server Lxc_php7_2.dev;
    server Lxc_php7_3.dev;
    server Lxc_php7_4.dev;
    server Lxc_php7_5.dev;
}

upstream yii {
    server Lxc_php7.dev weight=3;
    server Lxc_php7_2.dev weight=2;
    server Lxc_php7_4.dev weight=4;
    server Lxc_php7_5.dev weight=1;
    server Lxc_php7_6.dev weight=6;
}

upstream ci {
    server Lxc_php5.dev;
    server Lxc_php5_2.dev;
}

server {
    listen 80;
    listen [::]:80;
    server_name kelompok06.fpsas;
    root /var/www/html;
    index index.html;

    location /app {
        rewrite /app/(.*)$ /$1 break;
        proxy_pass http://ci;
    }

    #location /blog {
    #    rewrite /blog/(.*)$ /$1 break;
    #    proxy_pass http://Lxc_php7.dev;
    #}

    location /phpmyadmin {
        rewrite /phpmyadmin/(.*)$ /$1 break;
        proxy_pass http://lxc_mariadb.dev;
    }

    location /product {
        rewrite /product/(.*)$ /$1 break;
        proxy_pass http://yii;
    }

    location / {
        #rewrite /landing/(.*)$ /$1 break;
        proxy_pass http://laravel;
    }
}

server {
    listen 80;
    listen [::]:80;
    server_name news.kelompok06.fpsas;
    root /var/www/html;
    index index.html;

    location / {
        rewrite /app/(.*)$ /$1 break;
        proxy_pass http://blog;
    }
}
```

### Disclaimer / Batasan / Catatan

1. Jika terdapat masalah koneksi antara LXC dan Docker, solusinya adalah menghapus layanan Docker.
2. Pastikan semua dependencies dan paket yang diperlukan sudah terpasang sebelum menjalankan skrip Ansible untuk menghindari kegagalan dalam konfigurasi.

### Referensi

- [Sistem Administrasi Server 2021](https://github.com/aldonesia/Sistem-Administrasi-Server-2021)
- [Ujian SAS - Final Project Report](https://github.com/ignatiaindy/Ujian-SAS/blob/main/UAS/Final%20Project%20Report.md)
- [Install Packages with Ansible](https://opensource.com/article/20/9/install-packages-ansible)
- [LXC Containers on Ubuntu](https://ubuntu.com/server/docs/lxc-containers)

# sister-2024
