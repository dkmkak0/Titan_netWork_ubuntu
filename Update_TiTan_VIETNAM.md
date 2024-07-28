## Hướng dẫn Cập nhật Titan Node trên Ubuntu 22.04

Xin chào các Titan! Mình vừa cập nhật node Titan của mình trên Ubuntu 20.04 và muốn chia sẻ kinh nghiệm để giúp mọi người tránh gặp phải những lỗi mình đã gặp.

## Bước 1: Chuẩn bị

* **Sao lưu dữ liệu:** Nếu bạn đã từng tham gia testnet Herschel, hãy sao lưu dữ liệu quan trọng trong thư mục `~/.titan` để tránh mất mát.
* **Cài đặt Git và Go:** Đảm bảo bạn đã cài đặt Git và Go trên hệ thống của mình. Nếu chưa, hãy chạy lệnh sau:


```bash
rsync -av --exclude "data" ~/.titan ~/titan-test-1-config
```


```bash
sudo apt install git golang
```

## Bước 2: Cập nhật và cài đặt

1. **Tải xuống tập lệnh cập nhật:**

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/Titannet-dao/titan-chain/main/scripts/update.sh)"
```

2. **Clone mã nguồn Titan:**

```bash
git clone https://github.com/Titannet-dao/titan-chain.git
```

3. **Build công cụ dòng lệnh `titand`:**

```bash
cd titan-chain
go build ./cmd/titand
```

4. **Sao chép tệp thực thi:**

```bash
sudo cp titand /usr/local/bin
```

5. **Tạo tệp dịch vụ (nếu chưa có):**

```bash
sudo nano /etc/systemd/system/titan.service
```

Dán nội dung sau vào tệp và lưu lại (hãy chắc chắn đường dẫn đến thư mục `titan-chain` là chính xác):

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

6. **Nạp lại cấu hình và khởi động dịch vụ:**

```bash
sudo systemctl daemon-reload
sudo systemctl start titan
```

## Bước 3: Kiểm tra và khắc phục lỗi (nếu có)

* **Kiểm tra trạng thái dịch vụ:**

```bash
sudo systemctl status titan
```

Nếu dịch vụ không chạy, hãy xem log để tìm lỗi:

```bash
journalctl -u titan
```

* **Kiểm tra tệp cấu hình (`~/.titan/config.toml`) và tệp `genesis.json`:** Đảm bảo rằng chúng tồn tại và có nội dung đúng. Nếu cần, hãy tạo hoặc tải lại chúng.

## Lưu ý:

* Luôn tham khảo hướng dẫn chính thức của Titan để biết thông tin cập nhật nhất.
* Hãy cẩn thận khi thực hiện các lệnh và sao lưu dữ liệu quan trọng trước khi cập nhật.
