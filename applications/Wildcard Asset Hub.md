# WILDCARD Asset Hub

> [!NOTE]
> This document will be part of the terms and conditions of your agreement and, therefore, needs to contain all the required information about the project. Don't remove any of the mandatory parts presented in bold letters or as headlines (except for the title)! Lines starting with a `>` (such as this one) should be removed. Please use markdown instead of HTML (e.g., `![](image.png)` instead of `<img>`).
>
> See the [Grants Program Process](https://grants.web3.foundation/docs/process) on how to submit a proposal.

- **Team Name:** PolyCrypt GmbH
- **Payment Details (USDC,DOT):** 12fxpnqQeRdph6xH8PJ71EFGiFhUavjbvd2at5vAnhLdzGsu 

- **[Level](https://grants.web3.foundation/docs/Introduction/levels):** 3



## Project Overview :page_facing_up:

A trustless multi-chain L2 enabling seamless NFT and asset transfers across Polkadot, Ethereum, and beyond.

### Overview


WILDCARD is a next-generation, trustless multi-chain L2 designed to eliminate ecosystem lock-in for NFTs and fungible assets. It revolutionizes gaming and web3 markets by offering gasless, near-instant transactions and a non-custodial marketplace for secure asset minting, transfers, and trading. As a multi-chain solution, WILDCARD simplifies secure token bridging, allowing creators to launch chain-independent collections, games, and web3 experiences effortlessly.

WILDCARD seamlessly integrates into the Polkadot ecosystem via the WILDCARD Pallet , enabling easy deposits and withdrawals of Polkadot assets across parachains and to/from external chains. By leveraging XCM, WILDCARD can provide interoperability across the entire Polkadot ecosystem through a single parachain.

A critical next step for WILDCARD is Polkadot Asset Hub, which enhances asset management and interoperability within the network. Asset Hub's first-class interface for fungible and non-fungible assets provides an efficient, low-cost infrastructure for asset creation and transfers. Integrating with Asset Hub strengthens WILDCARD’s cross-chain liquidity and ensures faster, more scalable NFT movements across different blockchains.

Our core motivation is to bridge Polkadot and Ethereum, making NFTs and fungible assets more accessible while enhancing interoperability, scalability, and security for gaming, digital assets, and web3 applications.
Our team is committed to breaking the barriers of isolated NFT ecosystems and fostering a more connected, user-friendly blockchain environment and to facilitate TEE-based and non-TEE-based third-party integrations, reducing the entry barrier for traditional Web2 services, games, and platforms.

Unlike existing solutions like SnowBridge and XCM, which primarily focus on bridging fungible assets, WILDCARD is designed specifically for NFTs and is not limited to substrate-based ecosystems. Our use of Trusted Execution Environments (TEEs) enhances security, ensuring smooth and safe integration with gaming platforms.

**Roadmap & Milestones**
* 2020 – CommiTEE paper published (https://eprint.iacr.org/2020/1486)
* 2020 – ETHOnline 🚀 Hackathon Winner & Ethereum’s Impact Summit
* 2022 – Ajuna & PolyCrypt collaboration on W3F Grant
* 2023 – Publication of the CommiTEE protocol in EuroS&P
* 2023 – WILDCARD development kickoff (Polkadot Decoded), legal formation
* 2023 – MVP Milestone: NFT transfer on ETH Goerli ↔ Bajun Rococo
* 2024 – MVP Demo Milestone: NFT transfer on Bajun Rococo ↔ ETH Sepolia
* 2025 – Enhanced resilience & failure resistance supported by TrustChain EU Grant
* 2025 – Integration of Polkadot Asset Hub

With WILDCARD and Asset Hub, we’re paving the way for seamless asset movement across substrate-based and external ecosystems, unlocking new possibilities for NFT projects, gaming, and web3 innovation.


### Project Details
![](https://github.com/ssafai/temp/blob/ff4d5ba471a0b71ef2a0dd4633dd78f2982296b6/architecture.png)


Architecture: the WILDCARD L2 ledger runs inside a TEE, tracking the full consensus of the various connected chains via chain-specific secondary TEEs, and extracts protocol-specific events (such as token deposits), producing corresponding wrapped tokens on the L2 ledger. An untrusted operator acts as a message relay between users and the TEE, and orchestrates the general service. User-hosted (1:n honesty/availability) watcher TEEs ensure that if the system freezes due to an outage, all chains will freeze on the same checkpoint, guaranteeing the system to always be in a consistent state. When the outage is resolved, the system restarts from the checkpoint it was frozen on. The above diagram shows the current state of development and the plans we have for mainnet maturity and beyond. The most significant additions will be increased redundancy for stronger availability guarantees and more systematic service orchestration, including an administration control panel and safe UI / SDK delivery strategy using IPFS (to protect users from being served malicious frontends) or similar.

![](https://github.com/ssafai/temp/blob/ff4d5ba471a0b71ef2a0dd4633dd78f2982296b6/protocol.png)

The bridging protocol: tokens (fungible or non-fungible) get deposited onto the L2 ledger, remaining locked on the original chain. A wrapped token is issued on the L2 ledger for the same account that deposited it. We use a chain/type/collection descriptor on our L2 to track tokens from various chains. The wrapped token is then transferred to the destination account, which then issues an exit request. On the next epoch shift, the wrapped token is designated for withdrawal to the requested destination chain in the balance proofs issued by the L2 TEE. Next, the system waits for the finalisation of the finished epoch. Finalisation is achieved when we deem the balance proofs to be data-available to all users, which is determined through a challenge/response mechanism. Once the epoch is finalised, the user can redeem his balance proof to acquire a corresponding wrapped token on the destination blockchain (as a native collection with the same metadata as the original collection on the origin chain). Re-depositing a wrapped token onto the L2 will translate it back to the multi-chain asset descriptor referring to the original token. As opposed to native tokens, wrapped tokens get burned upon re-deposit, and withdrawal to the origin chain will unlock the original token.

If the operator fails to respond to a challenge to publish balance proofs, the system will freeze to the last finalised epoch to protect against malicious operation and to guarantee that users can always withdraw their funds. The watcher TEEs detect the freeze event and are authorised to propagate it across all connected chains (acting as single-purpose cross-chain oracles), so that it is guaranteed that the system halts in a consistent, well-defined state. The unfreeze protocol announces the L2 TEE’s restart (from the frozen epoch’s snapshot) on-chain, and after ensuring that only one TEE instance is running, the system resumes.

We also added a protocol to let us handle chain outages gracefully without causing the entire system to halt and freeze. As we plan to connect many chains to our system, this becomes a more probable risk we need to address in advance.

*Tech stack:* The initial codebase was mostly written in Go using the EGo toolset for writing TEE applications in Go. Since we reached general maturity of the project, we have started to migrate components to Rust for better performance and auditability. The core TEE only indirectly interacts with the various blockchains via the chain-specific TEEs that act as intermediaries and abstraction layer, as well as encapsulating third-party blockchain-specific code away from the core service, to reduce the attack surface. We ship a collection of web widgets and a typescript SDK to allow third-party developers to integrate WILDCARD into their dApps or websites.

*What do we not provide/implement*
* Our project will not include smart contract functionality directly on our L2. Our L2 is a pure asset management layer interconnecting various chains.
* We do not require users to buy a specific token to use our service. Fees can always be paid in a supported chain’s native currency.


### Ecosystem Fit
Polkadot currently offers various modules for NFT usage. Some are pallet-native, like the old pallet_unique and the new pallet_nfts, while others exist within EVM-based parachains. However, NFT ownership remains tightly bound to the ecosystem in which the asset is minted or issued. Currently, there is no solution that enables truly blockchain-agnostic NFT transfers between parachains,from the relay chain to a parachain or across different blockchain ecosystems (for example Ethereum). While solutions like XFM NFT may emerge for Substrate-based NFTs, our approach distinctly targets interoperability beyond the Substrate ecosystem, ensuring seamless NFT movement across diverse blockchain networks.

WILDCARD enables businesses, developers, and users to fully leverage NFTs by facilitating seamless cross-chain transfers. By adding Asset Hub compatibility/integration we simplify integration, enhance market accessibility, and unlock broader liquidity across parachains and external ecosystems.

Currently, NFT-based projects within the Polkadot ecosystem face structural limitations, as they remain tied to their specific parachains. This makes it challenging to access broader markets, integrate liquidity, or interact with external blockchain ecosystems. Asset Hub, a dedicated parachain for managing both fungible and non-fungible assets, represents a step toward addressing these challenges. By offering a standardized framework for NFTs, it provides a more structured approach to asset interoperability within Polkadot, though broader cross-ecosystem compatibility remains an ongoing area of development

With WILDCARD, we eliminate the barriers preventing NFTs from reaching new ecosystems. Our solution allows seamless off- and onboarding of assets—both fungible and non-fungible—between substrate-based chains and external ecosystems. By leveraging Asset Hub alongside WILDCARD, we enable NFTs to move across parachains efficiently and access global markets, including Ethereum and beyond.

This advancement ensures that NFT projects are no longer confined to their parachains, unlocking broader market access, increased liquidity, and greater adoption across multiple ecosystems.

Within the Polkadot Ecosystem, WILDCARD distinguishes itself through its use of Trusted Execution Environments (TEEs) to enhance asset accessibility. Unlike other projects in this space, such as SnowBridge &  XCM, which primarily focus on bridging fungible assets, WILDCARD broadens its scope to include a diverse array of asset types, notably focussing on non-fungible assets. This capability not only differentiates WILDCARD within the ecosystem but also facilitates smooth integration with gaming platforms, thereby solidifying its unique and pivotal role in the Polkadot network



Help us locate your project in the Polkadot/Substrate/Kusama landscape and what problems it tries to solve by answering each of these questions:

- Where and how does your project fit into the ecosystem?
- Who is your target audience (parachain/dapp/wallet/UI developers, designers, your own user base, some dapp's userbase, yourself)?
- What need(s) does your project meet?
- How did you identify these needs? Please provide evidence in the form of (scientific) articles, forum discussions, case studies, or raw data.
- Are there any other projects similar to yours in the Substrate / Polkadot / Kusama ecosystem?
  - If so, how is your project different? Please identify and assess any projects addressing the same need and explain how your project is distinct. Feel free to include applicable research data, statistics, or metrics.
  - If not, please indicate why such a project might not have been possible, successful, or attempted. 
- Are there any projects similar to yours in related ecosystems? 

## Team :busts_in_silhouette:

> [!IMPORTANT]
> Please note that the data provided in this section is for administrative and informational purposes only. All beneficiaries of a grant must also be listed in the KYC/KYB process during the application phase. See our [FAQ](https://grants.web3.foundation/docs/faq#what-is-kyckyb-and-why-do-i-have-to-provide-this-data) for more info.

### Team members

- Name of team leader
- Names of team members

### Contact

- **Contact Name:** Full name of the contact person in your team
- **Contact Email:** Contact email (e.g. john@duo.com)
- **Website:** Your website

### Legal Structure

- **Registered Address:** Address of your registered legal entity, if available. Please keep it in a single line. (e.g. High Street 1, London LK1 234, UK)
- **Registered Legal Entity:** Name of your registered legal entity, if available. (e.g. Duo Ltd.)

### Team's experience

Please describe the team's relevant experience. If your project involves development work, we would appreciate it if you singled out a few interesting projects or contributions made by team members in the past.

If anyone on your team has applied for a grant at the Web3 Foundation previously, please list the name of the project and legal entity here.

### Team Code Repos

- https://github.com/{your_organisation}/{project_1}
- https://github.com/{your_organisation}/{project_2}

Please also provide the GitHub accounts of all team members. If they contain no activity, references to projects hosted elsewhere or live are also fine.

- https://github.com/{team_member_1}
- https://github.com/{team_member_2}

### Team LinkedIn Profiles (if available)

- https://www.linkedin.com/{person_1}
- https://www.linkedin.com/{person_2}


## Development Status :open_book:

If you've already started implementing your project or it is part of a larger repository, please provide a link and a description of the code here. In any case, please provide some documentation on the research and other work you have conducted before applying. This could be:

- links to improvement proposals or [RFPs](https://grants.web3.foundation/docs/rfps) (requests for proposal),
- academic publications relevant to the problem,
- links to your research diary, blog posts, articles, forum discussions or open GitHub issues,
- references to conversations you might have had related to this project with anyone from the Web3 Foundation,
- previous interface iterations, such as mock-ups and wireframes.

## Development Roadmap :nut_and_bolt:

This section should break the development roadmap down into milestones and deliverables. To assist you in defining it, we have created a document with examples for some grant categories [here](../docs/Support%20Docs/grant_guidelines_per_category.md). Since these will be part of the agreement, it helps to describe *the functionality we should expect in as much detail as possible*, plus how we can verify and test that functionality. Whenever milestones are delivered, we refer to this document to ensure that everything has been delivered as expected.

Below we provide an **example roadmap**. In the descriptions, it should be clear how your project is related to Substrate, Kusama or Polkadot. We *recommend* that teams structure their roadmap as 1 milestone ≈ 1 month.

> [!CAUTION]
> If any of your deliverables are based on somebody else's work, make sure you work and publish *under the terms of the license* of the respective project and that you **highlight this fact in your milestone documentation** and in the source code if applicable! **Projects that submit other people's work without proper attribution will be immediately terminated.**

### Overview

- **Total Estimated Duration:** Duration of the whole project (e.g. 2 months)
- **Full-Time Equivalent (FTE):**  Average number of full-time employees working on the project throughout its duration (see [Wikipedia](https://en.wikipedia.org/wiki/Full-time_equivalent), e.g. 2 FTE)
- **Total Costs:** Requested amount in USD for the whole project (e.g. 12,000 USD). Note that the acceptance criteria and additional benefits vary depending on the [level](../README.md#level_slider-levels) of funding requested.
- **DOT %:** Percentage of Total Costs to be paid in (vested) DOT (≥ 50%)

### Milestone 1 Example — Basic functionality

- **Estimated duration:** 1 month
- **FTE:**  1,5
- **Costs:** 8,000 USD

> [!NOTE]
> **The default deliverables 0a-0d below are mandatory for all milestones**, and deliverable 0e at least for the last one.

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | Apache 2.0 / GPLv3 / MIT / Unlicense. See the [delivery guidelines](https://grants.web3.foundation/docs/Support%20Docs/milestone-deliverables-guidelines#license) for details. |
| **0b.** | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains how a user can (for example) spin up one of our Substrate nodes and send test transactions, which will show how the new functionality works. See the [delivery guidelines](https://grants.web3.foundation/docs/Support%20Docs/milestone-deliverables-guidelines#documentation) for details. |
| **0c.** | Testing and Testing Guide | Core functions will be fully covered by comprehensive unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. See the [delivery guidelines](https://grants.web3.foundation/docs/Support%20Docs/milestone-deliverables-guidelines#testing-guide) for details. |
| **0d.** | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 0e. | Article | We will publish an **article**/workshop that explains [...] (what was done/achieved as part of the grant). (Content, language, and medium should reflect your target audience described above.) |
| 1. | Substrate module: X | We will create a Substrate module that will... (Please list the functionality that will be implemented for the first milestone. You can refer to details provided in previous sections.) |
| 2. | Substrate module: Y | The Y Substrate module will... |
| 3. | Substrate module: Z | The Z Substrate module will... |
| 4. | Substrate chain | Modules X, Y & Z of our custom chain will interact in such a way... (Please describe the deliverable here as detailed as possible) |
| 5. | Library: ABC | We will deliver a JS library that will implement the functionality described under "ABC Library" |
| 6. | Smart contracts: ... | We will deliver a set of ink! smart contracts that will...


### Milestone 2 Example — Additional features

- **Estimated Duration:** 1 month
- **FTE:**  1,5
- **Costs:** 8,000 USD

...


## Future Plans

Please include here

- how you intend to finance the project's long-term maintenance and development,
- how you intend to use, enhance, and promote your project in the short term, and
- the team's long-term plans and intentions in relation to it.

## Referral Program (optional) :moneybag:

You can find more information about the program [here](https://grants.web3.foundation/docs/referral-program).

- **Referrer:** Name of the Polkadot Ambassador or GitHub account of the Web3 Foundation grantee
- **Payment Address:** Polkadot/Kusama (USDC) payment address. Please also specify the currency. (e.g. 15oF4... (USDC))

## Additional Information :heavy_plus_sign:

**How did you hear about the Grants Program?** Web3 Foundation Website / Medium / Twitter / Element / Announcement by another team / personal recommendation / etc.

Here you can also add any additional information that you think is relevant to this application but isn't part of it already, such as:

- Work you have already done.
- If there are any other teams who have already contributed (financially) to the project.
- Previous grants you may have applied for.
