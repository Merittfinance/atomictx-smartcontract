# Atomic Transactions Smartcontract

_a smart contract for atomically exchanging ERC20 tokens with Ethereum_

## Description

Key to trustless trading is the ability to exchange assets atomically, ie to ensure that an exchange either goes through, or it fails completely, meaning that eg in an exchange Ether vs Tokens exchange it never happens that _only_ the Either or the Token transfer happens, but not the other one. Whilst this does not take care of all possible risks--notably in a high-volatility environment failing to execute a transaction could leave one party with a substantial mark-to-market loos--but this is second order when compared to the so-called _Herstatt risk_, named after the infamous case of a bank collapsing _after_ it had collected the receiving legs of fx transactions that it entered into, but _before_ paying out on the associated legs.

This contract takes care of the _Herstatt risk_ for the specific case of exchanging tokens against Ethereum, by applying the following protocol:

- both parties announce the details of the planned transaction to the contract
- the token seller transfers the right amount of tokens into the contract
- the Ethereum seller sends the right amount of Ethereum into the contract
- if everything is consistent the tokens are sent to the buyer, and the Ethereum is sent to the seller
- in case of inconsistencies or cancellation everything is unwound, and tokens and Ethereum are sent back


## Interfaces

### Atomic Transaction interace

The *atomic transaction interface* allows people to atomically exchange a token against Ether. It implements the following functions

**sellToken**. The `sellToken(token_addr, counterparty_addr, token_amount, ether_amount)` function announces the sale of `token_amount` tokens of type `token_addr` to the counterparty `counterparty_addr` against a payment of `ether_amount` ether. It returns a unique transaction ID, or an error code.

**buyToken**. The `buyToken(token_addr, counterparty_addr, token_amount, ether_amount)` function announces the purchase of `token_amount` tokens of type `token_addr` to the counterparty `counterparty_addr` against a payment of `ether_amount` ether. It returns a unique transaction ID, or an error code.

**cancelTransaction**. The `cancelTransaction(txid)` function call cancels and unwinds the transaction, provided it has not yet been completed. This is particularly important when one party has already submitted funds into the contract but the other party has not.

If `sellToken` and `buyToken` have been called and are consistent, and the correct amounts of Ether and tokens have been sent into the contract, coming from the correct addresses, then the Ether and the tokens are exchanged. If the calls were inconsistent, or the amount or addresses are wrong, or `cancelTransaction` has been called then the Ether or tokens are returned to sender. 



[ERC20]: https://theethereum.wiki/w/index.php/ERC20_Token_Standard
[ERC20wp]: https://en.wikipedia.org/wiki/ERC20
