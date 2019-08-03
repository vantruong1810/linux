#  Install KONGA
Link reference: 
- Main: https://github.com/pantsel/konga/blob/master/README.md
- JP: https://blog.web.nifty.com/engineer/491
- EN: https://devhub.io/repos/pantsel-konga

# Prerequisites
- A running Kong installation [here](https://github.com/vantruong1810/linux/blob/master/kong.md "Kong installation")
- Nodejs >= 8 (8.11.3 LTS is recommended)
- Npm

# Installation
- Install `npm` and `node.js`
- Install `bower`, `gulp`, `sails` packages. ```sudo npm install bower gulp sails -g```
## Install NodeJS 10
```
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -
sudo apt-get install -y nodejs
```

## Install NodeJS 8
```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo npm install bower gulp sails -g
```

## Install Konga
```
cd /var/www/html # Optional
git clone https://github.com/pantsel/konga.git konga
cd konga
npm install
```
# Run
```
npm start # Run background `npm start --prod &`
```
# Use `pm2`
Install:
```
sudo npm install pm2 -g
```
Run `pm2` on startup & background:
```yaml
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu
cd konga
pm2 run -n "Konga" # If it doesn't work, please run `pm2 start npm -- start -n "Konga"`
pm2 save
```

Konga GUI will be available at `http://localhost:1337` or `http://localhost:1338` 

# Note:
Please change CONNECTIONS > default: `http://kong:8001` to `http://KongServerIP:8001` or `http://localhost:8001` (same server)
