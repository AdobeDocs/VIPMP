# Update a reseller account

Use the `PATCH /v3/resellers/<reseller-id>` API endpoint to update a reseller account.

## Assumptions

* Update replaces existing data.
* Non-editable fields (in red) need to be passed in but need to match the original values. Otherwise, an error is returned.
* `externalReferenceId` can now be updated.

## Request header

|Parameter | Description |
|--|--|
| X-Request-Id | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.|
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call.|
| Accept | **Required**. Specifies the response type. Must be "application/json" for proper usage. |
| Content-Type | **Required**. Specifies the request type. Must be "application/json" for proper usage. |
| Authorization | **Required**. Authorization token in the form "Bearer <token>" |
| X-Api-Key | **Required**. The API Key for your integration|

## Request body

CompanyProfile for reseller with optional externalReferenceId:

```bash
{
"externalReferenceId" : "999",
"companyProfile" : {
"companyName" : "Fairmont",
"preferredLanguage" : "en-US",
"marketSegments" : ["COM", "EDU"],
"address" : {
"country" : "US",
"region" : "CA",
"city" : "San Jose",
"addressLine1" : "200 Fairmont Ave",
"addressLine2" : "Apt 123",
"postalCode" : "95110-1234",
"phoneNumber" : "800-123-4567"
},
"contacts" : [
{
"firstName" : "Mickey",
"lastName" : "Mouse",
"email" : "mickey@mouse.com",
"phoneNumber" : "408-123-4567"
}
]
```

## Response body

```bash
{ Reseller resource }
```

### HTTP status codes

| Status code | Description |
|--|--|
|20o|Reseller account successfully updated|
|400 | Bad request|
|401|Invalid Authorization token|
|403| Invalid API Key|
|404|Ivalid reseller ID|