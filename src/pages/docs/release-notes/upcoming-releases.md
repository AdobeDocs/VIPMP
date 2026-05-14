# Upcoming releases

## Flexible Discounts are now discoverable before their start date

Partners can now view upcoming flexible discounts via the GET /v3/flex-discounts API immediately after they are published, even when the discount's start date is in the future. This is a default behavior change and does not require any additional flags or parameters.

**What changed?**

The `GET /v3/flex-discounts` endpoint now returns all published discounts regardless of whether their `startDate` has been reached.
Discounts with a future `startDate` are returned with status: ACTIVE.

**Why it matters**

Partners can now prepare marketing campaigns, brief resellers, and configure storefronts ahead of a discount's live date, rather than waiting for comms or account-manager outreach.

**Action required**

| Action                         | Details                                                                                                                                                                      |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Use startDate for liveness checks | The status field alone no longer indicates whether a discount is live. Compare startDate to the current date to determine if a discount can be applied to orders today.     |
| Audit existing logic            | Review any integration logic that branches on status: ACTIVE as a proxy for "currently usable."                                                                              |
| Legacy behavior                 | Pass `start-date=<today>` as a query parameter to return only currently-live discounts.                                                       |

## Overlay recommendations are now surfaced to partners

Partners can now view Adobe-identified customer purchase intent through the existing `GET /v3/recommendations` API. When an Adobe agent identifies purchase intent during an overlay interaction, a lead is created and returned in the recommendations response under a new `overlayRecommendations` field. Partners also receive an email notification when a lead is created.

**What changed?**

The `GET /v3/recommendations` endpoint now returns an `overlayRecommendations` object alongside the existing `productRecommendations`. This object contains two arrays:

- **`new`**: Leads where the customer expressed intent to purchase new products.
- **`renew`**: Leads where the customer expressed intent to renew existing subscriptions.

Each lead includes `createdAt`, `expiresAt`, `status`, and an `items` array with `offerId` and `quantity`. Leads have a status of `OPEN` until they are consumed during order placement or automatically expired past their `expiresAt` date.
