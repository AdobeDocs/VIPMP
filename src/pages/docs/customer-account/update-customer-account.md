# Update customer account

You can modify certain attributes of a customer account by using the `PATCH /v3/customers/<customer-id>` endpoint.

## Contact management rules

1. Minimum contact requirement
   - Each customer must have at least one contact maintained through the API.
2. Sync with Admin Console
   - Every contact created or updated via the API will automatically reflect in the customer’s Admin Console as a System Admin.
3. Additional admins through Admin Console
   - Customers can add more admins directly in the Admin Console to manage their contract.
   - These additional admins will not sync back to the API.
4. Contact removal
   - Contacts can be removed through the API by omitting them from the list of contacts. However, once a contact has synced to the Admin Console, it will remain there unless the customer manually removes it in the Admin Console.
5. Contact updates
   - Email ID and phone number of a contact can be updated through the Update Customer API.
   - Contact name cannot be updated through API.
   - To change a contact’s name (like updating the company name), a support ticket must be raised along with a business justification.

## Sending a welcome email

Any contacts specified in the `Update Customer` call will receive the admin welcome email. If an end-user does not receive it, the partner should retry the `Update Customer` call, ensuring the admin contact’s email is included in the request.

## Usage instructions

Ensure that you are aware of the following before updating a customer account:

- Do not alter values for the mandatory fields such as `companyName, country,` and `region`. Otherwise, an error is returned.
- The `contacts` section specifies the admins for the customer's account.
  - Any contacts that are removed will remain admins. To remove admins, a customer admin must use the Adobe Admin Console.
- Reseller must be enabled for the new market segment.
- Customer `externalReferenceId` may now be changed.
- Use either `commitmentRequest` or `recommitmentRequest` in the request to:

  - Request new 3YC for existing customers.
  - Request 3YC quantity increase for customers with existing commitment.
  - Request 3YC recommitment for customers with existing commitment.
    - Only allowed if the customer has a `COMMITTED` status for the commitment.

**Note:** Details of the request and response parameters are available in the [Resources](../references/resources.md#customer-top-level-resource) section of this documentation.

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
  "externalReferenceId": "99999",
  "globalSalesEnabled": true,
  "linkedMembership": {
    "type": "STANDARD",
    "name": "This is the Group Created for 1005516130"
  },
  "companyProfile": {
    "companyName": "Fairmont",
    "preferredLanguage": "en-US",
    "marketSegment": "COM",
    "address": {
      "country": "US",
      "region": "CA",
      "city": "San Jose",
      "addressLine1": "200 Fairmont Ave",
      "addressLine2": "Apt 123",
      "postalCode": "95110-1234",
      "phoneNumber": "800-123-4567"
    },
    "contacts": [
      {
        "firstName": "Mickey",
        "lastName": "Mouse",
        "email": "mickey@mouse.com",
        "phoneNumber": "408-123-4567"
      }
    ]
  }
}
```

## Response body

```json
{
  "externalReferenceId": "342",
  "customerId": "9876543210",
  "resellerId": "5556667778",
  "globalSalesEnabled": false,
  "companyProfile": {
    "companyName": "Fairmont",
    "preferredLanguage": "en-US",
    "marketSegment": "EDU",
    "marketSubSegments": ["K_12"],
    "address": {
      "country": "US",
      "region": "CA",
      "city": "San Jose",
      "addressLine1": "200 Fairmont Ave",
      "addressLine2": "Apt 123",
      "postalCode": "95110-1234",
      "phoneNumber": "800-123-4567"
    },
    "contacts": [
      {
        "firstName": "Mickey",
        "lastName": "Mouse",
        "email": "mickey@mouse.com",
        "phoneNumber": "408-123-4567"
      }
    ]
  },
  "discounts": [
    {
      "offerType": "LICENSE",
      "level": "02"
    }
  ],
  "cotermDate": "2020-05-02",
  "creationDate": "2019-05-02T22:49:52Z",
  "status": "1000",
  "linkedMembership": {
    "id": "51001315",
    "name": "This is the Group Created for 1005513636",
    "type": "STANDARD",
    "linkedMembershipType": "OWNER",
    "creationDate": "2024-07-17T03:47:35"
  },
  "links": {
    "self": {
      "uri": "/v3/customers/9876543210",
      "method": "GET",
      "headers": []
    }
  }
}
```

## HTTP status codes

| Status code | Description                  |
| ----------- | ---------------------------- |
| 200         | Account successfully updated |
| 400         | Bad request                  |
| 401         | Invalid Authorization token  |
| 403         | Invalid API Key              |
| 404         | Invalid customer ID          |
