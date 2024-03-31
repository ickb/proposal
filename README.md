# iCKB

## Listenable Introduction

If you would like to listen to an introduction of the project before diving in, Jordan Mack explains iCKB in less than 7 minutes during an episode of [Hashing it Out](https://www.youtube.com/watch?v=CcFFuenup38&t=781s).

## Problem

### NervosDAO Illiquidity

The NervosDAO is possibly the most important smart-contract of Nervos Layer 1 (L1). A CKB holder can lock his CKB in the NervosDAO in exchange for a receipt of that specific deposit. Every 180 epochs (~30 days) the depositor has the option of exchanging his receipt to unlock his initial deposit plus accrued interest. This creates an illiquidity for the depositor while the CKB is locked.

### Untapped Potential

There exists untapped potential in the Nervos ecosystem for a protocol that can liquify NervosDAO accrued interest and bridge it from L1 to L2. This protocol could enable CKB-based [Initial Stake Pool Offerings](https://www.meld.com/ispo) (ISPO), where users can lock CKB to support new early stage projects without losing their original CKB deposit.

The protocol could also be used to enable a community voting mechanism with funds locked in the NervosDAO, as well as a multitude more L1 & L2 applications!

Looking far away, this protocol could also enable Godwoken switch from pCKB to a new native token that protects every Godwoken user from CKB issuance.

### dCKB (Unmaintained)

In the past there has been an effort to tackle this challenge by [NexisDAO with dCKB](https://docs.nexisdao.com/nexisdao/mint-dckb). Their approach is to tokenize the holder receipt, which in turn becomes tradeable and so the holder keeps being liquid. The issue with their approach is that only the original owner can unlock the deposit. Judging by their [GitHub repository's issues](https://github.com/NexisDao/NexisDao-core/issues), dCKB does not appear to be actively maintained.

## Solution

### Enter iCKB

iCKB is the name for a [xUDT token](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0052-extensible-udt/0052-extensible-udt.md) that represents deposits in the protocol. As with dCKB, iCKB's approach is to tokenize NervosDAO receipts, but with a twist: the protocol owns all the CKB deposits and maintains a pool of them. This means that all the deposits and withdrawals are shared, so anyone can use anyone else's deposit to exit once it's mature.

This protocol aims to solve two problems with NervosDAO:

- CKB locked in the NervosDAO remains liquid as iCKB can truly be used as a normal currency.
- iCKB can be converted back to CKB quickly at any time without having to wait for maturity.

### Water Mill Analogy

As a [water mill](https://tenor.com/view/water-wheel-mill-gif-19806697) has many distinct buckets, each at different wheel positions, in which the water is:

- Collected
- Maintained
- Released

In the same way, the protocol can have many distinct deposits, each of them constantly moving at different stages of maturity:

- **Collected**: Users deposit CKB and receive iCKB.
- **Maintained**: Deposits accrue interest in the NervosDAO.
- **Released**: If a user wants to exchange iCKB for CKB, they can use any deposit that is at maturity.

### Feedback

Jordan Mack's comments on Nervos L1 & iCKB:
> In a more abstract sense, this doesn't violate any of intentions of the platform. The CKB that is staked is still out of circulation. iCKB does not grant the holder the ability to store data on the blockchain. In the most pure sense, iCKB is enabling the functionality that dCKB was trying to achieve. It better solves the problem because anyone can unlock the original CKB from the NervosDAO using iCKB instead of requiring the original owner to unlock it as with dCKB.

## Team

### Phroi

I'm the the ideas baker and solidity developer behind Opthy, 3° among peers at August 2021 Hackathon! [Phroi](http://phroi.com) as pseudonym exists since that Hackathon, here you can find the code I wrote in that occasion: [core](https://github.com/opthydefi/opthy-v0-core), [tests](https://github.com/opthydefi/opthy-v0-tests) and [ui](https://github.com/opthydefi/opthy-v0-ui-web). Later on I continued working with Hexmate on [Opthy](https://github.com/opthydefi/opthy-v1-core) until May 2022, when I left for becoming a solo Indie Hacker. Since then I'm working on a few projects under different pseudonyms.

### Discovering iCKB

During February 2022, while [testing the ground for a NervosDAO based ISPO](https://discord.com/channels/657799690070523914/657799690552606745/943306112889933864), I discovered the untapped need for a token that liquefies and bridges interests from L1 to L2 and so with Jordan Macks's help I started researching its feasibility.

## Diving Into The Protocol

### On-Chain, Trust-Less and Decentralized

This protocol defines a solid way to exchange between CKB and iCKB. The design aim is to make iCKB as simple, robust and neutral as possible, making it capable of meeting the current and future needs of Nervos users.

This protocol lives completely on Nervos Layer 1. It works by wrapping NervosDAO transactions: a deposit is first tracked by its protocol receipt and later on it's converted in its equivalent amount of iCKB.

### iCKB/CKB Exchange Rate Idea

The iCKB mechanism for wrapping interest is similar to [Compound's cTokens](https://compound.finance/docs/ctokens). The CKB to iCKB exchange rate is determined by block number. At the genesis block `1 CKB` is equal to `1 iCKB`. As time passes `1 CKB` is slowly worth less than `1 iCKB` at a rate that matches the issuance from the NervosDAO. This is because iCKB is gaining value. An easier way to understand this is to think of:

- CKB as inflationary
- iCKB as non-inflationary

Jordan Mack's comment on this method:
> That's a clever approach. Thinking of it as iCKB being the base and CKB being what is moving makes it much easier to understand.

The inflation rate of CKB is well defined by the [NervosDAO compensation rate](https://explorer.nervos.org/charts/nominal-apc) and only depends on:

- [The block concept in L1](https://docs.nervos.org/docs/basics/glossary/#block)
- [The epoch concept in L1](https://docs.nervos.org/docs/basics/glossary/#epoch)
- [The formula itself](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation)

Therefore, the iCKB/CKB exchange rate will always be precise as determined by the formula and the current block. The only risk to this deterministic peg would be a smart contract exploit to the deposit pool or minting contract. These kinds of attack vectors are greatly mitigated by external audits.

### Standard Deposit

As in real life bricks can be used to build houses of any size, in the same way seems natural to establish a reasonably small standard deposit size that can be used to construct deposits of any size.

In this way a few goals are achieved:

- Big deposits, split into standard deposits, increase the overall protocol liquidity.
- No size mismatch means anybody can use anybody else deposit to withdraw.
- The following form of DoS is prevented:

Let’s assume there is no requirement on deposit size, so as in NervosDAO users can choose the deposit size they prefer. Then an attacker who can borrow a big enough capital can simply attack by repeating the following two steps:

- Deposit CKB for iCKB in deposits as big as the entirety of his capital.
- Exchange iCKB for smaller CKB deposits.

This would greatly reduce the quality of the service for everyone, as the only remaining deposits would be as big or bigger than the attacker capital and since it’s impossible to withdraw partially from a NervosDAO deposit, this would greatly hamper the protocol fruition.

Back to the standard deposit definition, its size could be defined in CKB terms or in iCKB terms:

- Defining it in CKB terms means that as deposits are made in time, every deposit would have a different size due to the NervosDAO interests, so it's not working as intended.
- Defining it in iCKB terms means that at each block a standard deposit would have the same size both in CKB and iCKB. Of course as time passes, the deposit size would be fixed in iCKB-equivalent terms but gradually increasing in CKB terms.

Let's define the **standard deposit size** as `100000 iCKB`.

### iCKB/CKB Exchange Rate Calculation

Excluding deposit cell occupied capacity, per definition `100000 iCKB` are equal to `100000 CKB` staked in NervosDAO at the genesis block, let's calculate what this means.

From the last formula from [NervosDAO RFC Calculation section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation):
> Nervos DAO compensation can be calculated for any deposited cell. Assuming a Nervos DAO cell is deposited at block `m`, i.e. the `deposit cell` is included at block `m`. One initiates withdrawal and gets phase 1 `withdrawing cell` included at block `n`. The total capacity of the `deposit cell` is `c_t`, the occupied capacity for the `deposit cell` is `c_o`. [...] The maximum withdrawable capacity one can get from this Nervos DAO input cell is:
>
> `( c_t - c_o ) * AR_n / AR_m + c_o`

`AR_n` is defined in the [NervosDAO RFC Calculation section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation):

> CKB's block header has a particular field named `dao` containing auxiliary information for Nervos DAO's use. Specifically [...] `AR_i`: the current `accumulated rate` at block `i`. `AR_j / AR_i` reflects the CKByte amount if one deposit 1 CKB to Nervos DAO at block `i`, and withdraw at block `j`.

Let's fix a few constants:

- `c_o = 82 CKB` (occupied cell capacity of a standard deposit cell)
- `c_t = 100082 CKB` (total cell capacity equals the iCKB-equivalent deposit size plus its occupied capacity)
- `AR_0 = 10 ^ 16` (genesis accumulated rate)

So by depositing `100082` CKB at block `0`, **iCKB/CKB exchange ratio** at block `n` is defined as:

- `100000 iCKB := 100000 CKB * AR_n / 10 ^ 16` (excluding `82 CKB` of occupied cell capacity)

Conversely, by plugging block `m` as deposit block and block `0` as withdrawal block in NervosDAO's formula, it's possible to calculate how many iCKB are worth `100082` CKB deposited at block `m`:

- `100000 CKB * 10 ^ 16 / AR_m` (excluding `82 CKB` of occupied cell capacity)

This shows that the iCKB/CKB exchange rate only depends on a few constants and the accumulated rate, defined in the deposit's block header.

### Deposit

In NervosDAO, a deposit is a single transaction in which a CKB holder locks his CKB in exchange for a NervosDAO receipt of that **specific deposit**.

In the proposed protocol, a deposit is the process in which a CKB holder locks his CKB in exchange for iCKB tokens.

This process can't happen in a single transaction due to a Nervos L1 technical choice: as seen from the [previous section](#ickbckb-exchange-rate-calculation), to mint the iCKB equivalent for a deposit the protocol needs to access the current [`accumulated rate`](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation), which is defined in the deposit's block header, then again Nervos L1 is [off-chain deterministic](https://justjjy.com/Offchain-determinism), so [the current block header cannot be accessed while validating a transaction](https://github.com/nervosnetwork/ckb/blob/f93b498379173353b5804818b33227cc302ffd6a/script/src/syscalls/load_header.rs#L72).

Thus the protocol is forced to split a deposit in two phases:

1. In the first phase, the CKB holder locks his CKB in exchange for a protocol receipt of the **specific amount deposited**.
2. In the second phase, the deposit's header block is available, so the protocol receipt can be transformed into iCKB tokens.

### Deposit Phase 1

In this first phase the protocol:

- Transforms input CKB into NervosDAO deposit cells locked by iCKB Logic Script, in short a deposit.
- Awards to the user a protocol receipt of the deposits, effectively wrapping them.

Given the impossibility to access the header in this phase, it cannot exist a strict requirement on deposits iCKB-equivalent size. On the other hand, to achieve higher deposits fungibility and to prevent a certain form of DoS, the protocol needs to incentivize [standard deposits](#standard-deposit).

In particular, deposits bigger than the standard deposit size are actively disincentivized: the user will receive only 90% of the iCKB amount exceeding a standard deposit. The remaining 10% is offered as a discount to whoever is willing to withdraw from the oversized deposits.

On the other side, deposits smaller than the standard deposit size are intrinsically disincentivized by L1 dynamics. As deposits gets smaller they incur a bigger penalty in form of unaccounted occupied capacity, up to 10% of the deposit. This translates to a minimum deposit of `820 CKB`:

- `82 CKB` of fixed occupied capacity, used for state rent of the deposit cell with iCKB Logic Script.
- `738 CKB` of minimum unoccupied capacity, to be accounted by the receipt and later on converted in iCKB.

Taking in consideration these incentives, at least 90% of the deposit amount is always converted, of course the optimal strategy for a depositor is to split his CKB into standard deposits.

Since having a separate receipt per deposit cell would be capital inefficient, the protocol allows to account multiple deposit with a single receipt. An iCKB receipt accounts for a group of deposits with the same size, it just contains the single deposit unoccupied CKB capacity and the quantity of the accounted deposits. In a transaction output there can be many receipt cells and possibly more than one receipt for the same deposit size.

In a receipt cell data:

- The first `4 bytes`, currently zeroed, are reserved as unionID in case of future iCKB updates.
- The single deposit unoccupied CKB capacity is stored in `6 bytes`, this poses an hard-cap of `~2.8M CKB` per single deposit. This hard-cap prevents [a certain form of DoS](#standard-deposit), while still leaving enough slack for the standard deposit CKB size to grow for well over a hundred of years.
- The quantity of deposits is stored in `2 bytes`, this poses an hard-cap of `65535` deposits per receipt. On the other side for simplicity [a transaction containing NervosDAO script is currently limited to `64` output cells](https://github.com/nervosnetwork/ckb-system-scripts/blob/814eb82c44f560dbdad2be97eb85464062920237/c/dao.c#L38-L41) so that processing is simplified. This limitation may be relaxed later on in a future NervosDAO script update.

Summing up, in the first deposit phase, these rules must be followed:

- A **deposit** is defined as Nervos DAO deposit with an iCKB Logic Lock `{CodeHash: iCKB Logic Type ID, HashType: Type, Args: Empty}`.
- A group of same size deposits must be accounted by a receipt.
- A **receipt** is defined as a cell with iCKB Logic Type `{CodeHash: iCKB Logic Type ID, HashType: Type, Args: Empty}`, the first 12 bytes of cell data are reserved for:
  - `union_id` all zero, it's reserved for future updates to data encoding (4 bytes)
  - `receipt_count` keeps track of the quantity of deposits (2 bytes)
  - `receipt_amount` keeps track of the single deposit unoccupied capacity (6 bytes)
- No more than 64 output cells are allowed, due to the current NervosDAO restriction.
- CellDeps must contain iCKB Dep Group comprising of: iCKB Logic Script and Nervos DAO Script.

**Receipt data molecule encoding:**

```molecule
array Uint16 <byte; 2>;
array Uint48 <byte; 6>;

struct ReceiptDataV0 {
    receipt_count: Uint16,
    receipt_amount: Uint48,
}

union ReceiptData {
    ReceiptDataV0,
}
```

**Example of deposit phase 1:**

```yaml
CellDeps:
    - iCKB Dep Group cell
    - ...
Inputs:
    - ...
Outputs:
    - Nervos DAO deposit cell with iCKB Logic Lock:
        Data: 8 bytes filled with zeros
        Type: Nervos DAO
        Lock:
            CodeHash: iCKB Logic Type ID
            HashType: Type
            Args: Empty
    - ...
    - Receipt:
        Data: ReceiptData
            union_id: All zero, reserved for future updates (4 bytes)
            receipt_count: Quantity of deposits (2 bytes)
            receipt_amount: Single deposit unoccupied capacity (6 bytes)
        Type:
            CodeHash: iCKB Logic Type ID
            HashType: Type
            Args: Empty
        Lock: A lock that identifies the user
```

### Deposit Phase 2

A receipt accrues interests and it can be used to withdraw, but it's not liquid nor transferrable.

The second phase of the deposit transforms a receipt into its equivalent amount of iCKB tokens, which in turn is both liquid and transferrable. This conversion is now possible thanks to the header of the deposit block now available.

As seen in [iCKB/CKB Exchange Rate Calculation](#ickbckb-exchange-rate-calculation) for each receipt the equivalent amount of iCKB is well defined. The only difference being the incentivization: oversized receipts are subject to a 10% fee on the amount exceeding a standard deposit.

In the second deposit phase, these rules must be followed:

- The iCKB value of a receipt is calculated as

```pseudocode
iCKB_value(unoccupied_capacity, AR_m) {
    let s = unoccupied_capacity * AR_0 / AR_m;
    if s > standard_deposit_size {
        s = s - (s - standard_deposit_size) / 10
    }
    return s;
}

receipt_iCKB_value(receipt_count, receipt_amount, AR_m) {
    return receipt_count * iCKB_value(receipt_amount, AR_m);
}
```

- The total iCKB value of input tokens and input receipts must be bigger or equal to the total iCKB value of output tokens.
- iCKB xUDT flags are set to `0x80000000` to enable xUDT owner mode by input type.
- HeaderDeps must contain the transaction hash of the deposit block for each receipt.
- CellDeps must contain iCKB Dep Group comprising of: iCKB Logic Script, Standard xUDT Script and Nervos DAO Script.

**Example of deposit phase 2:**

```yaml
CellDeps:
    - iCKB Dep Group cell
    - ...
HeaderDeps: 
    - Deposit block
    - ...
Inputs:
    - Receipt:
        Data: ReceiptData
        Type:
            CodeHash: iCKB Logic Type ID
            HashType: Type
            Args: Empty
        Lock: A lock that identifies the user
    - ...
Outputs:
    - Token:
        Data: amount (16 bytes)
        Type:
            CodeHash: Standard xUDT Script
            HashType: Data1
            Args: [iCKB Logic Type ID, 0x80000000]
        Lock: A lock that identifies the user
```

### Withdrawal

In NervosDAO time is slotted in batches of 180 epochs depending on the initial deposit timing, a withdrawal is split in two steps:

1. In the first transaction the user requests the withdrawal.
2. In the second transaction the user withdraws the deposit plus interests. Must be after the end of the 180 epoch batch in which the first transaction happened.

As seen in [NervosDAO RFC Calculation section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation) the actual withdrawn CKB amount depends on the deposit block and on the withdrawal request block.

The proposed protocol instead proceed by un-wrapping iCKB tokens into NervosDAO withdrawal cells:

1. In the first transaction the user:
    - Requests the withdrawal from some protocol controlled deposits.
    - Respectfully to that quantity, burns a bigger or equal amount of iCKB tokens and/or receipts.
2. The second transaction is a Nervos DAO second withdrawal step.

As seen in [iCKB/CKB Exchange Rate Calculation](#ickbckb-exchange-rate-calculation) for each deposit and receipt the equivalent amount of iCKB is well defined. The only difference being the incentivization: requesting the withdrawal from an oversized deposit is incentivized by a 10% discount on the amount exceeding a standard deposit.

An additional NervosDAO constraint is that if deposit lock and withdrawal request lock differs, as in iCKB case, then NervosDAO requires the deposit lock and withdrawal request lock to have the same size. A non solution would be to use a lock with zero padded args in the deposit, then again different user locks would have different sizes, so it wouldn't solve the problem at hand. While iCKB Logic script is independent to the withdrawal request lock choice, this lock has some pretty restrictive constraints, as no information can be stored in its lock args nor in its cell data. For this reason has been developed [Owned Owner Script](#owned-owner-script).

Summing up, when withdrawing, these rules must be followed:

- The iCKB value of receipts and deposits is calculated as

```pseudocode
iCKB_value(unoccupied_capacity, AR_m) {
    let s = unoccupied_capacity * AR_0 / AR_m;
    if s > standard_deposit_size {
        s = s - (s - standard_deposit_size) / 10
    }
    return s;
}

receipt_iCKB_value(receipt_count, receipt_amount, AR_m) {
    return receipt_count * iCKB_value(receipt_amount, AR_m);
}

deposit_iCKB_value(capacity, occupied_capacity, AR_m) {
    return iCKB_value(capacity - occupied_capacity, AR_m);
}
```

- The total iCKB value of input tokens and input receipts must be bigger or equal to the total iCKB value of output tokens and input deposits, the deposits being withdrawn.
- Withdrawal Request lock must have zero args length and no information stored in the data cell.
- No more than 64 output cells are allowed, due to the current NervosDAO restriction.
- HeaderDeps must contain the transaction hash of the deposit block for each deposit being used to withdraw and each receipt cashed out.
- CellDeps must contain iCKB Dep Group comprising of: iCKB Logic Script, Standard xUDT Script and Nervos DAO Script.

**Example of withdrawal phase 1:**

```yaml
CellDeps:
    - iCKB Dep Group cell
    - ...
HeaderDeps: 
    - Deposit block
    - ...
Inputs:
    - Nervos DAO deposit cell with iCKB Logic Script:
        Data: 8 bytes filled with zeros
        Type: Nervos DAO
        Lock:
            CodeHash: iCKB Logic Type ID
            HashType: Type
            Args: Empty
    - Token:
        Data: amount (16 bytes)
        Type:
            CodeHash: Standard xUDT Script
            HashType: Data1
            Args: [iCKB Logic Type ID, 0x80000000]
        Lock: A lock that identifies the user
    - ...
Outputs:
    - Nervos DAO phase 1 withdrawal cell:
        Data: Deposit cell's including block number
        Type: Nervos DAO
        Lock: A lock that identifies the user
    - ...
```

## Ancillary Scripts

The iCKB protocol without additional scripts would be difficult to use, this section describes the L1 scripts that have been developed to address iCKB user needs.

These scripts offers solutions to specific lock needs, while supporting all users locks. The natural choice to prove user ownership would be to use the delegated signature validation pattern, then again given the incumbent OTX era this pattern has some specific OTX pitfalls. Let's assume that:

- The user lock is OTX signature based.
- The user unlocks some cells with signature in an OTX transaction, first OTX.

An attacker could do the following:

- Attacker includes all other cells locked with delegated user signature in a second OTX.
- Attacker packs this second OTX together with the first OTX in the same transaction.
- These second cells will unlock thanks to delegated signature validation.
- Attacker gains control of this second group of cells.

This is the reason why these scripts are instead designed around a similar but safer pattern:

- A single transaction mint both a controlled cell and controller cell.
- In a transaction there may be multiple controlled cell and controller cell.
- At minting time one of the cells reference the other one using the signed relative index distance between each other.
- The controlled cell satisfies specific user needs.
- The controlled cell uses the new script as lock
- The controlled cell may have an updating method using the new script logic.
- The controller cell has ownership of the controlled cell.
- The controller cell uses the new script as type.
- The controller cell has a lock that identifies the user.
- Melting both cells in the same transaction is the only way to consume both cells.

### Owned Owner Script

While iCKB Logic script is independent to the withdrawal request lock choice, this lock has some pretty restrictive constraints, as no information can be stored in its lock args nor in its cell data. For this reason has been developed Owned Owner Script. In a transaction there may be multiple owned cells and owner cells. This script lifecycle consists of two transactions: Mint and Melt.

**Owner data molecule encoding:**

```molecule
array Int32 <byte; 4>;

struct OwnerOwnedData {
    owned_distance : Int32,
}
```

#### Mint Owned Owner

In the Mint transaction, the output contains:

1. The owned cell with this script as lock.
2. The owner cell with this script as type and a lock that identifies the user. This cell memorizes in data the signed relative index distance between the owned cell and itself as a signed 32 bit integer encoded in little-endian.

Validation rule: `owned_index == owner_index + signed_distance`

**Example of withdrawal phase 1 using Owned Owner:**

```yaml
CellDeps:
    - iCKB Dep Group cell
    - Owned Owner data cell
    - ...
HeaderDeps: 
    - Deposit block
    - ...
Inputs:
    - Nervos DAO deposit cell with iCKB Logic Script:
        Data: 8 bytes filled with zeros
        Type: Nervos DAO
        Lock:
            CodeHash: iCKB Logic Type ID
            HashType: Type
            Args: Empty
    - Token:
        Data: amount (16 bytes)
        Type:
            CodeHash: Standard xUDT Script
            HashType: Data1
            Args: [iCKB Logic Type ID, 0x80000000]
        Lock: A lock that identifies the user
    - ...
Outputs:
    - Nervos DAO phase 1 withdrawal cell:
        Data: Deposit cell's including block number
        Type: Nervos DAO
        Lock: Owned role
            CodeHash: Owned Owner Hash
            HashType: Data1
            Args: Empty
    - Owner cell:
        Data: Signed distance from Owned cell (4 bytes)
        Type: Owner role
            CodeHash: Owned Owner Hash
            HashType: Data1
            Args: Empty
        Lock: A lock that identifies the user
    - ...
```

#### Melt Owned Owner

In the Melt transaction, the input contains both the owned cell and the owner cell. If one of the two is missing the script does't validate.

**Example of withdrawal phase 2 using Owned Owner:**

```yaml
CellDeps:
    - iCKB Dep Group cell
    - Owned Owner data cell
    - ...
HeaderDeps: 
    - Deposit block
    - ...
Inputs:
    - Nervos DAO phase 1 withdrawal cell:
        Data: Deposit cell's including block number
        Type: Nervos DAO
        Lock: Owned role
            CodeHash: Owned Owner Hash
            HashType: Data1
            Args: Empty
    - Owner cell:
        Data: Signed distance from Owned cell (4 bytes)
        Type: Owner role
            CodeHash: Owned Owner Hash
            HashType: Data1
            Args: Empty
        Lock: A lock that identifies the user
    - ...
Outputs:
    - ...
```

### Limit Order Script

Interacting directly with the iCKB protocol has some limitations:

- In transactions containing NervosDAO script, no more than 64 output cells are allowed.
- iCKB Logic discourages deposits bigger or smaller than the standard deposit size.
- There may be a mismatch between the amount the user wants to withdraw and the deposits available in the iCKB pool.
- NervosDAO doesn't allow to partially withdraw from a deposit.
- There is no easy way to merge multiple user intentions within a single deposit or withdrawal.

To abstract over NervosDAO and iCKB protocol limitations, it has been created a lock that implements limit order logic, abstracting user intentions, and that anyone can match partially or fulfill completely, similarly to an ACP lock. This lock aims to be compatible with all types that follows the sUDT convention of storing the amount in the first 16 bytes of cell data, at the moment sUDT and xUDT. In a transaction there may be multiple orders cells. This script lifecycle consists of three kind of transactions: Mint, Match and Melt.

**Limit Order Args molecule encoding:**

```molecule
array Hash <byte; 32>;
array Uint64 <byte; 8>;
array Uint32 <byte; 4>;
array Int32 <byte; 4>;

struct OutPoint {
    tx_hash : Hash,
    index : Uint32,
}

struct OrderInfo {
    is_udt_to _ckb: byte,
    ckb_multiplier: Uint64,
    udt_multiplier: Uint64,
    log_min_match: byte,
}

struct MintOrderArgs {
    orderInfo: OrderInfo,
    master_distance: Int32,
}

struct MatchOrderArgs {
    order_info: OrderInfo,
    master_outpoint: OutPoint,
}

struct FulfillOrderArgs {
    master_outpoint: OutPoint,
}

union OrderArgs {
    MintOrderArgs,
    MatchOrderArgs,
    FulfillOrderArgs,
}
```

#### Mint Limit Order

In the Mint transaction, the output contains:

1. The limit order cell itself with an UDT as type and this script as lock. This lock args memorizes:
    - `is_udt_to _ckb` expresses the order direction.
    - `ckb_multiplier` and `udt_multiplier` expresses the order exchange ratio.
    - `log_min_match` expresses the logarithm in base 2 of the minimum partial match of the wanted asset.
    - `master_distance` expresses the signed relative index distance between this cell and the master cell.

2. The master cell with this script as type and a lock that identifies the user. This cell controls the limit order cell.

Validation rules:

- `orders_index + master_distance == master_index`
- Order cell data length must be at least 16 bytes.

**Example of Limit Order mint:**

```yaml
CellDeps:
    - Limit Order data cell
    - ...
Inputs:
    - ...
Outputs:
    - Limit Order cell:
        Data: [amount (16 bytes), ...]
        Type: UDT
        Lock: Limit Order role
            CodeHash: Limit Order Type ID
            HashType: Type
            Args: MintOrderArgs variant of OrderArgs
    - Master cell:
        Data: ...
        Lock: Master role
            CodeHash: Limit Order Type ID
            HashType: Type
            Args: Empty
        Lock: A lock that identifies the user
```

#### Match and Fulfill Limit Order

In Match and Fulfill transactions the allowed input limit OrderArgs variants are MintOrderArgs and MatchOrdersArgs. While the allowed output limit OrderArgs variants are MatchOrdersArgs and FulfillOrdersArgs.

Validation rules:

- `in_ckb * ckb_multiplier + in_udt * udt_multiplier <= out_ckb * ckb_multiplier + out_udt * udt_multiplier`
- `in_wanted_asset + 2^log_min_match <= out_wanted_asset`
- excluding the first 16 bytes that encode the amount, the rest of data bytes must be equal between input and output order.
- FulfillOrdersArgs is not allowed as input order.
- MintOrderArgs is not allowed as output order.

**Example of Limit Order Match:**

```yaml
CellDeps:
    - Limit Order data cell
    - ...
Inputs:
    - Limit Order cell:
        Data: [amount (16 bytes), ...]
        Type: UDT
        Lock: Limit Order role
            CodeHash: Limit Order Type ID
            HashType: Type
            Args: MintOrderArgs variant of OrderArgs
Outputs:
    - Limit Order cell:
        Data: [amount (16 bytes), ...]
        Type: UDT
        Lock: Limit Order role
            CodeHash: Limit Order Type ID
            HashType: Type
            Args: MatchOrderArgs variant of OrderArgs
```

#### Melt Limit Order

In the Melt transaction, the input contains both the order cell and the master cell. If one of the two is missing the script does't validate. Any limit OrderArgs variant is allowed as input.

**Example of Limit Order melt:**

```yaml
CellDeps:
    - Limit Order data cell
    - ...
Inputs:
    - Limit Order cell:
        Data: [amount (16 bytes), ...]
        Type: UDT
        Lock: Limit Order role
            CodeHash: Limit Order Type ID
            HashType: Type
            Args: FulfillOrdersArgs variant of OrderArgs
    - Master cell:
        Data: ...
        Lock: Master role
            CodeHash: Limit Order Type ID
            HashType: Type
            Args: Empty
        Lock: A lock that identifies the user
Outputs:
    - ...
```

## Future

At the inception of iCKB, the following were the intended possible use cases:

- CKB-based Initial Stake Pool Offerings.
- The official Nervos DAO community voting mechanism.
- Godwoken switch from pCKB to iCKB, protecting users from CKB issuance.
- A multitude more L1 & L2 applications!

A few things changed since the inception, these are the updated use cases:

- ISPO is a well thought model, but currently Nervos has already a Community Fund. So the need has already been addressed. Developing an ISPO now may provide different paths & rules for accessing funding.
- Switching from pCKB to iCKB would give free interests to any CKB holder on Godwoken, then again feels like Godwoken never really found its use case.
- In the short term the most impactful iCKB result will be that more users will feel comfortable in stacking into NervosDAO by using iCKB. So more and more CKB will be locked into NervosDAO, which is an achievement in itself.
- In the long term more and more decentralized finance protocols will integrate iCKB. So users will be able to enjoy the interests payed out by both these protocols and NervosDAO.

## Useful Resources

- [Hashing it Out introduction to iCKB](https://www.youtube.com/watch?v=CcFFuenup38&t=781s)
- [iCKB journey into CoBuild on Nervos Talk](https://talk.nervos.org/t/ickb-journey-into-cobuild/7918)
- [\[AMA\] Everything iCKB on Nervos Talk](https://talk.nervos.org/t/ama-everything-ickb/7871)
- [Thread for iCKB development on Discord](https://discord.com/channels/657799690070523914/980237827122032730)
- [Reference implementation](https://github.com/ickb/v1-core)
- [Reference proposal](https://github.com/ickb/proposal)
- [Initial proposal on Nervos Talk](https://talk.nervos.org/t/looking-for-feedback-ickb-a-tokenization-of-nervosdao-deposits/6772)

## License

This proposal is licensed under the terms of the [Creative Commons Attribution Share Alike 4.0 International License](https://github.com/ickb/proposal/blob/master/LICENSE.txt).
