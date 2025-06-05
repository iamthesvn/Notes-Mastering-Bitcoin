# Bitcoin Script Language

## Key Characteristics of Bitcoin Script

1. **Limited Flow Control**
   
   The Script language does not allow loops or complex flow control capabilities other than conditional flow controls. This limitation ensures that the language cannot be used to create an infinite loop that causes a denial-of-service attack.
   
   Example: While Script has `IF-ELSE-ENDIF` constructs for conditional execution, it has no `WHILE`, `FOR`, or other loop constructs that could potentially run forever.

2. **Not a General Purpose Language**
   
   _Script is not a general purpose language._
   
   Q: What does that mean?
   A: This means Script is deliberately limited in functionality and designed for a specific purpose—validating transaction conditions in Bitcoin. Unlike general-purpose languages such as JavaScript or Python, Bitcoin Script:
   
   - Cannot perform arbitrary computations
   - Has no file system access
   - Cannot interact with networks or external systems
   - Cannot store persistent state between executions
   - Does not support complex data structures like objects or arrays
   - Has a very limited set of operations focused only on cryptographic functions and stack manipulation
   
   These limitations are intentional security features that prevent malicious scripts from compromising the Bitcoin network while still providing enough flexibility to implement various transaction conditions.

3. **Stack-Based Execution Model**
   
   Bitcoin's scripting language is called a stack-based language.
   
   Q: What is a stack-based language?
   A: A stack-based language is a programming language that uses a last-in, first-out (LIFO) stack as its primary data structure for passing operands to commands. In Bitcoin Script:
   
   - Operations (opcodes) take their inputs from the top of the stack
   - Results are pushed back onto the top of the stack
   - There are no named variables—all data manipulation happens directly on the stack
   - Execution proceeds from left to right through the script
   - Each opcode either pushes data onto the stack or pops data off the stack, performs an operation, and (optionally) pushes a result back
   
   For example, to add two numbers (3 and 4) in a stack-based language like Bitcoin Script:
   ```
   3 4 OP_ADD
   ```
   This pushes 3 to the stack, pushes 4 to the stack, then executes OP_ADD which pops those two values, adds them, and pushes the result (7) back onto the stack.
   
   Stack-based languages are ideal for Bitcoin because they are deterministic, easy to analyze for security properties, and straightforward to implement on constrained systems.

4. **Intentionally Limited Opcodes**
   
   Bitcoin Script was designed with a limited set of operations (opcodes), and some were disabled in early Bitcoin versions as a security measure. Key aspects include:
   
   - Around 80 opcodes defined, with some disabled or reserved
   - No string manipulation or complex math operations
   - Focus on crypto operations (hashing, signature verification)
   - Simple arithmetic and boolean logic operations
   - Stack manipulation operations
   
   This limitation ensures that script execution is predictable, always terminates, and cannot be used to attack the network.

5. **Stateless Verification**
   
   Scripts are stateless, meaning they don't maintain any information between executions. Each transaction is validated independently based only on the script execution without any stored context from previous transactions. This property is essential for Bitcoin's decentralized validation model.

