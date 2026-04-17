## Resources Used:
https://solidity-by-example.org/ (mostly used)



## Receive vs Fallback 
```
         is msg.data empty?
             /   \
            yes  no
            /     \
       receive()?  fallback()
        /   \
      yes   no
     /        \
    receive()  fallback()
```

## Call vs transfer vs send
```
        // // transfer
        // payable(msg.sender).transfer(address(this).balance);

        // // send
        // bool sendSuccess = payable(msg.sender).send(address(this).balance);
        // require(sendSuccess, "Send failed");

        // call
        (bool callSuccess,) = payable(msg.sender).call{value: address(this).balance}("");
        require(callSuccess, "Call failed");

        ### Difference b/w them:
```
## View vs Pure
```html
 view: to read storage variables only but no modification allowed
 pure: neither read nor modification is allowed from storage variables
```

## Modifier 
```
   modifier nameofModifier(){
     // logic
     _;
 }

   function abc() public nameofModifier{
     //Logic
}
```
## Payable 
```
    Makes Functions and address payable, means allow them to receive native currency of the blockchain(eth),

    address public payable me = 0x0120101212124fuf939434809;

    function abc() public payable{
     // LOGIC
     }
```
## Constant vs Immutable
```
   constant - value gets defined while declaring variable and value cannot be changed later
     uint public constant registrationo = 1244;

   immutable - value gets defined in constructor while deploying contract & variable value and cannot be changed later
     uint public immutable carRegister;
     constructor(){
      carRegister = 199;
        }
```
## Visibility Types
```
public : can be called inside and outside contract (everywhere).
private: can only be called inside this contract it is in.
external : can only be called from any contract except itself(this contract).
internal : can be called inside and from contracts inheriting it.
```
## Storage Areas
```
memory : short term, can read & write, used inside a function
calldata : short term , only read , used for function parameters
storage : permanent,  read & write , used for state variable

NOTE: memory is also used for declaring variables inside a function

| FOR DYNAMIC LENGTH VARIABLES

function params: need to specify, memory or calldata
state variables: no need to specify directly storage
```
## STACK & TRANSIENT STORAGE
```
Data stored in transient storage is cleared out after transaction.
tstore and tload are opcodes for assembly.
Use Cases: for reentrancy guard etc.

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

// Storage - data is stored on the blockchain
// Memory - data is cleared out after a function call
// Transient storage - data is cleared out after a transaction

interface ITest {
    function val() external view returns (uint256);
    function test() external;
}

// Contract for testing TestStorage and TestTransientStorage
// Shows the difference between normal storage and transient storage
contract Callback {
    uint256 public val;

    fallback() external {
        val = ITest(msg.sender).val();
    }

    function test(address target) external {
        ITest(target).test();
    }
}


contract TestTransientStorage {
    bytes32 constant SLOT = 0;

    function test() public {
        assembly {
            tstore(SLOT, 321)
        }
        bytes memory b = "";
        msg.sender.call(b);
    }

    function val() public view returns (uint256 v) {
        assembly {
            v := tload(SLOT)
        }
    }
}

contract ReentrancyGuardTransient {
    bytes32 constant SLOT = 0;

    modifier lock() {
        assembly {
            if tload(SLOT) { revert(0, 0) }
            tstore(SLOT, 1)
        }
        _;
        assembly {
            tstore(SLOT, 0)
        }
    }

    // 4909 gas
    function test() external lock {
        // Ignore call error
        bytes memory b = "";
        msg.sender.call(b);
    }
}


```
## Events, Enums, Errors, Require & Assert
```
| EVENTS
   event UpdateRegister(int indexed abc);

   function checking() public {
     // LOGIC
     emit UpdateRegister(12);
}

| Errors - only used while/when in reverting state 
  error NotAllowed(uint256 available, uint256 required);

  function calcc(uint abc) public {
     // LOGIC
     require( abc > 0, NotAllowed(12, 11));
}

| Require & Assert

  function jacl(uint abc) public {
    require(abc > 12, "hello"); require and revert both returns string when failed while try&catch
    assert(abc>6); // reverts with Panic(0x01) , returns bytes while try&catch
}

| Enums

  enum RegistrationStatus{
   started,
   not_started,
   inprogress,
   ended
}

   function newRegister() public returns(RegistrationStatus){
     return RegistrationStatus.in_progress;
}
```

## Try Catch 
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

// External contract used for try / catch examples
contract Foo {
    address public owner;

    constructor(address _owner) {
        require(_owner != address(0), "invalid address");
        assert(_owner != 0x0000000000000000000000000000000000000001);
        owner = _owner;
    }

    function myFunc(uint256 x) public pure returns (string memory) {
        require(x != 0, "require failed");
        return "my func was called";
    }
}

contract Bar {
    event Log(string message);
    event LogBytes(bytes data);

    Foo public foo;

    constructor() {
        // This Foo contract is used for example of try catch with external call
        foo = new Foo(msg.sender);
    }

    // Example of try / catch with external call
    // tryCatchExternalCall(0) => Log("external call failed")
    // tryCatchExternalCall(1) => Log("my func was called")
    function tryCatchExternalCall(uint256 _i) public {
        try foo.myFunc(_i) returns (string memory result) {
            emit Log(result);
        } catch {
            emit Log("external call failed");
        }
    }

    // Example of try / catch with contract creation
    // tryCatchNewContract(0x0000000000000000000000000000000000000000) => Log("invalid address")
    // tryCatchNewContract(0x0000000000000000000000000000000000000001) => LogBytes("")
    // tryCatchNewContract(0x0000000000000000000000000000000000000002) => Log("Foo created")
    function tryCatchNewContract(address _owner) public {
        try new Foo(_owner) returns (Foo foo) {
            // you can use variable foo here
            emit Log("Foo created");
        } catch Error(string memory reason) {
            // catch failing revert() and require()
            emit Log(reason);
        } catch (bytes memory reason) {
            // catch failing assert()
            emit LogBytes(reason);
        }
    }
}

```

##  break & continue &  "?:"
```
//  SYNTAX <condition> ? <ifTrue> : <if false>

 function addit(uint num1) public returns(uint) {
  return num1 / 2 + (num1 % 3 == 0 ? 0 : 1);
```

## Keccak256
```
| A deterministic hashing mechainsm used to produces hashes from input, cannot produce input from output

Some use cases are:

- Creating a deterministic unique ID from an input
- Commit-Reveal scheme
- Compact cryptographic signature (by signing the hash instead of a larger input)

 function jik()public{
  keccak256(120);
} 

```

## New Keyword vs Inheritance in Solidity
```
🔹 1️⃣ Using new Keyword

When you use:
```
```
Child c = new Child();
```
```
What happens?
A completely new contract is deployed. It gets a separate address.

It has its own:
- storage
- balance
- state

The parent contract just stores its address.
The contract using new is deployed on one address,
The contract created with new is deployed separately on a different address.

✔ Two contracts
✔ Two addresses
✔ Separate storage
```

```
🔹 2️⃣ Using Inheritance

Example:
```
```
contract A {
    function foo() public pure returns(uint) {
        return 1;
    }
}

contract B is A {
}
```
```
What happens? When you deploy B:
   ONLY B is deployed. A is not deployed separately. B contains the code of A.

There is one contract, One address, One storage layout

So your statement:
In inheritance, it's not the case, There is single contract that will deployed. That contract will have the functionality of both. Single address Single contract is deployed.

✅ That is correct.

Just improve grammar slightly:

👉 In inheritance, only the derived contract is deployed.
The parent contract’s code becomes part of the child contract.
There is a single contract with a single address.
```
```
⚠️ Important Auditor-Level Detail

There is one subtle thing many beginners miss:

With new:

You are making an external call, Gas cost is higher, Reentrancy becomes possible
Inter-contract trust assumptions apply

```

```
With inheritance:

It’s internal logic, No external calls, Cheaper gas, No cross-contract attack surface

This difference is VERY important in audits.
```
## Virtual, override & Super in Inheritance
```
2️⃣ virtual & override Rules

✅ virtual
Must be written in parent if you want it to be overridden.
```
```
function foo() public virtual {}
```

```
✅ override
Must be written in child if overriding.
```

```
function foo() public override {}
```
```
🚨 Important:

If multiple parents define the same function:
override(B, C), You MUST list all parents.
```

```

3️⃣ If Multiple Parents Have Same Function
Case A: Both override it

✔ You MUST override in child
❌ Otherwise → Compile error

Case B: Only one overrides

✔ No need to override in child
That implementation is used

Case C: None override (only defined in base)

✔ No issue
Base version is used
```

```
4️⃣ What super Actually Means

❌ NOT: "call my immediate parent"

✅ ACTUAL MEANING:

Call the next contract in Solidity’s C3 linearized inheritance order.

5️⃣ C3 Linearization Rule
If:
contract D is B, C

Linearization order:

D → C → B → A

⚠ Parent order matters.
```
## Chain(.super) Mechainsm in Inheritance adv
```
> How can we make B, C, and A all execute their versions of the same function?

# 🧱 First Understand the Problem

Imagine this structure:

```
```
     A
   /   \
  B     C
   \   /
     D
```

```
* A has `foo()`
* B overrides `foo()`
* C overrides `foo()`
* D overrides `foo()`

Now if you call:

```solidity
D.foo()
```

By default, only ONE implementation runs unless we chain them.

```

```
 ❌ What Happens If Parents Don’t Call `super`?

Example:
```

```solidity
contract B is A {
    function foo() public virtual override returns (string memory) {
        return "B";
    }
}

contract C is A {
    function foo() public virtual override returns (string memory) {
        return "C";
    }
}

contract D is B, C {
    function foo() public override(B, C) returns (string memory) {
        return super.foo();
    }
}
```

```
### Execution Order (important)

Since:
contract D is B, C

Linearization becomes:
D → C → B → A

Now when calling:

D.foo()
```
```
Steps:

1. D.foo()
2. super → goes to C.foo()
3. C returns "C"
4. Execution stops

❗ B and A never run.
```
```
## 🔥 Why Did It Stop?

Because C did NOT call `super`.
It just returned `"C"`.

So the chain ended there.
```
```
# ✅ How To Make Everyone Execute

Each contract must call `super`.
Think of it like passing a baton in a relay race. If one runner refuses to pass the baton → race ends.
# 🟢 Correct Version
```

```solidity
contract A {
    function foo() public virtual returns (string memory) {
        return "A";
    }
}

contract B is A {
    function foo() public virtual override returns (string memory) {
        return string.concat("B ", super.foo());
    }
}

contract C is A {
    function foo() public virtual override returns (string memory) {
        return string.concat("C ", super.foo());
    }
}

contract D is B, C {
    function foo() public override(B, C) returns (string memory) {
        return super.foo();
    }
}
```

```

# 🧠 Now What Happens?

Linearization:
 D → C → B → A

Calling:


D.foo()
```

```
Step by step:

1. D.foo()
2. super → C.foo()
3. C runs → calls super
4. super → B.foo()
5. B runs → calls super
6. super → A.foo()
7. A returns "A"

Now values combine:

A → "A"
B → "B A"
C → "C B A"

Final result:


"C B A"
```

```

# 🎯 The Golden Rule

If you want ALL parents to execute:

👉 Every overridden function must call `super`.

If even one parent doesn’t call `super`, execution chain stops there.

```

```
# 🏗 Real-World Example (Very Important)

OpenZeppelin uses this pattern in hooks like:
```

```solidity
_beforeTokenTransfer()
```

Each contract adds logic but calls:

```solidity
super._beforeTokenTransfer(...)
```

```
That way:

* Pausable logic runs
* ERC20 logic runs
* Snapshot logic runs
* Your custom logic runs

All in correct order.

If you forget `super` in one override?

🔥 You break the whole system.

```

```
# 🧠 Simple Mental Model

Imagine inheritance like a linked list:

D → C → B → A

Calling `super` means:

👉 “Call the next node”

No `super` = break the chain.
```

## Interfaces
```
**What is an Interface in Solidity?**
An **interface** in Solidity is a way to define a contract’s external functions without implementing them. It allows us to interact with other contracts without knowing their full source code.

**1. Why Use Interfaces?**

✅ **Calling External Contracts** → Allows interacting with deployed contracts
✅ **Cross-Contract Communication** → Essential for DeFi (e.g., Uniswap, Aave, Chainlink)
✅ **Efficiency** → Uses less gas compared to full contract inheritance
✅ **Security** → Enforces function signatures without exposing implementation

**2. How Interfaces Work**

Interfaces in Solidity:
- Contain **only function signatures** (no implementation)
- Functions are **implicitly `external`**
- Cannot declare state variables
- Cannot have constructors
```
```
 **3. Basic Interface Example**
Let’s say we have an **ERC20 token contract** deployed somewhere. We don’t have its full code, but we know its function signatures.

**ERC20 Token Interface**
```

```solidity
solidity
CopyEdit
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

interface IERC20 {
    function transfer(address recipient, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
}

```

```
🚀 **This defines an interface for any ERC20 token.**

**4. Using an Interface to Call a Contract**
Now, let’s **interact** with the ERC20 contract **without knowing its implementation**.

**Calling ERC20 Functions via Interface**
```

```solidity
solidity
CopyEdit
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

interface IERC20 {
    function transfer(address recipient, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
}

contract TokenInteractor {
    function sendTokens(address token, address recipient, uint256 amount) external {
        IERC20(token).transfer(recipient, amount);
    }

    function getBalance(address token, address owner) external view returns (uint256) {
        return IERC20(token).balanceOf(owner);
    }
}

```

```
✅ **How it Works:**

- We pass the `token` contract address at runtime.
- The contract **calls** the `transfer()` and `balanceOf()` functions from the external contract.
- No need to deploy the actual ERC20 contract inside this contract.

**5. Low-Level Calls vs Interface Calls**

An alternative to using interfaces is using **low-level `call`**, but **interfaces are safer and more readable**.

 **Low-Level `call()` (Risky)**

```

```solidity
solidity
CopyEdit
(bool success, bytes memory data) = token.call(
    abi.encodeWithSignature("transfer(address,uint256)", recipient, amount)
);
require(success, "Transfer failed");

```

```
🚨 **Downsides:**

- More error-prone
- Harder to debug
- No compile-time function signature validation

✅ **Interface calls are safer and preferred.**

**6. Interfaces with Custom Smart Contracts**
Let’s say we have a **Lending Platform** contract. Other contracts need to interact with it.

**Lending Platform Contract**

```

```solidity
solidity
CopyEdit
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract LendingPlatform {
    mapping(address => uint256) public deposits;

    function deposit() external payable {
        deposits[msg.sender] += msg.value;
    }

    function withdraw(uint256 amount) external {
        require(deposits[msg.sender] >= amount, "Insufficient balance");
        deposits[msg.sender] -= amount;
        payable(msg.sender).transfer(amount);
    }
}

```

```
Now, let’s create an interface for **external contracts to interact with it**.

**Lending Interface**
```

```solidity
solidity
CopyEdit
interface ILendingPlatform {
    function deposit() external payable;
    function withdraw(uint256 amount) external;
}

```

**Using the Interface in Another Contract**

```solidity
solidity
CopyEdit
contract User {
    function depositFunds(address lendingPlatform) external payable {
        ILendingPlatform(lendingPlatform).deposit{value: msg.value}();
    }

    function withdrawFunds(address lendingPlatform, uint256 amount) external {
        ILendingPlatform(lendingPlatform).withdraw(amount);
    }
}

```

✅ **Now, this contract can deposit and withdraw funds from any lending platform implementing this interface.**

```
**7. Interfaces with Chainlink Oracles**
Chainlink provides price feeds using interfaces.

**Chainlink Price Feed Interface**
```

```solidity
solidity
CopyEdit
interface AggregatorV3Interface {
    function latestRoundData()
        external
        view
        returns (
            uint80 roundId,
            int256 answer,
            uint256 startedAt,
            uint256 updatedAt,
            uint80 answeredInRound
        );
}

```

**Using the Chainlink Interface**

```solidity
solidity
CopyEdit
contract PriceConsumer {
    AggregatorV3Interface internal priceFeed;

    constructor(address _priceFeed) {
        priceFeed = AggregatorV3Interface(_priceFeed);
    }

    function getLatestPrice() public view returns (int) {
        (, int price, , , ) = priceFeed.latestRoundData();
        return price;
    }
}

```

✅ **This contract fetches price data without needing Chainlink’s full implementation.**
```

 **8. Summary**

| Feature | Details |
| --- | --- |
| **What is an Interface?** | A way to interact with external contracts without implementation |
| **Why Use It?** | Saves gas, improves security, enables modularity |
| **How to Declare?** | `interface InterfaceName { function myFunction() external; }` |
| **Implicit Rules** | No state variables, all functions `external` |
| **Use Cases** | ERC20, Lending, Chainlink, Uniswap, DeFi protocols |


**9. What’s Next?**

Do you want:

1. **Advanced topics like interface inheritance?**
2. **Security risks of using interfaces?**
3. **How Uniswap and Aave use interfaces in production?**

When using an **interface** in Solidity, there are two different contexts:

1. **Implementing the Interface in a Contract** (e.g., `MyToken` implementing `IERC20`)
2. **Interacting with an External Contract Using the Interface** (e.g., calling an ERC-20 contract's functions from another contract)


```

**1️⃣ Implementing an Interface in a Contract**

When you implement an interface **inside a contract**, you are defining the contract itself. So you don’t need to specify an address because the contract already knows its own address.

Example:

```solidity
solidity
CopyEdit
contract MyToken is IERC20 {
    mapping(address => uint256) private balances;

    function transfer(address to, uint256 amount) external override returns (bool) {
        balances[msg.sender] -= amount;
        balances[to] += amount;
        return true;
    }
}

```

- Here, `transfer` is defined **inside** `MyToken`.
- Since `MyToken` itself follows the `IERC20` structure, no need to use an address.

```

**2️⃣ Interacting With an External Contract**
If you want to **call an already deployed ERC-20 contract**, Solidity needs to know **which contract address** to talk to. That's when we wrap the interface around an address like this:
```

```solidity
solidity
CopyEdit
IERC20(0xTokenContractAddress).transfer(to, amount);

```

Example:

```solidity
solidity
CopyEdit
contract TokenSender {
    function sendTokens(address token, address to, uint256 amount) external {
        IERC20(token).transfer(to, amount);
    }
}

```

- Here, `IERC20(token)` tells Solidity:**"This is an ERC-20 contract at this address. Call its transfer function."**

## Libraries
```
- Libraries are similar to contracts, but you can't declare any state variables and you can't send ether.
- A library is embedded into the contract if all library functions are internal.
- Otherwise the library must be deployed and then linked before the contract is deployed.
```
File 1:
```
pragma solidity ^0.8.18;

import {AggregatorV3Interface} from "@chainlink/local/src/data-feeds/interfaces/AggregatorV3Interface.sol"; 

library PriceConverter {

    // We could make this public, but then we'd have to deploy it
    function getPrice() internal view returns (uint256) {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(
            0x694AA1769357215DE4FAC081bf1f309aDC325306
        );
        (, int256 answer, , , ) = priceFeed.latestRoundData();
        return uint256(answer * 10000000000);
    }

    function getConversionRate(
        uint256 ethAmount
    ) internal view returns (uint256) {
        uint256 ethPrice = getPrice();
        uint256 ethAmountInUsd = (ethPrice * ethAmount) / 1000000000000000000;
        return ethAmountInUsd;
    }
}
```
File 2:
```

pragma solidity ^0.8.19;
import "./PriceConverter.sol"; // Library

contract Practice {
    using PriceConverter for uint256;

    function getRegister() public {
        newRegister(10, "Taha");
        msg.value.getConversionRate(); // msg.value will be treated as first parameter as we have declared it with Using keyword to uint256.
    }

```
```
// msg.value will be treated as first parameter as we have declared it with Using keyword to uint256
```

## Abstract Contract: 
```
It means a contract have both implemented and un-implemented(like those functions inside interface) functions.

- When Inheriting an abstract contract, we need to override the unimplemented functions of that abstract contract
- Sometimes contracts do callback on these unimplemented functions like in ChainlinkVRF.
```

## Abi.encode:
 It converts whatever is given to it in its binary form and returns us the bytes format. 

## Abi.encodePacked:
 It does same as abi.encode but without padding
