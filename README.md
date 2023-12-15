## Locker

Minimalistic timelock contract.

`Locker` implements the simplest possible (?) timelock contract. It has a programmed immutable `owner` (`owner` can be set either during compilation or during execution of constructor).


Functionality: 

- A non-owner can send ether to it, but nothing else. 
- An owner can either schedule a release, OR request a payout. 
  - `schedule`: The owner specifies an amount; that amount can be claimed one week later. 
  - `payout`: if previously scheduled release is past due, then the amount is sent to `owner`. 

Technical details:

- It only ever uses one single storage `slot`.
- All `jump` operations jump forward.
- 

Caveats:

- This contract does not handle ERC20-tokens or nfts, _only_ ether
- This contract is not configurable (ownership, length of time delay).


### Testing

```
./build-wrapper test
```

### Code

Runtime bytecode (owner `0xfffffffffffffffffffffffffffffffffffffffe`)
```
$ eas ./src/main.etk 
7f000000000000000000000000fffffffffffffffffffffffffffffffffffffffe331415606657361561003e575f3560801b4262093a8001175f556066565b5f548063ffffffff1642116050576062565b60801c5f5f5f5f93335a5f5f55f16066575b5f5ffd5b5f5ff3
```


Constructor code (owner `0xfffffffffffffffffffffffffffffffffffffffe`)
```
$ eas ./src/ctor.etk 
606a8060095f395ff37f000000000000000000000000fffffffffffffffffffffffffffffffffffffffe331415606657361561003e575f3560801b4262093a8001175f556066565b5f548063ffffffff1642116050576062565b60801c5f5f5f5f93335a5f5f55f16066575b5f5ffd5b5f5ff3

```

Constructor code (sender becomes owner):
```
$ eas ./src/ctor2.etk 
606a80600d5f39336001525ff37f000000000000000000000000fffffffffffffffffffffffffffffffffffffffe331415606657361561003e575f3560801b4262093a8001175f556066565b5f548063ffffffff1642116050576062565b60801c5f5f5f5f93335a5f5f55f16066575b5f5ffd5b5f5ff3
```

### Pseudocode

This is an assembly-contract, but below is a _sketch_ of what a corresponding solidity-contract could somewhat look like. It probably doesn't compile, but is just meant to give an idea of what the assembly does. 

```solidity

const WEEK = 604800 // 60 * 60 * 24 * 7 seconds 

// locker implements the simplest possible (?) timelock contract. It has a programmed owner (set either
// during compilation or during construction). 
// - A non-owner can send ether to it (nothing else) 
// - An owner can schedule a release. The owner then specifies an amount, that amount can be claimed one week later. 
// - An owner can claim a release. If the previously set time is passed, then the previously set amount is sent to the sender.
// - This contract only ever  uses one storage slot. 
// Caveats:
// - This contract does not handle ERC20-tokens or nfts, _only_ ether
// - This contract is not configurable (ownership, length of time delay).

contract locker{
  
   var amount_time = 0;
   
   function default() external payable{
     if msg.sender != 0x0wner { // hardcoded in constructor
       return // return - not revert. We want to accept payments.
     }
     
     // At this point, we know that the sender is indeed the owner. 
     
     if len(calldata) > 0 { 
       
       // The owner wants to schedule a new release
       
       data = calldata[0];
       // We the amount into the upper 128 bits, and time in the lower. 
       this.amount_time = data << 128 || (now() + WEEK)
       return
     }
     
     // The owner wants to perform a payout
     
     var release_time = this.amount_time  && 0xFFFFFFFF; // 64 bits
     var release_amount = this.amount_time  >> 128; // 128 bits     
     if (  release_time < now() ) {  
       revert(); // too early
     }

     // Clear storage and send funds
     this.amount_time = 0
     // send amount, using all available gas
     if !msg.sender.call{ value: release_amount }("") { 
       revert()
     }
   }
}
```
