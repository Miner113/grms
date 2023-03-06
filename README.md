![GRMS cli build - Ubuntu 18.04](https://github.com/GRMS-Coin/grms/workflows/GRMS%20cli%20build%20-%20Ubuntu%2018.04/badge.svg)\
![GRMS cli build - Ubuntu 20.04](https://github.com/GRMS-Coin/grms/workflows/GRMS%20cli%20build%20-%20Ubuntu%2020.04/badge.svg)\
![GRMScli build - Windows cross compile 18.04](https://github.com/GRMS-Coin/grms/workflows/GRMS%20cli%20build%20-%20Windows%20cross%20compile%2018.04/badge.svg)\
![GRMS cli build - Windows cross compile 20.04](https://github.com/GRMS-Coin/grms/workflows/GRMS%20cli%20build%20-%20Windows%20cross%20compile%2020.04/badge.svg)\
![GRMS cli build - MacOS 10.15 Catalina](https://github.com/GRMS-Coin/grms/workflows/GRMS%20cli%20build%20-%20MacOS%2010.15%20Catalina/badge.svg)\
![GRMS Header](https://grms.pw/images/logo.png "GRMS Header")

## GRMS 
This repository hosts the GRMS core blockchain software that is required to host all Komodo-based blockchains used by the [GRMS Platform](https://grms.pw/).

All stable releases used in production / notary node environments are hosted in the 'grms' branch. 
- [https://github.com/GRMS-Coin/grms](https://github.com/GRMS-Coin/grms/tree/grms)

GRMS is powered by the [Komodo Platform](https://komodoplatform.com/en), and contains code enhancements from the [Tokel Platform](https://github.com/TokelPlatform/tokel).

## List of GRMS Technologies
- All technologies from the main Komodo Platform codebase, such as:
  - Delayed Proof of Work (dPoW) - Additional security layer and Komodo's own consensus algorithm
  - zk-SNARKs - Komodo Platform's privacy technology for shielded transactions (however, it is unused and inaccessible in any of GRMS chains)
  - CC - Custom Contracts to realize UTXO-based "smart contract" logic on top of blockchains
- Enhancements inherited from the Tokel Platform codebase, such as:
  - Improvements to the Tokens & Assets CCs
  - Improvements to Komodo's nSPV technology. nSPV is a super-lite, next-gen SPV technology gives users the ability to interact with their tokens in a decentralized & trust-less fashion on any device, without the inconvenience and energy cost of downloading the entire blockchain.
- Agreements CC, a Komodo Custom Contract allowing fully on-chain digital contract creation & management
- Token Tags CC, a Komodo Custom Contract enabling amendable data logs attached to existing Tokens

## Installation
In order to run any of GRMS Komodo-based blockchains as a full node, you must build the GRMS daemon and launch the specified blockchain through it.
If you wish to run a production-grade GRMS blockchain, make sure you are running it from the 'grms' branch of this repository in order to avoid syncing issues.

#### Dependencies
```shell
#The following packages are needed:
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git zlib1g-dev wget curl bsdmainutils automake cmake clang ntp ntpdate nano -y
```

#### Linux
```shell
git clone https://github.com/GRMS-Coin/grms --branch grms --single-branch
cd grms
./zcutil/fetch-params.sh
./zcutil/build.sh -j$(expr $(nproc) - 1)
#This can take some time.
```

#### OSX
Ensure you have [brew](https://brew.sh/) and Command Line Tools installed.

```shell
# Install brew
/bin/bash -c "$(curl -fSSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
# Install Xcode, opens a pop-up window to install CLT without installing the entire Xcode package
xcode-select --install
# Update brew and install dependencies
brew update
brew upgrade
brew tap discoteq/discoteq; brew install flock
brew install autoconf autogen automake
brew update && brew install gcc@8
brew install binutils
brew install protobuf
brew install coreutils
brew install wget
# Clone the GRMS repo
git clone https://github.com/GRMS-Coin/grms --branch grms --single-branch
cd grms
./zcutil/fetch-params.sh
./zcutil/build-mac.sh -j$(expr $(sysctl -n hw.ncpu) - 1)
# This can take some time.
```

#### Windows
The Windows software cannot be directly compiled on a Windows machine. Rather, the software must be compiled on a Linux machine, and then transferred to the Windows machine. You can also use a Virtual Machine-based installation of Debian or Ubuntu Linux, running on a Windows machine, as an alternative solution.
Use a Debian-based cross-compilation setup with MinGW for Windows and run:

```shell
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git zlib1g-dev wget libcurl4-gnutls-dev bsdmainutils automake curl cmake mingw-w64 libsodium-dev libevent-dev
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup target add x86_64-pc-windows-gnu

sudo update-alternatives --config x86_64-w64-mingw32-gcc
# (configure to use POSIX variant)
sudo update-alternatives --config x86_64-w64-mingw32-g++
# (configure to use POSIX variant)

git clone https://github.com/GRMS-Coin/grms --branch grms --single-branch
cd grms
./zcutil/fetch-params.sh
./zcutil/build-win.sh -j$(expr $(nproc) - 1)
#This can take some time.
```

#### Launch GRMS
Change to the GRMS src directory:

```shell
cd ~/grms/src
```

Launch the GRMS chain command:

```shell
./grmsd &
```

Running the grmsd executable will launch the most up-to-date GRMS blockchain. If you wish to run a specific blockchain that is not the default, refer to GRMS Blockchain Specifics below.

Now wait for the chain to finish syncing. This might take while depending on your machine and internet connection. You can check check sync progress by using tail -f on the debug.log file in the blockchain's data directory. Double check the number of blocks you've downloaded with an explorer to verify you're up to the latest block.

GRMS uses CryptoConditions that require launching the blockchain with the -pubkey parameter to work correctly. Once you have completed block download, you will need to create a new address or import your current address. After you have done that, you will need to stop the blockchain and launch it with the -pubkey parameter.

You can use the RPC below to create a new address or import a privkey you currently have.

```shell
./grms-cli getnewaddress
```

```shell
./grms-cli importprivkey
```

Once you have completed this, use the validateaddress RPC to find your associated pubkey.

```shell
./grms-cli validateaddress *INSERTYOURADDRESSHERE*
```

Once you have written down your pubkey, stop the blockchain daemon.

```shell
cd ~/grms/src
./grms-cli stop
```

Wait a minute or so for the blockchain to stop, then relaunch the blockchain with the command below. Please remove the ** and replace them with the pubkey of the address you imported.

```shell
cd ~/grms/src
./grmsd -pubkey=**YOURPUBKEYHERE** &
```

You are now ready to use the GRMS software to its fullest extent.

## GRMS Resources
- GRMS Website: [https://grms.pw](https://grms.pw)
- GRMS Explorer: [Invitation](https://explorer.grms.pw)
- GRMS MOBILE WALLET: [Invitation](https://atomicdex.io/en/mobile/)
- GRMS DESKOP WALLET: [Invitation](https://atomicdex.io/en/desktop/)
- GRMS Exchanges AtomicDex: [Invitation](https://atomicdex.io/)
- GRMS Email: [support@grms.pw](mailto:support@grms.pw)

## GRMS Blockchain Specifics

- Max Supply: 120 million GRMS
- Block Time: 30 seconds
- Starting Block Reward (Era 3): 12-9-5 GRMS
- Mining Algorithm: 50% PoW |50% PoS



## License

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
