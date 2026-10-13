# Discover and verify Adobe Instant Deal Registration using APIs

Adobe Instant Deal Registration uses existing discovery and order APIs. There is no registration endpoint and no partner-supplied code or ID.

## Discover using the Recommendations API

Set `includeDealRegistrations` to `true` in the request body of `POST /v3/recommendations` to discover the Instant Deal Registration opportunities. The default is `false`.

```json
{
  "recommendationContext": "GENERIC",
  "customerId": "{{customerId}}",
  "country": "US",
  "language": "MULT",
  "includeDealRegistrations": true
}
```

The request fields relevant to Instant Deal Registration discovery are:

| Field | Description |
|---|---|
| `recommendationContext` | Context used to generate recommendations. Supported contexts include `GENERIC`, `ORDER_PREVIEW`, and `RENEWAL_ORDER_PREVIEW`. \<br /\> - For `GENERIC`, the API returns Instant Deal Registration opportunities associated with offers currently held by the customer. If an active deal registration exists for an offer in the customer's subscription portfolio, it is included in the response. \<br /\> - For `ORDER_PREVIEW` and `RENEWAL_ORDER_PREVIEW`, the system evaluates the offers in the request and returns any Instant Deal Registration opportunities applicable to those offers.|
| `customerId` | Customer for whom recommendations and Instant Deal Registration opportunities are requested. |
| `country` | Country used to determine applicable recommendations. If omitted, the customer's country is used. |
| `language` | Language for returned recommendation content. |
| `includeDealRegistrations` | Set to `true` to include Instant Deal Registration opportunities. The default is `false`. |

When requested, the response includes the opportunities in `discounts.dealRegistrations`. Opportunities associated with products already owned by the customer are returned first. Entries use the `DEAL_REGISTRATION` category and do not contain `code` or `id`.

Sample response:

```json
{
  "discounts": {
    "dealRegistrations": [
      {
        "category": "DEAL_REGISTRATION",
        "name": "Acrobat Pro Q1 Deal Registration",
        "description": "10% deal registration for qualifying partners on Acrobat Pro orders",
        "startDate": "2026-01-01T00:00:00Z",
        "endDate": "2026-04-10T23:59:59Z",
        "status": "ACTIVE",
        "qualification": { "baseOfferIds": ["65304578CA01A12"] },
        "outcomes": [{ "type": "PERCENTAGE_DISCOUNT", "discountValues": [{ "value": 10 }] }]
      }
    ]
  },
  "productRecommendations": [...]
}
```

The response fields for Instant Deal Registration discovery are:

| Field | Description |
|---|---|
| `discounts` | Container for discount-related recommendations. |
| `discounts.dealRegistrations` | Array of Instant Deal Registration opportunities. Returned only when `includeDealRegistrations` is `true` and opportunities are available. |
| `discounts.dealRegistrations[].category` | Always `DEAL_REGISTRATION`. |
| `discounts.dealRegistrations[].name` | Display name of the opportunity. |
| `discounts.dealRegistrations[].description` | Display description of the opportunity. |
| `discounts.dealRegistrations[].startDate` | Start of the availability window in ISO 8601 format. |
| `discounts.dealRegistrations[].endDate` | End of the availability window in ISO 8601 format. |
| `discounts.dealRegistrations[].status` | `ACTIVE` or `EXPIRED`. |
| `discounts.dealRegistrations[].qualification.baseOfferIds` | Offer IDs to which the opportunity applies. |
| `discounts.dealRegistrations[].outcomes` | Deal registration outcomes. Each item contains `type` and `discountValues`. |

For the complete endpoint details, see [Fetch Recommendations](../recommendations/apis.md#fetch-recommendations).

## Discover using the Flexible Discounts API

`DEAL_REGISTRATION` entries are not included in the Flexible Discounts API's default category set. To view any active deals, request them explicitly, as shown in the following example:

`GET <ENV>/v3/flex-discounts?categories=DEAL_REGISTRATION&market-segment=COM&country=US`

A sample response is as follows:

```json
{
  "flexDiscounts": [
    {
      "category": "DEAL_REGISTRATION",
      "name": "Acrobat Pro Q1 Instant Deal Registration",
      "description": "10% Instant Deal Registration credit for qualifying partners on Acrobat Pro orders",
      "startDate": "2026-01-01T00:00:00Z",
      "endDate": "2026-04-10T23:59:59Z",
      "status": "ACTIVE",
      "qualification": { "baseOfferIds": ["65304578CA01A12"] },
      "outcomes": [{ "type": "PERCENTAGE_DISCOUNT", "discountValues": [{ "value": 10 }] }]
    }
  ]
}
```

**Note:** The Instant Deal Registration details returned in the response use the same structure and fields as flexible discount details.

The entry does not contain `code` or `id`. For the complete endpoint contract, see [Get Flexible Discounts](../flex-discounts/apis.md#get-flexible-discounts).

## Verify Instant Deal Registration through Order APIs

Adobe evaluates and processes deal registrations by using the existing Order APIs:

| Operation | Method and endpoint | Integration details |
|---|---|---|
| [Preview Order](../order-management/order-scenarios.md#preview-an-order) | `POST /v3/customers/<customer-id>/orders` with `orderType: PREVIEW` | Use `fetch-price=true` to receive deal registration amounts for an applied line item. |
| [Create Order](../order-management/create-order.md) | `POST /v3/customers/<customer-id>/orders` with `orderType: NEW` | There is no need to add any codes to the request. |
| [Get Order](../order-management/get-order.md#get-details-of-a-specific-order) | `GET /v3/customers/<customer-id>/orders/<order-id>` | Use `fetch-price=true` to retrieve persisted deal registration amounts when available. |
| [Get Order History](../order-management/get-order.md#get-the-order-history-of-a-customer) | `GET /v3/customers/<customer-id>/orders` | Use `fetch-price=true` to retrieve deal registration amounts in order-history results when available. |
| [Preview Renewal](../order-management/order-scenarios.md#preview-renewal-orders) | `POST /v3/customers/<customer-id>/orders` with `orderType: PREVIEW_RENEWAL` | Adobe evaluates eligible renewal line items automatically. Use `fetch-price=true` for deal registration amounts. |
| [Return](../order-management/order-scenarios.md#return-or-cancellation-of-order) | `POST /v3/customers/<customer-id>/orders` with `orderType: RETURN` | Deal amount included in the return order. |

Instant Deal Registration applies independently of `flexDiscountCodes`. A partner-supplied flexible discount can be present on the same order request, but partners never submit an Instant Deal Registration identifier.

## Understand Instant Deal Registration response fields

Adobe evaluates and applies an eligible deal without a code or ID, regardless of whether the order request contains a flexible discount code. Pricing appears in the Get Order by ID and Get Order History APIs when the `fetch-price=true` query parameter is included in the request:

Sample request: `<ENV>/v3/customers/11233441871/orders/02938476?fetch-price=true`

Sample response when no flexible discount code is applied:

```json
{
  "referenceOrderId": "",
  "externalReferenceId": "9c6a5425-c6e0-480b-b075-7b439a6375d",
  "orderId": "5120008001",
  "customerId": "1005831871",
  "currencyCode": "USD",
  "orderType": "NEW",
  "status": "1000",
  "creationDate": "2026-03-17T11:42:29Z",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "65304578CA01A12",
      "quantity": 20,
      "subscriptionId": "",
      "status": "1000",
      "currencyCode": "USD",
      "remainingQuantity": 20,
      "proratedDays": 90,
      "isDealRegistered": true,
      "pricing": {
        "partnerPrice": 209.76,
        "discountedPartnerPrice": 209.76,
        "netPartnerPrice": 51.72,
        "lineItemPartnerPrice": 1034.40,
        "earnedDealRegPerUnit": 5.17,
        "earnedDealRegAmount": 103.40
      }
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 1034.40,
      "totalEarnedDealRegAmount": 103.40,
      "currencyCode": "USD"
    }
  ]
}
```

If a flexible discount is applied to the same line item, the flexible discount is applied first, and the deal registration amount is then calculated based on the discounted price. The following example shows a line item with both a flexible discount and a deal registration amount applied:

```json
{
  "referenceOrderId": "",
  "externalReferenceId": "9c6a5425-c6e0-480b-b075-7b439a6375d",
  "orderId": "5120008001",
  "customerId": "1005831871",
  "currencyCode": "USD",
  "orderType": "NEW",
  "status": "1000",
  "creationDate": "2026-03-17T11:42:29Z",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "65304578CA01A12",
      "quantity": 20,
      "subscriptionId": "",
      "status": "1000",
      "currencyCode": "USD",
      "remainingQuantity": 20,
      "proratedDays": 90,
      "isDealRegistered": true,
      "pricing": {
        "partnerPrice": 209.76,
        "discountedPartnerPrice": 199.76,
        "netPartnerPrice": 49.26,
        "lineItemPartnerPrice": 985.20,
        "earnedDealRegPerUnit": 4.93,
        "earnedDealRegAmount": 98.60
      },
      "flexDiscountCodes": ["BLACK_FRIDAY"],
      "flexDiscounts": [
        {
          "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
          "code": "BLACK_FRIDAY",
          "result": "SUCCESS"
        }
      ]
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 985.20,
      "totalEarnedDealRegAmount": 98.60,
      "currencyCode": "USD"
    }
  ]
}
```

The following table identifies the parameters that are applicable in the Instant Deal Registration context:

| Field | Availability | Description |
|---|---|---|
| `lineItems[].isDealRegistered` | `isDealRegistered` is included only when Instant Deal Registration was auto-injected for a line item: \<br /\> - `isDealRegistered` does not return: Instant Deal Registration was not injected or evaluated for the line item. \<br /\> - `isDealRegistered` returns and is `false`: Deal registration was attempted but qualification failed. \<br /\> - `isDealRegistered` returns `true`: Deal registration was attempted and qualification passes. |
| `lineItems[].pricing.earnedDealRegPerUnit` | Qualifying lines when `fetch-price=true` | Deal registration amount per unit. |
| `lineItems[].pricing.earnedDealRegAmount` | Qualifying line items when `fetch-price=true` | Total deal registration amount for the line item: `earnedDealRegPerUnit` multiplied by quantity. |
| `pricingSummary.totalEarnedDealRegAmount` | When `fetch-price=true` and at least one line item qualifies | Sum of `earnedDealRegAmount` across qualifying line items. |

**Note:** When `isDealRegistered` is `false`, the amount fields are omitted. If `isDealRegistered` is absent, Instant Deal Registration was not applied to the line item.



The deal registration amount is additive and remains separate from `lineItemPartnerPrice`, `discountedPartnerPrice`, and `totalLineItemPartnerPrice`. On Return orders, `earnedDealRegPerUnit`, `earnedDealRegAmount`, and `totalEarnedDealRegAmount` are not represented as negative values.
