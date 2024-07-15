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
