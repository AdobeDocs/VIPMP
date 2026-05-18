# Upcoming releases

## Overlay recommendations are now surfaced to partners

Partners can now view Adobe-identified customer purchase intent through the existing `GET /v3/recommendations` API. When an Adobe agent identifies purchase intent during an overlay interaction, a lead is created and returned in the recommendations response under a new `overlayRecommendations` field. Partners also receive an email notification when a lead is created.

**What changed?**

The `GET /v3/recommendations` endpoint now returns an `overlayRecommendations` object alongside the existing `productRecommendations`. This object contains two arrays:

- **`new`**: Leads where the customer expressed intent to purchase new products.
- **`renew`**: Leads where the customer expressed intent to renew existing subscriptions.

Each lead includes `createdAt`, `expiresAt`, `status`, and an `items` array with `offerId` and `quantity`. Leads have a status of `OPEN` until they are consumed during order placement or automatically expired past their `expiresAt` date.

For more information, see [Overlay recommenations](../recommendations/index.md#overlay-recommendations).
