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

