# Introduction to Bitcoin

The currency is called bitcoin (lowercase), while the network is called Bitcoin (uppercase).

The users of the Bitcoin network control the keys to their own bitcoin, which are used to prove ownership. These keys are used to sign transactions to unlock and spend their bitcoin.

> *Keys are often stored in a digital wallet on each user's computer or smartphone.*

**Q: Does that mean losing your computer or smartphone is the same as losing your bitcoin?**

A: Not necessarily. While your keys are stored on your device, proper Bitcoin security involves creating backups. Most wallet applications provide a recovery seed phrase (typically 12-24 words) that you should write down and store securely offline. With this backup, you can restore your wallet on a new device if your original one is lost or damaged. Without such backups though, losing your device would indeed mean permanently losing access to your bitcoin. This is why the Bitcoin community emphasizes "Not your keys, not your coins" and the importance of proper key management.

> *Units of bitcoin are created through a process called "mining," which involves repeatedly performing a computational task that references a list of recent Bitcoin transactions.*

**Q: What is this "computational task"?**

A: The computational task in Bitcoin mining is finding a solution to a cryptographic puzzle. Specifically, miners must find a number (called a nonce) that, when combined with the block data and hashed using SHA-256 twice, produces a result that begins with a specific number of zeros. This is extremely difficult and requires enormous computational power and countless attempts through trial and error. The only way to find this solution is by randomly trying billions of different nonces until one produces a hash with the required number of leading zeros. This proof-of-work system ensures that miners must expend real computational resources (electricity and hardware) to validate transactions and secure the network, making it prohibitively expensive to attack.

**Additional reading:** [Learn Me A Bitcoin - How Does Bitcoin Work](https://learnmeabitcoin.com/beginners/how-does-bitcoin-work/)

> *The difficulty of the computational task that miners must perform is adjusted dynamically so that, on average, someone succeeds every 10 minutes regardless of how many miners (and how much processing) are competing at any moment.*

**Q: What is the system in the bitcoin protocol that is responsible for this? What is the lever that is pulled to adjust the difficulty of the computational task?**

A: The system responsible for this adjustment is called the Difficulty Adjustment Algorithm (DAA). Every 2,016 blocks (approximately two weeks), the Bitcoin protocol automatically recalculates the mining difficulty based on how quickly the previous 2,016 blocks were mined. The specific lever that gets adjusted is the "target threshold" - the number that the hash output must be less than to be considered valid. If blocks were mined too quickly in the previous period (less than 10 minutes average), the target is lowered (making it harder to find a valid hash). If blocks were mined too slowly, the target is raised (making it easier). This self-regulating mechanism ensures that blocks are created at a relatively constant rate regardless of how much mining power joins or leaves the network, maintaining Bitcoin's predictable issuance schedule.

> *At approximately block 1,411,200, which is expected to be produced around the year 2035, 99% of all bitcoins that will ever exist will have been issued.*

**Q: Will 100% of the 21 million bitcoins ever be issued? (I'm asking this question since I am aware of the Zeno's paradox.)**

A: I'm going to leave it to the reader to find the answer to this question. (Hint: Can a bitcoin be indefinitely divided? Understand what a "satoshi" is.)

## Key Bitcoin Components

Bitcoin (with a capital B) consists of:

1. A decentralized peer-to-peer network (the Bitcoin protocol)
2. A public transaction journal (the blockchain)
3. A set of rules for independent transaction validation and currency issuance (consensus rules)
4. A mechanism for reaching global decentralized consensus on the valid blockchain (proof-of-work algorithm)