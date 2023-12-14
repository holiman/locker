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
73fffffffffffffffffffffffffffffffffffffffe331415605a573615610032575f3560801b4262093a8001175f55605a565b5f548063ffffffff1642116044576056565b60801c5f5f5f5f93335a5f5f55f1605a575b5f5ffd5b5f5ff3
```


Constructor code (owner `0xfffffffffffffffffffffffffffffffffffffffe`)
```
605e8060095f395ff373fffffffffffffffffffffffffffffffffffffffe331415605a573615610032575f3560801b4262093a8001175f55605a565b5f548063ffffffff1642116044576056565b60801c5f5f5f5f93335a5f5f55f1605a575b5f5ffd5b5f5ff3
```

Constructor code (sender becomes owner):
```
605e600d5f3933600152595ff373fffffffffffffffffffffffffffffffffffffffe331415605a573615610032575f3560801b4262093a8001175f55605a565b5f548063ffffffff1642116044576056565b60801c5f5f5f5f93335a5f5f55f1605a575b5f5ffd5b5f5ff3
```
