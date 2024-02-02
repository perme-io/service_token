# PERME-Service-Token
(ICON JAVA SCORE)
---

# Getting Started
## Requirements

1. **Openjdk 11**

2. **goloop CLI**
   see. https://icondev.io/getting-started/how-to-write-a-smart-contract

~~~
$ git clone git@github.com:icon-project/goloop.git
$ GOLOOP_ROOT=/path/to/goloop
$ cd ${GOLOOP_ROOT}
$ brew install rocksdb
$ brew install snappy
$ CGO_CFLAGS="-I/opt/homebrew/opt/rocksdb/include" CGO_LDFLAGS="-L/opt/homebrew/opt/rocksdb/lib -L/opt/homebrew/opt/snappy/lib -lrocksdb -lstdc++ -lm -lz -lbz2 -lsnappy" make goloop
$ ./bin/goloop version

goloop version v1.2.6 ...  # Success if version information is displayed.
~~~

## Generate a keystore and get test ICX

~~~
$ ${GOLOOP_ROOT}/bin/goloop ks gen --out keystore.json --password xxxx

hxd9d79a5695f...2459905 ==> keystore.json  # Success if hxADDR is displayed.
~~~

Faucets for test ICX: https://icondev.io/icon-stack/icon-networks/main-network#test-network-faucets


## Build

~~~
$ gradle build
$ gradle optimizedJar
~~~


## Test

~~~
$ gradle cleanTest test -i
~~~


## Deploy

Generate a keystore and get test ICX (see above.)

Update Token Information

edit: java-score/build.gradle
~~~gradle
   parameters {
        arg('_name', 'YourTokenName')
        arg('_symbol', 'TTT <- YourTokenSymbol')
        arg('_decimals', '0x12 <- YourTokenDecimals')
        arg('_initialSupply', '0x3e8 <- YourTokenInitialSupply')
    }
~~~

Deploy
~~~
$ gradle java-score:deployToLisbon -PkeystoreName=keystore.json -PkeystorePass=xxxx
~~~

Check Contract on https://tracker.lisbon.icon.community/

Update Score
~~~
${GOLOOP_ROOT}/bin/goloop rpc sendtx deploy ./java-score/build/libs/java-score-0.9.0-optimized.jar --uri https://lisbon.net.solidwallet.io/api/v3 --key_store keystore.json --key_password xxxx --nid 2 --step_limit 10000000000 --content_type application/java --to cxf3...f57
${GOLOOP_ROOT}/bin/goloop rpc txresult 0x31...fdb --uri https://lisbon.net.solidwallet.io/api/v3
~~~


### Test with Deployed Contract
~~~
# get_balance
${GOLOOP_ROOT}/bin/goloop rpc call --to cx2f97...2819 --uri https://lisbon.net.solidwallet.io/api/v3 --method balanceOf --param _owner=hx8bb9...07c6

# send_token
${GOLOOP_ROOT}/bin/goloop rpc sendtx call --to cx2f97...2819 --uri https://lisbon.net.solidwallet.io/api/v3 --key_store keystore.json --key_password xxxx --nid 2 --step_limit 10000000000 --method transfer --param _to=hx608a...5dcd --param _value=1
~~~
