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

iCKB is the name for a [SUDT token](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0025-simple-udt/0025-simple-udt.md) that represents deposits in the protocol. As with dCKB, iCKB's approach is to tokenize NervosDAO receipts, but with a twist: the protocol owns all the CKB deposits and maintains a pool of them. This means that all the deposits and withdrawals are shared, so anyone can use anyone else's deposit to exit once it's mature.

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

### On-Chain, Trust-Less & Decentralized

This protocol defines a solid way to exchange between CKB and iCKB. The design aim is to make iCKB as simple and robust as possible, while keeping it sustainable in the long term.

This protocol lives completely on Nervos Layer 1. Once deployed no entity have control over it, so it's not upgradable. It works by wrapping NervosDAO transactions: a deposit is first tracked by a receipt and later on it's converted in its equivalent amount of iCKB, which is determined by the exchange rate of CKB for iCKB at the time of the deposit.

### CKB / iCKB Exchange Rate

The iCKB mechanism for wrapping interest is similar to [Compound's cTokens](https://compound.finance/docs/ctokens). The CKB to iCKB exchange rate is determined by block number. If we could go back in time to block 0, then 1 CKB would be equal to 1 iCKB. As time passes 1 CKB is slowly worth less than 1 iCKB at a rate that matches the issuance from the NervosDAO. This is because iCKB is gaining value. An easier way to understand this is to think of:

- CKB as inflationary
- iCKB as non-inflationary

Jordan Mack's comment on this method:
> That's a clever approach. Thinking of it as iCKB being the base and CKB being what is moving makes it much easier to understand.

The inflation rate of CKB is well defined by the [NervosDAO compensation rate](https://explorer.nervos.org/charts/nominal-apc) and only depends on:

- [The block concept in L1](https://docs.nervos.org/docs/basics/glossary/#block)
- [The epoch concept in L1](https://docs.nervos.org/docs/basics/glossary/#epoch)
- [The formula itself](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation)

Therefore, the CKB/iCKB exchange rate will always be precise as determined by the formula and the current block. The only risk to this deterministic peg would be a smart contract exploit to the deposit pool or minting contract. These kinds of attack vectors are greatly mitigated by external audits.

## 👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇 WORK IN PROGRESS 👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇

### Fixed iCKB-Equivalent Deposit Size

As in real life bricks can be used to build houses of any size, in the same way seems natural to fix a reasonably small standard deposit size, so that:

- Deposits too big are split into standard deposits and possibly even spread over longer periods.
- Deposits too small are better served by secondary markets.

This deposit standard size could be defined in CKB terms or in iCBK terms:

- A fixed CKB means that as deposit are made in time, every deposit would have a different size due to the NervosDAO interests, so it's not working as intended.
- A fixed iCKB-equivalent deposit size means that at every block all the deposits would have the same size both in CKB and iCKB. Of course as time passes, the deposit size would be fixed iCKB-equivalent terms but gradually increasing in CKB terms.

In this way a few goals are achieved:

- Big deposits now increase the overall protocol liquidity.
- No size mismatch means anybody can use anybody else deposit freely to withdraw.
- A fixed iCKB-equivalent deposit size simplifies code, so it minimizes the hacks attack surface.

### Practical Exchange Rate CKB / iCKB Calculation

Let's define 10000 iCKB as the standard deposit size. This is a made up number, but possibly not too far from the real one.

As per definition 10000 iCKB is equal to 10000 CKB staked in NervosDAO at block 0, let's calculate what this means.

From the last formula from [NervosDAO RFC Calculation section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation):
> Nervos DAO compensation can be calculated for any deposited cell. Assuming a Nervos DAO cell is deposited at block `m`, i.e. the `deposit cell` is included at block `m`. One initiates withdrawal and gets phase 1 `withdrawing cell` included at block `n`. The total capacity of the `deposit cell` is `c_t`, the occupied capacity for the `deposit cell` is `c_o`. [...] The maximum withdrawable capacity one can get from this Nervos DAO input cell is:
>
> `( c_t - c_o ) * AR_n / AR_m + c_o`

`AR_n` is defined in the [NervosDAO RFC Calculation section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation):

> CKB's block header has a particular field named `dao` containing auxiliary information for Nervos DAO's use. Specifically [...] `AR_i`: the current `accumulated rate` at block `i`. `AR_j / AR_i` reflects the CKByte amount if one deposit 1 CKB to Nervos DAO at block `i`, and withdraw at block `j`.

Let's fix a few constants:

- `c_t = 10000 CKB` (deposit cell capacity, the standard deposit size)
- `c_o = 110 CKB` (occupied deposit cell capacity)
- `m = 0` (deposit block is block 0)
- `AR_m = AR_0 = 10 ^ 16` (block 0 accumulated rate)

So at block `n`:
`10000 iCKB  = 9890 CKB * AR_n / 10 ^ 16 + 110 CKB`

This shows that the iCKB / CKB exchange rate only depends on a few constants and `AR_n`, the block `n` accumulated rate.

### Deposits

In NervosDAO a CKB holder can lock his CKB in exchange for a receipt of that specific deposit, while in our case the protocol proceed by wrapping NervosDAO deposit transaction into iCKB. The protocol in one transaction:

- Requires a fixed iCKB-equivalent size deposit.
- Takes control of the deposit.
- Stakes it in NervosDAO.
- Adds the receipt to its pool of receipts.
- Mints to the depositor a fixed amount of iCKB.

Naturally the user can choose to deposit an integer multiple of the standard deposit, the protocol takes care of splitting it in many fixed NervosDAO deposits and mints the correct iCKB amount.

### Withdrawals

Withdrawals are a bit more complicated in NervosDAO, time is slotted in batches of 180 epochs depending on the initial deposit timing, so a withdrawal goes like this:

- With the first transaction the user requests the withdrawal.
- With the second transaction the user withdraws the deposit plus interests. Must be after the end of the 180 epoch batch in which the first transaction happened.

As seen in [NervosDAO RFC Calculation section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation) the actual withdrawn CKB amount depends on the timing of the request of withdrawal transaction in respect to the epoch batch.

While in our case the protocol proceed by un-wrapping iCKB transactions into base NervosDAO transactions:

- The iCKB holder **freely** chooses from the pool one or more deposits to withdraw from. All deposits are of a fixed iCKB-equivalent size, the only differentiating factor between them is the recurring maturity date.
- With the first transaction the user sends to the protocol the equivalent amount of iCKB and chooses the specific deposits to withdraw from, while the protocol in turn requests to NervosDAO the withdrawal of these specific deposits, assigns them to the user and burns the received iCKB.
- With the second transaction the user withdraws the equivalent CKB amount. Same constraints as with the second NervosDAO transaction.

## Status

While iCKB is stable, it is still an on-going research project:

- There exists a [reference proposal](https://github.com/ickb/proposal).
- There exists a [public discord thread](https://discord.com/channels/657799690070523914/980237827122032730) for the discussion of iCKB development.

## License

This proposal is licensed under the terms of the [Creative Commons Attribution Share Alike 4.0 International License](https://github.com/ickb/proposal/blob/master/LICENSE.txt).
