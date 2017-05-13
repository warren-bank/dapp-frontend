### [dapp-frontend](https://github.com/warren-bank/dapp-frontend)

#### Description:

Command-line tool to initialize a Dapp frontend with a complete development toolchain.

Eventually, several toolchains will be available from which to select.
Currently, the following are implemented:
* `react`
  * React + Sass + Webpack
  * file watchers that trigger Webpack to rebundle .js and .css
  * http server (localhost:3000)

Within each toolchain, the example Dapp is a rewrite of the [`MetaCoin` Dapp](https://github.com/trufflesuite/truffle-init-webpack) that's used as an example (boilerplate) by `Truffle`.

#### Installation:

```bash
npm install -g @warren-bank/dapp-frontend
npm install -g @warren-bank/dapp-deploy

# needed to initialize the `react` toolchain
npm install -g create-react-app
npm install -g node-sass
```

#### Example:

```bash
mkdir ~/react_frontend_dapp
cd ~/react_frontend_dapp

dapp frontend react

# notes:
#   - in example project, compiled contracts are located in frontend dapp workspace.
#   - in real workflow, compiled contracts are located in `@dapphub/dapp` project workspace (./out)
#   - contract deployment requires: *.abi, *.bin
#   - frontend (web3.js) requires: *.abi.json, *.deployed.json
compiled_contracts_dir='./src/contracts/compiler-artifacts'
frontend_contracts_dir='./src/contracts'

# start: Ethereum JSON-RPC server
mnemonic='foo bar baz'
lxterminal -e testrpc -m $mnemonic

# clean and (re)populate: CONTRACTNAME.deployed.json
rm ${frontend_contracts_dir}/*.deployed.json
dapp deploy -i $compiled_contracts_dir -O $frontend_contracts_dir'/{{contract}}.deployed.json'

# clean and (re)populate: CONTRACTNAME.abi.json
rm ${frontend_contracts_dir}/*.abi.json
cp ${compiled_contracts_dir}/*.abi ${frontend_contracts_dir}/
rename 's/\.abi$/.abi.json/' ${frontend_contracts_dir}/*.abi

# start: http server and file watcher (.js, .css)
lxterminal -e npm start

# start: file watcher (.scss)
# notes:
#   - at launch, it will recompile (.scss => .css)
#   - the new .css bundle will trigger webpack to rebundle the .js
#   - this new .js bundle will contain the updated contract deployment addresses
#   - the browser will display 10000 "MetaCoin" tokens as available balance (in account index #0)
lxterminal -e npm run watch-css

# open Dapp in Chromium browser w/ "MetaMask" extension
# in "MetaMask":
#   - Localhost 8545
#   - Settings > Reveal Seed Words
#         produces the $mnemonic used to initialize `testrpc`
# this ensures that both Ethereum clients are sharing the same accounts (on the same blockchain)
xdg-open 'http://localhost:3000/'
```

#### Usage:

```bash
$ dapp-frontend --help

dapp-frontend command <directory_path>

Commands:
  react                                              initialize toolchain: 'React'

Options:
  --help                                             Show help [boolean]

Examples:
  dapp-frontend react ~/react_frontend_dapp          toolchain: 'React'
                                                     directory path: '~/react_frontend_dapp'
  dapp-frontend react                                toolchain: 'React'
                                                     directory path: '.'

copyright: Warren Bank <github.com/warren-bank>
license: GPLv2
```

#### Notes:

* This tool is intended to complement the `dapphub/dapp` toolchain, but it has no dependency on `dapp`.
* It uses vanilla [`web3.js`](https://github.com/ethereum/web3.js/) to communicate with an Ethereum JSON-RPC server.
* It uses [ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) data (.abi.json) to generate [contract](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethcontract) objects in javascript. This data is produced by the Solidity compiler (solc).
* It uses the addresses of deployed contracts (.deployed.json) to associate contract objects with the usable instances that exist in the blockchain. This data is produced by [`dapp-deploy`](https://github.com/warren-bank/dapp-deploy).

#### Dependencies:

* As indicated in the `Install` instructions,<br>
  a prerequisite is that some external tools need to be installed globally.
* These are large (powerful) packages, where there's no need to download a duplicate copy.
* The one exception is [`dapp-deploy`](https://github.com/warren-bank/dapp-deploy), which is a very small package that is intended to be used in combination with this tool. Strictly speaking, it can be omitted and the data structures it serves to produce (.deployed.json) can be written by hand.

#### Executables:

* When `dapp` is installed, this tool can be invoked by the command: `dapp frontend command <directory_path>`
* When used standalone, it can be invoked by the command: `dapp-frontend command <directory_path>`
* Additionally, each toolchain can be invoked directly:
  * `react`:<br>`create-react-dapp <directory_path>`

#### Legal:

* copyright: [Warren Bank](https://github.com/warren-bank)
* license: [GPLv2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.txt)
