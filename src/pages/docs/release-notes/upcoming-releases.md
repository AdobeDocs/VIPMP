# Upcoming releases

**Note:** This is the initial version of the documentation, published early to provide partners with visibility into upcoming changes. The documentation will be updated as additional details become available. The expected release dates are indicative only and may change.

## Flexible Discounts qualification criteria enhancement

**Expected release:** January 2026.

The Flexible Discounts API qualification enrichment provides additional criteria to help partners assess whether a discount is relevant to a customer or order before submission. No new request parameter is required. Existing `baseOfferIds`, discount outcomes, and discount lock dates retain their meaning.

**What's new?**

- Discount-wide `orderTypes` and `retainContractQuantityAtRenewal` fields describe applicable order types and renewal quantity requirements.
- Optional `requirementSets` describe alternative groups of customer, subscription, order, and partner conditions.
- Partner-visible conditions cover customer status, product ownership, renewal windows, three-year commitment enrollee status, minimum quantities, upgrade percentages, retention rates, and discount usage caps.
- The same qualification structure is available for general Flexible Discounts discovery, customer-context discovery, on-demand discounts, Deal Rewards, and the partner Sandbox.

**Why it matters**

Partners can use the returned qualification criteria during discount discovery and compare them with customer subscription and to determine the criteria for which a customer can qualify for a discount. However, discovery does not guarantee eligibility. Some applicable conditions may not be returned, and Adobe's qualification at the time of order submission remains authoritative.

**Partner actions**

| Action | Details |
|---|---|
| Preserve grouped logic | Check all applicable discount-wide fields and ensure that all conditions within at least one requirement set are met. Do not combine conditions across different requirement sets. |
| Continue handling qualification failures | Existing error `2141` handling remains applicable.|
| Test discovery and order behavior | When enabled in Sandbox, compare the returned criteria with test customer data and test both successful and unsuccessful order-time qualification scenarios. |

For field definitions and examples, see [Evaluate qualification criteria](../flex-discounts/apis.md#evaluate-qualification-criteria). For Sandbox guidance, see [Test discount qualification criteria](../../sandbox/sandbox-portal/flex-discounts/index.md#test-discount-qualification-criteria).

## Local currency support

**Expected release:** November 2026

Local currency support introduces country-specific price lists through the existing [Fetch Price List](../manage-pricing/index.md) API. The pricing and currency rules for each country take effect on its cutover date. The cutover date is the date on which the country-specific price list becomes available and takes effect.

| Country | Currency after cutover | What changes |
|---|---|---|
| South Korea (`KR`), Türkiye (`TR`), Brazil (`BR`), India (`IN`) | USD | Country-specific prices replace regional USD prices. The currency does not change. |
| Canada (`CA`) | CAD | Country-specific CAD prices apply, with subscriptions moving from USD to CAD. |
| Mexico (`MX`) | MXN | Country-specific MXN prices apply, with subscriptions moving from USD to MXN. |

**Important:** Local currency support will be rolled out in phases. The first phase introduces country-specific pricing in USD. The second phase introduces CAD for Canada and MXN for Mexico. Follow official communications from Adobe for information about the releases.

**What changed?**

- Country-specific prices can differ from regional prices for the same offer, currency, and price list month. Each offer's `firstOrderDate` for each order indicates when the country-specific price takes effect for orders.
- New orders use the applicable country-specific price list and currency, subject to commitment exceptions. Existing subscriptions transition to the new currency at anniversary renewal, while active 3YC and HGO commitments retain the existing currency until the commitment ends.
- Subscriptions in both the old and new currencies can remain active for the same SKU. At renewal, eligible quantities from the old-currency subscription are consolidated into the new-currency subscription, excluding quantities that were renewed early. Partners can disable auto-renewal for the old-currency subscription to prevent consolidation.
- Existing flexible discount codes are carried over during consolidation.
- API requests to create orders or previews using the old currency are rejected after the currency change, subject to commitment exceptions.
- Eligible early renewals in the new currency cannot reference an old-currency subscription.

**Why it matters**

Partners can quote country-specific prices and manage currency transitions without losing subscription history or counting early-renewed seats twice.

**Action required**

| Action | Details |
|---|---|
| Request the applicable price list | Retrieve country-specific prices in the applicable currency. See the [pricing guide](../manage-pricing/index.md#request-country-specific-prices) for request details. |
| Track both subscriptions | Retain subscription IDs and currencies, and support two active subscriptions for the same SKU during the transition. |
| Review renewal quantities | Use [Preview Renewal](../order-management/order-scenarios.md#preview-renewal-orders) and review the [combined renewal rules](../renewals/auto-renewals.md#renewals-when-the-country-currency-changes). |
| Update order handling | Handle HTTP 422 currency rejections and the [exception for early renewals in the new currency](../renewals/manual-renewals.md#early-renewal-after-a-currency-change). |
| Preserve pricing exceptions | Retain commitment price locks, early renewals placed before cutover, and the original order's pricing and currency for returns and switch reverts. |

For more information, see [Local currency support](../manage-pricing/index.md#local-currency-support).

## Adobe Instant Deal Registration

**Expected release:** January 2027

**Important:** The Adobe Instant Deal Registration program is currently under review, and the API documentation may be subject to change. Existing API documentation and Sandbox access will remain available. However, partners are recommended to pause any ongoing integration development and defer new integration work until further guidance is provided.

Adobe Instant Deal Registration is an automatically applied, codeless reseller credits, removing the need for a manual registration step on qualifying orders. There is no code to request, submit, or track. Partners see the instant deal registration amount directly in any qualifying orders and can view information on the deal through two discovery APIs. For more information see, [Adobe Instant Deal Registration overview](../instant-deal-registration/index.md).

**What changed?**

- The [Flexible Discounts](../flex-discounts/apis.md#get-flexible-discounts) API introduces a `DEAL_REGISTRATION` discovery category that provides descriptive details, validity dates, status, qualification criteria, and instant deal registration outcomes without exposing a code or identifier. Partners must explicitly request this category during discovery.
- The [Recommendations](../recommendations/apis.md#fetch-recommendations) API introduces the `includeDealRegistrations` request parameter. The value defaults to `false`. When set to true, the response includes a `discounts.dealRegistrations` section and prioritizes opportunities whose eligible products intersect with products already owned by the customer.
- Preview Order, Create Order, Get Order, Get Order History, Preview Renewal, and Return responses include `isDealRegistered` when Instant Deal Registration was auto-injected for a line item. The field value indicates whether the deal registration amount was successfully applied.
- For requests with `fetch-price=true`, qualifying line items include per-unit `earnedDealRegPerUnit` and line item-total `earnedDealRegAmount`; `pricingSummary` includes the aggregated `totalEarnedDealRegAmount`.

For more information, see [Discover and verify Adobe Instant Deal Registration using APIs](../instant-deal-registration/apis.md).

**Why it matters**

Partners can identify Instant Deal Registration opportunities in advance through Order Preview, Get Recommendations, or the Flexible Discounts API. When a qualifying order is placed, the applicable deal registration amount is applied automatically and is reflected immediately in the order results.

Unlike a traditional deal registration rebate, the deal registration amount appears as an upfront line item in the recon file, reported separately from `lineItemPartnerPrice`, `discountedPartnerPrice`, and `totalLineItemPartnerPrice`.

**Partner actions**

| Action | Details |
|---|---|
| Parse the line item-level application result | If `isDealRegistered` is `true`, the deal registration amount was applied. If it is `false`, Instant Deal Registration was not applied to the line item. |
| Keep deal registration amounts separate from partner price | Treat `earnedDealRegPerUnit`, `earnedDealRegAmount`, and `totalEarnedDealRegAmount` as credits, not reductions to existing partner-price fields. |
| Update recommendation requests where needed | Set `includeDealRegistrations` to `true` only when Adobe Instant Deal Registration recommendations are required. Never expect or submit a code or ID. |
| Test affected API surfaces | Validate discovery and application of instant deal registration in APIs such as Flexible Discount, Recommendations, Preview Order, Create Order, Get Order, Get Order History, Preview Renewal, and Return in Sandbox. |

### Testing Adobe Instant Deal Registration in Sandbox

Sandbox provides predefined scenarios that simulate both qualifying and non-qualifying Instant Deal Registration outcomes while remaining contract-compatible with production responses. See [Test Adobe Instant Deal Registration in Sandbox](../../sandbox/sandbox-portal/instant-deal-registration/index.md).
