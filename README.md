# openpilot-setup

## SSH

1. Add SSH key to github

2. Add github to comma device in networking

3. SSH into comma
```
ssh comma@<ip_addr>
```

## Setup ngrok

1. Remount / as rw
```
sudo mount -o remount,rw /
```

2. Install ngrok
```
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list && sudo apt update && sudo apt install ngrok
```

3. Remount /persist as rw:
```
sudo mount -o remount,rw /persist
```

4. Create ngrok directory in /persist:
```
sudo mkdir -p /persist/ngrok
```

5. Remount / as rw
```
sudo mount -o remount,rw /
```

6. Create ngrok config file:
```
sudo nano /persist/ngrok/ngrok.yml
```

7. Add your [auth_token](https://dashboard.ngrok.com/get-started/your-authtoken) and [domain](https://dashboard.ngrok.com/domains) from ngrok website and copy the modified example into ngrok.yml

```
version: "3"
agent:
    authtoken: <auth_token>
endpoints:
  - name: comma
    url: <domain>
    upstream:
      url: 8082
```

8. Remount / as rw, Install ngrok as service, Start ngrok as service, Reboot and test
```
sudo mount -o remount,rw /
sudo ngrok service install --config /persist/ngrok/ngrok.yml
sudo ngrok service start
sudo reboot
```

## Install traccar

1. Mount and clone traccar-python-client
```
sudo mount -o remount,rw /persist
cd /persist
git clone https://github.com/webnizam/traccar-python-client
cd traccar-python-client
```

2. Edit .env config
```
sudo nano .env
```

```
DB_PATH=/run/shm/gps_data.db
BUFFER_SIZE=10
SERVER_URL=<traccar server URL>
DEVICE_ID=<traccar device ID>
UPDATE_FREQUENCY=5
```

3. Remount / as rw, run setup sevice, reboot and test
```
sudo mount -o remount,rw /
sudo bash setup_service.sh
sudo reboot
systemctl status gps-tracker.service
```

## Enable/Disable traccar
```
sudo mount -o remount,rw /

# Disable
sudo systemctl stop gps-tracker.service
sudo systemctl disable gps-tracker.service

# Enable
sudo systemctl start gps-tracker.service
sudo systemctl enable gps-tracker.service

sudo reboot
systemctl status gps-tracker.service
```

