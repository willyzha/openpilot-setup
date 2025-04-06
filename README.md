# openpilot-setup

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

7. Add your [Authtoken](https://dashboard.ngrok.com/get-started/your-authtoken) and [Edge-label](https://dashboard.ngrok.com/edges) from ngrok website and copy the modified example into ngrok.yml

> **_HINT:_** `Edge-label` should look something like `edghts_XXXXXXXXXXXXXXXXXXXX`

```
version: "3"
tunnels:
    OP_web:
        labels:
            - edge=<edge-label>
        addr: http://localhost:8082
    OP_ssh:
        proto: tcp
        addr: 22
agent:
    authtoken: <auth_token>
```

8. Remount / as rw, Install ngrok as service, Start ngrok as service, Reboot and test
```
sudo mount -o remount,rw /
sudo ngrok service install --config /persist/ngrok/ngrok.yml
sudo ngrok service start
sudo reboot
```

## Setup traccar

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

