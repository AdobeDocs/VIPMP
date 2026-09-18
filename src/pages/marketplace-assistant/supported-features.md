---
title: Supported features
description: Learn which capabilities are currently supported by Marketplace Assistant.
---

# Supported features

Marketplace Assistant currently supports four capability areas: customer data, catalog and price list, renewals, and VIP Marketplace product knowledge. Responses are generated using live data from Commerce Partner APIs or content from Adobe documentation and knowledge bases. Each response identifies its source so you can verify the information.

Marketplace Assistant capabilities continue to expand. See [Upcoming capabilities](#upcoming-capabilities) for planned additions.

## Customer data

Ask questions about a specific customer's account, discount programs, orders, and subscriptions. Customer data inquiries require a customer ID. Searching by company name is not currently supported.

**Account and profile**

- "Show me the account details for customer `{customerId}`."
- "What discount levels are on `{customerId}`'s account?"

**Discount programs**

- "Is `{customerId}` on 3YC, and what tier?"
- "Does `{customerId}` have a Linked Membership? Who is the owner?"
- "What volume discount tier is `{customerId}` on, and what would move them up?"

**3YC status and compliance**

- "Is customer `{customerId}` 3YC compliant?"
- "Give me the 3YC summary for `{customerId}`."
- "What is the last date to accept the 3YC clause for `{customerId}`?"

**Orders**

- "Show me `{customerId}`'s order history."
- "Show me the details for order `{orderId}`."
- "Why did order `{orderId}` fail? Can it still be returned?"

**Subscriptions**

- "Show me all subscriptions for `{customerId}`."
- "Why is this subscription not auto-renewing?"

**What to expect:** Responses use live data from the system of record rather than cached or estimated values. Marketplace Assistant cannot reconstruct historical changes, such as when or why a value changed, because audit history is not retained. If required data cannot be verified, Marketplace Assistant explicitly states this instead of inferring a compliance status or discount level.

## Price list

Ask about pricing, SKU discovery, and discount eligibility, including Three-Year Commit (3YC) pricing.

**Example prompts**

- "What's the price for SKU `{skuId}` in Germany, commercial segment?"
- "How much does Creative Cloud cost in Canada?"
- "Why is this customer's discount level `{level}`?"
- "What is `{customerId}`'s 3YC lock-in price for Acrobat Pro?"
- "How does this customer get to discount level `{level}`?"
- "How close is `{customerId}` to the next discount level, and what would get them there?"
- "Which SKUs are available in France?"
- "What SKUs are available for Adobe Sign?"

**What to expect:** Pricing responses identify the resolved product or SKU, market segment, country, currency, and price list month used, even when Marketplace Assistant applies default values. For customer-specific pricing requests, responses also include the customer's discount level and an explanation of why that level applies. If Marketplace Assistant asks a clarifying question, such as the product edition or market segment, provide the requested information to receive an accurate response.

**Limitations**

- Customer lookup requires a customer ID. Searching by company name is not supported.
- SKU discovery currently supports country-level filtering only. Filtering by sub-country regions, such as states or provinces, is not yet supported.
- Cross-region pricing is available only when both your organization and the customer are enabled for that capability.
- Education-segment customers are always shown standard pricing because 3YC pricing does not apply to that segment.
- Determining whether a specific product contributes to volume discount or 3YC quantity totals is not yet supported.
- Historical pricing inquiries, such as previous pricing or price changes over time, are not supported. Contact Adobe Partner Support for pricing history or dispute resolution.

## Renewals

Ask about upcoming renewals, auto-renewal status, and why a renewal did not complete.

**Example prompts**

- "When is the renewal happening for customer `{customerId}`?"
- "How much will customer `{customerId}` be charged at renewal?"
- "Give me a line-by-line breakdown of the renewal for `{customerId}`, including product name, quantity, and price."
- "Which subscriptions for `{customerId}` are not set to auto-renew?"
- "Will the renewal go through for customer `{customerId}`?"
- "Customer `{customerId}` did not renew last cycle. Why?"
- "Customer `{customerId}`'s subscription is stuck in a pending renewal state. What is going on?"

**What to expect:** Marketplace Assistant returns a clear status for each renewal question, such as on track, at risk, or not renewed, along with a product-level breakdown and, when applicable, the specific reason a renewal did not proceed. When action is required, the response identifies whether the next step must be completed by your organization or Adobe and provides specific dates rather than relative time references.

**Limitations**

- Marketplace Assistant can diagnose renewal status but cannot place orders, change auto-renewal settings, or update renewal preferences on your behalf. Use Bridge or Commerce Partner APIs to perform those actions.
- Early renewals and late renewals are managed separately and are not currently supported by Marketplace Assistant.
- Locked-in 3YC pricing is covered under catalog and price list questions, not renewal-related questions.

## VIP Marketplace product knowledge

Ask general questions about VIP Marketplace features, terms, and processes, including 3YC requirements. Marketplace Assistant answers these questions using Adobe documentation and knowledge bases and identifies the source material used so you can review it directly.

The quality and depth of these responses depend on the underlying documentation. As Adobe documentation evolves and improves, Marketplace Assistant responses become more comprehensive and accurate.

## Upcoming capabilities

Adobe adds new capabilities to Marketplace Assistant on an ongoing basis, typically every few weeks. Planned additions include flexible discounts and early renewals. This page is updated as new capabilities become available.
