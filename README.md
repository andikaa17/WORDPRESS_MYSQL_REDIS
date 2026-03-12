# Langkah-langkah WordPress Docker Stack

Setup WordPress CMS menggunakan Docker Compose dengan MySQL sebagai database dan Redis sebagai object cache.

## 1. Clone Repository

- git clone https://github.com/username/wordpress-docker.git
- cd wordpress-docker

  pastikan isinya sama seperti ini:

<img width="460" height="967" alt="image" src="https://github.com/user-attachments/assets/197af0ae-13a0-4188-a089-55bc05a4133e" />

## 2. Jalankan Docker Compose

- docker-compose up -d

  <img width="1919" height="602" alt="Cuplikan layar 2026-03-12 033349" src="https://github.com/user-attachments/assets/b083024a-c9a9-48f4-94ea-4cf486a16797" />

## 3. Verifikasi Container Berjalan

- docker ps ( untuk memastikan 3 container berikut berjalan)

<img width="1918" height="121" alt="Cuplikan layar 2026-03-12 033505" src="https://github.com/user-attachments/assets/fcabae1f-b55a-48a4-9757-d2226ee7a2ed" />

## 4. Akses WordPress

Buka browser dan akses:

- http://localhost:8000

## 5. Install WordPress

- Pilih bahasa -> klik Continue
- Isi form (Site Title, Username, Password, Email)
- Klik Install WordPress

  <img width="925" height="912" alt="Cuplikan layar 2026-03-12 055044" src="https://github.com/user-attachments/assets/bfe7c98a-fc27-4f59-babc-4f4f8e80dd4e" />

- Login ke dashboard

<img width="1919" height="1031" alt="Cuplikan layar 2026-03-12 033307" src="https://github.com/user-attachments/assets/13a8be87-7df5-449b-85c1-9f0a01829c69" />

## 6. Setup Redis Object Cache

- Di dashboard WordPress, klik Plugins -> Add New
- Search: Redis Object Cache -> Install Now -> Activate

<img width="1918" height="523" alt="Cuplikan layar 2026-03-12 033737" src="https://github.com/user-attachments/assets/49f26304-0b33-43e0-96ae-738af064d4f9" />

- Klik Settings -> Redis -> Enable Object Cache
- Pastikan status berubah menjadi Connected

  <img width="647" height="714" alt="image" src="https://github.com/user-attachments/assets/3c2efed7-f6be-453c-bd70-9f5fc91287a3" />

  Jika mengalami seperti gambar dibawah bisa dengan cara config manual :
  - docker exec -it wordpress-docker-wordpress-1 bash (masuk ke container dahulu)
  - sed -i "131s/._/define('WP_REDIS_HOST', 'redis');\ndefine('WP_REDIS_PORT', 6379);\n\n\/\* That's all, stop editing! Happy publishing. _\//" wp-config.php
    ( karena diwindows kadang mengalami bug seperti ini)

<img width="844" height="975" alt="Cuplikan layar 2026-03-12 034108" src="https://github.com/user-attachments/assets/7a579889-c19d-48c3-9dda-61c596896ef0" />

## 7. Stop Stack

- docker-compose down jika ingin dimatikan

# Pertanyaan

#### 1. Kenapa perlu volume untuk MySQL?

Supaya data tidak hilang ketika container di-restart atau dihapus. Tanpa volume, semua data database akan ikut hilang begitu container mati karena data tersimpan di dalam container, bukan di host.

#### 2. Apa fungsi depends_on?

Memastikan container MySQL sudah jalan lebih dulu sebelum container WordPress dijalankan. Karena WordPress butuh koneksi ke MySQL saat pertama kali start, kalau MySQL belum siap maka WordPress akan gagal konek.

#### 3. Bagaimana cara WordPress container connect ke MySQL?

Lewat Docker network (wp_network). WordPress menggunakan hostname mysql (nama service di docker-compose.yml) sebagai alamat database, bukan IP address. Docker secara otomatis me-resolve nama service tersebut ke IP container MySQL yang sedang berjalan.

#### 4. Apa keuntungan pakai Redis untuk WordPress?

Redis menyimpan hasil query database ke memory (cache), sehingga request berikutnya tidak perlu query ke MySQL lagi. Hasilnya website jadi lebih cepat, beban database berkurang, dan performa meningkat terutama saat traffic tinggi.
