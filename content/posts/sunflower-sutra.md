---
date: 2022-10-01T21:42:09Z
title: '12 Random Words: The unnoticed math behind a sentence and its significance
  in Web3'
author: Vamsi Ramakrishnan
hero_image: "/content/images/adrien-olichon--aOsCcTJXWY-unsplash.jpg"

---
![](https://mirror-media.imgix.net/publication-images/_3i0p-cJepmMp4NwAyzl-.png?height=146&width=292&h=146&w=292&auto=compress)

## For the impatient

The internals of a non-custodial software wallet like MetaMask and How it uses a clever combination of randomness, mnemonics, one way functions, hashing, deterministic derivations and asymmetric encryption to generate one sentence of 12 - 24 words that can create or recover the numbers that hold the key to all your identity representations and assets across multiple blockchains, achieve all of this while storing nothing on centralised servers.

![The end to end process](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FPD9LXeLDH-jVYONd2C9ve.png&w=3840&q=90)

The end to end process

![](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FgWJxkBSQb-Eb41JH8XRZ5.png&w=3840&q=90)

## On Randomness

> “No matter how sophisticated our choices, how good we are at dominating the odds, randomness will have the last word.”― Nassim Nicholas Taleb, [Fooled by Randomness: The Hidden Role of Chance in Life and in the Markets](https://www.goodreads.com/work/quotes/3119175)

Randomness may not seem like much, its role is often overlooked and its power understated. But it is a public good like power, libraries, roadways and water. The internet wouldn’t work without randomness. Good randomness is extremely important and hard to find. Both humans and computers are pretty bad at generating randomness. Which is why we still rely on wiggling our mouse, hissing into the microphone, or for more sophisticated techniques rely on lava lamps, on nature for picking up randomness.

Randomness is a cornerstone in cryptography and by extension in cryptocurrencies and web3. A combination of randomness, asymmetric cryptography, and clever signing and key generation algorithms in an extremely large solution space serves as the bedrock of Web3. It ensures someone else in the universe does not end up with the same Ethereum address as you do and makes it extremely hard for both humans and computers to guess your Key that protects access to all your digital assets and identity.

## On wallets

The proposition that no-one except has access to your keys to your digital assets , and that you do not have to trust anything but mathematics was an exciting proposition even on Jan3rd, 2009 when first genesis block of bitcoin said

> The Times 03/Jan/2009 Chancellor on brink of second bailout for banks".

There was no way to access this technology until February 2009 when BitcoinQT, the first ever wallet in town that required users to download the entire blockchain locally to make it usable. It held information about the random string of numbers that represent who we are and which in-turn points to what we are worth and allowed to spend/do in blockspace. The private key which gave you access to your digital assets was stored in an unencrypted wallet.dat file in the desktop. There are horror stories of users deleting the wallet.dat file and losing access to BTCs worth millions today.

![BitcoinQT : Source bitcoin.com](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FHR1HCjPo2MSab5dlAOBbW.webp&w=1920&q=90)

BitcoinQT : Source bitcoin.com

Since the wallet was a full local copy of the blockchain as time progressed, users had to wait up to a week to really be able to get the data to sync and start transacting. You couldn’t transact without mining, there was no supply, no one was transacting with Bitcoin. There were no exchanges, no liquidity pools, no reserves, no swapping.

![Source:bitcoin.com](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2Fdc3zpnAZFOfhH0dkHPRhy.webp&w=3840&q=90)

Source:bitcoin.com

Fast forward to 2022, it takes me a couple minutes to set up MetaMask on my Android device, buy Ethereum with Transak or Moonpay with my credit card. A few more minutes to connect to ENS - Ethereum Name service to claim my handle. My very own unique 7 letter string with a .eth suffix for a few $. I was geeking out in rabbit hole of Layer-3 quests, I am human after all, i seeked validation and bragged for social proof on crypto-twitter, minted my first NFTs, signed open letters for an all-encompassing social graph. I bought wrapped assets while googling about slippage by swapping a fraction of my ether on Uniswap.

> All of this felt like magic.

### What piqued my curiosity to investigate further. ?

    When I expected that I’d need a whole lot of copying and exporting from my first device; 
    to start from where I left in a different device. Since I was using Metamask, which is a non-custodial wallet;

All I needed was the secret Backup Phrase. It did not recover just one Key but all keys and all addresses from all networks when I selected that network in the new device.

    How did this magic portal work. What kind of sorcery was this. 

![Restoring the wallet from a secret backup phrase](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FadsTaZ2lCLaAdPhATTxS0.png&w=3840&q=90)

Restoring the wallet from a secret backup phrase

MetaMask surpassed 30 Million MAUs in March 2022. Wallets are an indispensable part of the Internet built on Web3 principles. Wallet wars are reminiscent of the heady pre-dotcom browser wars. It is healthy to scrutinize it’s inner workings and ask some of these questions

* How are these 12 random words generated
* What is the source of the randomness
* What makes a wallet even possible
* Does this information get stored elsewhere
* If yes, who gets to access that piece of information,
* If the answer is no one does, then what happens when I lose my keys…

This post is a summary of my findings as I went down the rabbithole trying to figure out answers.

### Seed Phrase Generation

The most important requirements for a Seed Phrase is

1. Not Easy to Guess - Use Random / Pseudo Random Number Generators
2. But extremely easy to remember - Leverage Mnemonics

The Source for a Random Number Generator is **“**`Entropy`**”**

### Entropy

[Entropy](https://en.wikipedia.org/wiki/Entropy) is the measurement of uncertainty or disorder in a system. Good entropy comes from the surrounding environment which is unpredictable and chaotic. The amount of surprise found after a trial.

    RNG      Random Number Generator
    PRNG     Pseudo Random Number Generator

### RNG vs PRNG

True Random number Generators would stop when you stop typing gibberish on your keyboard at random pressure or if you stop wiggling your mouse as in when the entropy tap dries out. Pseudorandom generators pool the initial entropy and use that continue to generate randomness.

So how do these wallets tap into this randomness, most PRNGs tap into Linux/Mac Device userland files.

    Wallets --> Browser --> OS Kernel --> /dev/urandom or /dev/random

As Metamask responds eloquently

![Metamask about randomness](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2F6T_eZtNy-RqMqMLlBWuLi.png&w=1920&q=90)

Metamask about randomness

Well one does not have to rely on OS entropy to generate randomness. You could also roll a dice as described here in this twitter thread to generate your mnemonic phrase as described in this tweet.

## the internals of random number to mnemonic phrase conversion

It’s worth knowing why people put time and energy into developing a mnemonic phrase generation mechanism. The people that wrote `BIP0039` , as early as 2013, expanded as `Bitcoin Improvement Proposal` understood that wallets are meant for humans and words are far more superior as a mode of interaction as opposed binary or hex representations. It is not unfair to say the foundations of Ethereum was built on top of the shoulders of Bitcoin.

> **Motivation**  
> A mnemonic code or sentence is superior for human interaction compared to the handling of raw binary or hexadecimal representations of a wallet seed. The sentence could be written on paper or spoken over the telephone.

### Seed Generation & Recovery Phase

When I want to generate a phrase for recovery the wallet software taps into the entropy of the operating system to generate a random number , which is concatenated with a part of its own `sha256 hash` which then is chunked into `11 Bytes` used against a lookup table of words to generate the recovery phrase.

The vice versa is also true. When I use another device, I don’t need the random 256 bit number, I just need the mnemonic recovery phrase from which I can deterministically reconstruct my seed phrase.

    Generation Phase
    Random number (128-256 bit entropy) ==> 12-24 Word Mnemonic 
    
    Recovery Phase
    12-24 Word Phrase ==> Random Number

![From Entropy to Seed Phrase](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FBM3jSt4njZfgMSq1-N-zj.jpeg&w=3840&q=90)

From Entropy to Seed Phrase

### It’s quite simple in Python with bip_utils

    from bip_utils import Bip39MnemonicGenerator, Bip39WordsNum
    mnemonic = Bip39MnemonicGenerator().FromWordsNumber(Bip39WordsNum.WORDS_NUM_24)
    print(mnemonic)

See it in action it using this replit link

Now you have the master seed. With this seed you can recover/ derive or regenerate all of your keys and addresses across multiple networks. It’s worth asking why we need an arrangement such as this

> Backup your seed phrase only once, keep it very very safe and make sure no one else ever gets access to it. You never have to backup individual keys. Just the master phrase. And you’re good.

What’s the probability of someone brute force guessing my private key

![Trevor or Metamask the underlying principles are the same](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FMMtJre78brXTHKwaeFASm.png&w=3840&q=90)

Trevor or Metamask the underlying principles are the same

## The Genius Of Gregory Maxwell

Gregory Maxwell, considered to be one of the developers of Bitcoin, came up with the concept of HD wallets. Hierarchical deterministic wallets. In simple terms, if you had a parent key, you can spin off generations of childRen. Thereby backing up the parent key is sufficient. Before HD-wallets came into being we had to deal with Sequential Wallets, where every address and key to that address had to be backed up separately.

![](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2FuJmXURWzYmH7DSfY4YUbS.png&w=3840&q=90)

## How the magic works

Descending from the master node is a tree of an infinite number of private keys. To determine a particular key you need the master node (your mnemonic seed) and the location of the key in the tree (called its “path”). The tree is created using an algorithm defined by `BIP 32` called the CKD function. You can reach any node on the HD tree given the node’s position and the value of the master node.

![](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2F2fohaaOQYMoZpvSveoXdM.jpeg&w=3840&q=90)

To put things in perspective, the structure looks like this at 4 levels or depths. `BIP0044` codifies this as a multi-account hierarchy.

    m / purpose’ / coin_type’ / account’ / change / address_index

![](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2F-WCtPzxIQatklNLeamK9j.png&w=1920&q=90)

Users of Ethereum are utilizing an address with a derivation path of `m/44'/60'/0'/0/0`.

* The apostrophes (e.g., in the first three levels) indicate that the value is hardened.
* 44 — purpose — this path follows the BIP 44 standard.
* 60 — coin_type — 60 indicates the Ethereum network.
* 0 — account — intended to represent types of wallet users.
* 0 — change — remains 0 for Ethereum addresses and is a Bitcoin hangover.
* 0 — address_index — the index of the account

### So what your wallet really does

Derive the public address of the tree and starts scanning for transactions in the public blockchain using etherscan or any other indexing service that has a cache of address transaction lookup ( think of DNS ) at the 0 address index of each node. Displays the current balance of that address. It stops at the index where it does not find any corresponding transaction for the index.

## Other Reading

* [Stackexchange](https://bitcoin.stackexchange.com/questions/62533/key-derivation-in-hd-wallets-using-the-extended-private-key-vs-hardened-derivati)
* [The Journey from Seed Phrase to address](https://medium.com/mycrypto/the-journey-from-mnemonic-phrase-to-address-6c5e86e11e14)
* [Ethereum HD wallet 201](https://wolovim.medium.com/ethereum-201-hd-wallets-11d0c93c87f7)
* [Ian Coleman’s Mnemonic to Keys calculator](https://iancoleman.io/bip39/)