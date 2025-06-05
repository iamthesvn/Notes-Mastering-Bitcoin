# Authorization and Authentication 

_When you receive bitcoins, you have to decide who will have permission to spend them, called authorization. You also have to decide how full nodes will distinguish the authorized spenders from everyone else, called authentication._

## Core Concepts

### Why is it called a pay to public key hash (P2PKH) "script"?

It's called a "script" because in Bitcoin, transaction validation conditions are expressed using a scripting language called Script. P2PKH (Pay to Public Key Hash) is a specific locking and unlocking script pattern. The transaction output contains a script that locks the funds to a specific public key hash, and the input that spends it must provide a script that satisfies the conditions - typically providing a signature and public key that proves ownership. The term "script" reflects that these are actually small programs that are executed by Bitcoin nodes to validate transactions.

### What is an output script and an input script in the context of Bitcoin transactions?

In Bitcoin transactions, an output script (also called a locking script or scriptPubKey) defines the conditions that must be met to spend the bitcoins in that output. It effectively "locks" the funds. An input script (also called an unlocking script or scriptSig) provides the data necessary to satisfy the conditions specified in a previous output script. 

When a transaction is validated, the input script is combined with the output script it's trying to spend, and the resulting script is executed. If the script executes successfully, the spending transaction is valid. This script-based mechanism is how Bitcoin implements its authorization and authentication system without requiring central authorities.

### What is the "outpoint" that is present in every input script of a transaction?

An outpoint is not actually part of the input script (scriptSig), but rather a separate component of every transaction input. An outpoint precisely identifies which previous transaction output is being spent by referencing: 

1. The transaction ID (txid) of the previous transaction
2. The specific output index number (vout) within that transaction

This outpoint information, combined with the input script, allows Bitcoin nodes to locate the exact output being spent, verify its value, and check that the input script satisfies the conditions specified in the corresponding output script. Without outpoints, the network would not be able to track which outputs have been spent and which remain unspent (UTXOs).

## UTXO and Script Storage

### Are the transaction scripts only held in the UTXO set until the unspent amount is spent?

Transaction scripts are stored in the UTXO (Unspent Transaction Output) set only as long as the output remains unspent. Once the transaction output is spent, it's removed from the UTXO set. This is why the UTXO set represents the current state of all spendable outputs in the network.

> _P2SH shifts the burden in data storage for the long script from the output (which additionally to being stored on the blockchain is in the UTXO set) to the input (only stored on the blockchain)._

### Is the UTXO set not part of the blockchain? Where is it stored?

The UTXO set is not directly stored in the blockchain itself. While the blockchain contains all transactions (spent and unspent), the UTXO set is a separate data structure maintained by each node for efficiency. It's essentially an in-memory database (often cached to disk) that each node constructs and maintains by processing the blockchain from genesis. 

This separation allows nodes to quickly verify new transactions without scanning the entire blockchain history. The UTXO set represents the current state derived from the blockchain but is stored separately by each node.

> _P2SH shifts the transaction fee cost of a long script from the sender to the recipient, who has to include the long redeem script to spend it._

## Script Execution Costs

### Do each opcode have a fixed cost?

Yes, in Bitcoin script, each opcode has an associated computational cost in terms of "weight" or "cost units." Bitcoin uses a system that assigns different costs to different operations to prevent scripts from being computationally intensive and to mitigate potential denial-of-service attacks. 

These costs affect the transaction fees since more complex scripts require more processing resources from the network. The exact cost structure has evolved with various Bitcoin protocol updates and soft forks.

## Bitcoin Script Language

### What is the difference between a procedural language and a stack-based language?

The main differences between procedural and stack-based languages are:

1. **Execution model**:
   - Procedural languages follow a sequence of statements with explicit control flow (if/else, loops)
   - Stack-based languages manipulate data on a stack with operations that pop inputs and push results

2. **Variable handling**:
   - Procedural languages use named variables stored in memory
   - Stack-based languages primarily work with unnamed values on a stack

3. **Syntax and readability**:
   - Procedural languages typically have more readable syntax (e.g., `x = a + b`)
   - Stack-based languages use postfix notation (e.g., `a b + x =` or just `a b +`)

4. **Control structures**:
   - Procedural languages have explicit control structures
   - Stack-based languages implement control through stack manipulation and conditionals

Bitcoin Script is a stack-based language, which makes it simpler to analyze and more predictable in execution, which is important for a financial system.

