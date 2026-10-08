# Create customer account

Before placing an order, a reseller needs to create a customer account by using the Create Customer Account API:

| Endpoint | Method|
|---|---|
|/v3/customers| POST|

The API returns the customer resource with a link to the [Get Customer Account Details](get-customer-account.md) endpoint.

## Usage instructions

Ensure that you are aware of the following before creating a customer account:

* Customer account ID is created by this API and returned synchronously.
* The customer payment instrument is managed on the partner marketplace.
* `cotermDate` is usually calculated when a customer’s first order is placed.
  * Most subscriptions, except for Stock Credit Packs, will end or renew on the `cotermDate`.
* Use `cotermDate` in your request to create an extended-term customer. For all market segments except EDU, the `cotermDate` can be set to a date that is more than one to three years from the current date. For EDU customers, the term can be extended up to four years from the current date. Extended-term customers are not eligible to enroll in 3YC until they enter the last year of their extended term. See [Extended-term customers and 3YC enrollment](three-year-commit.md#extended-term-customers-and-3yc-enrollment) for details.
* Use `externalReferenceId` to pass it to the marketplace’s Customer ID.
  * Optional and does not need to be unique.
* `Contacts` specifies admins for the customer's account and receive an admin welcome email.
  * Contact names for existing email addresses cannot be changed. If you send a different name for an existing email, that name will be returned in the synchronous response, but it will not persist in the system. The existing first and last name of the contact will be used and returned in any GET calls.
* A successful _Create Customer_ request returns the customer with status 1002 (Pending) while final validation runs asynchronously. This validation includes account screening. Depending on the outcome, the account may become Active, Inactive, 1023 (Screening), or 1022 (Sanctioned). For more information, see [Account screening statuses](#account-screening-statuses).

  Customers in **Pending** status can place orders but cannot be updated through the [Update Customer](./update-customer-account.md) API until the account becomes active or inactive. All orders placed for a pending customer fail if the customer becomes inactive.
* `discounts` contains a list of objects with the customer’s discount levels for each `offerType`.
* Customers can be created with a specified market segment.
  * Reseller must be enabled for that market segment.
  * If no market segment is specified in the request, the customer becomes part of the commercial (COM) market segment by default.
* The `benefits` array must include the `LARGE_GOVERNMENT_AGENCY` indicator for LGA customers.

## Account screening statuses

As part of the asynchronous validation process, every new customer account is screened against sanctions and watchlists before it can transact. This screening helps identify sanctioned parties before any orders are placed. The account `status` indicates the screening outcome:

| Status | Meaning |
|--------|---------|
| 1002 (Pending) |Screening has cleared successfully. The account is still undergoing the remaining account creation validations. |
| 1023 (Screening) | The account is awaiting a screening adjudication decision. No action is required from the partner while the account remains in this status. |
| 1022 (Sanctioned) | The account has been flagged as a sanctioned party and is blocked from proceeding. |

Use the [Get Customer Account Details](get-customer-account.md) endpoint to monitor the account and retrieve the final `status` after screening is complete.

## Request header

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

**Note:** For details of the request and response parameters, see [Request and response parameters](#request-and-response-parameters).

## Request Body

Customer resource without read-only fields:

```json
{
  "resellerId": "5556667778",
  "externalReferenceId": "342",
  "companyProfile": {
    "companyName": "Fairmont",
    "preferredLanguage": "en-US",
    "marketSegment": "EDU",
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
        "firstName": "Donald",
        "lastName": "Duck",
        "email": "donald@duck.com",
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
        "firstName": "Donald",
        "lastName": "Duck",
        "email": "donald@duck.com",
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
  "cotermDate": "",
  "creationDate": "2019-05-02T22:49:52Z",
  "status": "1002",
  "links": {
    "self": {
      "uri": "/v3/customers/9876543210",
      "method": "GET",
      "headers": []
    }
  }
}
```

## Request and response parameters

### Customer (top-level resource)

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|externalReferenceId | String \<br /\>Optional| Marketplace’s ID for customer. ID does not need to be unique.| Max: 35 characters|
|customerId (read only)| String | Unique ID for customer created upon account creation| Max: 40 characters|
|resellerId | String | ID of reseller tied to customer | Max: 40 characters|
|globalSalesEnabled  | String | Global status of a customer  | Max: 40 characters|
|tags | String |Special label on the customer. Example: _HVD_MIGRATED_CUSTOMER_ | Max: 40 characters|
|linkedMembership  | `linkedMembership` resource  | Information about the linked membership  | |
|companyProfile | `CompanyProfile` resource| Information about the customer | |
|discounts | `discounts` resource| Details of the discount applicable to the customer, including the discount level.  | |
|benefits | `benefits` resource| Details of the benefits applied to the customer account and its corresponding status. For example, the type parameter indicates LARGE_GOVERNMENT_AGENCY if the customer is an LGA customer. | |
|cotermDate (read only)| String (date) | Date that renewal order is to be placed. Should be one year after the first order is provisioned (if a 1-yr term) and gets updated upon each renewal order.| 10 characters|
|creationDate (read only)| String (datetime)| Date and time of account creation in UTC| |
|status (read only)| String | Status code of customer account. See [Account screening statuses](#account-screening-statuses) for the screening-related values. | 4 characters|
|links (read only)| **Links** resource | Deep links to get customer account details| |

For more details, refer to [Resources and fields](../references/resources.md#customer-top-level-resource).

### HTTP status codes

| Status code | Description                 |
| ----------- | --------------------------- |
| 201         | Customer account created    |
| 400         | Bad request                 |
| 401         | Invalid Authorization token |
| 403         | Invalid API Key             |
| 404         | Invalid reseller ID         |
