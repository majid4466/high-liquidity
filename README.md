# high-liquidity
A protocol to improve liquidity of cryptocurrency coins

## The Problem
Buying and selling crypto coins is not easy. You need to open an account with an exchange where fiat money is accepted. There are not many, and your choice is further limited by your jurisdiction. This is a major blow to adoption of crypto coins.

## Poroposed Solutions
We propose two solutions; one involving a trusted third party (an escrow service), and one with smart contracts.
The escrow based solution has an advantage where it does not need to wait for coins to adopt and implement the protocol. The smart contract based solution has an advantage where it does not need a trusted escrow service.

Both solutions depend on adoption by at least one fiat money transfer service such as Paypal, Skrill, OKPAY, etc. The service should:
  1. Allow users to generate (multiple) alias tags for their accounts and ensure the tags are unique within the service
  2. Allow the sender to opt-out of charge back rights for the transfer
  3. When setting up a transfer, allow the sender to specify
      1. a *sender-alias-tag*
      2. a *receiver-alias-tag*
      3. a *not-after* timestamp
      4. a *proof-of-payment query token*
  4. Verify the *receiver-alias-tag* is valid and belongs to the reseiver
  5. Verify that the transfer could be processed before the *not-after* timestamp
  6. Offer an end point to receive the pop query token and send back a json response verifying the non-refundable payment has gone through

### Escrow Service
Neither solution will work if no fiat money transfer service implements a *proof-of-payment* endpoint. But, for the first solution (escrow service), that is the only dependancy.

To examine it, let's assume the following scenario:

Alice and Bob both have an account with Escrow. They also both have an account on Skrill. Alice has a two ETH credit in the escrow service. So the coin is in the Escrow's wallet not Alice's. She can withdraw her coin to her wallet if she wants. She can also transfer the credit to another Escrow user (e.g. Bob).

Alice wants to sell one ETH for 1000 USD. She places the offer:

```
1 ETH for 1000 USD
Skrill Accepted
Use y5xo7s93sk in PoP receiver-tag
Use e6rew34njt57dsq as PoP query token
Trade window open till 1516959500
[(Hidden) Send USD to alice23@gmail.com]
```



Bob needs some ETH and finds Alice's offer good enough and decides to buy it. He clicks the "Buy" button.

The system locks the offer and shows Bob the receiving email for Alice.

Bob logs in to Skrill and opens the payment form. He enters:
  - 1000 USD
  - to be paid to alice23@gmail.com
  - non-reversiblly
  - before 1516959500
  - using y5xo7s93sk as receiver-tag
  - making PoP available with token e6rew34njt57dsq
Bob makes the payment.

When the payment is processed, bob clicks on "Check Proof-of-Payment" on Escrow site
Escrow server checks the result by sending a request to https://pop.skrill.com/e6rew34njt57dsq
Escrow server receives the following response:
```
{
  token: "e6rew34njt57dsq",
  service: "skrill",
  sender: "45f63k945t",
  receiver: "y5xo7s93sk",
  amount: 1000.00,
  currency: "USD",
  reversible: false,
  timestamp: 1516953562
}
```
Having verified PoP, the Escrow site 

### Smart Contract

Pseudocode
```
function check_escrow() {
  
  if (time.now() <  contract.not_before) {
    return;
  }
  
  if (time.now() > contract.not_after) {
    self.balance += escrow.amount;
    return;
  }
  
  pop = json_decode(wget contract.endpoint + '/' + contract.token);
  if (typeof pop != object) {
    return;
  }
  
  if (
        "token" in pop && pop.token == contract.token &&
        "sender" in pop && pop.sender == contract.sender_tag &&
        "receiver" in pop && pop.receiver == contract.receiver_tag &&
        "amount" in pop && pop.amount == contract.amount &&
        "currency" in pop && pop.currency == contract.currency &&
        "reversible" in pop && ( pop.reversible == contract.reversible || contract.reversible ) &&
        "timestamp" in pop && pop.timestamp >= contract.not_before && pop.timestamp <= contract.not_after
      ) {
      self.balance -= escrow.amount;
      contract.receiving_wallet.send(escrow.amount);
  }
  
  return;
  
}
```
To be continued
