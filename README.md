# ASABurn
## A Smart Contract to burn any ASA

ASABurn can be used to **provably** burn any token on Algorand.

No one, not the ASABurn creator, nor ASABurn itself, not the ASA creator can ever retrieve/send/use the tokens. They are locked forever in the ASABurn account. Since the ASABurn code cannot be updated by anyone either, the lock is forever and guaranteed.
Unless the ASA has clawback enabled, in which case the clawback account can of course always clawback the tokens anytime. The ASA manager account could set the clawback account to the ASABurn account or set it to null, thereby guaranteeing the burning of their tokens.

The ASABurn code is simple and can be verified easily (see below).

ASABurn is intended as a burn address for ASAs, not ALGO.
The ASABurn creator can withdraw ALGOs from the ASABurn account. These should be the fees paid to ASABurn.
If a user tries to burn ALGOs, these ALGOs will effectively become donations to ASABurn.

ASABurn will eventually become a DAO (assuming ASABurn is used by the community) and the DAO token holders will decide when to withdraw the fees earned and what to do with them.

## How to use

**The execution below requires some technical expertise. I have an idea how to easily offer this as a GUI. Coming soonish.**

Step 1 (optional - only needed if ASABurn is not opted into your ASA yet)
As usual, your account has to first opt-into ASABurn
```
// testnet
export USER=your account
export SYSTEM_ID=94624777
goal app optin --app-id $SYSTEM_ID --from $USER
```
Note that step 1 is a simple smart contract opt-in - you can do this from any wallet

Step 2 (optional - only needed if ASABurn is not opted into your ASA yet)
```
// testnet
export USER=your account
export ASA=your ASA id
export SYSTEM_ID=94624777
export SYSTEM_ACCOUNT=VHYB7V4XFGGPRZ7ECK7VCX25HIOKBIX774CLXXZNOPKGAWV6ONVDBVS55A
goal clerk send --from=$USER --to=$SYSTEM_ACCOUNT --amount=1000000 --out=optin_send.stxn
goal app call --from=$USER --app-id=$SYSTEM_ID --foreign-asset $ASA --app-arg="str:optin" --out=optin_call.stxn
cat optin_send.stxn optin_call.stxn > combinedtransactions.txn
goal clerk group --infile combinedtransactions.txn --outfile groupedtransactions.txn
goal clerk sign --infile groupedtransactions.txn --outfile optin.stxn
goal clerk rawsend --filename optin.stxn
```
1 ALGO is sent to ASABurn to cover the opt-in fee and balance for the ASA and the rest as a fee for ASABurn

Step 3  
Send ASA tokens to ASABurn
```
// testnet
export USER=your account
export ASA=your ASA id
export AMOUNT=amount of tokens you want to burn
export SYSTEM_ACCOUNT=VHYB7V4XFGGPRZ7ECK7VCX25HIOKBIX774CLXXZNOPKGAWV6ONVDBVS55A
goal asset send --assetid=$ASA --from=$USER --to=$SYSTEM_ACCOUNT --amount=$AMOUNT
```
Note that step 3 is a simple ASA transaction - send tokens to SYSTEM_ACCOUNT as usual and they are burned - you can do this from any wallet

Done  
After step 3, your tokens are burned

You can show your community that the tokens are in the ASABurn burn, thereby proving that they are burned.




## "Proof"
Since the code is quite simple, we will prove here that it does exactly as intended. This is in lieu of using expensive verification services that have historically still left bugs open, despite charging fully for their "proof".

We are looking at the file `code/state_approval_program.teal`

### 1
Lines 11-34 handle the various types of app calls (`NoOp`, `OptIn`, `CloseOut`, `UpdateApplication`, `DeleteApplication`).  
`UpdateApplication` and `DeleteApplication` jump to `decline`, i.e. no one can change the ASABurn code, not even the creator.  
`OptIn` and `CloseOut` jump to `approve`, allowing anyone to opt-into ASABurn (to make app calls to it).
`NoOp` jumps to `handle_noop`, where the rest of the logic is located.

### 2
`handle_noop` is in lines 43-56 and allows 2 cases.  
Argument *optin* jumps to `handle_optin` and handles opting-in ASABurn to use an ASA.  
Argument *withdraw* jumps to `handle_withdraw` and handles the withdrawing of ALGOs from ASABurn to the creator.

### 3
`handle_optin`  
Lines 63-82 check that we have 2 transactions, the 1st is a 1 ALGO payment to ASABurn and the 2nd is the app call.  
Lines 84-94 executes the ASA opt-in of ASABurn.  
Line 96 ends the app call.

### 4
`handle_withdraw`  
Lines 104-108 assert that only the creator can initiate a withdrawal.  
Lines 110-126 send all ALGOs in the ASABurn account minus the ALGOs needed to stay opted-into the ASAs to the creator.  
Lien 128 ends the app call.

### Done
That's the entire contract.

## Parameters
testnet

ASABurn account: VHYB7V4XFGGPRZ7ECK7VCX25HIOKBIX774CLXXZNOPKGAWV6ONVDBVS55A  
ASABurn app id: 94624777  
creator account: BURN3MK23BN3DDV2JVYBL7VYIPBUFCKXQWK5W5ECG5FJMG2QNSGODQBGI4 (will eventually be controlled by DAO)

mainnet - not deployed yet  
ASABurn account:  
ASABurn app id:  
creator account: BURN3MK23BN3DDV2JVYBL7VYIPBUFCKXQWK5W5ECG5FJMG2QNSGODQBGI4 (will eventually be controlled by DAO)
