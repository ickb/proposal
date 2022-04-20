# Proposal: Project CKB++

## Problem

### NervosDAO Illiquidity

NervosDAO is possibly the most important smart-contract of Nervos Layer 1 (L1 for short). Briefly a CKB holder can lock his CKB in exchange for a receipt of that specific deposit. Every 180 epochs (~30 days) the holder can exchange back his receipt to unlock his deposit plus the accrued Interests. This creates an illiquidity for CKB depositor.

### Untapped Potential

In the Nervos ecosystem exists the untapped potential for a Protocol that liquefies and bridges NervosDAO interests from L1 to L2. This Protocol could enable NervosDAO-based ISPOs, the official Nervos DAO community voting mechanism and a multitude more L1 & L2 applications!

In particular this Protocol could enable the future Hexmate development of an ISPO, which I highly support and anticipate!

## Solution Bird View

### DCKB (Unmaintained)

In the past there has been an effort to tackle this challenge by NexisDAO with dCKB. Their approach is to tokenize the holder receipt, which in turn becomes tradeable and so the holder keeps being liquid. The issue with their approach is that only the original owner can unlock the deposit. Currently dCKB seems unmaintained.

### Enter CKB++

CKB++ is provisional name for a sUDT token that represents deposits in the Protocol. As with dCKB, CKB++'s approach is to tokenize NervosDAO receipts, but with a twist: the Protocol owns all the CKB deposits and maintains a pool of them. This means that all the deposits and withdrawals are shared, so anyone can use anyone else's deposit to exit once it's mature.

### Water Mill Analogy

A water mill has many distinct buckets, each at different wheel positions, in which the water is:

- collected;
- kept and transported;
- released;
- ...

In the same way, the Protocol can have many distinct liquidity buckets, each at different time-phases, in which CKB are:

- collected: users deposit CKB and receive CKB++;
- kept to accrue interests;
- released: if a user wants to exchange CKB++ for CKB and there is a deposit at maturity, then he can release the locked CKB;
- ...

### Feedback

Jordan Mack words on Nervos L1 & CKB++:
> In a more abstract sense, this doesn't violate any of intentions of the platform. The CKB that is staked is still out of circulation. CKB++ does not grant the holder the ability to store data on the blockchain. In the most pure sense, CKB++ is enabling the functionality that dCKB was trying to achieve. It better solves the problem because anyone can unlock the original CKB from the NervosDAO using CKB++ instead of requiring the original owner to unlock it as with dCKB.

## Team

### Rise of Phroi

I'm the the ideas baker and solidity developer behind Opthy, 3° among peers at August 2021 Hackathon! [Phroi](https://phroi.com) as pseudonym exists since that Hackathon, here you can find the code I wrote in that occasion: [core](https://github.com/opthydefi/opthy-v0-core), [tests](https://github.com/opthydefi/opthy-v0-tests) and [ui](https://github.com/opthydefi/opthy-v0-ui-web). Later on I continued working with Hexmate on [Opthy](https://github.com/opthydefi/opthy-v1-core), receiving funding from Nervos Foundation.

### Strengths

- Innovative!
- Self-motivated!
- Up for the challenge!

### Weaknesses

- My soft skills!
- My broken English!
- Too humble, or so they say!

### Discovering CKB++

Fast forward, two months ago while [testing the ground for Hexmate's ISPO](https://discord.com/channels/657799690070523914/657799690552606745/943306112889933864), I discovered the untapped need for a token that liquefies and bridges interests from L1 to L2 and so I started researching its feasibility.

### Departing from Hexmate to Focus on CKB++

In the meantime I embarked on a journey of all-round self-discovery and after a while I realized that I identify as a solo Indie Hacker, a long time dream. Coincidentally CKB++ is of the right size to be incrementally worked on as a solo Indie Hacker!

While nobody is able to work in total isolation, with the right feedback, guidance and support from Nervos Community & Foundation I can transform CKB++ into a reality in a less than year.

## Technical Challenges

### NervosDAO Gotchas

Any technically viable solution needs to consider [NervosDAO RFCs](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md). A particular detail that rules out most of the competing implementations for CKB++ is well documented in its [Gotchas section](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#gotchas):

>CKB has a maturity constraint on referencing header: a block header can only be referenced in a cell that is committed at least 4 epochs after the referenced block header. This constraint limits Nervos DAO withdrawal in the following ways:
>
> - Phase 1 withdrawal transaction can only be committed 4 epochs after the fund is originally deposited.
> - Phase 2 withdrawal transaction can only be committed 4 epochs after phase 1 withdrawal transaction is committed.

## Core Layer

### Core Layer is On-Chain, Trust-Less & Decentralized

This part of the Protocol lives completely on-chain, once deployed it's independent from any entity, so it's not upgradable. It fixes a CKB++-equivalent size deposit, so CKB capacity for any deposit is determined in fixed CKB++-equivalent terms. It wraps NervosDAO transactions, transforming them appropriately into CKB++.

### Deposits

In NervosDAO a CKB holder can lock his CKB in exchange for a receipt of that specific deposit, while in our case the Protocol proceed by wrapping NervosDAO deposit transaction into CKB++. The Protocol in one transaction:

- requires a fixed CKB++-equivalent size deposit;
- takes control of the deposit;
- stakes it in NervosDAO;
- adds the receipt to its pool of receipts;
- mints to the depositor a fixed amount of CKB++;

### CKB / CKB++ Rate

Let's assume we fix an EPOCH-0 so a block when 1 CKB = 1 CKB++, then as time moves on 1 CKB < 1 CKB++ = 1 CKB staked at EPOCH-0, so we can think:

- CKB as inflationary
- CKB++ as non inflationary

Jordan Mack comment on this particular:
> That's a clever approach. Thinking of it as CKB++ being the base and CKB being what is moving makes it much easier to understand.

Now this inflation rate is well defined by the [NervosDAO compensation rate](https://explorer.nervos.org/charts/nominal-apc) and only depends on:

- [the block concept in L1](https://docs.nervos.org/docs/basics/glossary/#block)
- [the epoch concept in L1](https://docs.nervos.org/docs/basics/glossary/#epoch)
- [the formula itself](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation)

So the CKB / CKB++ Rate cannot be broken except in the case of an attacker being able to compromise the CKB++ minter or the pool of CKB deposits. This kind of attack vectors are greatly mitigated by external audits.

### Withdrawals

Withdrawals are a bit more complicated in NervosDAO, time is slotted in batches of 180 epochs depending on the initial deposit timing, so a withdrawal goes like this:

- with the first transaction the user request the withdrawal. Must be 4 epochs after deposit;
- with the second transaction the user withdraw the deposit plus interests. Must be 4 epochs after the first transaction and after the end of the 180 epoch batch in which the first transaction happened.

As seen with [calculations](https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0023-dao-deposit-withdraw/0023-dao-deposit-withdraw.md#calculation) the actual withdrawn CKB amount depends on the timing of the request of withdrawal transaction in respect to the epoch batch.

While in our case the Protocol proceed by un-wrapping CKB++ transactions into base NervosDAO transactions:

- the CKB++ holder **freely** chooses from the pool a deposit to withdraw from. All deposits are of a fixed CKB++-equivalent size, the only differentiating factor between them is the recurring maturity date;
- with the first transaction the user sends to the protocol the fixed amount of CKB++ and chooses the specific deposit to withdraw from, while the Protocol in turn assigns to the user that specific deposit and burns the received CKB++;
- with the second transaction the user withdraws the equivalent CKB amount. Same constraints as with the second NervosDAO transaction.

## Incentivization Layer

### Incentivization Layer is On-Chain, Trust-Less & Decentralized

As for the Core, the Incentivization Layer of the Protocol lives completely on-chain, once deployed it's independent from any entity, so it's not upgradable. This Layer exist as the Protocol needs to incentivize a well time distributed deposit pool and needs to dis-incentivize actions that leads to the opposite. Since direct access to the Core Layer is disabled this layer cannot be bypassed.

### Adding Incentives and Disincentives

Any improvement aimed to incentivize a well time distributed deposit pool involves fees. In particular this entails a central entity with automated logic, so a L1 script, which with-in every user transactions:

- sets disincentives in form of CKB++ fees;
- accumulates them;
- distributes incentives in form of CKB++ benefits;

A benefit of asking for CKB++ fees is that leads to a slight lock-in as a user needs to fetch from secondary markets these additional CKB++ to pay for the fees.

Another approach would be to have strict rules, that for example clearly states when a deposit/withdrawal can happen or not, but usually it's easy for a determined attacker to abuse stricter rules against everyone else.

In the following sections I'll refer to the **epoch population** concept, this is a short hand for quantity of deposits at maturity in a certain epoch in the Core Layer deposit pool.

### Withdrawing

Withdrawing means consuming a shared resource, so any other user will not be able to use it.

Let's **assume** we don't implement the Incentivization Layer, so users have direct access to the Core Layer. Then a attacker with minimal expenditure of capital can simply:

- exchange CKB++ for CKB deposits near maturity;
- deposit CKB for CKB++, postponing the maturity;
- ...

An attack aimed at postponing indefinitely the deposit maturity would greatly reduce the quality of the service for everyone, hampering its fruition.

A good countermeasure is to add withdrawal fees that depends on how much is populated the epoch:

- scarcely populated: high withdrawal fees, up to 180 epochs worth of CKB interests;
- average populated: from minimal to no withdrawal fees;
- over-populated: from no fees to incentives;

### Depositing

Depositing means adding a shared resource, so any other user will be able to use it, but there is a catch.

Let's **assume** we don't implement the Incentivization Layer, so users have direct access to the Core Layer. Then a attacker with minimal expenditure of capital can simply:

- exchange CKB++ for CKB deposits in sparsely populated epochs;
- deposit CKB for CKB++ in already densely populated epochs;
- ...

This would greatly reduce the quality of the service for everyone, as the Protocol ability to exchange CKB++ into CKB would be halted outside the overpopulated epochs, hampering its fruition.

So the catch is that the utility of a deposit depends on how much already populated is that epoch.

A good countermeasure is to add deposit fees that depends on how much is populated the epoch:

- over-populated: high deposit fees, up to 180 epochs worth of CKB interests;
- average populated: from minimal to no deposit fees;
- scarcely populated: from no fees to incentives;

## 👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇 WORK IN PROGRESS 👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇⚠️👇

## Periphery Layer & Deposit Size

### Deposit Size Choice

- adaptive bad idea, can be manipulated adversarially
- fixed CKB++-equivalent deposit size set a common standard and simplify code, so minimize possibility of hacks
- block of such a size to minimize over-head, but not too big that makes hard for everyone to freely get in and out
- variable size deposits can be adapted to use a fix deposit size
- deposits too Big cannot be accepted as such, but they need to be split into standard deposits, better if spread over a longer period
- deposits too Small are better served by secondary markets, such as AMMs
- if the choice of the block size 10 years from now becomes evidently wrong, then it's always possible to hard fork the protocol into a V2.0

### Periphery Layer

- CKB/CKB++ limit orders platform on L1 + arbitrage bot
- CKB/CKB++ limit orders & Uniswap alike AMM on L2  + arbitrage bot
- deposits too BIG can (but don't need to) use limit orders. An arbitrage bot takes care of exchange them gradually
- deposits too SMALL are better served by AMM. An arbitrage bot takes care of keeping the price balanced

### Path to Sustainability

- Bot & limit orders fees leads to a future where I can maintain and upgrade the services offered
- Fees can be managed by DAO or directly by me
- DAO could happen [similarly to this](https://genesysgo.medium.com/the-comprehensive-guide-to-genesysgo-and-the-shdw-ido-278b90d3186c) (thanks Sebastien for nominating it months ago), but would carry a big development overhead;
- Directly managed by me: it's still decentralized as anyone can write a bot to arbitrage between the core and limit orders & AMM contracts. Alternatively a third party can design a totally independent system to interface users and the core protocol, as the core is totally independent from me.

## Road-map & Incentives

I'll go from learning L1 scripting to creating a fully functional L1 & L2 Protocol. So while I'm pretty independent, I'll need dedicated support from the Nervos, monday to friday, no more than 48 hours delays in responses.

I do not plan on selling any CKB that I ask as incentives because the the arbitrage bots will need CKB capital to indeed arbitrage.

### First Month: Proof of Concept

I'll create a Proof of Concept L1 script, showing the basic functionalities of the core layer from command line in Aggron Testnet. Possibly not safe to use in production, but with a clear path to a safe resolution.

This is a risky period, there might exists some technical blocks long or impossible to work around. While I love everything about CKB++, I do need to ask a starting 1000$ incentive in CKB from Nervos foundation, so we are both taking a small risk.

### First Four Months: Core & Incentivization Layers

I'll create the definitive L1 scripts for Core & Incentivization Layers. I'll need to simulate the Protocol incentives. I'll set-up an Aggron Testnet based basic website. The code will be released under a BUSL-1.1 license.

If time permits it I'll set-up these L1 scrips on Lina Mainnet and add a basic styling on this website.

Once these layer are online I'll personally gain nothing from them, so as incentives I need to ask for additional 10000$ in CKB. This excludes external Core & Incentivization Layers audits or expenses to deploy them on Mainnet.

### First Year: Fully Working Protocol

I'll create and deploy the Periphery Layer, which comprises many L1 & L2 contracts and off-chain bots. I'll create a fully working Aggron testnet and Lina Mainnet well designed website. The code developed in this stage probably will live in a private repository. It may become public under BUSL-1.1 if I create a DAO at a later stage.

Once everything is on Mainnet and audited, I **may** gain personally from this, still it's a risky long shot, so I'm open about discussing incentives for this phase. I estimate the incentives for this stage as additional 20000$ in CKB, in two separate steps so that Nervos can evaluate the progress I make on the the Periphery Layer private repositories and on the website.

If we audit at the same time Core, Incentivization & Periphery Layer the audit expenses should be lower and so Nervos could fully sponsor them.

About the cost of deploying the Periphery contracts on Lina Mainnet, I'll need to understand the associated costs and possibly ask for a refund.

### Future Plans

Once everything is realized, I may develop a DAO and a token wrapping the generated fees and distributing the arbitrage bot responsibilities.

Looking even more far away CKB++ will enable Hexmate ISPO, the official Nervos DAO community voting mechanism and a multitude more L1 & L2 applications!
