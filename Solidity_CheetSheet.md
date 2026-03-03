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
