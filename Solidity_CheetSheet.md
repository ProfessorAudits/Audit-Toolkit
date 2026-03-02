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

| STACK & TRANSIENT STORAGE

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
  error NotAllowed();

  function calcc(uint abc) public {
     // LOGIC
     require( abc > 0, NotAllowed());
}

| Require & Assert

  function jacl(uint abc) public {
    require(abc > 12, "hello");
    assert(abc>6);
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
