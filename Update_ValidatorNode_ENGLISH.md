## Titan Node Update Guide on Ubuntu 22.04

Hello Titans! I recently updated my Titan node on Ubuntu 20.04 and wanted to share my experience to help others avoid the errors I encountered.

## Step 1: Preparation

* **Backup data (Optional):** If you participated in the Herschel testnet, back up important data in the `~/.titan` directory to avoid loss. You can use the following command:

```bash
rsync -av --exclude "data" ~/.titan ~/titan-test-1-config
```

* **Install Git and Go:** Ensure you have Git and Go installed on your system. If not, run the following command:

```bash
sudo apt install git golang
```

## Step 2: Update and Installation

1. **Download the update script:**

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/Titannet-dao/titan-chain/main/scripts/update.sh)"
```

2. **Clone the Titan source code:**

```bash
git clone https://github.com/Titannet-dao/titan-chain.git
```

3. **Build the `titand` command-line tool:**

```bash
cd titan-chain
go build ./cmd/titand
```

4. **Copy the executable file:**

```bash
sudo cp titand /usr/local/bin
```

5. **Create a service file (if not already present):**

```bash
sudo nano /etc/systemd/system/titan.service
```

Paste the following content into the file and save it (make sure the path to the `titan-chain` directory is correct):

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

6. **Reload the configuration and start the service:**

```bash
sudo systemctl daemon-reload
sudo systemctl start titan
```

## Step 3: Check and Troubleshoot (if necessary)

* **Check the service status:**

```bash
sudo systemctl status titan
```

If the service is not running, check the logs for errors:

```bash
journalctl -u titan
```

* **Check the configuration file (`~/.titan/config.toml`) and the `genesis.json` file:** Ensure they exist and have the correct content. If needed, create or re-download them.

## Note:

* Always refer to the official Titan documentation for the latest information.
* Be careful when executing commands and back up important data before updating.
