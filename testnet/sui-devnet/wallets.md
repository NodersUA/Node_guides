***Creating a wallet***

**The wallet is created once, when the network is updated, you have to recover the previously created one.**
```bash
# To interact with the blockchain it is necessary to create a wallet, to do this you need to run the command below, answer the first questions:
y
Enter
0
sui client
Make a backup copy of the:
Mnemonic phrase;
Folder with the keys, saving it in a safe place (the command shows the path)
echo $HOME/sui/sui_config/
# Make sure that the address has been created
sui keytool list
```
***Request tokens from the faucet***
```bash
# Display and copy the wallet address (in the left column)
sui keytool list
# Go to:
#🚰・devnet-faucet and send a command with the wallet address:
!faucet 0x___
```
***Create a NFT***
```bash
⠀NFT-example is created by the command
sui client create-example-nft
```
