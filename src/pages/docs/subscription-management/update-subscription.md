# Update subscription

You can modify the auto-renewal configuration and add a flexible discount to the subscription by using the `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>` endpoint.

| Endpoint | Method|
|---|---|
|`/v3/customers/<customer-id>/subscriptions/<subscription-id>`| PATCH|

## Usage instructions

Ensure that you are aware of the following before updating the auto-renewal configuration:

- The `autoRenewal` preferences can only be updated for an active subscription.
- The `autoRenewal` object is only evaluated at the renewal time, which is defined in the `cotermDate` parameter.
- If the `renewalQuantity` is higher than `currentQuantity` at the renewal time, then the additional licenses will be included in renewal. If it is lower, then licenses will be removed at renewal.
- If the desired `autoRenewal` preference is to return to the default behavior without explicit renewal quantity, the `renewalQuantity` variable must be set to ‘null’.
- The `flexDiscountCodes` parameter indicates the flexible discounts applicable for the subscription. For more information, see [Update a subscription with flexible discount codes](#update-a-subscription-with-flexible-discount-code) and [Remove flexible discount from a subscription](#remove-a-flexible-discount-from-a-subscription).

- The following three states are possible for autorenewal:
  - **Disabled**
    \<br /\> You can achieve this by setting `enabled` to `false`.
  - **Enabled with renewal quantity** \<br /\>If the `renewalQuantity` has been explicitly set, the `renewalQuantity` will remain unchanged by additional orders or cancellations until subscription becomes inactive.
  - **Enabled without explicit renewal quantity** \<br /\>In this case, all active licenses in the subscription will be renewed. The `renewalQuantity` will still be returned with the number of purchased licenses.

**Restrictions:**

- Maximum renewalQuantity:

  - 10,000 for Team products
  - 200,000 for Enterprise products (not supported yet)

## Request header

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

## Request Body

`CompanyProfile` object with optional `externalReferenceId`:

```json
{
  "autoRenewal": {
    "enabled": true,
    "renewalQuantity": 7,
    "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
  }
}
```

## Response body

```json
{
  "subscriptionId": "cc8efgh8bc4354a4b38006c87804ceNA",
  "currentQuantity": 0,
  "offerId": "65304470CA01012",
 
  "autoRenewal": {
    "enabled": true,
    "renewalQuantity": 7,
    "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
  },
  "renewalDate": "2026-05-20",
  "creationDate": "2025-10-20T22:49:55Z",
  "status": "1009",
 
  "links": {
    "self": {
      "uri": "/v3/customers/P1005053489/subscriptions/cc8efgh8bc4354a4b38006c87804ceNA",
      "method": "GET",
      "headers": []
    }
  }
}
```

**Note:** Any contacts specified in this call will receive the admin welcome email. This can be resent if an end-user did not receive it.

## HTTP status codes

| Status code | Description                         |
| ----------- | ----------------------------------- |
| 200         | AutoRenewal updated                 |
| 400         | Bad request                         |
| 401         | Invalid Authorization token         |
| 403         | Invalid API Key                     |
| 404         | Invalid customer or subscription ID |

The following sections explain how to modify a subscription with Flexible Discount Codes and High Growth Offers.

## Update a subscription with flexible discount code

Use the `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>` API with `flexDiscountCodes` in the request to update a subscription with the corresponding flexible discount.

**Note:** Flexible discount codes are not validated while updating a subscription. Verification of customer eligibility occurs exclusively through the Preview Renewal API.

#### Request

The `flexDiscountCodes` parameter indicates the flexible discounts applicable for the subscription.

```json
{
  "autoRenewal": {
    "enabled": true, // If Auto Renew is OFF, it must be turned ON to apply PromoCode. If it is already ON, this field is OPTIONAL.
    "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
  }
}
```

#### Response

```json
{
  "subscriptionId": "8675309",
  "currentQuantity": 10,
  "usedQuantity": 2,
  "offerId": "65304470CA01012",
 
  "autoRenewal": {
    "enabled": true,
    "renewalQuantity": 5,
    "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
  },
  "renewalDate": "2020-05-20",
  "creationDate": "2019-05-20T22:49:55Z",
  "deploymentId": "12345",
  "currencyCode": "USD",
  "status": "1000",
 
  "links": {
    "self": {}
  }
}
```

## Remove a flexible discount from a subscription

Use the `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>` with the query parameter `reset-flex-discount-codes=true` to remove a flexible discount from a subscription

#### Request

- **Request URL:** `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>?reset-flex-discount-codes=true`
- **Request body:** None.

#### Response

```json
{
  "subscriptionId": "8675309",
  "currentQuantity": 10,
  "usedQuantity": 2,
  "offerId": "65304470CA01012",
 
  "autoRenewal": {
    "enabled": true,
    "renewalQuantity": 5
  },
  "renewalDate": "2020-05-20",
  "creationDate": "2019-05-20T22:49:55Z",
  "deploymentId": "12345",
  "currencyCode": "USD",
  "status": "1000",
 
  "links": {
    "self": {}
  }
}
```

## Update a subscription with High Growth Offers

Use the `PATCH /v3/customers/{customer-id}/subscriptions/{sub-id}?reset-renewal-code=false` API to update the renewal preferences for the customer's subscription with the High Growth Offer details.

For more information, see [Manage High Growth Offer using APIs](../customer-account/high-growth-apis.md).
