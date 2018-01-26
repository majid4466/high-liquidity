# high-liquidity
A protocol to improve liquidity of cryptocurrency coins

## The Problem
Buying and selling crypto coins is not easy. You need to open an account with an exchange where fiat money is accepted. There are not many, and your choice is further limited by your jurisdiction. This is a major blow to adoption of crypto coins.

## Poroposed Solutions
We propose two solutions; one involving a trusted third party (an escrow service), and one with smart contracts.
The escrow based solution has an advantage where it does not need to wait for coins to adopt and implement the protocol. The smart contract based solution has an advantage where it does not need a trusted escrow service.

Both solutions depend on adoption by at least one fiat money transfer service such as Paypal, Skrill, OKPAY, etc. The service should:
  1. Allow the sender to opt-out of charge back rights for the transfer
  2. Allow the sender to specify a sender-tag, a receiver-tag, and a not-after timestamp when making the transfer
  3. Generate a proof-of-payment inspection token
  4. Offer an end point to receive the inspection token and send back a json response verifying the non-refundable payment has gone through

### Escrow Service
Let's assume the following scenario:

Alice and Bob both have an account with Escrow. They also both have an account on Skrill. Alice has one Monero credit in the escrow. So the coin is in the Escrow's wallet, and Alice has one Monero credit in Escrow's records. She can withdraw her coin to her wallet if she wants. She can also transfer the credit to another Escrow user (e.g. Bob).

### Smart Contract
To be added
