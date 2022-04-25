# Proposal: Project CKB++

## Problem

### NervosDAO Illiquidity

NervosDAO is possibly the most important smart-contract of Nervos Layer 1 (L1 for short). Briefly a CKB holder can lock his CKB in exchange for a receipt of that specific deposit. Every 180 epochs (~30 days) the holder can exchange back his receipt to unlock his deposit plus the accrued Interests. This creates an illiquidity for CKB depositor.

### Untapped Potential

In the Nervos ecosystem exists the untapped potential for a Protocol that liquefies and bridges NervosDAO interests from L1 to L2. This Protocol could enable CKB-based [Initial Stake Pool Offerings](https://www.meld.com/ispo) (ISPO for short), the official Nervos DAO community voting mechanism and a multitude more L1 & L2 applications!

In particular this Protocol could enable the future Hexmate development of an ISPO, which I highly support and anticipate!

## Solution Bird View

### DCKB (Unmaintained)

In the past there has been an effort to tackle this challenge by [NexisDAO with dCKB](https://docs.nexisdao.com/nexisdao/mint-dckb). Their approach is to tokenize the holder receipt, which in turn becomes tradeable and so the holder keeps being liquid. The issue with their approach is that only the original owner can unlock the deposit. Currently dCKB seems unmaintained.

### Enter CKB++

CKB++ is provisional name for a [sUDT token](https://talk.nervos.org/t/rfc-simple-udt-draft-spec/4333) that represents deposits in the Protocol. As with dCKB, CKB++'s approach is to tokenize NervosDAO receipts, but with a twist: the Protocol owns all the CKB deposits and maintains a pool of them. This means that all the deposits and withdrawals are shared, so anyone can use anyone else's deposit to exit once it's mature.

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

### Fixed CKB++-Equivalent Deposit Size

Let's **assume** we don't implement any requirement on deposit size, so as in NervosDAO users can choose the deposit size they prefer. Then an attacker who can borrow a big enough capital can simply:

- exchange CKB++ for smaller CKB deposits;
- deposit CKB for CKB++ in deposits as big the the entirety of his capital;
- ...

This would greatly reduce the quality of the service for everyone, as the only remaining deposits would be as big or bigger than the attacker capital and since it's impossible to withdraw partially from a NervosDAO deposit, this would greatly hamper the Protocol fruition.

A good countermeasure is to fix a reasonable standard deposit size. As in real life bricks can be used to build houses of any size, in the same way:

- deposits too big should be split into standard deposits and possibly even spread over longer periods;
- deposits too small are better served by secondary markets.

This deposit standard size can be defined in CKB terms or in CBK++ terms:

- a fixed CKB means that as deposit are made in time every deposit would have a different size due to the NervosDAO interests, so it's a bad idea;
- a fixed CKB++-equivalent deposit size means that at every block all the deposits have the same size both in CKB and CKB++, of course as time passes this standard deposit size will gradually increase.

This way a few goals are achieved:

- big deposits now increase the overall Protocol liquidity;
- no size mismatch means anybody can use anybody else deposit freely to withdraw;
- a fixed CKB++-equivalent deposit size simplifies code, so minimize the hacks attack surface.

### Deposits from Core Layer Perspective

In NervosDAO a CKB holder can lock his CKB in exchange for a receipt of that specific deposit, while in our case the Protocol proceed by wrapping NervosDAO deposit transaction into CKB++. The Protocol in one transaction:

- requires a fixed CKB++-equivalent size deposit;
- takes control of the deposit;
- stakes it in NervosDAO;
- adds the receipt to its pool of receipts;
- mints to the depositor a fixed amount of CKB++;

### Withdrawals from Core Layer Perspective

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

There are two advantages of asking for CKB++ fees, such as either:

- a slight lock-in, as the user needs to fetch from secondary markets these additional CKB++ for paying the fees;
- a more liquid Protocol, as the user prioritizes actions without fees, so actions that benefit everyone.

Another approach would be to have strict rules, that for example clearly states when a deposit/withdrawal can happen or not, but usually it's easy for a determined attacker to abuse stricter rules against everyone else.

In the following sections I'll refer to the **epoch population** concept, this is a short hand for quantity of deposits at maturity in a certain epoch in the Core Layer deposit pool.

### Deposits from Incentivization Layer Perspective

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

### Withdrawals from Incentivization Layer Perspective

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

## Periphery Layer

### Taking Care of User Needs

The Core Layer of the Protocol defines a solid way to exchange between CKB and CKB++ in fixed blocks, while the Incentivization Layer takes care of making this exchange liquid.

The standard deposit size makes already very easy for most users to exchange back and forth between CKB and CKB++, but naturally there are two under-served categories of users:

- big depositors, those with a capital more than hundred times the standard deposit size. These users would need to spend a lot of time manually choosing the timing for each standard deposit;
- small depositors, those with a capital smaller than the standard deposit size. These users would need to rely on secondary markets.

On one side we have defined a Protocol essentially by excluding ideas not compatible with NervosDAO substrate, on the other we have these very legit user needs.

The Periphery Layer addresses these user needs, making the the Protocol handy to use irrespective of user capital size and location.

### Periphery Layer's Technology

We need to find a technology that can interface, abstract and adequately prioritize variable user needs on L1 & L2 to the Protocol Internal layers on L1, but actually in the wild already exists a more general solution to this problem, we are essentially approaching a CKB / CKB++ market.

Any kind of technology that enables market price discovery could theoretically work. This is because we can develop an off-chain bot that every time the CKB / CKB++ price is unbalanced, it arbitrages the price difference between the CKB / CKB++ market and the Protocol, balancing it back again.

Currently in the DeFi space there are two main techniques of price discovery:

- [Limit Orders Book](https://www.investopedia.com/terms/o/order-book.asp) based markets, the traditional solution;
- [Geometric Mean Market Maker](https://arxiv.org/abs/1911.03380) such as Uniswap, a new and evolving solution;

Geometric Mean Market Makers are a form of Automated Market Makers and they are very successful in DeFi as they're relatively easy to implement, provide liquidity to and trade against. On the other side trades of size comparable to the liquidity pool experience high slippage. Of course this behavior depends highly on the bonding curve and liquidity pool size.

On the other side Limit Orders Book based markets have a very different kind of slippage and big trades can work around slippage by using Limit Orders. On the other side they have different issues in DeFi, as being on-chain doesn't really work well with high frequency trading, both for the blockchain speed and fee costs associated with every on-chain transaction.

Currently hybrid models are being researched by different parties, myself included, and in all likelihood the Periphery Layer will adapt one of these to its particular needs, which are:

- CKB / CKB++ exchange rate is well defined, any significant deviation must be easily arbitraged back;
- Limit orders are needed to interface, abstract and adequately prioritize deposits & withdrawals;
- A form of Automated Market Maker is needed to minimize liquidity providers interactions;
- Fair incentives for every stakeholder: users, liquidity providers and service maintainers.

### Path to Sustainability

Both the arbitrage bot and the market platform could generate fees. This is good as it leads to a future where I can maintain the service for the long run and possibly upgrade it to a DAO.

On one side in the beginning the service offered by the Periphery Layer will be directly managed by me. This could even work in the long term as it's still decentralized as anyone can write a bot to arbitrage between the Inner Protocol and the CKB/CKB++ market. To be even more decentralized a third party can design a totally independent system to interface users and the Inner Protocol, as the Inner Protocol is totally independent from me.

On the other side in the long term the DAO could happen similarly to [GenesysGo](https://genesysgo.medium.com/the-comprehensive-guide-to-genesysgo-and-the-shdw-ido-278b90d3186c), but would carry a big development overhead and it's something that can only be developed at a later time.

## Road-Map & Incentives

I'll go from learning L1 scripting to creating a fully functional L1 & L2 Protocol. So while I'm pretty independent, I'll need dedicated support from the Nervos, monday to friday, no more than 48 hours delays in responses.

I do not plan on selling any CKB that I ask as incentives because the the arbitrage bots will need CKB capital to indeed arbitrage.

### First Month: Proof of Concept

I'll create a Proof of Concept L1 script, showing the basic functionalities of the core layer from command line in Testnet. Possibly not safe to use in production, but with a clear path to a safe resolution.

This is a risky period, there might exists some technical blocks long or impossible to work around. While I love everything about CKB++, I do need to ask a starting 1000$ incentive in CKB from Nervos foundation, so both parties are taking a small risk.

### First Four Months: Core & Incentivization Layers

I'll create the definitive L1 scripts for Core & Incentivization Layers. I'll need to simulate the Protocol incentives. I'll set-up a L1 Testnet based basic website. This website will provide users the ability to directly interact with the Incentivization Layer, with the addition of off-chain utilities for gradually exchanging CKB and CKB++. This will happen by keeping the browser tab open, bypassing the future Periphery Layer. The code will be released under a BUSL-1.1 license.

If time permits it I'll set-up these L1 scrips on Mainnet and add a basic styling on this website.

Once these layer are online I'll personally gain nothing from them, so as incentives I need to ask for additional 10000$ in CKB. This excludes external Core & Incentivization Layers audits or expenses to deploy them on Mainnet.

### First Year: Fully Working Protocol

I'll create and deploy the Periphery Layer, which comprises many L1 & L2 contracts and off-chain bots. I'll create a fully working Testnet and Mainnet well designed website which will provide users the ability to either:

- on L1 & L2 interact with the Periphery Layer and its associated on-chain limit order utilities for gradually exchanging CKB and CKB++.
- only on L1 directly interact with the Incentivization Layer using its off-chain utilities;

The code developed in this stage probably will live in a private repository. It may become public under BUSL-1.1 if I create a DAO at a later stage.

Once everything is on Mainnet and audited, I **may** gain personally from this, still it's a risky long shot, so I'm open about discussing incentives for this phase. I estimate the incentives for this stage as additional 20000$ in CKB, in two separate steps so that Nervos can evaluate the progress I make on the the Periphery Layer private repositories and on the website.

If we audit at the same time Core, Incentivization & Periphery Layer the audit expenses should be lower and so Nervos could fully sponsor them.

About the cost of deploying the Periphery contracts on Mainnet, I'll need to understand the associated costs and possibly ask for a refund.

### Future Plans

Once this Protocol is realized, I may develop a DAO, a token wrapping the generated fees and distributing the arbitrage bot responsibilities. Looking even more far away CKB++ will enable CKB-based Initial Stake Pool Offerings, the official Nervos DAO community voting mechanism and a multitude more L1 & L2 applications!

### License

This proposal is licensed under the terms of the [Creative Commons Attribution Share Alike 4.0 International license](LICENSE.txt).
