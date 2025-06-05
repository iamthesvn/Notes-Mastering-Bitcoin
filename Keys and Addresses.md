# Keys and Addresses

## Cryptographic Foundations

### What is public key cryptography?

Public key cryptography is an asymmetric cryptographic system that uses pairs of keys: public keys that can be shared widely, and private keys which are known only to the owner. The public key can be derived from the private key, but the private key cannot feasibly be derived from the public key. In Bitcoin, this enables:

1. Authentication - proving you own funds through digital signatures
2. Receiving payments - sharing your public key/address without revealing your private key
3. Non-repudiation - transactions signed with your private key can't be denied later

Bitcoin specifically uses Elliptic Curve Digital Signature Algorithm (ECDSA) with the secp256k1 curve as its public key cryptography system.

### What is the discrete logarithm problem?

The discrete logarithm problem is the mathematical hard problem that underlies much of modern cryptography, including Bitcoin's security. It can be stated as:

Given a cyclic group, a generator g of the group, and an element h in the group, find the integer x such that g^x = h.

In the context of elliptic curves (what Bitcoin uses), this translates to: given a point G on the curve and another point P, find the integer k such that P = k × G, where × represents the elliptic curve point multiplication operation. 

The security of Bitcoin relies on the fact that while multiplication (k × G) is computationally easy, finding k given G and k × G is computationally infeasible with current technology.

### What is the elliptic curve math?

Elliptic curve cryptography uses mathematical curves defined by the equation y² = x³ + ax + b (where a and b are constants). Bitcoin specifically uses the secp256k1 curve, defined by the equation y² = x³ + 7 over a very large prime field.

Key properties of elliptic curve math in Bitcoin include:

1. Points on the curve can be added together using specific formulas (not regular addition)
2. A point can be "multiplied" by an integer by repeatedly adding it to itself
3. If G is a base point and k is a large random integer (private key), then K = k × G (the public key) is another point on the curve
4. Given K and G, it's computationally infeasible to find k (the discrete logarithm problem)

This mathematical relationship enables the creation of private-public key pairs and the digital signatures that secure Bitcoin transactions.

## Transaction Mechanics

### How do the users with a wallet transmit a transaction to the full nodes?

When a Bitcoin wallet creates a transaction, it follows these steps to transmit it to the network:

1. The wallet software constructs a valid transaction (inputs, outputs, scripts, etc.)
2. It signs the transaction using the private key(s) corresponding to the UTXOs being spent
3. The signed transaction is serialized into a standard binary format
4. The wallet transmits this transaction to one or more Bitcoin nodes it's connected to using the Bitcoin peer-to-peer protocol
5. These nodes validate the transaction and, if valid, relay it to other nodes they're connected to
6. The transaction propagates through the network via this "flooding" protocol
7. Eventually miners include the transaction in a block

The initial transmission typically happens over TCP/IP using the Bitcoin network protocol. Light wallets might send transactions through a trusted node, API service, or an Electrum server rather than connecting directly to the peer-to-peer network.

### Is elliptic curve cryptography different from elliptic curve multiplication?

Yes, they are related but distinct concepts:

- **Elliptic curve cryptography (ECC)** is the broader cryptographic system and methodology that uses the mathematical properties of elliptic curves to create secure cryptographic protocols. It encompasses the entire framework, including key generation, signing, verification, and encryption algorithms.

- **Elliptic curve multiplication** (or point multiplication) is a specific mathematical operation within ECC. It's the operation of multiplying a point on the curve by an integer scalar, resulting in another point on the curve. This operation is fundamental to ECC because it's computationally easy to perform but extremely difficult to reverse (the discrete logarithm problem).

Elliptic curve multiplication is the core mathematical operation that enables elliptic curve cryptography, but ECC includes many other elements and protocols built around this operation.

### What actually happens during "signing"? What are the inputs and outputs of the signing process?

During the Bitcoin transaction signing process:

**Inputs:**
1. The transaction data to be signed (or more precisely, a hash of the specific parts of the transaction)
2. The signer's private key
3. A random number k (nonce) that must be generated securely for each signature

**Process:**
1. A hash of the transaction data is calculated, producing a 256-bit number
2. This hash, along with the private key and the nonce, is used in the ECDSA algorithm to calculate:
   - A point R on the elliptic curve
   - A value s derived from the hash, R, and the private key

**Outputs:**
1. The digital signature, which consists of two numbers: r (the x-coordinate of R) and s
2. These values are encoded in a standard format (DER encoding in Bitcoin) to produce the final signature

The signature proves the signer knows the private key corresponding to a specific public key, without revealing that private key, and also proves the signer's intent to authorize the specific transaction being signed.

### How are private keys used to create signatures? Is the private key used to confirm ownership of the funds or the signature?

The relationship works as follows:

1. **The private key is used to create signatures**:
   - The private key itself is never shared or transmitted in transactions
   - It mathematically generates signatures for specific transaction data
   - Each signature is unique to both the data being signed and the private key used

2. **The signature confirms ownership of funds**:
   - When a transaction is being verified, the signature, public key, and transaction data are used together
   - Verifiers check that the signature was created by the holder of the private key associated with the public key that locks the funds
   - A valid signature proves the creator had knowledge of the private key without revealing that private key

In summary, the private key creates signatures, and those signatures (not the private key directly) prove ownership of funds. The private key is what you need to possess to spend funds, but it's the signature that actually proves this possession to the network.

### Is a Bitcoin public key different from a Bitcoin address?

Yes, a Bitcoin public key and a Bitcoin address are different, although they are related:

**Public Key:**
- A 256-bit number derived from the private key through ECDSA operations
- Represented as either a 65-byte (uncompressed) or 33-byte (compressed) value
- Used directly in the validation of digital signatures

**Bitcoin Address:**
- A further derivation from the public key, intended for sharing with others
- Created by hashing the public key (usually through both SHA-256 and RIPEMD-160 hash functions) 
- Encoded with additional information like network prefix and checksum
- Usually represented in Base58Check encoding (traditional addresses) or Bech32 encoding (SegWit addresses)

The transformation from public key to address serves several purposes:
1. Shortens the lengthy public key to a more manageable size
2. Provides additional security if quantum computers ever break ECDSA (as the public key is hashed)
3. Includes error-checking mechanisms to prevent typos
4. Identifies which network the address belongs to (mainnet, testnet, etc.)

### What are output and input scripts?

Input and output scripts are small programs written in Bitcoin's Script language that enforce the conditions for spending bitcoins:

**Output Scripts (scriptPubKey):**
- Set the conditions required to spend the bitcoins in that output
- Common types include P2PKH (Pay to Public Key Hash), P2SH (Pay to Script Hash), and P2WPKH (Pay to Witness Public Key Hash)
- Essentially "lock" the bitcoins, specifying who can spend them and how
- Remain in the UTXO set until spent

**Input Scripts (scriptSig):**
- Provide the data necessary to satisfy the conditions in the corresponding output script
- Typically contain signatures and public keys or other data needed to unlock the funds
- When combined with the output script and executed, must evaluate to "true" for the transaction to be valid

When a transaction is validated, for each input:
1. The input script and the output script it's trying to spend are combined
2. The combined script is executed by the Bitcoin Script interpreter
3. If the script executes successfully (resolving to true), the spending condition is considered satisfied

This script-based system provides Bitcoin with its versatile yet deterministic authorization mechanism.

## Security Considerations

### Why are elliptic curves used in public key cryptography?

Elliptic curves offer several advantages over traditional cryptographic methods:

1. **Security efficiency**: For the same level of security, ECC requires significantly smaller keys than RSA. A 256-bit ECC key provides security comparable to a 3072-bit RSA key.

2. **Computational efficiency**: Elliptic curve operations typically require less computational resources than equivalent RSA operations, making them more efficient for devices with limited resources.

3. **Smaller signatures**: ECC produces more compact signatures than RSA for equivalent security levels, which is important in a blockchain where space is valuable.

4. **Strong security foundation**: The discrete logarithm problem on elliptic curves is believed to be harder than the integer factorization problem used in RSA, providing a strong security foundation.

5. **Standardization**: The specific curve used by Bitcoin (secp256k1) has well-understood properties and has undergone significant cryptographic analysis.

These properties make elliptic curve cryptography an ideal choice for Bitcoin, where efficiency, security, and compact representation are all important considerations.

### Has there ever been a publicly recorded case of 2 private keys colliding?

No, there has never been a publicly recorded case of two randomly generated private keys colliding in Bitcoin or any other major cryptographic system. This is because:

1. The private key space in Bitcoin is 2^256, an astronomically large number (approximately 10^77)
2. This number is larger than the estimated number of atoms in the observable universe (around 10^80)
3. With proper random number generation, the probability of a collision is negligible

While there have been cases of Bitcoin funds being stolen, these have always been due to:
- Poor random number generation
- Software vulnerabilities
- Social engineering attacks
- Reuse of nonces (k values) in signatures
- Leaked or exposed private keys

Rather than actual collisions in the key space. The security of Bitcoin relies on the practical impossibility of such collisions occurring.

### Why isn't someone bruteforcing the list of all private keys that can exist? They can then check the address against the UTXO set to see if it has any value and use the private key to spend it.

Trying to brute-force Bitcoin private keys is infeasible due to the sheer size of the key space:

1. **Enormous search space**: With 2^256 possible private keys, even if you could check one trillion keys per second, it would take approximately 10^59 years to search the entire space.

2. **Sparse target space**: Only a tiny fraction of possible addresses actually contain funds. Even if you generated a valid private key, the probability it corresponds to a funded address is astronomically small.

3. **Energy constraints**: The energy required to perform such a brute force attack would exceed the total energy output of the sun for many years.

4. **Economic disincentives**: The cost of the computing power and electricity needed would vastly exceed the expected value of any bitcoins you might find.

To put this in perspective: finding a private key by random guessing would be like trying to find a specific grain of sand on all the beaches on Earth, when you don't even know what planet the grain is on. The security of Bitcoin rests on this computational infeasibility.

### What is SegWit?

Segregated Witness (SegWit) is a Bitcoin protocol upgrade activated in 2017 that fundamentally changed how transaction data is structured. Key aspects include:

1. **Technical structure**: SegWit separates ("segregates") the transaction signatures ("witnesses") from the transaction data, storing them in a separate structure.

2. **Capacity increase**: It effectively increases Bitcoin's block capacity without changing the nominal 1MB block size limit by discounting signature data in the block weight calculation.

3. **Transaction malleability fix**: SegWit solves the transaction malleability problem by removing signatures from the transaction ID calculation, enabling more complex protocols like the Lightning Network.

4. **New address formats**: Introduced new address formats (starting with "3" for P2SH-wrapped SegWit addresses and "bc1" for native SegWit addresses).

5. **Script versioning**: SegWit introduced a version field for Bitcoin Script, allowing for smoother future protocol upgrades.

6. **Efficiency improvements**: SegWit transactions typically have lower fees due to their reduced weight in blocks.

SegWit was implemented as a soft fork, meaning it was backward-compatible with older Bitcoin software, though older software can't create or fully validate SegWit transactions.
