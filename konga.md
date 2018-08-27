#  Install KONGA
Link reference: 
- Main: https://github.com/pantsel/konga/blob/master/README.md
- JP: https://blog.web.nifty.com/engineer/491
- EN: https://devhub.io/repos/pantsel-konga

# Prerequisites
- A running Kong installation [here](https://github.com/vantruong1810/linux/blob/master/kong.md "Kong installation")
- Nodejs >= 8 (8.11.3 LTS is recommended)
- Npm
- bower, gulp

# Installation
- Install `npm` and `node.js`. Instructions can be found [here](https://github.com/pandao/editor.md "Node").
- Install `bower`, ad `gulp` packages.

## Install NodeJS 8
```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo npm install bower gulp sails -g
```

## Install Konga
```
git clone https://github.com/pantsel/konga.git
cd konga
npm install
```
# Configuration
```
cd konga/config
cp -pr local_example.js local.js
vim local.js

npm start # Run background `npm start --prod &`
```
Konga GUI will be available at `http://localhost:1337`

# Login
Admin: `admin` | `adminadminadmin`
Demo: `demo` | `demodemodemo`

# Note:
Please change CONNECTIONS > default: `http://kong:8001` to `http://localhost:8001`
