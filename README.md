# RoadSense
Prototipe IoT dua perangkat edge device: indikasi ketidakrataan permukaan berdasarkan guncangan.

## Publikasi GitHub Pages
1. Buat repository PUBLIC bernama `roadsense` di akun `Art3mis-007`.
2. Ekstrak ZIP. Upload isi folder ini ke root repository: index.html, app.js, style.css, mqtt.min.js, README.md dan .nojekyll (opsional). Jangan upload ZIP saja.
3. Commit changes.
4. Settings > Pages > Build and deployment > Source: Deploy from a branch.
5. Branch: main, folder: /(root), Save.
6. Tunggu deployment selesai. Alamat target: https://art3mis-007.github.io/roadsense/

## Demo dua iPhone
Buka URL HTTPS langsung di Safari. Gunakan Wi-Fi/hotspot yang sama dan internet aktif.
Kode sesi harus sama, contoh `jalan-demo29`. Pilih A di HP pertama, B di HP kedua.
Tekan Hubungkan cloud, tunggu cloud terhubung. Tekan Izinkan sensor di masing-masing HP lalu Allow.
Diamkan HP dan kalibrasi 5 detik. Setelah kedua baseline muncul tekan Mulai bersama.
Biarkan Safari di depan dan layar menyala. Berhenti dan Unduh CSV setelah pengukuran.
Untuk laptop, gunakan peran Dashboard saja dengan sesi yang sama.

## Arsitektur dan algoritma
Dua edge device iPhone masing-masing menggunakan accelerometer dan gyroscope lewat DeviceMotionEvent.
Setiap edge menghitung RMS dan puncak besar percepatan linear serta rata-rata besar rotationRate per interval satu detik.
Fallback percepatan: low-pass gravitasi dengan konstanta waktu 0,8 detik.
MQTT over secure WebSocket: wss://broker.hivemq.com:8884/mqtt.
Topik: roadsense/v1/<kode-sesi>/data/A atau B, status/A atau B, dan command.
Perintah mulai, berhenti, kalibrasi dikirim lewat cloud ke kedua HP. Pesan tidak retained.

Ambang demo: sedang RMS >= max(0,7; baseline*3). Tinggi RMS >= max(1,8; baseline*6) atau puncak >=4 m/s².
Rotasi >=35 derajat/detik ditandai kemungkinan manuver. Baseline diukur saat HP diam selama 5 detik.
Ambang heuristik ini belum tervalidasi untuk diagnosis jalan. Polisi tidur, suspensi, kecepatan dan dudukan memengaruhi hasil.
Sebut hasil sebagai indikasi guncangan/ketidakrataan, bukan kepastian jalan rusak.

## Batas pengujian
Pemeriksaan sintaks dan alur pemrosesan dengan input sintetis telah dilakukan pada versi awal.
Pengujian sensor fisik dua iPhone dan koneksi broker di jaringan pengguna tetap diperlukan.
Jika cloud terputus: pastikan internet aktif, tunggu reconnect, muat ulang halaman bila perlu.
Jika sensor tidak aktif: Safari langsung melalui HTTPS, izin gerak aktif, layar menyala.
Jangan gunakan file:// atau HTTP dari alamat IP laptop untuk sensor iOS.
Tidak ada data GPS, akun aplikasi, atau penyimpanan permanen; CSV berisi data yang diterima tab selama terbuka.
Broker publik; kode sesi unik mengurangi benturan tetapi bukan autentikasi. Jangan kirim data pribadi.

Pustaka: MQTT.js 5.10.4, lisensi MIT, disertakan sebagai mqtt.min.js.
