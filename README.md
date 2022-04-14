# Create an NFT collection on Solana

## Prerequest :

- [NodeJS](https://nodejs.org/en/ "NodeJS")
- [Yarn](https://classic.yarnpkg.com/lang/en/docs/install/ "Yarn")
- [Solana tool suite](https://docs.solana.com/cli/install-solana-cli-tools "Solana tool suite"),
- [Phantom](https://phantom.app/download "Phantom")
- [Rust](https://www.rust-lang.org/tools/install "Rust")

#### Install metaplex :

```bash
git clone https://github.com/metaplex-foundation/metaplex.git
cd metaplex/js
yarn install
```

## Preparing wallet :

launch those commands in your root project folder

```bash
#principal wallet
solana-keygen grind --starts-with p1:1
#get sol to your wallet, replace the json file by the one generated :
solana airdrorp 2 p1ePUMbgtwo9kyrNBV7DwpBcn8g12VGMGs9UiYzUuxB.json
#buyer wallet
solana-keygen grind --starts-with p2:1
#get sol to your wallet :
solana airdrorp p2kyFJzjDbpi8JVKyqDRAJo5hZg2JS6gN8HbehvYcwv.json
```

if the airdrop command does not pass, go to https://solfaucet.com and put your adress to get SOL for the devent

#### Verify your balance

```bash
solana balance p1ePUMbgtwo9kyrNBV7DwpBcn8g12VGMGs9UiYzUuxB.json
```

Prepare your NFTs images and metadata. You can find NFTs example with their metadata in the assets folder

##SPL tokens :
To make a presale or a whitelist, you will need to generate and distribute SPL tokens. those tokens allow their holders to mint before everyone else.

#### Generate SPL tokens :

```bash
#pointing the keypair path to the first wallet (seller) :
solana config set -k p1ePUMbgtwo9kyrNBV7DwpBcn8g12VGMGs9UiYzUuxB.json
#generate a keypair that we will use to generate our token
solana-keygen grind --starts-with To1:1
#create the SPL token, we use the --decimal 0 argument to make sure one token is equal to one token
spl-token create-token To1DJLReVbgFGVtRGUEj8X9LxFVkjLubfPMJRA2sZQe.json --decimals 0
#create an account
spl-token create-account To1DJLReVbgFGVtRGUEj8X9LxFVkjLubfPMJRA2sZQe.json
#mint our 10 tokens (mint as many token as NFTs)
spl-token mint To1DJLReVbgFGVtRGUEj8X9LxFVkjLubfPMJRA2sZQe.json 10
#to verify your token balance
spl-token accounts
```

#### Distribute the tokens with gumdrop:

To distribute the tokens we are going to use gumdrop.

First, clone the gumdrop repo and install the modules :

```bash
git clone https://github.com/metaplex-foundation/gumdrop.git
cd gumdrop
yarn install
```

We now have to generate our distribution list. Create a distlist.json file in your root project directory

```json
[
  {
    "handle": "p2kyFJzjDbpi8JVKyqDRAJo5hZg2JS6gN8HbehvYcwv",
    "amount": 2
  }
]
```

The handle part is for the buyer wallet and the amount is how many token he can mint

To create the gumdrop, launch this command by replacing the public keys by the ones you generated before :

```bash
ts-node gumdrop/packages/cli/src/gumdrop-cli.ts create -k p1ePUMbgtwo9kyrNBV7DwpBcn8g12VGMGs9UiYzUuxB.json --claim-integration transfer --transfer-mint To1DJLReVbgFGVtRGUEj8X9LxFVkjLubfPMJRA2sZQe --distribution-method wallets --distribution-list dislist.json
```

In your Phantom extension, click on the left top button then add a wallet. the import wallet. you are going to import your buyer's wallet with the private key. To do so, open the json of your buyer wallet, in my case p2kyFJzjDbpi8JVKyqDRAJo5hZg2JS6gN8HbehvYcwv.json and copy the array in the private key area then click import.

Go to .log/devnet/ErhDp2cgvNuZjBov8wuKCY39CdF4tCFU6rrfaghHEVp6/ (your folder may be different because it is generated by the gumdrop command). You will find a urls.json file. Open it and open the url in your browser and connect your buyer wallet. This url can only be used by the whitelisted wallets. They can use it to mint the SPL token.
Click on the Claim Gumdrop button and approuve the transaction. Congratulation, you have your SPL tokens ! To see them, open phamtom and click on Unknown token. You will see that you have two tokens.
