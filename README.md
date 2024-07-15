curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -


echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt update

echo "deb http://security.ubuntu.com/ubuntu focal-security main" | sudo tee /etc/apt/sources.list.d/focal-security.list

sudo apt-get update

sudo apt-get install libssl1.1

sudo apt install mongodb-org

sudo systemctl start mongod.service

sudo systemctl status mongod.service

sudo systemctl enable mongod


### Creacion de servicios serveo.net



```bash
sudo nano /etc/systemd/system/serveo-tunnel.service
```

Dentro del archivo:

```ini
[Unit]
Description=Serveo SSH Tunnel
After=network.target

[Service]
ExecStart=/usr/bin/ssh -R port:localhost:27017 serveo.net
Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
```

> Nota: reemplazar `port` por el puesrto deseado

Guardar el archivo



Recargar systemctl para que reconozca el servicio nuevo 

```bash
sudo systemctl daemon-reload
```

Iniciamos el servicio:

```bash
sudo systemctl start serveo-tunnel.service
```

Habilitar el serrvicio cuando se arranca el equipo:

```bash
sudo systemctl enable serveo-tunnel.service
```

Verificar estado del servicio:

```bash
sudo systemctl status serveo-tunnel.service
```

