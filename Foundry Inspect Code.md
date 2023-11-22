# Foundry Inspact Code

How many slots do we need ?

Explain your calculation!

We have 6 different uint variable in solidity programming and each of them has different byte sizes. Logic is simple “**every uint8 variable is equal to 1 byte**”.


uint8 : **1 bytes**

uint16: **2 bytes**

uint32 : **4 bytes**

uint64 : **8 bytes**

uint128 : **16 bytes**

uint256 : **32 bytes**

We start our code:

``` text copy
contract Add {
    uint8 byte1;
    uint16 byte2;
    uint32 byte4;
    uint64 byte8;
    uint128 byte16;
    uint256 byte32;
}
```

How many slots do we need ?

We use the following code in foundry. Please remember our contract’s name is **Add**.

forge inspect **Add** storage-layout — pretty

``` 
| Name   | Type    | Slot | Offset | Bytes  |
 -------- --------- ------ -------- --------
| byte1  | uint8   | 0    | 0      | 1      |
| byte2  | uint16  | 0    | 1      | 2      |
| byte4  | uint32  | 0    | 3      | 4      |
| byte8  | uint64  | 0    | 7      | 8      |
| byte16 | uint128 | 0    | 15     | 16     |
| byte32 | uint256 | 1    | 0      | 32     |
```

We see that only **first five variables are in the first(0) slot** and **last variable goes to second(1) slot**.

**A big thanks to you for read this type!**