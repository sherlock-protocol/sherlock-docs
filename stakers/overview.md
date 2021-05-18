A staker stakes in Sherlock's insurance pool because they will see a very high APY made up of 3 streams:

1. Fees from protocol customers (this will likely be biggest)
2. Interest earned from staking collateral on Aave, etc.
3. Rewards distributed in SHER (Sherlock’s governance token)

A staker will know that their capital is at risk but they’ll feel comfortable staking because:

1. They are getting a high yield in return for the risk
2. They see that the security team’s incentives are aligned with theirs
3. There will be a first-money out pool and a protocol deductible which means a hack would have to drain both of those pools before the staker would lose 1 dollar

A staker will be able to stake in any token on our whitelist of tokens (ETH, DAI, etc.). They will have to pay a moderate gas fee in order to stake their token (let’s say ETH) and they’ll receive lockETH (the token we mint to represent stakes in Sherlock’s insurance pool) in return.

Some feedback we received from the staker side early on is the ability to:
- Earn interest on a token of their choosing (i.e. a whitelist instead of one token)
- Keep exposure to their staked token over time (instead of exposure to a melting pot of tokens)

#### Why not make stakers stake with SHER (our governance token)? This could give SHER utility and prop up the price?

We expect the price of SHER to be highly correlated with hack events on protocols we actively cover. As soon as there is a hack on a covered protocol, it’s reasonable to assume SHER’s price will drop. If the insurance pool consists entirely of SHER, it’s likely the dollar value of our pool will shrink right at the moment we need to tap into the pool to repay a hack.

#### Why not make stakers stake in one token (like ETH) only?
1. We think this limits the potential size of our insurance pool.
2. If we end up covering pools that consist mainly of stablecoins, it creates a funding/liability mismatch.

**Expanding on #1:** You can imagine there are many stakers who would only be interested in staking in (and maintaining exposure to) a certain token and if we only allow one token to be staked (like ETH), then our pool size almost certainly will not be as big as it could be.
**Expanding on #2:** We also intend to cover pools for protocols that have varying token types. Some pools we cover will be all stablecoins, some will be ETH and others may be exposed to BTC. If our insurance pool is all ETH but we are covering a lot of stablecoin pools, you can imagine a situation where ETH crashes and all of the sudden we can’t cover many of the stablecoin pools we could previously cover (Our $100M ETH insurance pool crashes to a $10M ETH insurance pool due to ETH price drop by 90%, and the $80M DAI pool we were covering is still an $80M DAI pool). On the other hand, if our pool is all denominated in stablecoins and we are covering ETH pools at protocols, you can imagine a steep increase in the price of ETH would cause similar problems.
**Side Note on Insurance Pool Management:** We will never be able to perfectly “hedge” or “match” currency risk at the pools we cover because our insurance protocol utilizes leverage. So if our total cover outstanding is $200M -- $100M ETH and $100M DAI -- and our insurance pool is $100M (so 2x leverage), our pool cannot position itself to hedge out currency risk perfectly. It’s reasonable to say a 50/50 split of $50M ETH and $50M Dai could make the most sense however. Because we need to be able to manage token price risk, it’s helpful to have stakers stake in multiple token types (ETH, stablecoins, etc.). At any given time, it may be the case where stakers heavily bias to one token or token category (for example, there are less high yielding opportunities for ETH in DeFi right now). If our insurance pool biases in the extreme to one token or token category, we have the ability to increase or decrease APYs on specific token pools in order to incentivize stakers to stake in one token over another. This is another benefit of allowing a whitelist of tokens to be staked.
