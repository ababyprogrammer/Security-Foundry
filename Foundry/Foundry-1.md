**Foundry** is a smart contract development toolchain.

Foundry manages your dependencies, compiles your project, runs tests, deploys, and lets you interact with the chain from the command-line.

---

**Foundry Resources**

The Foundry Book(https://book.getfoundry.sh/) is the definitive resource if you want to read more about Foundry.
How to Foundry(https://www.youtube.com/watch?v=Rp_V7bYiTCM) is an excellent introductory video?

# Installation

Open your terminal and type in the following command:

``` type copy
curl -L https://foundry.paradigm.xyz | bash
```

This will download _foundryup_. Then install Foundry by running:

``` type copy
foundryup
``` type copy

To verify that Foundry is installed, run _forge --version_:

``` type copy
forge — version
```

You see the following message.

forge 0.2.0 (ccdfd3a 2022–04–27T00:03:53.441110+00:00)

# forge init

To start a new project with Foundry, use _forge init_:

**Note** = If it doesn’t work, you can use forge init — force

``` type copy
forge init hello_foundry2

cd hello_foundry2
tree
```
This is representation of “hello_foundry2” in your home root.

``` type copy
    veridelisi@veridelisi:~/hello_foundry2$ tree
    .
    |__ foundry.toml
    |__ lib
    |   |__ forge-std
    |   |   |__ lib
    |   |       |__ ds-test
    |   |           |__ default.nix
    |   |           |__ demo
    |   |           |   |__ demo.sol
    |   |           |__ LICENSE
    |   |           |__ Makefile
    |   |           |__ src
    |   |           |   |__ test.sol
    |   |__ LICENSE-APACHE
    |   |__ LICENSE-MIT
    |   |__ README.md
    |   |__ src
    |       |__ console2.sol
    |       |__ console.sol
    |       |__ test
    |       |   |__ StdAssertions.t.sol
    |       |   |__ StdCheats.t.sol
    |       |   |__ StdError.t.sol
    |       |   |__ StdMath.t.sol
    |       |   |__ StdStorage.t.sol
    |       |__ Test.sol
    |       |__ Vm.sol
    |__ src
    |   |__ Contract.sol
    |__ test
    |   |__ Contract.t.sol
```

There are 3 directories.

---

# forge build

We can build the project with _forge build_:

``` type copy
forge build
```

You see the following message.

``` type copy
veridelisi@veridelisi:~/hello_foundry2$ forge build
[ : ] Compiling...
[ : ] Compiling 3 files with 0.8.13
[ : ] Solc finished in 362.42ms
Compiler run successful

```

# forge test

And run the tests with _forge test_:

```type copy
forge test
```

You see the following message.

```type copy
veridelisi@veridelisi:~/hello_foundry2$ forge test
[ : ] Compiling...
No files changed, compation skipped

Running 1 test for test/Contract.t.sol:ContractTest
[PASS] testExample() (gas: 190)
Test result: ok. 1 passed; 0 failed; finished in 3.64ms
```

Ok.

# Openzeppelin and foundry physical path problem
Do you want to install openzeppelin ?

``` text copy
forge install openzeppelin/openzeppelin-contracts
```

didn't work?

``` text copy
forge install OpenZeppelin/openzeppelin-contracts — no-commit
```
and system load this repository to the lib folder

but you need to arrange for using NPM style physical path.

``` text copy
touch remappings.txt
```

add the following path to the .txt file

``` text copy
openzeppelin-contracts/=lib/openzeppelin-contracts/contracts/
```

save it!

You can now import openzeppelin files as

``` text copy
import “openzeppelin-contracts/security/ReentrancyGuard.sol”;

import “openzeppelin-contracts/access/Ownable.sol”;
```