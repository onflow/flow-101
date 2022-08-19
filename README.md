
# Flow 101 Quest 🪄

The goal of this quest is to practice interactive with smart contracts on Flow. In this quest we will be interacting with the [Yearbook](https://flow-view-source.com/testnet/account/0x63ffd70144f80d07/contract/YearbookMinter) contract. The theory behind this contract (as well as some introductory Cadence concepts) are covered in [THEORY.md](https://github.com/onflow/flow-101-quest/blob/main/THEORY.md). 

This README contains a practical quest. Anyone who completes this quest will receive a Soulbound Proof-of-Knowledge NFT.

## Quest Overview 📖
1. Create a Testnet Account
2. Interact with a smart contract
- - Sign transactions (mutate the chain)
- - Execute scripts (query the chain)

## What You'll Learn 💻
1. Learn how to use Flow CLI, an essential tool as a Flow developer
2. Create a testnet account on Flow
3. Send a transactions, such as signing the 'Flow Yearbook'!

## Prize 🏆

All quest completionists will receive **a super exclusive soulbound proof-of-knowledge NFT**. Aside from bragging rights, this special NFT will grant access to completion-gated channels in the official Flow Discord.

*Note: The NFT type we are using ([FLOATs](https://floats.city/)) is supported by Instagram, so if the feature has been rolled out to your account, you’ll be able to share your accomplishment with your friends, family and colleagues.*


## FAQs❓

#### Who is eligible for this quest?
Everyone! You don't need any pre-requisites :) Jump right in!

#### How long will this take?
Assuming you know the basics of a command line, it will take approximately 15 minutes!


## Step 0 - Prerequisites

**Install/Update the Flow CLI**: you **will** need the Flow CLI to complete this quest. Visit the [Flow CLI Installation](https://developers.flow.com/tools/flow-cli/install) documentation and follow the instructions. You simply need to run a single command in your terminal to install it. If you already have it installed, make sure it’s the latest version (check that page for updating instructions).

**Clone this repo** (optional): This repo already provides the completed transactions and scripts in order to complete the quest. The recommended way to follow along is to clone the repo. Alternatively, you can download the repo or follow along by creating the necessary files manually. You can clone the repo by using this command:

```sh
git clone https://github.com/onflow/flow-101-quest
cd flow-101-quest
```

## Step 1 - Start the Flow CLI

Once you are in your working directory, we'll initialize the Flow CLI and configure it in order to query Testnet.

```
flow init
```

You should see something like this: 

```
Configuration initialized
Service account: 0xf8d6e0586b0a20c7

Start emulator by running: 'flow emulator' 
Reset configuration using: 'flow init --reset'
```

## Step 2 - Create your Testnet Account

Before we can sign the yearbook, we'll need our own account! Luckily for us, we just need to run a simple command with the Flow CLI.

#### 1. Create a Testnet Account

```
flow accounts create
```

#### 2. Name your Account
Name your new account `hero` and follow the rest of the instructions on screen.

```
Enter an account name: hero
```

> 💡You can pick any name, we are trying to keep the instructions in line with your experience. If you would decide to name your account differently, please use that name everywhere we refer to `hero` account and address.


#### 3. Set your network to Flow Testnet

```
Use the arrow keys to navigate: ↓ ↑ → ← 
? Choose a network: 
    Local Emulator
  ▸ Flow Testnet
    Flow Mainnet
```

#### 4. Save Account Info

```
✔ Flow Testnet

❗ This command will perform the following:
 - Generate a new ECDSA P-256 public and private key pair.
 - Save the private key to hero.private.json and add it to .gitignore.
 - Create a new account on Flow Testnet paired with the public key.
 - Save the newly-created account to flow.json.


? Do you want to continue? [y/N] y
```

#### 5. Fund your Testnet Account
```
Please complete the following steps in a web browser:
 1. Complete the captcha challenge.
 2. Click the 'Create Account' button.
 3. Return to this window.

✔ Press <ENTER> to open in your browser...: █
```

Once you press `<ENTER>`, your browser will be automatically directed to the [Flow Testnet Faucet](https://testnet-faucet.onflow.org/) with your account information **pre-populated**. 

The only actions that is required are: 

```
Please complete the following steps in a web browser:
 1. Complete the captcha challenge.
 2. Click the 'Create Account' button.
 3. Return to this window.

You can also navigate to the link manually: https://testnet-faucet.onflow.org/?key=<key_that_is_pre_populated>

Waiting for your account to be created, please finish all the steps in the browser...
```

![Funding your testnet account from Flow faucet](./assets/testnet_faucet.gif)

#### 6. You're all set!

```
🎉 New account created with address 0xebeb17c521a0d375 and name hero.

Here’s a summary of all the actions that were taken:
 - Added the new account to flow.json.
 - Saved the private key to hero.private.json.
 - Added hero.private.json to .gitignore.
```

After you finish all the steps, you will notice that 2 new files. 
1) `hero.private.json` 
2) `flow.json`  

If you inspect the files, you should see the address and private key for your freshly minted account 👍!

## Step 3 - Class is in Session!

The official Flow Yearbook contract is already deployed to Testnet, so in this quest we'll simply be interacting with it from the command line, via the Flow CLI. You can view it on Flow View Source (one of Flow's contract explorers). Click [here](https://flow-view-source.com/testnet/account/0x63ffd70144f80d07/contract/YearbookMinter) to view the contract. Alternatively, check the [THEORY.md](https://github.com/onflow/flow-101-quest/blob/main/THEORY.md) file for context on how the contract works.

In this quest, we'll be skipping the [theory](https://github.com/onflow/flow-101-quest/blob/main/THEORY.md) and showing you how to interact with scripts and transactions via the Flow CLI. Let's go!

#### 1. Init Account

First let's take a look at our first transaction. If you cloned the repo, you'll find it in `cadence/transactions/init-accound.cdc`. Otherwise, just create a file called `init-account.cdc` and paste the content with following Cadence code:

```javascript
import YearbookMinter from 0x63ffd70144f80d07

transaction {
  prepare(signer: AuthAccount) {
    // checks if we have a yearbook resource in our account
    let yearbookExists = signer.getCapability(YearbookMinter.publicPath)
      .check<&YearbookMinter.Yearbook>()

    // if it doesn't find one, let's create a new one.
    if(!yearbookExists){
      let book <- YearbookMinter.createYearbook(ownerAddress: signer.address)
      signer.save(<-book, to: YearbookMinter.storagePath)
      signer.link<&YearbookMinter.Yearbook>(YearbookMinter.publicPath, target: YearbookMinter.storagePath)
    }
  }
}
```

Now we will use the Flow CLI to send this transaction and sign it with our `hero` account

```
flow transactions send ./init-account.cdc --signer=hero --network=testnet
```
> NOTE: if you cloned the repo, the file `./init-account.cdc` is located in `./cadence/transactions/`. Based on where you are in the directory, update the path in the command above accordingly. For example, if you are in the home directory of the repo, you should use `./cadence/transactions/init-account.cdc` instead in the command above.

Let's dissect this script:

`--signer` flag will tell the CLI to use your `hero` profile as a signer 

`--network` flag will specify which network we are interacting with - in this case we are using `Testnet`

This step initiates your account and creates a Yearbook resource if it doesn't exist yet.

#### 2. Get Messages (from a Yearbook)

To keep things civil, we limited the messages that people can leave on each others' yearbooks. You'll need to specify a message key instead of a custom message. Let’s get a list of available keys.

If you cloned the repo, you'll find the next file in `cadence/scripts/get-message-keys.cdc`. If you're creating them from scratch, create a file called `get-message-keys.cdc` and paste the following Cadence code:

```javascript
import YearbookMinter from 0x63ffd70144f80d07

pub fun main(): [String] {
  return YearbookMinter.allowedMessages.keys
}
```

Execute the script with the following Flow CLI command:

```
flow scripts execute ./get-message-keys.cdc --network=testnet
```
> NOTE: if you cloned the repo, the file `get-message-keys.cdc` is located in `./cadence/scripts/`. Based on where you are in the directory, update the path in the command above accordingly, or navigate to that folder.


This will give you a list of keys:

```javascript
"hello": "Hello"
"bff": "You are the best friend anyone could ask for!"
"cya": "See you around"
"gator": "Later, aligator!"
"fun": "You make my life fun!"
```

Pick your favorite, and now let's leave a message in the main Flow Yearbook!

#### 3. Sign the Yearbook

In order to sign the Flow Yearbook, you will need to submit a transaction. 

* Flow Yearbook Testnet Address: `0x63ffd70144f80d07` 


To sign the yearbook, we'll be executing the code below. If you cloned the repo, you'll find the file in `cadence/transactions/leave-message.cdc`. If you're creating them from scratch, create a file called `leave-message.cdc` and paste the following Cadence code:

```javascript
import YearbookMinter from 0x63ffd70144f80d07

transaction(yearbookOwner: Address, messageKey: String){
    prepare(signer: AuthAccount){
        // borrow the public reference & capability to the Yearbook at the address specified
        let yearbookReference = getAccount(yearbookOwner)
            .getCapability(YearbookMinter.publicPath)
            .borrow<&YearbookMinter.Yearbook>()
            ?? panic(YearbookMinter.errNoYearbook)
        
        // sign the yearbook
        yearbookReference.leaveMessage(signer: signer.address, messageKey: messageKey)
    }
}
```

This transaction takes two arguments: 
- `yearbookOwner` - the address of the Yearbook owner we are trying to modify
- `messageKey` - the message key we’ve just discussed

To run this transaction, use the following command. We are using the `fun` message key as an example, feel free to choose your favorite from the list in the previous section.

```
flow transactions send ./leave-message.cdc 0x63ffd70144f80d07 fun --signer=hero --network=testnet 
```

> NOTE: if you cloned the repo, the file `./leave-message.cdc` is located in `./cadence/transactions/`. Based on where you are in the directory, update the path in the command above accordingly, or navigate to that folder by typing `cd cadence/transactions`.  


#### 4. Read Messages from Yearbook

#### Get Yearbook Messages

Additionally you can also read all previous messages left by other heroes - both from your and others' accounts.

To do this, we'll use the `get-yearbook-messages.cdc` script file, which you will find in `cadence/scripts/`. Otherwise create it from scratch and paste in the following Cadence code:

```javascript
import YearbookMinter from 0x63ffd70144f80d07

pub fun main(owner: Address): {Address: String}{
    // get a reference to the yearbook
    let yearbookReference = getAccount(owner)
        .getCapability(YearbookMinter.publicPath)
        .borrow<&YearbookMinter.Yearbook>() 
        ?? panic(YearbookMinter.errNoYearbook)
    
    // return its messages
    return yearbookReference.messages
}
```

Let’s check our Yearbook and see who left messages there:

```javascript
flow scripts execute ./get-yearbook-messages.cdc 0x63ffd70144f80d07 --network=testnet 
```
> NOTE: if you cloned the repo, the file `./get-yearbook-messages.cdc` is located in `./cadence/scripts/`. Based on where you are in the directory, update the path in the command above accordingly, or navigate to that folder by typing `cd cadence/scripts/`.  

You should be able to see a list of addresses and corresponding messages, they have left in our Yearbook. 

You can also update that `0x63ffd70144f80d07` to your own address - which can be found within `hero.private.json` file and check who left messages in your Yearbook. If you don't have any messages, you can create another testnet account and try leaving one, or share this with a friend and get them to leave a message! :) 


## Step 4 - Get a Mainnet Account to receive your NFT!
In order for us to deliver your Soulbound proof-of-knowledge NFT (FLOAT), you will need to send us your Mainnet account address. The easiest way is via [Float City](https://floats.city/) webpage - which will also help to initialize your account with FLOAT Collection.

1. Visit [https://floats.city](https://floats.city) 
2. Click on “Connect Wallet” 
3. Login with the wallet of your choice (choose wisely! This is where you will receive your FLOAT! If you want to show this off on Instagram, choose Dapper)
4. Click on the address in the top right
5. Copy the Address from the “Account” tab (this is your mainnet account!)
6. Optional: On that page, make sure to initialize your FLOAT collection if this is your first time receiving/using FLOATs

![Gif on how to access mainnet address from https://floats.city](./assets/FLOAT_mainnet.gif)

## Step 5 - You made it! 👏

Congratulations on sending your first transactions on Testnet and utilizing Flow CLI commands! You're well on your way to becoming a proficient developer on Flow. In order to receive your soulbound Proof-of-Knowledge NFT, please fill out the form with the following information: 

- Testnet Account Address (to verify your work)
- Mainnet Account Address (to receive the FLOAT)
- Email Address (so we can reach out for SWAG!)

# [>> SUBMISSION FORM <<](https://share.hsforms.com/1ouJ1prrSR566_ZuB9krH5Q3u4gy)

*Verification process will be automatically processed every week and you can expect to see your FLOAT in your account within a week's time of your form submission*
