# iCKB Project 2024 Overview

- **Project Name:** iCKB
- **Network:** Nervos Network
- **Purpose:** iCKB is a transformative protocol designed to address liquidity challenges associated with NervosDAO deposits by tokenizing them into an Extensible User Defined Token (xUDT) called iCKB. This allows CKB locked in NervosDAO to remain liquid and easily convertible back to CKB without waiting for maturity.
- [**Github**](https://github.com/ickb/)
- [**Logo**](https://github.com/ickb/proposal/blob/master/logo.png)


## Key Features and Applications

- **Liquidity Solution:** Tokenization of NervosDAO deposits into iCKB enhances liquidity.
- **New Applications:** iCKB serves as a DeFi primitive that enables new applications, including enhanced utilization of NervosDAO, the potential for double yield through integrations with external protocols and the ability for users from other chains to enjoy NervosDAO's interests.

## Progress and Achievements in 2024

1. **[Proposal](https://github.com/ickb/proposal):** Revised to enhance user experience from the Contracts up, optimized data structures and integrated novel tech like xUDT and RGB++.

2. **Core Development:**
   - **[Lumos Utils](https://github.com/ickb/lumos-utils):** Rewritten as a functional wrapper for Lumos's Transaction Skeleton, making it extensible and stable. Published as an npm package. Fully switching from Lumos to CCC is a work in progress.
   - **[Core Utils](https://github.com/ickb/v1-core):** Rewritten to interact with iCKB contracts based on Lumos Utils. Published as an npm package. Fully switching from Lumos to CCC is a work in progress.
   - **[Contracts](https://github.com/ickb/v1-core/tree/master/scripts):** Iteratively improved switching to the latest tech like xUDT until formal Audit. Later Deployed to mainnet in a non-upgradable manner. As a bonus, Limit Orders have a novel feature allowing users to provide liquidity with dual exchange ratios for CKB to UDT and UDT to CKB, similar to AMM but at constant ratios.

3. **Frontend Development:**
   - **[Fulfillment Bot](https://github.com/ickb/v1-bot):** Transitioned from proof of concept to MVP, supporting partial order fulfillments. Fully switching from Lumos to CCC is a work in progress.
   - **[Interface](https://github.com/ickb/v1-interface):** Created a MVP interface for interacting with iCKB contracts on Mainnet and Testnet. Following user feedback, ickb.org DApp has integrated JoyID via CCC and switched to a streamlined user experience. Fully switching from Lumos to CCC is a work in progress.

4. **[Internal and External Audit](http://scalebit.xyz/reports/20240911-ICKB-Final-Audit-Report.pdf):** Addressed minor issues raised during the audits of contracts and proposal. No vulnerabilities found.

## Milestones Achieved

- **Operational Status:** iCKB is running smoothly on both mainnet and testnet.
- **Liquidity Bootstrapping:** Achieved a TLV of 3 million iCKB with 22 holders.
- **DApp Support:** Dedicated ickb.org DApp and integration in nervdao.com DApp.
- **Community Engagement:** Ongoing education and CKCON presentation to raise awareness about iCKB.
- **Integrations:** Positive discussions with UTXO Stack Team, UTXO Swap Team and Stable++ Team regarding future integrations.

## Future Outlook

In the short term, iCKB will enhance user confidence in staking within NervosDAO, leading to increased CKB locking. On the tech side, fully switching from Lumos to CCC is a work in progress.

In the medium term, more decentralized finance protocols are expected to integrate iCKB, allowing users to earn interests from both these protocols and NervosDAO. Talks are already taking place with UTXO Stack Team, UTXO Swap Team and Stable++ Team for potential integrations, with a positive outlook.

In the long term, iCKB is positioned to facilitate integration with more UTXO chains, enabling users from other chains to benefit from NervosDAO's interests while maintaining liquidity.

## Conclusion

The iCKB project has made significant strides in enhancing the Nervos ecosystem by addressing liquidity challenges, improving core functionalities and engaging with the community. The ongoing development and positive discussions with other teams indicate a promising future for the protocol.
