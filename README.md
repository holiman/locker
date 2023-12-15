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
