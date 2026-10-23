# Upcoming releases

**Note:** This is the initial version of the documentation, published early to provide partners with visibility into upcoming changes. The documentation will be updated as additional details become available. The expected release dates are indicative only and may change.

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

**Expected release:** November 2026

Adobe Instant Deal Registration is an automatically applied, codeless reseller credits, removing the need for a manual registration step on qualifying orders. There is no code to request, submit, or track. Partners see the intant deal registration amount directly in any qualifying orders and can view information on the deal through two discovery APIs. For more information see, [Adobe Instant Deal Registration overview](../instant-deal-registration/index.md).

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

## Churn and seat expansion propensity are now surfaced in the Recommendations API

**Expected release:** November 2026

Partners can now see churn risk and seat expansion signals for each customer through the existing [Fetch Recommendations](../recommendations/apis.md#fetch-recommendations) (`POST /v3/recommendations`) API.

**What changed?**

Set the request parameter `includePropensity` to ["churn"], ["seatExpansion"], or `["churn", "seatExpansion"]` to retrieve the corresponding `churn` and `seatExpansion` propensity signals. Each contains a `probability` rating (`HIGH`, `MEDIUM`, or `LOW`), a `refreshDate`, and a `reasons` array with up to seven leading indicators ordered by relevance.

Seat expansion also includes `predictedAddonSize` with the expected number of additional seats.

**Important:** An empty object `{}` for either node means propensity data is not available for that customer. Do not interpret `{}` as LOW risk or LOW expansion potential.

**Why it matters**

Partners can now identify at-risk customers and high-growth opportunities using data-driven signals, rather than relying on anecdotal indicators or waiting for account-manager outreach.

**Action required**

| Action | Details |
|---|---|
| Parse the new `propensity` object | It appears at the same level as `productRecommendations`. Handle three states: absent (feature not enabled), empty `{}` (data unavailable), and populated (data available). |
| Do not equate `{}` with LOW | An empty object means no data. Treat it as unknown, not low risk. |
| No changes needed for existing fields | `productRecommendations` and `overlayRecommendations` are unchanged. Existing integrations continue to work without modification. |

For more information, see [Propensity Intelligence](../recommendations/index.md#propensity-intelligence).

### Testing Propensity Signals in sandbox

A set of 13 predefined test seeds is configured in the Sandbox so you can exercise every combination of rating level and empty-array behavior during integration testing. Each seed returns a fixed response. 

For more information, see [Testing Propensity Signals in sandbox](../../sandbox/sandbox-portal/recommendations/index.md#testing-propensity-signals-in-sandbox).
