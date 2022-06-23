# ASABurn

### Note: Instead of using ASABurn, you can also sends the tokens to any address and re-key that to Zero-D (DDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDPAJIW7Y) [https://twitter.com/d13_co/status/1536045524989657088?s=20&t=2p2JYYpUWfGJlUq8czzfSA].

## A burn address for any ASA on Algorand

### Easy to use
Just send your ASA tokens to  
testnet: IWWAGEIEFLD4PNZWB2CYPBDM27QSKXMWBQQQUJHVLCDXE6JMA3GXJQHV7E  
mainnet: *not deployed yet*

ASABurn is a smart contract that keeps all tokens locked forever. The smart contract can also never be changed.

### Opt ASABurn into ASA
ASABurn needs to opt-into each ASA once before tokens can be burned.

Step 0, env vars
```
export USER=your account
export ASA=your ASA id
// testnet
export SYSTEM_ID=95487107
export SYSTEM_ACCOUNT=IWWAGEIEFLD4PNZWB2CYPBDM27QSKXMWBQQQUJHVLCDXE6JMA3GXJQHV7E
// mainnet
export SYSTEM_ID= *not deployed yet*
export SYSTEM_ACCOUNT= *not deployed yet*
```

Step 1, opt-into the smart contract.
```
goal app optin --app-id $SYSTEM_ID --from $USER
```

Step 2, send 1 ALGO to ASABurn and run ASABurn with param 'optin'.
```
goal clerk send --from=$USER --to=$SYSTEM_ACCOUNT --amount=1000000 --out=optin_send.stxn
goal app call --from=$USER --app-id=$SYSTEM_ID --foreign-asset $ASA --app-arg="str:optin" --out=optin_call.stxn
cat optin_send.stxn optin_call.stxn > combinedtransactions.txn
goal clerk group --infile combinedtransactions.txn --outfile groupedtransactions.txn
goal clerk sign --infile groupedtransactions.txn --outfile optin.stxn
goal clerk rawsend --filename optin.stxn
```
1 ALGO is sent to ASABurn to cover the opt-in fee and balance for the ASA and the rest as a fee for ASABurn.
Once ASABurn is opted-into an ASA, anyone can burn tokens of that ASA for free by simply sending them to the ASABurn account.

### Details
No one, not the ASABurn creator, nor ASABurn itself, not the ASA creator can ever retrieve/send/use the tokens. They are locked forever in the ASABurn account. Since the ASABurn code cannot be updated by anyone either, the lock is forever and guaranteed.
Unless the ASA has clawback enabled, in which case the clawback account can of course always clawback the tokens anytime. The ASA manager account could set the clawback account to the ASABurn account or set it to null, thereby guaranteeing the burning of their tokens.

The ASABurn code is simple and can be verified easily (see below).

ASABurn is intended as a burn address for ASAs, not ALGO.
The ASABurn creator can withdraw ALGOs from the ASABurn account. These should be the fees paid to ASABurn.
If a user tries to burn ALGOs, these ALGOs will effectively become donations to ASABurn.

ASABurn will eventually become a DAO (assuming ASABurn is used by the community) and the DAO token holders will decide when to withdraw the fees earned and what to do with them.

You can show your community that the tokens are in the ASABurn burn, thereby proving that they are burned.


### "Proof"
Since the code is quite simple, we will prove here that it does exactly as intended. This is in lieu of using expensive verification services that have historically still left bugs open, despite charging fully for their "proof".

We are looking at the file `code/state_approval_program.teal`

#### section boilerplate
Handle the various types of app calls (`NoOp`, `OptIn`, `CloseOut`, `UpdateApplication`, `DeleteApplication`).  
`UpdateApplication` and `DeleteApplication` jump to `decline`, i.e. no one can change the ASABurn code, not even the creator.  
`OptIn` and `CloseOut` jump to `approve`, allowing anyone to opt-into ASABurn (to make app calls to it).
`NoOp` jumps to `handle_noop`, where the rest of the logic is located.

#### section handle_noop
`handle_noop` allows 2 cases.  
Argument *optin* jumps to `handle_optin` and handles opting-in ASABurn to use an ASA.  
Argument *withdraw* jumps to `handle_withdraw` and handles the withdrawing of ALGOs from ASABurn to the creator.

#### section handle_optin
`handle_optin` checks that we have 2 transactions, the 1st is a 1 ALGO payment to ASABurn and the 2nd is the app call.  
Then execute the ASA opt-in of ASABurn.  
Finally, end the app call.

#### section handle_withdraw
`handle_withdraw` asserts that only the creator can initiate a withdrawal.  
Then send all ALGOs in the ASABurn account minus the ALGOs needed to stay opted-into the ASAs to the creator.
Finally, end the app call.

#### done
That's the entire contract.

### Parameters
testnet

ASABurn account: IWWAGEIEFLD4PNZWB2CYPBDM27QSKXMWBQQQUJHVLCDXE6JMA3GXJQHV7E  
ASABurn app id: 95487107  
creator account: BURN3MK23BN3DDV2JVYBL7VYIPBUFCKXQWK5W5ECG5FJMG2QNSGODQBGI4 (will eventually be controlled by DAO)

mainnet - not deployed yet  
ASABurn account:  
ASABurn app id:  
creator account: BURN3MK23BN3DDV2JVYBL7VYIPBUFCKXQWK5W5ECG5FJMG2QNSGODQBGI4 (will eventually be controlled by DAO)
