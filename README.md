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
ExecStart=/usr/bin/ssh -o StrictHostKeyChecking=no -R <port>:localhost:27017 serveo.net
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
Verificar estado del servicio:

```bash
sudo systemctl status serveo-tunnel.service
```

Habilitar el serrvicio cuando se arranca el equipo:

```bash
sudo systemctl enable serveo-tunnel.service
```


Pasos para la configuración de administradores en MongoDB:

Paso 1:
En la ruta `/etc/mongod.conf`, la seguridad viene desactivada por defecto. Localice el apartado de seguridad y reemplácelo por lo siguiente:

```yaml
security:
  authorization: enabled
```

Paso 2:
Reinicie el servicio de MongoDB:

```bash
sudo systemctl restart mongod
```

Paso 3:
Abra MongoDB Compass y realice la conexión normalmente. Si no se ha configurado previamente un usuario, use las credenciales por defecto proporcionadas durante la instalación inicial.

Paso 4:
En la consola de Compass, cree el usuario root:

```javascript
use admin

db.createUser({
   user: "root",
   pwd: passwordPrompt(),
   roles: [ { role: "root", db: "admin" } ]
})
```

Nota: Asegúrese de guardar las credenciales de acceso en un lugar seguro, ya que después de ejecutar este comando perderá acceso al cluster.

Paso 5:
Realice una nueva conexión con el siguiente string:

```
mongodb://root:<password>@localhost:27017/
```

Paso 6:
En la consola de Compass, cree el rol y usuario administrador para una única base de datos:

```javascript
db.createRole(
  {
    role: "administrador",
    privileges: [
      {
        resource: { db: "nombre_base_de_datos", collection: "" },
        actions: ["find", "insert", "update", "remove", "createCollection", "createIndex", "dropCollection"]
      },
      {
        resource: { db: "nombre_base_de_datos", collection: "system.users" },
        actions: ["find", "insert", "update", "remove"]
      },
      {
        resource: { db: "nombre_base_de_datos", collection: "system.roles" },
        actions: ["find", "insert", "update", "remove"]
      }
    ],
    roles: []
  }
)

db.createUser(
  {
    user: "admin",
    pwd: "campus2024",
    roles: [
      { role: "administrador", db: "" }
    ]
  }
)
```
# Esponer el puerto 27017 de mongo en la red LAM
```bash
sudo apt-get install net-tools
```
```bash
sudo ufw status
```
```bash
sudo ufw enable
```
```bash
sudo ufw status
```
```bash
sudo ufw allow 27017/tcp
```
Nota: Asegúrese de tener creada la base de datos a la cual va a asignar el usuario. Tenga en cuenta que las colecciones creadas bajo el usuario root serán accesibles únicamente por el usuario root.

Paso 7:
Realice una nueva conexión con el siguiente string:

```
mongodb://<nombre_de_usuario>:<password>@localhost:<port>/nombre_base_de_datos
```

Nota: Este nuevo usuario tiene acceso total únicamente a la base de datos específica asignada.
