Once we’ve decided on the cover amount (say $100M) and the price of coverage (say we agree on 2%
annually), then we can calculate the per-block premium that the protocol will send to Sherlock.

Because it’s extremely gas-inefficient to send a payment every block, we will have the protocol
initially send us two weeks worth of payment up front. The first weeks worth will be
“drawn down” in our internal accounting block-by-block and the second weeks worth will be a
buffer, just in case there is some problem with payment once the first week has been
drawn down.

In this way, a protocol can manage their payment by sending us one weeks’ 
payment at a time (or a months’ worth of prepay if they want to). If a protocol isn’t able
to pay for whatever reason, the coverage stops at the block where the payment stream ended.
