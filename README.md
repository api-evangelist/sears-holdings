# Sears Holdings

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

> **No longer operating.** This company or product has shut down, been discontinued, or been deprecated. This profile is kept as a historical record and is not maintained or rated.

Sears Holdings was the American holding company behind the Sears and Kmart department store chains. It filed for Chapter 11 in October 2018 and its retail operations were acquired in 2019 by Transform Holdco LLC, which trades as Transformco (Transform SR Brands LLC), the entity that operates Sears.com, Kmart.com and ShopYourWay.com today. The live developer surface carried forward from that estate is the Sears Marketplace Seller API — a versioned XML-over-HTTPS integration for third-party sellers covering catalog and item management, item class and attribute libraries, inventory, pricing, purchase order retrieval, order cancellations, returns and partial or full refunds, ASN and shipment updates, order prep time, installation service items, and remittance, removed-item, content-match and processing reports. Every API is published with a downloadable XSD and a sample XML document, both fetchable without credentials from seller.marketplace.sears.com, and calls are signed with an HMAC-SHA256 authorization header rather than OAuth. There is no OpenAPI, GraphQL, MCP server or A2A agent card on any Sears, Kmart or Transformco host.

**Areas:** Fortune 100 · Retail · E-Commerce · Marketplace · Order Management · Inventory · Product Catalog · Seller Integration · XML · Defunct

## Sears Holdings on APIs.io (historical record)

Everything profiled for Sears Holdings is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/sears-holdings/](https://apis.io/providers/sears-holdings/)** — or go straight to one of the APIs or properties below.

### APIs (1)

- [Sears Marketplace Seller API](https://apis.io/apis/sears-holdings/sears-marketplace-seller-api/) — Versioned XML-over-HTTPS API for third-party sellers on Sears Marketplace (Sears.com, Kmart.com, ShopYourWay.com). GET calls export purchase orders,…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [sears-marketplace-seller](https://apis.io/servers/sears-holdings/sears-marketplace-seller/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Sears Holdings Authentication](https://apis.io/security/sears-holdings/sears-holdings-authentication/)
- [Sears Holdings Domain Security](https://apis.io/security/sears-holdings/sears-holdings-domain-security/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Sears Holdings Plans Pricing](https://apis.io/plans/sears-holdings/sears-holdings-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Sears Holdings Rate Limits](https://apis.io/rate-limits/sears-holdings/sears-holdings-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.sears.com/)
- [Documentation](https://marketplace.sears.com/docs/)
- [Getting started](https://marketplace.sears.com/docs/api-guide/using-sear-marketplace-apis-for-xml-integration/)
- [API reference](https://marketplace.sears.com/docs/api-guide/)
- [Pricing](https://marketplace.sears.com/sell-on-sears/program-fees/)
- [Sign up](https://marketplace.sears.com/sell-on-sears/)
- [Support](https://marketplace.sears.com/support/)
- [Blog](https://www.sears.com//blog)
- [Terms of service](https://marketplace.sears.com/terms-of-use/)
- [Privacy policy](https://www.sears.com/en_us/customer-service/policies/privacy-policy.html)

## API Evangelist rating

**Not rated** — this company is recorded as no longer operating, so no score is published. If it is in fact operating, say so on this repository and it will be re-profiled.

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Sears Holdings: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/sears-holdings/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
