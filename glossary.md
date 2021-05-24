#Glossary

## Sherlock Terminology

#### Buffer for Stakers / "First Money Out" Pool
- This is the pool of tokens that needs to be completely liquidated (in the event of a hack on one of our covered protocols) before any staker tokens can be used to pay out the hack. This pool of money is "protection" for stakers. The entire pool is "junior" to the staking pools in terms of liquidation priority. Most or all of the tokens in this pool will be contributed by the Sherlock protocol as an added protection for stakers.

#### Cooldown
- In order to remove a "stake" from a staking pool, a user must first activate the cooldown period. This period represents the time between the activation or intention to remove the stake and the time at which the stake is actually released from the protocol.

#### Coverage
- If funds at a protocol are covered, it means that Sherlock will reimburse the funds in the event of an exploit (as long as the exploit falls into a category that Sherlock agreed to reimburse). 

#### lockToken
- The token that represents a staker's proportional stake in the Sherlock staking pool. See the "lockToken" section under the "Tokens" header for more info.

#### Premium
- The amount a protocol pays Sherlock. In return, Sherlock reimburses covered exploits experienced by the protocol.

#### SHER
- Sherlock's governance token. See the "SHER" section under the "Tokens" header for more info.

#### SHERX
- A tokenized version of interest paid to stakers due to protocol premiums. See the "SHERX" section under the "Tokens" header for more info.

#### Staking
- The act of depositing money into a Sherlock staking pool. Once money has been deposited, tokens accrue to the depositor's address in the form of APY. All money in the staking pools are at risk of being liquidated due to an exploit at one of the protocols covered by Sherlock.

#### Unstaking
- The act of removing money from a Sherlock staking pool. This action can only be taken once the cooldown period for the stake has expired.
