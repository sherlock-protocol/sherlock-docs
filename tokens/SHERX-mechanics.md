Below we discuss some of the more technical aspects of the SHERX token.

#### Example of Accruing SHERX tokens
Because every DAI token earns interest equally from Aave or Compound, Bob and Alice’s lockDAI amounts will represent an unchanging percentage of the DAI staker pool, assuming no further deposits or withdrawals -- even as the amount of DAI in the pool increases.

Sherlock takes advantage of this dynamic in order to calculate SherX owed to each staker. Calculating owed SherX is tricky because the protocol must track whose stake accumulated how much. The amount of SherX paid to the DAI pool per block is not affected by user staking or unstaking like the Aave interest -- it is independent of staker actions.

Because we are dealing with two different token types (DAI and SherX), owed interest must be tracked a bit differently. Sherlock creates a staker mapping and accrues SherX to each staker based on how long their DAI, etc. is staked in a staking pool. The only actions that can cause changes to the SherX payouts are:

1. A stake action
2. An unstake action
3. A change in the SherX payout itself

A change in SherX payout is easy to handle because it affects all stakers proportionally.

Therefore stake actions or unstake actions are the only true changes of state for calculating owed SherX. Each time a user stakes or unstakes, Sherlock calculates the blocks elapsed since the previous stake or unstake action and proportionally accrues SherX to the users involved in that window (during each window there is no other relevant change of state). Then the weightings are recalculated for each staker based on the new proportional ownership of the pool. This state persists until the next stake or unstake action, during which the payout amounts are calculated for the elapsed blocks and a new proportional payout state is created going forwards. This way the SherX owed to each staker is tracked.

Because different amounts of SherX can be owed to the same amount of lockTokens (100 lockETH could have earned 1 SherX or 100 SherX depending on when it was staked), this presents a challenge for the “fungibility” of lockTokens (and making them ERC-20 compliant), but the only change necessary to keep fungibility is liquidating the SherX balance on transfer events so that every new recipient of lockTokens receives no SherX. The "liquidated" SherX are distributed to the sender of the lockTokens. This function is described in detail in the "Transfer" section under the "Stakers" heading.

#### Mechanics Behind the SHERX Token
SHERX is an aggregation of all the tokens protocols use (or used in the past) to pay for coverage. It's reasonable to expect many protocols to pay using their governance token but some may also pay in ETH, DAI or other non-governance tokens. Sherlock will also likely contribute SHER to the SherX collateral pool as an added incentive for staking. It’s too early to tell which tokens SherX will be composed of and the exact amounts of each. As more protocols come onboard or payment methods change, the composition of SherX will also change. So if you receive SherX when there are only 3 tokens in the underlying collateral pool, your share of SherX may eventually be composed of far more than 3 tokens as new protocols come on or as payment methods change.

**How do you make sure a staker gets paid the correct amount of SherX?**
There are two pieces of data sent on-chain (and updated) for each protocol:

1. The amount of tokens the protocol will stream per block
2. The price of those tokens

It's very important to make sure each staker gets streamed the right dollar amount of SherX (to compensate them for the risk they are taking) at the block when it is streamed.

Here's a simplified example of how SherX is distributed per block:

Assumption: Initial ratio for SherX token to USD is 1:$10

**T=0**
Total Protocol Payment Streams: 1 ETH
Price of ETH: $3000
Value Streamed: $3000 (1 ETH * $3k price)
Total Pool $ Value: $3000
SherX distributed at T=0: 300 (1:$10 initial ratio)

Changes:

1. ETH price drops from $3000 to $1500, and the protocol's payment per block is updated to 2 ETH from 1 ETH (to maintain $3000 being streamed per block)
2. A new protocol is added that streams $1500 in DAI per block

**T=1**
Total Protocol Payment Streams: 2 ETH and 1500 DAI
Price of ETH: $1500
Price of DAI: $1
Streamed this block: $4500 (2 ETH * $1500 + 1500 DAI * $1)
Streamed in past: $1500 (1 ETH * $1500)
Total Pool $ Value: $6000 (3 ETH * $1500 + 1500 DAI * $1)
SherX Distributed at T=1:
(SherX this block / streamed this block) = (SherX in past  /  streamed in past)
(SherX this block / $4500) = (300 / $1500)
SherX this block = 900

#### Example Explained:
At block 0, an arbitrary amount of SherX can be minted for each dollar streamed. A 1:10 ratio is assumed to begin with. The only payment stream is 1 ETH per block. The protocol will receive info that 1 ETH is paid and the price of ETH is $3000, so the total USD payment per block is $3000. Keeping with the 1:10 ratio, 300 SherX tokens are minted for that block.

At T=1, the price of ETH drops to $1500. In addition, a new protocol joins the pool and streams a premium of $1500 in DAI. So at T=1 there is a total of $4500 being streamed ($3000 for the original protocol, streamed in ETH, and $1500 for the new protocol, streamed in DAI).

What’s important is that stakers at T=1 are owed $4500 in premiums. Now there are 3 ETH and 1500 DAI in the pool, at a current value of $6000 (3 ETH * $1500 + 1500 DAI * $1). Stakers at T=0 were given 300 SherX for the 1 ETH paid (it was worth $3000, now worth $1500). So 300 SherX now represents 25% ($1500 / $6000) of the pool. This means that in order to pay stakers at T=1 the correct dollar amount ($4500), 900 SherX tokens need to be allocated to them (300 SherX represents ¼ of the $6000 pool, so 900 SherX represents ¾ of the $6000 pool). This method ensures that stakers are paid the correct amount of SherX on each block, so they are properly compensated for the risk they are taking.

#### What Does a SherX Token Represent?
As seen in the example above, SherX holders are exposed to the price fluctuations of the tokens underpinning SherX. If a protocol pays $3000 in ETH and you are minted $3000 worth of SherX for that block, it does not mean you will always have $3000 worth of SherX. It means you own a fraction of the total SherX pool. If the whole pool is 1 ETH and the price of ETH goes up to $4500, now your stake is worth 50% more. At the same time, this doesn’t mean your SherX will always represent 1 ETH. If another payment token is added (like DAI), then your SherX will actually represent some ETH and some DAI. And now you will be exposed to the price fluctuations of both ETH and DAI. In this way, the basket of tokens underpinning SherX (and the token weights) might be constantly changing. Your SherX token represents a fraction of all the SherX outstanding and thus represents a fraction of all the tokens underpinning SherX. Although the total supply of SherX will keep increasing block by block, the underlying collateral will be growing proportionally as well. And SherX holders will always be able to redeem SherX for the underlying collateral at any time. This means SherX’s price should always be roughly “pegged” to the value of its underlying collateral (due to arbitrage opportunities).

#### How Does SHERX Work Without Any Oracles?
Sherlock is a backstop for exploits at other protocols, so heavy emphasis is placed on making the protocol as secure as possible. Sherlock was designed to minimize reliance on third parties (such as oracles). Instead of using an oracle to get prices, they are essentially sent on-chain whenever a protocol payment is updated. There are two instances when protocol payments are updated:

1. Once per week
2. When the on-chain premium USD amount is out of sync with the desired premium USD amount by +/- 10% (the two factors that can influence this are token price changes and changes in TVL at the relevant protocol)

Because of this dynamic, there will be instances when Sherlock's “on-chain” token prices are not perfectly in-line with current market prices. These periods will not persist longer than a week and the magnitudes of difference should rarely exceed +/- 10%. But it is worth noting that sometimes a protocol may be paying a bit too much (or too little) and a staker may be receiving a bit too much (or too little).

For example, if the only stream to our pool is sent on-chain as 1 ETH and $3000, then a subsequent 5% downward move in ETH would mean that we are registering stakers as receiving $3000 worth of SherX on that block, but they are only actually receiving $2850 ($3000 * 95%). And a protocol would be “paying” $3000 according to our on-chain data but they would actually only be paying $2850. If this becomes a major issue, the +/- 10% threshold can be tightened (the tradeoff is the gas cost to Sherlock of updating protocol payments on-chain more frequently).
