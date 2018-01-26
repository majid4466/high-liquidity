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
    - a *sender-alias-tag*
    - a *receiver-alias-tag*
    - a *not-after* timestamp
    - a *proof-of-payment query token*
  4. Verify the *receiver-alias-tag* is valid and belongs to the reseiver
  5. Verify that the transfer could be processed before the *not-after* timestamp
  6. Offer an end point to receive the pop query token and send back a json response verifying the non-refundable payment has gone through

### Escrow Service
Neither solution will work if no fiat money transfer service implements a *proof-of-payment* endpoint. But, for the first solution (escrow service), that is the only dependancy.

To examine it, let's assume the following scenario:

Alice and Bob both have an account with Escrow. They also both have an account on Skrill. Alice has one Monero credit in the escrow. So the coin is in the Escrow's wallet, and Alice has one Monero credit in Escrow's records. She can withdraw her coin to her wallet if she wants. She can also transfer the credit to another Escrow user (e.g. Bob).

Sample request to payment inspection end point:
https://pop.skrill.com/e6rew34njt57dsq

Sample response from payment inspection end point:
```
{
  token: "e6rew34njt57dsq",
  service: "skrill",
  sender: "45f63k945t",
  receiver: "y5xo7s93sk",
  amount: 360.50,
  currency: "USD",
  reversible: false,
  timestamp: 1516953562
}
```

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
