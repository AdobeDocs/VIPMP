# Create order

Use the `POST /v3/customers/<customer-id>/orders` endpoint to place an order for a customer. Read more about [scenarios where an order is created](order-scenarios.md).

## Assumptions

Ensure that you are aware of the following before creating an order:

- `orderType` is required in the Create Order request.
  - Possible values are: NEW, RETURN, PREVIEW, PREVIEW_RENEWAL, or RENEWAL
  - See [Order Scenarios](./order-scenarios.md) for request and response samples for each order type.
- `subscriptionId` is mandatory in lineitems for:
  - `orderType` RENEWAL
  - `orderType` PREVIEW_RENEWAL if lineitems are present
- `referenceOrderId` is required for RETURN orders and should not be included for other order types.
- For `RETURN` orders that reference a `NEW` or `RENEWAL` order, you can now return a quantity that is less than the original line item quantity, within 14 days of the original order. The requested quantity is validated against the line item’s current `remainingQuantity` value on the original order. See [Return or cancellation of order](order-scenarios.md#return-or-cancellation-of-order) for details on eligibility, exclusions, and error codes.
- `currencyCode` should now be sent at the lineItem-level instead of order level.
  - For backwards compatibility, `currencyCode` can still be sent at the order level.
- The `discountCode` is applicable only to High Volume Discount customers who have migrated from VIP to VIP MP. You can use the discount code only if their discount level in VIP is between 17 and 22.
- `flexDiscountCodes` can be used in the request to apply Flexible Discounts for customers who meet the eligibility criteria. For additional details, see [Managing Flexible Discounts](../flex-discounts/apis.md).

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

Order resource without read-only fields:

```json
{
  "externalReferenceId": "759", // (optional)
  "currencyCode": "USD", // (to be deprecated, use lineItem currencyCode)
  "orderType": "NEW | RETURN | PREVIEW | PREVIEW_RENEWAL | RENEWAL",
  "referenceOrderId": "", // (for returns only)
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "currencyCode": "USD",
      "deploymentId": "12345",
      "discountCode": "HVD_L18_PRE",
    },
  ],
}
```

## Response body

```json
{
  "externalReferenceId": "759",
  "orderId": "0123456789",
  "customerId": "9876543210",
  "orderType": "NEW",
  "referenceOrderId": "",
  "currencyCode": "USD",
  "creationDate": "2019-05-02T22:49:54Z",
  "status": "1002",
  "source": "API",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "subscriptionId": "",
      "status": "1002",
      "currencyCode": "USD",
      "deploymentId": "12345"
    }
  ],
  "links": {
    "self": {
      "uri": "/v3/customers/9876543210/orders/0123456789",
      "method": "GET",
      "headers": []
    }
  }
}
```

**Notes:**

- See [Order Scenarios](./order-scenarios.md) for request and response samples for each order type.
- See [Order resource](../references/resources.md#order-top-level-resource) for descriptions for each request and response parameter.

## HTTP status codes

| Status code | Description                 |
| ----------- | --------------------------- |
| 201         | Order created               |
| 400         | Bad request                 |
| 401         | Invalid Authorization token |
| 403         | Invalid API Key             |
| 404         | Invalid customer ID         |
