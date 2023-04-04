# splits
Splitter Contracts

## Comparison of existing contracts

There are two reference implemetations for splitter contracts:
* OppenZeppelin PaymentSplitter: [Documentation](https://docs.openzeppelin.com/contracts/2.x/api/payment) and [contract in GitHub](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/finance/PaymentSplitter.sol)
* [0xSplits](https://www.0xsplits.xyz/): [Documentation](https://docs.0xsplits.xyz/smartcontracts/overview) and [contract in GitHub](https://github.com/0xSplits/splits-contracts/blob/main/contracts/SplitMain.sol)

A comparison of lines of code yields the following:

| Project | Files | Lined of code | Blank Lines | Comment Lines | Total Lines |
| --- | --- | --- | --- | ---| --- |
| OpenZeppelin | 1 | 94 | 31 | 89 | 214 |
| 0xSplits | 5 | 654 | 102 | 522 | 1278 |

Just by looking at the complexity of the code, we choose to start with OpenZeppelin's implementation for the sake of simplicity.

Additional features that may be factored in later include:

| Feature | OpenZeppelin | 0xSplits |
| --- | --- | --- |
| Handles ether splits | ✅ | ✅ |
| Handles ERC20 splits | ✅ | ✅ |
| Pull-Payment Implementation | ✅ | ✅ |
| Distribution of split can be updated | ❌ | ✅ |

## Considerations

From the corresponding OpenZeppeling documentation:

> This contract allows to split Ether payments among a group of accounts. The sender does not need to be aware that the Ether will be split in this way, since it is handled transparently by the contract.
>
> The split can be in equal parts or in any other arbitrary proportion. The way this is specified is by assigning each account to a number of shares. Of all the Ether that this contract receives, each account will then be able to claim an amount proportional to the percentage of total shares they were assigned.
>
> `PaymentSplitter` follows a *pull payment* model. This means that payments are not automatically forwarded to the accounts but kept in this contract, and the actual transfer is triggered as a separate step by calling the `release` function.

Modifications to the current implemenation:

- The contract needs to know which account is the validator owner, in case the validator exits. In the standard implementation the recipient accounts are stored in an array. In this implementation, the validator owner should always be listed in the first position (index 0). This requires no modification to the contract code, but instead be implemented in the UI that deploys the contract.
- Should a validator exit, this contract shall receive the full stake (32 ETH), which should not be split, but rather forwarded in full to the validator owner.

## Implementation

The modified `PaymentSplitter.sol` can be found int the contracts folder with the following 3 modifications to the original contract. You can check that these are the only modifications by doing a **diff** between the original [contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/finance/PaymentSplitter.sol) and the modified [contract](contracts/PaymentSplitter.sol).

1. Line 44, defines an internal constant *STAKE* with a value of 32 ether:

```Solidity
    uint256 private constant STAKE = 32 ether;
```

2. Lines 131-133, if *totalReceived* is larger than *STAKE*, substracts *STAKE* from *totalReceived*, which helps keep the accounting in check because we are only tracking the rewards and how they should be split among the various payees: 

```Solidity
        if(totalReceived >= STAKE) {
            totalReceived -= STAKE;
        }
```

3. Lines 201-206, modifies the original `return` value for the function `_pendingPayment()` by storing the original value in the *payment* variable, and then checks if the current balance is equal or larger than *STAKE*, and if the payee is the validator, in which case adds the value of *STAKE* to the payment, otherwise returns as the original function.

```Solidity
        uint256 payment = (totalReceived * _shares[account]) / _totalShares - alreadyReleased;
        if(address(this).balance >= STAKE && account == payee(0)){
            return STAKE + payment;
        } else {
            return payment;
        }
```

## License

This software is licensed under the [MIT License](LICENSE).
