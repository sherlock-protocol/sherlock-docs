Because protocols will be paying premiums in different types of tokens, it will be very gas-intensive to pay stakers in all of these different token types. As an alternative, we created SHERX token which simply represents the many different token types paid by protocols. This way, we can transfer one token to stakers (SHERX) instead of transferring 10 or 20 different tokens. SHERX will always be redeemable for its proportional underlying collateral (the 10 or 20 different tokens) so it will always have intrinsic value.

Below we discuss some of the more technical aspects of the SHERX token.

#### Example of Accruing SHERX tokens
Because every DAI token earns interest equally from Aave, Bob and Alice’s lockDAI amounts will represent an unchanging percentage of the DAI staker pool, assuming no further deposits or withdrawals. We’re able to take advantage of this dynamic in order to calculate SherX owed to each staker. Calculating owed SherX is tricky because we have to remember whose stake accumulated how much. The amount of SherX paid to the DAI pool per block is not affected by user staking or unstaking like the Aave interest -- it is independent of staker actions. Because we are dealing with two different token types (DAI and SherX), we have to track owed interest a bit differently. We took inspiration from SushiSwap in order to track the SherX owed to each staker. We create a staker mapping and accrue SherX to each staker based on how long their DAI is staked in the insurance pool. The only actions that can cause changes to the SherX payouts are a stake action, unstake action, or a change in the SherX payout itself. A change in SherX payout is easy to handle because it affects all stakers proportionally. Therefore stake actions or unstake actions are the only true changes of state for calculating owed SherX. Each time a user stakes or unstakes, we calculate the blocks elapsed since the previous stake or unstake action and proportionally accrue SherX to the users involved in that window (during which there was no other relevant change of state). Then we recalculate the weightings for each staker based on the new proportional ownership of the pool. This state persists until the next stake or unstake action, during which the payout amounts are calculated for the elapsed blocks and a new proportional payout state is created going forwards. This way we’re able to track the SherX owed to each staker. Because different amounts of SherX can be owed to the same amount of lockTokens (100 lockETH could have earned 1 SherX or 100 SherX depending on when it was staked), this presents a challenge for the “fungibility” of lockTokens (and making them ERC-20 compliant), but the only change we need to make in order to keep fungibility is drawing down the SherX balance on transfer events so that every new recipient of lockTokens receives no SherX. The SherX are distributed to the sender of the lockTokens in the method described in “The transfer function.”

#### Mechanics Behind the SHERX Token
We think of the SherX token as an ETF representing all the tokens protocols use (or used) to pay for insurance. We expect many protocols to pay using their governance token but we also expect some protocols to pay in ETH, DAI or other non-governance tokens. We also expect to contribute our own governance token (SHER) to the SherX pool as an added incentive for staking. It’s too early to tell which tokens SherX will be composed of and the amounts of each. As more protocols come onboard or payment methods change the composition of SherX will also change. So if you receive SherX when there are only 3 tokens underlying it, your share of SherX may eventually represent far more than 3 tokens as we bring on new protocols or as payment methods change.

**How do we ensure a staker gets paid the correct amount of SherX?**
As noted earlier, there are two pieces of data sent on-chain for each protocol:

1. The amount of tokens the protocol will stream per block
2. The dollar value of tokens the protocol will stream per block

A very important criteria for SherX is to make sure each staker gets streamed the right dollar amount they deserve (for the risk they are taking) at the block when it is streamed.
We’ll walk through a simplified example of how SherX is distributed per block:

Assumption: Initial ratio for SherX to USD is 1:10

**T=0**
Total Protocol Streams: 1 ETH
Price of ETH: $3000
Value Streamed: $3000
Total Pool $ Value: $3000
SherX distributed at T=0: 300

Changes:

1. ETH price drops from $3000 to $1500, and we update the protocol’s ETH per block to 2 ETH per block (to maintain $3000 being streamed per block)
2. We add a new protocol that streams $1500 in DAI per block

**T=1**
Total Protocol Streams: 2 ETH and 1500 DAI
Price of ETH: $1500
Price of DAI: $1
Streamed this block: $4500 (2 ETH * $1500 + 1500 DAI * $1)
Streamed in past: $1500 (1 ETH * $1500)
Total Pool Size: $6000 (3 ETH * $1500 + 1500 DAI * $1)
SherX Distributed at T=1:
(SherX in past  /  streamed in past) = (SherX this block / streamed this block)
(300 / $1500) = (SherX this block / $4500)
SherX this block = 900

#### Description of the Example Above:
At block 0, we can pick an arbitrary amount of SherX to mint for each dollar streamed. Let’s assume a 1:10 ratio to begin with. Let’s assume the only payment stream is 1 ETH per block. If ETH’s price is $3000, we will also know that $3000 is streamed for this block (because we send the dollar value of each protocol’s stream-per-block on-chain). Keeping with the 1:10 ratio, we’ll mint 300 SherX tokens for that block. At T=1, the price of ETH drops to $1500. In addition, a new protocol joins the pool and streams a premium of $1500 in DAI. So at T=1 we have a total of $4500 being streamed ($3000 for the original protocol, streamed in ETH, and $1500 for the new protocol, streamed in DAI). What’s important is that stakers at T=1 are owed $4500 in premiums. Now we have 3 ETH and 1500 DAI in the pool, at a current value of $6000 (3 ETH * $1500 + 1500 DAI * $1). Stakers at T=0 were given 300 SherX for the 1 ETH paid (it was worth $3000, now worth $1500). So 300 SherX now represents 25% ($1500 / $6000) of the pool. This means that in order to pay stakers at T=1 the correct dollar amount ($4500) we need to allocate 900 SherX tokens to them (300 SherX represents ¼ of the $6000 pool, so 900 SherX represents ¾ of the $6000 pool). Using this method, we can ensure that stakers are paid the correct amount of SherX on each block, so they are properly compensated for the risks they are taking.

#### What Does a SherX Token Represent?
As seen in the example above, SherX holders are exposed to the price fluctuations of the tokens underpinning SherX (much like a traditional ETF). If a protocol pays $3000 in ETH and you are minted $3000 worth of SherX for that block, it does not mean you will always have $3000 worth of SherX. It means you own a fraction of the total SherX pool. If the whole pool is 1 ETH and the price of ETH goes up to $4500, now your stake is worth 50% more. At the same time, this doesn’t mean your SherX will always represent 1 ETH. If another payment token is added (like DAI), then your SherX will actually represent some ETH and some DAI. And now you will be exposed to the price fluctuations of both ETH and DAI. In this way, the basket of tokens underpinning SherX (and the token weights) might be constantly changing. Your SherX token represents a fraction of all the SherX outstanding and thus represents a fraction of all the tokens underpinning SherX. Although the total supply of SherX will keep increasing block by block, the underlying collateral will be growing proportionally as well. And we will always allow SherX holders to redeem SherX for the underlying collateral at any time. This means SherX’s price should always be roughly “pegged” to the value of its underlying collateral (due to arbitrage opportunities).

#### How Does SHERX Work Without Any Oracles?
Sherlock is a backstop for exploits at other protocols, so we place heavy emphasis on making our own protocol as secure as possible. We’ve gone out of our way to reduce Sherlock’s reliance on third parties (such as oracles). Instead of using an oracle to get prices, we essentially send them on-chain whenever we update protocol payments. There are two instances when we update protocol payments:

1. Weekly
2. On a token price move of +/- 10% (technically it’s a $ premium divergence of +/- 10%, TVL is the other variable)
Because of this, there will be instances when our “on-chain” token prices are not perfectly in-line with current market prices. These periods will not persist longer than a week and the magnitudes of difference should never exceed +/- 10%. But it is worth noting that sometimes a protocol may be paying a bit too much (or too little) and a staker may be receiving a bit too much (or too little). So if the only stream to our pool is sent on-chain as 1 ETH and $3000, then a subsequent 5% downward move in ETH would mean that we are registering stakers as receiving $3000 worth of SherX on that block, but they are only actually receiving $2850 ($3000 * 95%). And a protocol would be “paying” $3000 according to our on-chain data but they would actually only be paying $2850. We don’t expect this to be a major issue but we can tighten our +/- 10% threshold if it becomes one (the tradeoff is the gas cost to Sherlock of updating protocol payments on-chain more frequently).
