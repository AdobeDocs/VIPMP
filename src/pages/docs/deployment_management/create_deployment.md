# Create deployments

Use the `POST /v3/customers/<customer-id>/deployments` endpoint to create a deployment resource for a customer.

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

Deployment `companyProfile` with only address information:

```json
{
  "companyProfile": {
    "address": {
      "country": "US",
      "region": "CA",
      "city": "San Jose",
      "addressLine1": "200 Fairmont Ave",
      "addressLine2": "Apt 123",
      "postalCode": "95110-1234",
      "phoneNumber": "800-123-4567"
    }
  }
}
```

## Response body

Deployment resource:

```json
{
  "deploymentId": "345434543",
  "companyProfile": {
    "address": {
      "country": "US",
      "region": "CA",
      "city": "San Jose",
      "addressLine1": "200 Fairmont Ave",
      "addressLine2": "Apt 123",
      "postalCode": "95110-1234",
      "phoneNumber": "800-123-4567"
    }
  },
  "creationDate": "2019-05-02T22:49:52Z",
  "status": "1000",
  "links": {
    "self": {
      "uri": "/v3/customers/5556667778/deployments/345434543",
      "method": "GET",
      "headers": []
    }
  }
}
```

## HTTP status codes

| Status code | Description                 |
| ----------- | --------------------------- |
| 201         | Deployment created          |
| 400         | Bad request                 |
| 401         | Invalid Authorization token |
| 403         | Invalid API Key             |
| 404         | Invalid customer ID         |
