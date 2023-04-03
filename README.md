# splits
Splitter Contracts

## Comparison of existing contracts

There are two reference implemetations for splitter contracts:
* OppenZeppelin PaymentSplitter: [Documentation](https://docs.openzeppelin.com/contracts/4.x/) and [contract in GitHub](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/finance/PaymentSplitter.sol))
* [0xSplits](https://www.0xsplits.xyz/): [Documentation](https://docs.0xsplits.xyz/smartcontracts/overview) and [contract in GitHub](https://github.com/0xSplits/splits-contracts/blob/main/contracts/SplitMain.sol)

A comparison of lines of code yields the following:

| Project | Files | Lined of code | Blank Lines | Comment Lines | Total Lines |
| --- | --- | --- | --- | ---| --- |
| OpenZeppelin | 1 | 94 | 31 | 89 | 214 |
| 0xSplits | 5 | 654 | 102 | 522 | 1278 |

Just by looking at the complexity of the code, we choose to start with OpenZeppelin's implementation for the sake of simplicity.
