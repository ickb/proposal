# Proposal: Project CKB++

## Problem

### NervosDAO Illiquidity

The NervosDAO is possibly the most important smart-contract of Nervos Layer 1 (L1). A CKB holder can lock his CKB in the NervosDAO in exchange for a receipt of that specific deposit. Every 180 epochs (~30 days) the holder has the option of exchanging his receipt to unlock his initial deposit plus accrued interest. This creates an illiquidity for depositor while the CKB is locked.

### Untapped Potential

There exists untapped potential in the Nervos ecosystem for a protocol that can liquify NervosDAO accrued interest and bridge it from L1 to L2. This protocol could enable CKB-based [Initial Stake Pool Offerings](https://www.meld.com/ispo) (ISPO), where users can lock CKB to support new early stage projects without losing their original CKB deposit. The protocol could also be used to enable a community voting mechanism with funds locked in the NervosDAO, as well as a multitude more L1 & L2 applications!

### DCKB (Unmaintained)

In the past there has been an effort to tackle this challenge by [NexisDAO with dCKB](https://docs.nexisdao.com/nexisdao/mint-dckb). Their approach is to tokenize the holder receipt, which in turn becomes tradeable and so the holder keeps being liquid. The issue with their approach is that only the original owner can unlock the deposit. Currently dCKB does not appear to be actively maintained.

## Solution

### Enter CKB++

CKB++ is the provisional name for a [sUDT token](https://talk.nervos.org/t/rfc-simple-udt-draft-spec/4333) that represents deposits in the protocol. As with dCKB, CKB++'s approach is to tokenize NervosDAO receipts, but with a twist: the protocol owns all the CKB deposits and maintains a pool of them. This means that all the deposits and withdrawals are shared, so anyone can use anyone else's deposit to exit once it's mature.

This protocol aims to solve two problems with NervosDAO:

- CKB locked in the NervosDAO remains liquid and it can truly be used as a normal currency.
- CKB++ can be converted back to CKB quickly at any time without having to wait for maturity.

### Water Mill Analogy

As a [water mill](https://tenor.com/view/water-wheel-mill-gif-19806697) has many distinct buckets, each at different wheel positions, in which the water is:

- Collected
- Maintained
- Released

In the same way, the protocol can have many distinct liquidity buckets, each of them constantly moving at different stages of maturity:

- **Collected**: Users deposit CKB into liquidity buckets and receive CKB++.
- **Maintained**: Liquidity buckets accrue interest in the NervosDAO.
- **Released**: If a user wants to exchange CKB++ for CKB, they can use any liquidity bucket that is at maturity.

### Feedback

Jordan Mack's comments on Nervos L1 & CKB++:
> In a more abstract sense, this doesn't violate any of intentions of the platform. The CKB that is staked is still out of circulation. CKB++ does not grant the holder the ability to store data on the blockchain. In the most pure sense, CKB++ is enabling the functionality that dCKB was trying to achieve. It better solves the problem because anyone can unlock the original CKB from the NervosDAO using CKB++ instead of requiring the original owner to unlock it as with dCKB.

## Team

### Phroi

I'm the the ideas baker and solidity developer behind Opthy, 3° among peers at August 2021 Hackathon! [Phroi](https://phroi.com) as pseudonym exists since that Hackathon, here you can find the code I wrote in that occasion: [core](https://github.com/opthydefi/opthy-v0-core), [tests](https://github.com/opthydefi/opthy-v0-tests) and [ui](https://github.com/opthydefi/opthy-v0-ui-web). Later on I continued working with Hexmate on [Opthy](https://github.com/opthydefi/opthy-v1-core) until May 2022, when I left for becoming a solo Indie Hacker. Since then I'm working on a few projects under different pseudonyms.

### Discovering CKB++

During February 2022, while [testing the ground for a NervosDAO based ISPO](https://discord.com/channels/657799690070523914/657799690552606745/943306112889933864), I discovered the untapped need for a token that liquefies and bridges interests from L1 to L2 and so I started researching its feasibility.

## Core Layer

### Core Layer is On-Chain, Trust-Less & Decentralized

Core Layer defines a solid way to exchange between CKB and CKB++ in fixed blocks. The design aim is to make CKB++ as simple, robust and neutral as possible.

This part of the protocol lives completely on Nervos Layer 1. Once deployed it is independent and not upgradable. It wraps NervosDAO transactions by transforming them into CKB++ and does not require a receipt. It tracks all deposits by a fixed amount of CKB++, so CKB capacity for any deposit is determined by the current exchange rate of CKB for CKB++.

### CKB / CKB++ Exchange Rate

The CKB to CKB++ exchange rate is determined by epoch. If we could go back in time to epoch 0, then 1 CKB would be equal to 1 CKB++. As time passes 1 CKB is slowly worth less than 1 CKB++ at a rate that matches the issuance from the NervosDAO. This is because CKB++ is gaining value. An easier way to understand this is to think of:

- CKB as inflationary
- CKB++ as non-inflationary

Jordan Mack's comment on this method:
> That's a clever approach. Thinking of it as CKB++ being the base and CKB being what is moving makes it much easier to understand.

The inflation rate of CKB is well defined by the [NervosDAO compensation rate](https://explorer.nervos.org/charts/nominal-apc) and only depends on:

- [The block concept in L1](https://docs.nervos.org/docs/basics/glossary/#block)
- [The epoch concept in L1](https://docs.nervos.org/docs/basics/glossary/#epoch)
- [The formula itself](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation)

Therefore, the CKB/CKB++ exchange rate will always be precise as determined by the formula and the current epoch/block. The only risk to this deterministic peg would be a smart contract exploit to the deposit pool or minting contract. These kinds of attack vectors are greatly mitigated by external audits.

### Fixed CKB++-Equivalent Deposit Size

Let's **assume** we don't implement any requirement on deposit size, so as in NervosDAO users can choose the deposit size they prefer. Then an attacker who can borrow a big enough capital can simply:

- Exchange CKB++ for smaller CKB deposits.
- Deposit CKB for CKB++ in deposits as big as the entirety of his capital.

This would greatly reduce the quality of the service for everyone, as the only remaining deposits would be as big or bigger than the attacker capital and since it's impossible to withdraw partially from a NervosDAO deposit, this would greatly hamper the protocol fruition.

Let's now instead **assume** we require deposits to be capped at certain size. Then as before an attacker who can borrow a capital as big as the maximum deposit size can simply:

- Exchange CKB++ for smaller CKB deposits.
- Deposit CKB for CKB++ in deposits as big as the maximum deposit size.

This would greatly reduce the quality of the service for users trying to withdraw smaller deposits, as the only remaining deposits would be as big as the maximum deposit size and since it's impossible to withdraw partially from a NervosDAO deposit, this would hamper the protocol fruition for a whole category of users.

A good countermeasure is to fix a reasonably small standard deposit size. As in real life bricks can be used to build houses of any size, in the same way:

- Deposits too big should be split into standard deposits and possibly even spread over longer periods.
- Deposits too small are better served by secondary markets.

This deposit standard size could be defined in CKB terms or in CBK++ terms:

- A fixed CKB means that as deposit are made in time, every deposit would have a different size due to the NervosDAO interests, so it's not working as intended.
- A fixed CKB++-equivalent deposit size means that at every block all the deposits would have the same size both in CKB and CKB++. Of course as time passes, the deposit size would be fixed CKB++-equivalent terms but gradually increasing in CKB terms.

In this way a few goals are achieved:

- Big deposits now increase the overall protocol liquidity.
- No size mismatch means anybody can use anybody else deposit freely to withdraw.
- A fixed CKB++-equivalent deposit size simplifies code, so it minimizes the hacks attack surface.

### Deposits from Core Layer Perspective

In NervosDAO a CKB holder can lock his CKB in exchange for a receipt of that specific deposit, while in our case the protocol proceed by wrapping NervosDAO deposit transaction into CKB++. The protocol in one transaction:

- Requires a fixed CKB++-equivalent size deposit.
- Takes control of the deposit.
- Stakes it in NervosDAO.
- Adds the receipt to its pool of receipts.
- Mints to the depositor a fixed amount of CKB++.

Naturally the user can choose to deposit an integer multiple of the fixed deposit, the protocol takes care of splitting it in many fixed NervosDAO deposits and mints the correct CKB++ amount.

### Withdrawals from Core Layer Perspective

Withdrawals are a bit more complicated in NervosDAO, time is slotted in batches of 180 epochs depending on the initial deposit timing, so a withdrawal goes like this:

- With the first transaction the user request the withdrawal.
- With the second transaction the user withdraw the deposit plus interests. Must be after the end of the 180 epoch batch in which the first transaction happened.

As seen with [calculations](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation) the actual withdrawn CKB amount depends on the timing of the request of withdrawal transaction in respect to the epoch batch.

While in our case the protocol proceed by un-wrapping CKB++ transactions into base NervosDAO transactions:

- The CKB++ holder **freely** chooses from the pool a deposit to withdraw from. All deposits are of a fixed CKB++-equivalent size, the only differentiating factor between them is the recurring maturity date.
- With the first transaction the user sends to the protocol the fixed amount of CKB++ and chooses the specific deposit to withdraw from, while the protocol in turn assigns to the user that specific deposit and burns the received CKB++.
- With the second transaction the user withdraws the equivalent CKB amount. Same constraints as with the second NervosDAO transaction.

## Future Plans

Currently the protocol logic lives completely on Layer 1 and it is comprised of only of the Core Layer. As CKB++ research progresses, CKB++ will naturally expand to Layer 2:

- **Periphery Layer**: it makes exchanging between CKB and CKB++ handy even for Layer 2 users.
- **CKB++ based ISPO**: a launchpad platform for staking CKB++ to fund projects and charities.

## Status

CKB++ is an on-going research project:

- this proposal is an organic overview of established concepts.
- there is a [public discord thread](https://discord.com/channels/657799690070523914/980237827122032730) for the discussion of cutting edge ideas.

## License

This proposal is licensed under the terms of the [Creative Commons Attribution Share Alike 4.0 International License](LICENSE.txt).
