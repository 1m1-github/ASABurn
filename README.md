# AlgoBurn
## A Smart Contract to burn any ASA

AlgoBurn can be used to **provably** burn any token on Algorand.

No one, not the AlgoBurn creator, nor AlgoBurn itself, not the ASA creator can ever retrieve/send/use the tokens. They are locked forever in the AlgoBurn account. Since the AlgoBurn code cannot be updated by anyone either, the lock is forever and guaranteed.
Unless the ASA has clawback enabled, in which case the clawback account can of course always clawback the tokens anytime. The ASA manager account could set the clawback account to the AlgoBurn account or set it to null, thereby guaranteeing the burning of their tokens.

The AlgoBurn code is simple and can be verified easily (see below).

AlgoBurn is intended as a burn address for ASAs, not ALGO.
The AlgoBurn creator can withdraw ALGOs from the AlgoBurn account. These should be the fees paid to AlgoBurn.
If a user tries to burn ALGOs, these ALGOs will effectively become donations to AlgoBurn.

AlgoBurn will eventually become a DAO (assuming AlgoBurn is used by the community) and the DAO token holders will decide when to withdraw the fees earned and what to do with them.

## How to use

Step 1
As usual, your account has to first opt-into AlgoBurn
Cost: 0.001 ALGO

Step 2
This step is only needed if AlgoBurn has never opted-into the ASA of interest:

Cost 1 ALGO

Step 3
Send ASA tokens to AlgoBurn
Cost 0.001 ALGO

Done
After step 3, your tokens are burned

You can show your community that the tokens are in the AlgoBurn burn, thereby proving that they are burned.


## Proof
Since the code is quite simple, we will prove here that it does exactly as intended. This is in lieu of using expensive verification services that have historically still left bugs open, despite charging fully for their "proof".


