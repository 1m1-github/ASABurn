# AlgoBurn
A Smart Contract to burn any ASA

This Smart Contract (SC) can be used to **provably** burn any token on Algorand.

The SC will opt-into the ASA of your choice and keep the tokens forever.
No one, not the SC creator, nor the SC itself, can ever use the tokens or send them anywhere. They are locked forever in the SC account. Unless the ASA has a clawback account, in which case the clawback account can of course always clawback the tokens anytime.

The SC code is simple and can be verified easily.

The SC creator can withdraw ALGOs from the SC account. These should be the fees paid to AlgoBurn. The fees will be withdrawn periodically, but not after each burn, to minimise costs for users.
If a user tries to burn ALGOs, these ALGOs will effectively become donations to AlgoBurn. AlgoBurn is intended as a burn address for ASAs, not ALGO.

Max cost: 0.105 ALGO
- 0.101 for the SC to opt-into the ASA (only needed once ever)
- 0.001 for the user to opt-into the SC (only needed once ever)
- 0.001 to run the SC
- 0.001 to send your tokens to the SC account
- 0.001 fee for AlgoBurn

Min cost: 0.003 ALGO
- 0.001 to run the SC
- 0.001 to send your tokens to the SC account
- 0.001 fee for AlgoBurn

AlgoBurn will be a DAO. DAO token holders will have full access to all the fees earned by AlgoBurn.
