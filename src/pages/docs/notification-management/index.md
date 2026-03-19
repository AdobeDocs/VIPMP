# Manage notifications

Partners can fetch different types of resources based on the notification type sent in the request. The query parameters of the API can also vary based on the notification type.

Currently, Adobe supports only `LICENSES_PENDING_PARTNER_ORDER` notification type and partner needs to pass the `reseller-id` query parameter for this notification type.

| Notification type              | Supported query parameters                                     | Description                                                                                                                                                                                                              |
|--------------------------------|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LICENSES_PENDING_PARTNER_ORDER | 1. reseller-id= 100022361 \<br /\> 2. limit=10 \<br /\> 3. offset=0 | The `LICENSES_PENDING_PARTNER_ORDER` notification type returns all customers under a given reseller who have at least one license created within the last 7 days for which order has not been placed yet by partner. |

## Get licenses that are pending partner order notification

Use the `GET /v3/notifications?notification-type=LICENSES_PENDING_PARTNER_ORDER&reseller-id=<reseller-id>&limit=10&offset=0` endpoint to get notifications for licenses that are pending partner orders.

### Request header

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

### Request body

None.

### Response body

```json
{
  "limit": 10,
  "offset": 0,
  "totalCount": 1,
  "count": 1,
  "items": [
    {
      "customerId": "1005379198",
      "resellerId": "1000177552",
      "notificationType": "LICENSES_PENDING_PARTNER_ORDER",
      "links": {
        "self": {
          "uri": "/v3/customers/1005379198/open-acquisitions",
          "method": "GET",
          "headers": []
        }
      }
    }
  ],
  "links": {
    "self": {
      "uri": "/v3/notifications?notification-type=
      LICENSES_PENDING_PARTNER_ORDER&
      reseller-id=1000177552&limit=10&offset=0",
      "method": "GET",
      "headers": []
    }
  }
}
```

### HTTP status codes

| Status code | Description                                 |
| ----------- | ------------------------------------------- |
| 200         | Successfully returned notification resource |
| 400         | Bad request                                 |
| 401         | Invalid Authorization token                 |
| 403         | Invalid API Key                             |

## See also

- [Get licenses pending partner order](../customer-account/get-licenses.md)
