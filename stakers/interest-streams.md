As a staker’s stake remains in our pool over time, it will earn three different streams of interest.

#### STREAM \#1
A staker’s initial stake (call it ETH) will likely be swept into an “already insured” protocol like Aave -- where it will continuously earn market-rate interest.

**Why Aave?**
Our #1 priority with our insurance pool funds is safety. It doesn’t make much sense for us to deposit our pool funds into the very same ecosystem we are trying to insure. However, certain protocols have their own insurance-like funds (Aave being a notable one with the Safety Module). We think it could be justified to place our insurance pool’s funds in protocols that meet a certain standard of safety and non-Sherlock insurance -- assuming we aren’t insuring that protocol or re-insuring its insurance fund ourselves.

#### STREAM \#2
A staker will also earn interest from protocol payments. A protocol that is paying Sherlock for insurance will “stream” payment block-by-block for coverage. We allow protocols to pay in effectively whatever token they want (this is very important for protocols to be able to afford the price of our insurance). Because protocols will pay in a variety of tokens, we decided to mint a new token, called SherX (short for Sherlock ETF), to represent shares of all the protocol payment tokens. If we are covering 10 protocols and they are all paying in different tokens (often their governance token), we don’t want to deal with paying stakers in 10 different tokens. So we mint SherX tokens to represent a staker’s share of earned fees.

**Why do we mint SherX instead of paying stakers directly in protocol tokens?**
It really comes down to gas. You can imagine if we have 15 or 20 protocols on board and 15 or 20 different tokens being paid into the pool, it’s very costly on unstake to pay stakers in 20 different tokens. On top of that, the staker might have staked ETH and they just want more ETH. So they have to do 20 separate transactions after the first 20 transactions to get all the tokens into ETH. Whereas if we mint SherX tokens to represent a share of the pool of 20 tokens, it’s more gas efficient (and gas doesn’t increase as we add more protocols/tokens) and it will be easier for the staker to get from SherX to ETH or a specific token they want to hold (we will likely do this transaction for them on Uniswap in our unstake function). In order to make this possible, we plan to implement a Uniswap pool for SherX (SherX vs. ETH) and we plan to allow anyone to redeem SherX tokens for the underlying collateral (20 different tokens, etc.) at any time using our redeem function. This will insure that SherX always trades fairly close to the value of its underlying collateral (because an arbitrager could buy discounted SherX tokens on Uniswap and redeem them for the full value of its underlying collateral).

So far we’ve covered 2 of the 3 interest streams a staker will receive. To review: on unstake, a staker will get a higher value of their staked token back (their stake * deposit rate on Aave * blocks staked) and they will get the SherX tokens they earned from their time staking in our insurance pool.

#### STREAM /#3
A staker may also receive Sherlock governance token (SHER) incentives. Especially as we are bootstrapping the pool, the APY generated from protocol payments may not be very high. In the bootstrapping phase, we will likely have a fraction of our ideal number of protocol customers. This is not only because selling to protocols may be difficult early on, but because we need to do security analysis on every protocol before we can onboard them. During this bootstrapping phase, it will be difficult to grow our insurance pool as stakers will not have much incentive to stake (due to low APY). This is the time when it will be critical to use our governance token to build up the pool. There are two main ways we plan to use SHER (our governance token) to build up the insurance pool:

1. Paying SHER to the staker pools in order to increase the effective APY on staking and encourage more stakers.
2. Selling SHER in the market for ETH or another token. We would then stake that ETH into the insurance pool. This is effectively protocol-controlled value (as it’s come to be known). We may decide to put these funds into a “first-money-out” pool in order to encourage stakers even more. They would now be protected from losses by the “first-money-out” pool in addition to any protection received from deductibles provided by protocols themselves. More on this later.

#### How we implement \#1 from a technical perspective (juicing the staker APYs with SHER)
We were considering adding our governance token payment stream “on top” of everything else and paying it out as a separate stream. On withdrawal this would look like:

- Staker receives their stake back (which includes interest earned from Aave)
- They receive SherX tokens
- They receive Sherlock governance tokens (SHER)
This model would separate the SherX (the ETF of protocol payment tokens) transaction from the SHER (our governance token) transaction. This requires separate SHER logic in our smart contracts and creates an extra transaction on stake withdrawal.

Instead, we are planning to integrate SHER incentives into SherX. So Sherlock itself will just look like another protocol payment stream, and it requires zero extra lines of Solidity code to implement SHER incentives. This will have exactly the same effect of juicing the effective APY for stakers and all the logic of distribution is already taken care of. It also means one less transaction on withdrawal.

#### Some interesting side effects of paying SHER incentives through SherX: 
Instead of paying SHER directly to stakers, we effectively maintain custody of our governance power (by minting the SherX that represents SHER and giving only SherX to stakers). Small stakers especially will not want to redeem their SherX tokens for the underlying collateral (because of the large gas costs associated with 10-20 token transfers). This will either mean that we will keep more governance power ourselves (if no one redeems SherX for the underlying collateral), or arbitragers will accrue governance power (since they will be the ones collecting large amounts of SherX and redeeming them for the underlying tokens -- including SHER). We see this side effect as slightly negative but we think it’s worth it to have such a simple SHER incentive mechanism.
