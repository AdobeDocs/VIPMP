# Upcoming releases

## Partial quantity returns for VIP Marketplace orders

**Expected release:** August, 2026

Partners can now return a portion of an eligible line item quantity from a NEW or RENEWAL order within 14 days of the order date. Previously, partners could return only the entire line item quantity in a single request.

**What changed?**

The following enhancements support partial quantity returns for eligible orders:

- The newly introduced  `remainingQuantity` parameter on the Order resource indicates the quantity that is still available for return in NEW and RENEWAL orders. The [Get Order Details](../order-management/get-order.md) and [Get Ordr History](../order-management/get-order.md#get-the-order-history-of-a-customer) APIs return this parameter. The value is initially set to the line item's quantity and decreases after each return or mid-term Switch Plan cancellation associated with that line item.
- You can now submit a return request for any quantity that is less than or equal to the line item's `remainingQuantity`. The request and response schema of a `RETURN` order remains unchanged.
- You can submit multiple partial returns against the same line item. Each return request is validated against the line item's current `remainingQuantity`.
- Partial quantity returns are not supported for Switch Plan orders, Stock Credit Packs, Acrobat license packs, MOQ offers, or consumables such as Sign transactions.
- Licenses associated with a returned quantity are deprovisioned immediately after the return order is completed.

For complete request and response examples and a list of supported error codes, see [Return or cancellation of order](../order-management/order-scenarios.md#return-or-cancellation-of-order).

**Why it matters**

Previously, when a customer needed fewer licenses than originally ordered, partners had to return the entire line item and place a new order for the correct quantity. This process could result in a revenue gap of 1 to 14 days between the full-term billed return and the day-billed replacement order.

With partial quantity returns, partners can return only the quantity that the customer no longer requires through a single request, eliminating the need to create a replacement order.

**Action required**

Review the following updates to ensure that your integration supports partial quantity returns:

| Action | Details |
|----------|----------|
| Read `remainingQuantity` on `NEW` and `RENEWAL` orders | Use the `remainingQuantity` parameter instead of the line item's `quantity` parameter to determine how many units are eligible for return. |
| Handle the new 422 error codes | Support the following error codes: `RETURN_QTY_EXCEEDS_REMAINING`, `RETURN_NOT_SUPPORTED_MOQ_SKU`, `RETURN_NOT_SUPPORTED_SWITCH_ORDER`, and `RETURN_VIOLATES_3YC_MCQ`. |
| Continue using existing full-line return logic | No changes are required for existing full-line returns. Returning the full original quantity of a line item in a single request continues to work as before. |

### Sandbox changes

The Order details also displays the remaining quantity available for return. For more information, see [Cancel an order in Sandbox](../../sandbox/sandbox-portal/order-management/cancel-order.md).

## Churn and seat expansion propensity are now surfaced in the Recommendations API

**Expected release:** August 2026

Partners can now see churn risk and seat expansion signals for each customer through the existing [Fetch Recommendations](../recommendations/apis.md#fetch-recommendations) (`POST /v3/recommendations`) API.

**What changed?**

Setting the request parameter `includePropensity` to `true` fetches  `churn` and `seatExpansion` arrays. Each contains a `probability` rating (`HIGH`, `MEDIUM`, or `LOW`), a `refreshDate`, and a `reasons` array with up to seven leading indicators ordered by relevance. 

Seat expansion also includes `predictedAddonSize` with the expected number of additional seats.

**Important:** An empty object `{}` for either node means propensity data is not available for that customer. Do not interpret `{}` as LOW risk or LOW expansion potential.

**Why it matters**

Partners can now identify at-risk customers and high-growth opportunities using data-driven signals, rather than relying on anecdotal indicators or waiting for account-manager outreach.

**Action required**

| Action | Details |
|---|---|
| Parse the new `propensity` object | It appear at the same level as `productRecommendations`. Handle three states: absent (feature not enabled), empty `{}` (data unavailable), and populated (data available). |
| Do not equate `{}` with LOW | An empty object means no data. Treat it as unknown, not low risk. |
| No changes needed for existing fields | `productRecommendations` and `overlayRecommendations` are unchanged. Existing integrations continue to work without modification. |

For more information, see [Propensity Intelligence](../recommendations/index.md#propensity-intelligence).

### Testing Propensity Signals in sandbox

A set of 13 predefined test seeds is configured in the Sandbox so you can exercise every combination of rating level and empty-array behavior during integration testing. Each seed returns a fixed response. 

For more information, see [Testing Propensity Signals in sandbox](../../sandbox/sandbox-portal/recommendations/index.md#testing-propensity-signals-in-sandbox).
