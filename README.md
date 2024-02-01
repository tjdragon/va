# Digital Virtual Account Specification

tj@zodia.io

## Intro

A virtual account is a concept that allows to manage sub-accounts from a main account.  
In the digital asset space, a wallet W (such as BTC, or ETH), the main account, has a one-to-many
relationship with sub-accounts.   
Each sub-account acts as a virtual wallet.

## Naming
- W stands for Wallet
- $W_a$ is the total amount for W
- SA stands for Sub-Account
- $SA_i$ is the SA at index i
- $SA_i,a$ is the amount allocated for $SA_i$
- F is the fee buffer, also technically a SA
- U is the un-allocated SA

## Design
A SA is implemented in a database, a table that links the W id to the SA id:

Sub Account Table:

```postgres-sql
CREATE TABLE SubAccount (
    WalletId varchar(255) NOT NULL,
    SubAccountId varchar(255) NOT NULL,
    SubAccountName varchar(255),
    ExternalAddress varchar(255),
    Amount numeric(30),
    PRIMARY KEY(WalletId, SubAccountId)
);
```

- It is often necessary to manage fees at the SA level. By default, a fee SA is created.
- The sum of the amounts in the SAs is always <= to the wallet amount: $S_a \get \sum_{i} F_a + U_a + SA_i$

## Endpoints

### Create Sub Account
Creates an entry into the SubAccount table.

### Allocate
Allocates some amount to a given SA.
This is an absolute value set by the wallet owner.
The server will check that the sum of the amounts in the SAs is always <= to the wallet amount.

### (Un)Freeze Sub Account

## Transfer Out Scenario
- On the UI, the wallet W owner creates an intent for a transfer out and adds the SA id.
- If no SA id is provided, it is a BAU process
- If the amount to transfer out exceeds the SA amount, the intent is rejected.
- Once the transfer out is successful, W's amount is updated (BAU), and the fee is subtracted from the SA Fee and the amount
subtracted from SA id.

![Tx Out Flow](./va-tx-out.png)

## Transfer In Scenario
- On a Transfer In, BAU process
- The Wallet owner W will allocate the amount received to the appropriate Sub Account

![Tx In Flow](./va-tx-in.png)

## Auditing
All actions performed by the wallet owner are recorded.

## Reporting
Users can download reports

## Security
- Actions taken by the user from the website use the website authentication mechanism
- Server side endpoints use the API authentication mechanism
