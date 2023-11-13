**Note**: I try to use assert equal, less than and greater than expressions for testing our values 
in solidity code.

# DSTest

We import assert from DSTest.

**Dappsys Test** (DSTest for short) provides basic logging and assertion functionality. It is included in the Forge Standard Library.

To get access to the functions, ``import forge-std/Test.sol`` and inherit from ``Test`` in your test contract:

``` text copy
import “forge-std/Test.sol”;

contract AccessModifiers is Test {

// … tests …

}
```

# Tools

- **assertEq**, assert equal
    (Where ``<type>`` can be ``address``, ``bytes32``, ``int``, ``uint``)

- **assertLt**, assert less than
    (Where ``<type>`` can be ``int``, ``uint``)

- **assertLe**, assert less than or equal to
    (Where ``<type>`` can be ``int``, ``uint``)

- **assertGt**, assert greater than
    (Where ``<type>`` can be ``int``, ``uint``)

- **assertGe**, assert greater than or equal to
    (Where ``<type>`` can be ``int``, ``uint``)

- **assertEqDecimal**, assert equal decimals
    (Where ``<type>`` can be ``int``, ``uint``)

- **assertTrue**, assert to be true
    (Asserts the ``condition`` is true.)

---

# Code:

[Foundry-Samples/AccessModifiers.sol at main · veridelisi/Foundry-Samples](https://github.com/veridelisi/Foundry-Samples/blob/main/AccessModifiers/src/AccessModifiers.sol?source=post_page-----48d3987152ed--------------------------------)

# Test

[Foundry-Samples/AccessModifiers.t.sol at main · veridelisi/Foundry-Samples](https://github.com/veridelisi/Foundry-Samples/blob/main/AccessModifiers/test/AccessModifiers.t.sol?source=post_page-----48d3987152ed--------------------------------)

---

**Source**:  [DSTest Reference](https://book.getfoundry.sh/reference/ds-test)