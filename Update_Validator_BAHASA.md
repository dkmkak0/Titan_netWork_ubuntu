## Panduan Pembaruan Node Titan di Ubuntu 22.04

Halo para Titan! Saya baru saja memperbarui node Titan saya di Ubuntu 20.04 dan ingin berbagi pengalaman untuk membantu semua orang menghindari kesalahan yang saya alami.

**Langkah 1: Persiapan**

* **Cadangkan data:** Jika Anda pernah berpartisipasi dalam testnet Herschel, cadangkan data penting di direktori `~/.titan` untuk mencegah kehilangan.
* **Instal Git dan Go:** Pastikan Anda telah menginstal Git dan Go di sistem Anda. Jika belum, jalankan perintah berikut:

```bash
rsync -av --exclude "data" ~/.titan ~/titan-test-1-config
```

```bash
sudo apt install git golang
```

**Langkah 2: Pembaruan dan Instalasi**

1. **Unduh skrip pembaruan:**

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/Titannet-dao/titan-chain/main/scripts/update.sh)"
```

2. **Kloning kode sumber Titan:**

```bash
git clone https://github.com/Titannet-dao/titan-chain.git
```

3. **Bangun alat baris perintah `titand`:**

```bash
cd titan-chain
go build ./cmd/titand
```

4. **Salin file yang dapat dieksekusi:**

```bash
sudo cp titand /usr/local/bin
```

5. **Buat file layanan (jika belum ada):**

```bash
sudo nano /etc/systemd/system/titan.service
```

Tempel konten berikut ke dalam file dan simpan (pastikan jalur ke direktori `titan-chain` benar):

```ini
[Unit]
Description=Titan Node Service
After=network-online.target

[Service]
User=root
WorkingDirectory=/root/titan-chain
ExecStart=/usr/local/bin/titand start
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

6. **Muat ulang konfigurasi dan mulai layanan:**

```bash
sudo systemctl daemon-reload
sudo systemctl start titan
```

## Langkah 3: Periksa dan Pecahkan Masalah (jika perlu)

* **Periksa status layanan:**

```bash
sudo systemctl status titan
```

Jika layanan tidak berjalan, lihat log untuk menemukan kesalahan:

```bash
journalctl -u titan
```

* **Periksa file konfigurasi (`~/.titan/config.toml`) dan file `genesis.json`:** Pastikan keduanya ada dan memiliki konten yang benar. Jika perlu, buat atau unduh ulang.

## Catatan:

* Selalu rujuk panduan resmi Titan untuk informasi terbaru.
* Berhati-hatilah saat menjalankan perintah dan buat cadangan data penting sebelum memperbarui.
