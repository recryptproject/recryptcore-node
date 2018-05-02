# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop recryptcore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/recryptcore-node.git
git clone git@github.com:<yourusername>/recryptcore-lib.git
```

To develop recrypt or to compile from source:

```bash
git clone git@github.com:<yourusername>/recryptcoin.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See recrypt documentation for building recrypt on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd bitcore-lib
npm install
cd ../bitcore-node
npm install
```
**Note**: If you get a message about not being able to download recrypt distribution, you'll need to compile recryptd from source, and setup your configuration to use that version.


We now will setup symlinks in `recryptcore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf recryptcore-lib
ln -s ~/recryptcore-lib
rm -rf recryptd-rpc
ln -s ~/recryptd-rpc
```

And if you're compiling or developing recryptcoin:
```bash
cd ../bin
ln -sf ~/recrypt/src/recryptd
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd recryptcore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/recryptd.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/recryptd.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch recryptcore-node.json
touch package.json
```

Edit `recryptcore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "recryptd",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "recryptd": {
      "spawn": {
        "datadir": "/home/<youruser>/.recrypt",
        "exec": "/home/<youruser>/recrypt/src/recryptd"
      }
    }
  }
}
```

**Note**: To install services [recrypt-insight-api](https://github.com/recryptproject/insight-api) and [recrypt-explorer](https://github.com/recryptproject/recrypt-explorer) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/recryptcore-lib
ln -s ~/recryptcore-node
ln -s ~/recrypt-insight-api
ln -s ~/recrypt-explorer
```

Make sure that the `<datadir>/recrypt.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashblock=tcp://127.0.0.1:28332
rpcallowip=127.0.0.1
rpcuser=user
rpcpassword=password
rpcport=18332
reindex=1
gen=0
addrindex=1
logevents=1
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../recryptcore-node/bin/recryptcore-node start
```