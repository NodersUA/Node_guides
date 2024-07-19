# Dill

```bash
curl -O https://dill-release.s3.ap-southeast-1.amazonaws.com/linux/dill.tar.gz
tar -xzvf dill.tar.gz && cd dill
```

```bash
# import wallet
./dill_validators_gen existing-mnemonic --num_validators=1 --chain=andes --folder=./
```

```bash
# import wallet to keystor
./dill-node accounts import --andes --wallet-dir ./keystore --keys-dir validator_keys/ --accept-terms-of-use
```

```bash
echo {my-password} > walletPw.txt
```

```bash
./start_light.sh -p walletPw.txt
```

```bash
ps -ef | grep dill
```
