# Upcoming releases

## Adobe Instant Deal Registration

**Expected release:** November 2026

Adobe Instant Deal Registration is an automatically applied, codeless reseller credits, removing the need for a manual registration step on qualifying orders. There is no code to request, submit, or track. Partners see the intant deal registration amount directly in any qualifying orders and can view information on the deal through two discovery APIs.

**What changed?**

- The [Flexible Discounts](../flex-discounts/apis.md#get-flexible-discounts) API introduces a `DEAL_REGISTRATION` discovery category that provides descriptive details, validity dates, status, qualification criteria, and instant deal registration outcomes without exposing a code or identifier. Partners must explicitly request this category during discovery.
- The [Recommendations](../recommendations/apis.md#fetch-recommendations) API introduces the `includeDealRegistrations` request parameter. The value defaults to `false`. When set to true, the response includes a `discounts.dealRegistrations` section and prioritizes opportunities whose eligible products intersect with products already owned by the customer.
- Preview Order, Create Order, Get Order, Get Order History, Preview Renewal, and Return responses include `isDealRegistered` when Instant Deal Registration was auto-injected for a line item. The field value indicates whether the deal registration amount was successfully applied.
- For requests with `fetch-price=true`, qualifying line items include per-unit `earnedDealRegPerUnit` and line item-total `earnedDealRegAmount`; `pricingSummary` includes the aggregated `totalEarnedDealRegAmount`.

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

## Account screening introduces new Sanctioned and Screening statuses

**Expected release:** September 2026

Partners can now view account screening progress while reseller, customer, and deploy-to accounts are checked against sanctions and watchlists before they are allowed to transact.

**What changed?**

Account creation now includes an asynchronous sanctions screening process. Two new status codes have been added:

| Status | Meaning |
|---|---|
| 1023 (Screening) | The account is awaiting a screening adjudication decision. |
| 1022 (Sanctioned) | The account has been identified as a sanctioned party and is blocked from transacting. |

These statuses apply to Reseller Account, Customer Account, and Deployment resources.

Status 1002 (Pending) remains unchanged but now indicates that sanctions screening has been successfully completed and the account is progressing through the remaining account creation checks.

**Why it matters**

Previously, sanctions screening occurred only after order submission, often leading to cancellations and manual remediation. Screening accounts before they can transact gives partners visibility into accounts under review or blocked due to sanctions, reducing uncertainty and eliminating most post-order cleanup.

**Action required**

| Action | Details |
|---|---|
| Handle the new status codes | Update polling and account status logic to recognize 1023 (Screening) and 1022 (Sanctioned) alongside existing Active, Inactive, and Pending statuses. |
| Expect delays before an account is usable | Orders, 3YC enrollment, and other actions that require an active account remain unavailable while an account is being screened, just as they are for any account that has not yet reached Active status. |
| No action needed for existing Active or Inactive accounts | The new statuses apply only to accounts undergoing creation or re-screening. |

For more information, see [Account screening statuses](../customer-account/create-customer-account.md#account-screening-statuses) and [Status codes and error handling](../references/error-handling.md).

## Extended-term customers can now enroll in 3YC during their last term year

**Expected release:** September 2026

Partners can now enroll eligible extended-term customers in a Three-Year Commitment (3YC) during the final year of the customer's extended term.

**What changed?**


| Timing of 3YC enrollment request | Behavior |
|---|---|
| Within the final year of the extended term (from one year before the anniversary date through the anniversary date) | The customer is automatically converted from an extended-term to a regular commitment, and the 3YC clause is created as part of the same request. |
| More than one year before the anniversary date | The request is rejected with the existing validation error. The customer remains on an extended term. No change to current behavior. |

**Why it matters**

Partners can now enroll eligible extended-term customers in 3YC as soon as they enter the qualifying window.

**Action required**

| Action | Details |
|---|---|
| No integration changes required | Submit the 3YC `commitmentRequest` through the existing [PATCH Update Customer API](../customer-account/update-customer-account.md) as usual. The conversion happens automatically when the customer is in the qualifying window. |
| Stop filing manual conversion tickets | Extended-term customers within their last term year no longer need a manual conversion before enrolling in 3YC. |
| Continue to expect rejection outside the window | Enrollment requests submitted more than one year before the anniversary date are still rejected; this has not changed. |

For more information, see [Extended-term customers and 3YC enrollment](../customer-account/three-year-commit.md#extended-term-customers-and-3yc-enrollment).

## Churn and seat expansion propensity are now surfaced in the Recommendations API

**Expected release:** August 2026

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
