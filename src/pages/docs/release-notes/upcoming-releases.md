# Upcoming releases

## Flexible discounts for Switch Orders (Mid-term upgrade discounts)

**Expected release:** June 2026

Partners can now apply flexible discount codes when customers switch products mid-term using `POST /v3/customers/{customerId}/orders` with `orderType: SWITCH` or `PREVIEW_SWITCH`. This allows promotional pricing to be offered at the point of upgrade, not just at renewal.

**What changed**

The following APIs have been updated:

- Preview Switch Order (`PREVIEW_SWITCH`): Accepts `lineItems[].flexDiscountCodes` in the request. Returns `lineItems[].flexDiscounts` in the response, showing whether each code was successfully applied.
- Create Switch Order (`SWITCH`): Accepts `lineItems[].flexDiscountCodes` in the request. Returns `lineItems[].flexDiscounts` in the response.
- Get Order (`GET /v3/customers/{customerId}/orders/{orderId}`): Returns `lineItems[].flexDiscounts` for `SWITCH` orders, in addition to the existing behavior for other `orderTypes`, including `NEW` orders.
- Get Order History (`GET /v3/customers/{customerId}/orders`):  Returns `lineItems[].flexDiscounts` for `SWITCH` orders in the order history list.

**Important:** `flexDiscountCodes` apply only to `lineItems` (the target product items being switched to). Codes on `cancellingItems` (the source product being cancelled) are not accepted or returned.

**Why it matters**

  Partners can now incentivize customers to upgrade products during their active subscription term by applying promotional discounts at the time of the switch. Previously, flexible discounts were only available on new orders and renewals. This enables upsell campaigns where discount codes can be offered to customers making mid-term product upgrades.

  **Action required**

| Action                                   | Details                                                                                                                                         |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Add `flexDiscountCodes` to switch order requests | Include `lineItems[].flexDiscountCodes` (array of strings) in your `PREVIEW_SWITCH` and `SWITCH` order requests to apply a flexible discount. This field is optional. |
| Check `flexDiscounts[].result` in the response   | Verify that `result: "SUCCESS"` is returned for each applied code before confirming the discount to the customer.                                |
| Update `Get Order` and `Get Order History` API integrations | If your integration reads order details or history for `SWITCH` orders, expect the `lineItems[].flexDiscounts` field to now be present in responses. |
| Use `Preview Switch Order` before placing the order | Call `PREVIEW_SWITCH` first to confirm discount applicability and see the discounted pricing before committing the order.                        |

For more information, see [Manage Flexible Discounts](../flex-discounts/index.md).

## Flexible Discounts for 3-Year Commitments (3YC)

**Expected release:** June 2026

Partners can discover and apply flexible discounts specifically designed for customers who are new to 3YC (orders that make them 3YC-compliant) or existing 3YC customers, including cases where minimum purchase quantity (MPQ) thresholds are met. Promotions can be reused and applied to the first term, the current term, or the full 3YC commitment duration.

**Why it matters**

Partners can now present 3YC-specific promotional pricing to eligible customers to incentivize new 3-year commitments or to reward existing 3YC subscribers. This removes the need for manual outreach to identify eligible customers.

**Action required**

| Action                                   | Details                                                                                                                                         |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Discover 3YC promotions through Get Flexible Discounts | Call `GET /v3/flex-discounts`.            |
| Apply the discount code on the order      | Include the `flexDiscountCodes` in your `NEW` or `RENEWAL` order request. Eligibility is evaluated dynamically. |

For more information, see [Manage Flexible Discounts](../flex-discounts/index.md).

## Overlay recommendations are now available to partners

**Expected release:** June 2026

Partners can now view Adobe-identified customer purchase intent through the existing [Fetch Recommendations](../recommendations/apis.md#fetch-recommendations) (`POST /v3/recommendations`) API. When an Adobe agent identifies purchase intent during an overlay interaction, a lead is created and returned in the recommendations response under a new `overlayRecommendations` field. Partners also receive an email notification when a lead is created.

**What changed?**

The `POST /v3/recommendations` endpoint now returns an `overlayRecommendations` object alongside the existing `productRecommendations`. This object contains two arrays:

- **`new`**: Leads where the customer expressed intent to purchase new products.
- **`renew`**: Leads where the customer expressed intent to renew existing subscriptions.

Each lead includes `createdAt`, `expiresAt`, `status`, and an `items` array with `offerId` and `quantity`. Leads have a status of `OPEN` until they are consumed during order placement or automatically expired past their `expiresAt` date.

For more information, see [Overlay recommenations](../recommendations/index.md#overlay-recommendations).

## Churn and seat expansion propensity are now surfaced in the Recommendations API

**Expected release:** June 2026

Partners can now see churn risk and seat expansion signals for each customer through the existing [Fetch Recommendations](../recommendations/apis.md#fetch-recommendations) (`POST /v3/recommendations`) API.

**What changed?**

The Recommendations API response now includes a `churn` object and a `seatExpansion` object. Each contains a `probability` rating (`HIGH`, `MEDIUM`, or `LOW`), a `refreshDate`, and a `reasons` array with up to seven leading indicators ordered by relevance. Seat expansion also includes `predictedAddonSize` with the expected number of additional seats.

**Important:** An empty object `{}` for either node means propensity data is not available for that customer. Do not interpret `{}` as LOW risk or LOW expansion potential.

**Why it matters?**

Partners can now identify at-risk customers and high-growth opportunities using data-driven signals, rather than relying on anecdotal indicators or waiting for account-manager outreach.

**Action required**

| Action | Details |
|---|---|
| Parse the new `churn` and `seatExpansion` nodes | Both appear at the same level as `productRecommendations`. Handle three states: absent (feature not enabled), empty `{}` (data unavailable), and populated (data available). |
| Do not equate `{}` with LOW | An empty object means no data. Treat it as unknown, not low risk. |
| No changes needed for existing fields | `productRecommendations` and `overlayRecommendations` are unchanged. Existing integrations continue to work without modification. |

For more information, see [Propensity Intelligence](../recommendations/index.md).
