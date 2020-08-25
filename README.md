# ethereum-genesis : verifier for ethereum genesis

First we review the code to see why we cannot change the genesis block or just the allocation from the ICO in that block. We see that the genesis [allocation](https://github.com/ethereum/go-ethereum/blob/master/core/genesis.go#L58) is part of the Genesis structure. We expect it to count for the block hash computation.
The allocation change the state trie and we see it [here](https://github.com/ethereum/go-ethereum/blob/master/core/genesis.go#L255) and its [root](https://github.com/ethereum/go-ethereum/blob/master/core/genesis.go#L280) goes into the header of the genesis block. Now the missing part is where we compute the hash of the block. The interesting function is [SetupGenesisBlock](https://github.com/ethereum/go-ethereum/blob/fc20680b95da65f952012f3370e5d316f0ba237d/core/genesis.go#L155):
```
// SetupGenesisBlock writes or updates the genesis block in db.
// The block that will be used is:
//
//                          genesis == nil       genesis != nil
//                       +------------------------------------------
//     db has no genesis |  main-net default  |  genesis
//     db has genesis    |  from DB           |  genesis (if compatible)
//
// The stored chain configuration will be updated if it is compatible (i.e. does not
// specify a fork block below the local head block). In case of a conflict, the
// error is a *params.ConfigCompatError and the new, unwritten config is returned.
//
// The returned chain configuration is never nil.
```

We compare the hash and it should give:
```
MainnetGenesisHash = common.HexToHash("0xd4e56740f876aef8c010b86a40d5f56745a118d0906a34e69aec8c0db1cb8fa3")
```

## Geth RPC
```
curl -X POST --header 'Content-Type: application/json' -d '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params": ["0x0", true],"id":1}' "127.0.0.1:8545"
```

```
{"jsonrpc":"2.0","id":1,"result":{"difficulty":"0x400000000","extraData":"0x11bbe8db4e347b4e8c937c1c8370e4b5ed33adb3db69cbdb7a38e1e50b1b82fa","gasLimit":"0x1388","gasUsed":"0x0","hash":"0xd4e56740f876aef8c010b86a40d5f56745a118d0906a34e69aec8c0db1cb8fa3","logsBloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000","miner":"0x0000000000000000000000000000000000000000","mixHash":"0x0000000000000000000000000000000000000000000000000000000000000000","nonce":"0x0000000000000042","number":"0x0","parentHash":"0x0000000000000000000000000000000000000000000000000000000000000000","receiptsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","sha3Uncles":"0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347","size":"0x21c","stateRoot":"0xd7f8974fb5ac78d9ac099b9ad5018bedc2ce0a72dad1827a1709da30580f0544","timestamp":"0x0","totalDifficulty":"0x400000000","transactions":[],"transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","uncles":[]}}
```

## Geth Dump
geth dumpgenesis > ethgenesis.json

## Etherscan

https://etherscan.io/block/0


