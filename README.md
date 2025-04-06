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

7. Add your Authtoken and Edge-label from ngrok website and copy the modified example into ngrok.yml
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
