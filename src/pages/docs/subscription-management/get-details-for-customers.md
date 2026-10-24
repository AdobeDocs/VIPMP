# Get details of a customer's subscriptions

Use the `GET /v3/customers/<customer-id>/subscriptions` API endpoint to get details of a customer's subscriptions.

## Request header

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

## Request body

None.

## Response body

```json
{
  "totalCount": 2,
  "items": [
    { Subscription resource },
    { Subscription resource }
  ]
}
```

Example:

```json
{
  "totalCount": 1,
  "items": [
    {
      "subscriptionId": "43b889db7b4e7aa2d42b54b9813eebNA",
      "offerId": "65322651CA01A12",
      "currentQuantity": 10,
      "usedQuantity": 4,
      "autoRenewal": {
        "enabled": true,
        "renewalQuantity": 10,
        "renewalCode": "MOQ_100",
        "flexDiscountCodes": [
                    "SUMMER_SALE_12"
                ]
      },
      "creationDate": "2024-10-23T10:23:11Z",
      "renewalDate": "2025-10-23",
      "status": "1000",
      "currencyCode": "USD",
      "links": {
        "self": {
          "uri": "/v3/customers/D1005038400/subscriptions/43b889db7b4e7aa2d42b54b9813eebNA",
          "method": "GET",
          "headers": []
        }
      }
    }
  ],
  "links": {
    "self": {
      "uri": "/v3/customers/D1005038400/subscriptions",
      "method": "GET",
      "headers": []
    }
  }
}
```

**Notes:**

- No parameters or filters are available at this time.
- The `renewalCode` parameter will be available in the response only if the customer receives a High Growth Offer on the next Anniversary Date.
- During a [country currency change](../manage-pricing/index.md#track-subscriptions-when-the-currency-changes), two active subscriptions can exist for the same SKU. Track each `subscriptionId` and `currencyCode` separately, including deployment context for global customers. Do not discard the old-currency record before it expires.

## HTTP status codes

| Status code | Description                                |
| ----------- | ------------------------------------------ |
| 200         | Subscription details successfully returned |
| 400         | Bad request                                |
| 401         | Invalid Authorization token                |
| 403         | Invalid API Key                            |
| 404         | Invalid customer ID                        |
