
# Manage Flexible Discounts using APIs

You can use the following APIs to get details of available flexible discounts and apply them while placing an order or creating or modifying a subscription:

- [Get Flexible Discounts](#get-flexible-discounts)
- [Create Order and Preview Order](#create-order-and-preview-order)
- [Get Order](#get-order)
- [Get Order History of a customer](#get-order-history-of-a-customer)
- [Preview Renewal](#preview-renewal-with-flexible-discount-code)
- [Apply flexible discounts when placing manual renewal orders](#apply-flexible-discounts-when-placing-manual-renewal-orders)
- [Create a subscription with flexible discount](#create-a-scheduled-subscription-with-flexible-discount)
- [Update subscription with a flexible discount code](#update-a-subscription-with-flexible-discount-code)
- [Remove a flexible discount from a subscription](#remove-a-flexible-discount-from-a-subscription)

## Get Flexible Discounts

Use the `GET Flexible Discounts` API to fetch flexible discounts that are applicable to a product:

| Endpoint           | Method |
|--------------------|--------|
| /v3/flex-discounts | GET    |

### Request

Sample Request URL: `GET <ENV>/v3/flex-discounts?market-segment=COM&country=US`

### Query parameters  

**Note:** Request query parameters such as Market segment and country are validated against the Partner contract data. You can also use other query parameters that are listed in the following table:

| Parameter          | Type             | Mandatory | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Range/Limits       |
|--------------------|------------------|-----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| categories           | String           | No        | Filter promotions by category. Possible values are: \<br /\>\<br /\>  - **STANDARD:** Represents all regular Flexible Discounts available in the VIP Marketplace. These discounts can be applied to eligible orders as long as they meet the defined qualification criteria. These discounts are not restricted to new customers or first-time purchases. \<br /\> **Example:** Seasonal discounts like Black Friday or volume-based discounts for enterprise accounts.\<br /\> \<br /\> - **INTRO:** Represents Introductory Offers designed to help acquire new customers or encourage existing customers to adopt products that are new to their subscription. These discounts are typically limited to a customer’s first purchase or first-time use of a specific product.  \<br /\> **Example:** Launch offer for Adobe Express at $39.99 for new customers. \<br /\> \<br /\>**Note:**  If not specified, both Intro and Standard discounts will be returned. |                    |
| market-segment     | String           | Yes       | Get flexible discounts by market segment. Example: "COM", "EDU".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 3 characters       |
| country            | String           | Yes       | Get flexible discounts by country using the ISO 3166-1 alpha-2 code. Example: "US", "IN".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | 2 or 3 characters  |
| offer-ids          | Array of strings | No        | Provide a comma-separated list of Offer IDs to retrieve applicable flexible discounts. Example: 65322535CA04A12, 86322535CA04A12                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                    |
| flex-discount-id   | String           | No        | Retrieve a flexible discount by its unique ID. This endpoint returns a single, unique flexible discount object. \<br /\> If flex-discount-id query parameter is provided in the request, other non-mandatory params cannot be provided in the same request.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | Max: 40 characters |
| flex-discount-code | String           | No        | Filter promotions by code. Examples: "DIWALI", "BLACK_FRIDAY".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |                    |
| start-date         | String (date)    | No        | Filter flexible discounts that were available on or after the specified date and time. This date can be without timestamp or with timestamp, for example, “2025-05-02" or "2025-05-02T22:49:54Z. Dates with timestamps are only accepted in ISO-8601 format with "Zulu" (UTC) time zone. This is the same format that all dates and times are in Adobe Commerce Partner API (CPAI) responses.                                                                                                                                                                                                                                                                                                                                                                                                                                                          |                    |
| end-date           | String (date)    | No        | Filter flexible discounts that were available on or before this moment in time. This date can be without timestamp or with timestamp, for example, “2025-05-02" or "2025-05-02T22:49:54Z. Dates with timestamps are only accepted in ISO-8601 format with "Zulu" (UTC) time zone. This is the same format that all dates and times are in Adobe Commerce Partner API (CPAI) responses.                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |                    |
| limit              | Integer          | No        | Specify the number of items to be returned in the response. Default: 20, Max: 50.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                    |
| offset             | Integer          | No        | Set the start offset for the result items. Default: 0                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |                    |

#### **Sample request URLs**

- Sample request URL with all query parameters: `<ENV>/v3/flex-discounts?categories=STANDARD&market-segment=COM&country=US&offer-ids=65322535CA04A12,86322535CA04A12&flex-discount-code=BLACK_FRIDAY&start-date=2025-03-01&end-date=2025-03-31&limit=20&offset=0`
- Sample request URL where flexible discount ID is used:  `<ENV>/v3/flex-discounts?country=US&market-segment=COM&flex-discount-id=55555555-1533-4564-ade1-cd6946a97f29`

### Request Header  

See [Headers](../references/api-headers.md) section.  

### Request Body  

None.

### Response

```json
{
  "limit": 20,
  "offset": 0,
  "count": 3,
  "totalCount": 3,
  "flexDiscounts": [
    {
      "id": "55555555-313b-476c-9d0b-6a610d5b91e0",   // INTRO - Fixed Price
      "category": "INTRO",
      "code": "INTRO-PHSP",
      "name": "Intro Discount - Photoshop",
      "description": "Intro Discount - Photoshop - 15.99", 
      "startDate": "2025-11-30T23:59:59Z", 
      "endDate": "2026-12-31T23:59:59Z", 
      "qualification": {
        "baseOfferIds": [
          "11083117CA01A12"
        ]
      },
      "outcomes": [
        {
          "type": "FIXED_PRICE",
          "discountValues": [
            {
              "country": "US",
              "currency": "USD",
              "value": 15.99
            }
          ]
        }
      ]
    },
    {
      "id": "55555555-313b-476c-9d0b-6a610d5b91e0",   // STANDARD - Fixed Discount
      "category": "STANDARD",
      "code": "BLACK_FRIDAY",
      "name": "BLACK_FRIDAY",
      "description": "BLACK_FRIDAY - 10 USD off PHSP",
      "startDate": "2025-11-01T23:59:59Z", 
      "endDate": "2025-12-31T23:59:59Z",
      "qualification": {
        "baseOfferIds": [
          "11083117CA01A12"
        ]
      },
      "outcomes": [
        {
          "type": "FIXED_DISCOUNT",
          "discountValues": [
            {
              "country": "US",
              "currency": "USD",
              "value": 10.00
            }
          ]
        }
      ]
    },
    {
      "id": "55555555-313b-476c-9d0b-6a610d5b91e0",   // STANDARD - Percentage Discount
      "category": "STANDARD",
      "code": "NEW YEAR",
      "name": "NEW YEAR",
      "description": "NEW YEAR - 20% off on all Products",
      "startDate": "2025-12-01T23:59:59Z", 
      "endDate": "2026-01-31T23:59:59Z",
      "outcomes": [
        {
          "type": "PERCENTAGE_DISCOUNT",
          "discountValues": [
            {
              "value": 20
            }
          ]
        }
      ]
    }
  ],
  "links": {
    "self": {
      "uri": "/v3/flex-discounts?customer-id=<>&categories=STANDARD,INTRO&limit=20&offset=20",
      "method": "GET",
      "headers": []
    },
    // next link will be present only if the next resource is present
    "next": {
      "uri": "/v3/flex-discounts?customer-id=<>&categories=STANDARD,INTRO&limit=20&offset=40",
      "method": "GET",
      "headers": []
    },
    // prev link will be present only if a previous resource is present
    "prev": {
      "uri": "/v3/flex-discounts?customer-id=<>&categories=STANDARD,INTRO&limit=20&offset=0",
      "method": "GET",
      "headers": []
    }
  }
}
```

### Response parameters

| Parameter     | Type   | Description                                                               |
|---------------|--------|---------------------------------------------------------------------------|
| limit         | String | Number of items to be included in the current response.                   |
| offset        | String | Offset applied for the current response.                                  |
| count         | String | The count of flexible discount entities included in the current response. |
| totalCount    | String | Total count of flexible discount entities, if no limit was applied.       |
| flexDiscounts | Object | Provides details of the available flexible discounts.                     |

#### flexDiscounts object

| Parameter                              | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|----------------------------------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| category                               | String           | Filter promotions by category. Possible values are: \<br /\>\<br /\>  - **STANDARD:** Represents all regular Flexible Discounts available in the VIP Marketplace. These discounts can be applied to eligible orders as long as they meet the defined qualification criteria. These discounts are not restricted to new customers or first-time purchases. \<br /\> **Example:** Seasonal discounts like Black Friday or volume-based discounts for enterprise accounts.\<br /\> \<br /\> - **INTRO:** Represents Introductory Offers designed to help acquire new customers or encourage existing customers to adopt products that are new to their subscription. These discounts are typically limited to a customer's first purchase or first-time use of a specific product.  \<br /\> **Example:** Launch offer for Adobe Express at $39.99 for new customers. |
| id                                     | String           | A unique system-generated identifier for a flexible discount. It should be used to retrieve or reference a specific flexible discount, especially when accessing detailed metadata of a flexible discount. It is also included in the order response.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| name                                   | String           | Name of the flexible discount.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| description                            | String           | Description of the flexible discount. It also provides additional details about the eligibility criteria for the flexible discount. For example, "Exclusive 20% off for Teams customers of CC All Apps in US"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| code                                   | String           | A readable identifier used to apply a flexible discount during order placement. This code will appear on the invoice. For example, a discount code like BLACK_FRIDAY may be reused across different years such as 2025 and 2026. However, to ensure consistency and prevent duplication, only one active flexible discount can exist for a given code at any point in time.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| endDate                                | String (Date)    | The final date when the flexible discount can be used. Dates with timestamps are only accepted in ISO-8601 format with "Zulu" (UTC) time zone. This is the same format that all dates and times are in Adobe Commerce Partner API (CPAI) responses.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| startDate                              | String (Date)    | The date from which  the flexible discount can be used. Dates with timestamps are only accepted in ISO-8601 format with "Zulu" (UTC) time zone. This is the same format that all dates and times are in Adobe Commerce Partner API (CPAI) responses.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| status                                 | String Enum      | Status of flexible discount. Possible values: ACTIVE, EXPIRED                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| qualification                          | Object           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| qualification.baseOfferIds             | Array of strings | List of Base Offer IDs of products eligible for flexible discount. Example: ["Offer ID 1", "Offer ID 2"] \<br /\>**Note**: The list of base Offer IDs will be empty if the flexible discount applies to all products.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| outcomes[]                             | Array of objects |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| outcomes[] → type                      | String           | Type of flexible discount. Possible values are: PERCENTAGE_DISCOUNT, FIXED_DISCOUNT, FIXED_PRICE                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| outcomes[].discountValues[]            | Array of objects |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| outcomes[].discountValues[] → country  | String           | Country Code: ISO 3166-1 alpha-2 code. Example: "US", "IN". Note: Not applicable for PERCENTAGE_DISCOUNT type.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| outcomes[].discountValues[] → currency | String           | Currency Code: ISO 4217. Example: "USD", "EUR". Note: Not applicable for PERCENTAGE_DISCOUNT type.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| outcomes[].discountValues[] → value    | Integer          | The discount value. For example, if the value is 15: 15% discount is applicable if the type is PERCENTAGE_DISCOUNT. A discount of 15 USD, or any currency provided in the response, is applicable for the FIXED_DISCOUNT discount type.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |

### Sample Response (Failure)

On failure, the response includes the appropriate HTTP status code based on the reason or type of failure. For example, if the API key is invalid, the response has HTTP 403 (Forbidden):

```json
{ "code": "4115", "message": "Api Key is invalid or missing" }
```

| Status Code | Description                                 |
|-------------|---------------------------------------------|
| 200         | Successfully fetched the flexible discounts |
| 400         | Bad request                                 |
| 401         | Invalid Authorization token                 |
| 403         | Invalid API Key                             |
| 404         | Invalid request                             |

## Create Order and Preview Order

Pass the `flexDiscountCodes` at the lineItems level in the `Create Order` and `Preview Order` requests.

| Endpoint                             | Method |
|--------------------------------------|--------|
| `/v3/customers/<customer-id>/orders` | POST   |

**Notes:**

- Order creation will fail even if any line item contains an invalid flexible discount code.
- Currently, only one flexible discount code is allowed per line item in Order Preview.

### Request Header

See [Headers](../references/api-headers.md) section.

### Request Body

The following sample request shows how to apply a flexible discount code to a Create Order request to get a discounted price:

```json
{
  "orderType": "NEW", // NEW or PREVIEW
  "externalReferenceId": "759",
  "currencyCode": "USD",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "80004567CA01A12",
      "quantity": 1,
      "currencyCode": "USD",
      "flexDiscountCodes": ["SUMMER_SALE_123"]
    },
    {
      "extLineItemNumber": 2,
      "offerId": "80004561CA02A12",
      "quantity": 11,
      "currencyCode": "USD",
      "flexDiscountCodes": ["WINTER_SALE_123"]
    }
  ]
} 
```

The `flexDiscountCodes` parameter in the above request indicates the flexible discount codes applied to the Order.

### Response

```json
{
    "referenceOrderId": "",
    "orderType": "NEW",
    "externalReferenceId": "759",
    "customerId": "9876543210",
    "orderId": "5120008001",
    "currencyCode": "USD",
    "creationDate": "2019-05-02T22:49:54Z",
    "status": "1002",
    "lineItems": [
        {
            "extLineItemNumber": 1,
            "offerId": "80004567CA01A12",
            "quantity": 1,
            "status": "1002",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
                    "code": "SUMMER_SALE_123",
                    "result": "SUCCESS"
                }
            ]
        },
        {
            "extLineItemNumber": 2,
            "offerId": "80004561CA02A12",
            "quantity": 11,
            "status": "1002",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55522355-313b-476c-9d0b-7a710f4h83s4",
                    "code": "WINTER_SALE_123",
                    "result": "SUCCESS"
                }
            ]
        }
    ],
    "links": { // As existing response fields } 
    }
 ```

 The following table provides the flexible discount details included in the response:

| Name                   | Type   | Description                                                                                        |
|------------------------|--------|----------------------------------------------------------------------------------------------------|
| flexDiscounts          | Object | Details of the flexible discount applied to that lineItem                                          |
| flexDiscounts[].id     | String | A unique identifier for the promotion. Used to retrieve or reference a specific flexible discount. |
| flexDiscounts[].code   | String | The flexible discount code that was applied to that lineItem                                       |
| flexDiscounts[].result | String | “SUCCESS" indicates that the flexible discount code applicability was successful.              |

### HTTP Status Codes

Same as the standard [Create Order](../order-management/create-order.md) request.

## Get Order

The [GET Order](../order-management/get-order.md) API response also includes the flexible discount applied to the order.

| Endpoint                                        | Method |
|-------------------------------------------------|--------|
| `/v3/customers/<customer-id>/orders/<order-id>` | GET    |

### Request Header

See [Headers](../references/api-headers.md) section.  

### Request Body

None.

### Response

```json
{
    "referenceOrderId": "",
    "orderType": "NEW",
    "externalReferenceId": "759",
    "customerId": "9876543210",
    "orderId": "5120008001",
    "currencyCode": "USD",
    "creationDate": "2019-05-02T22:49:54Z",
    "status": "1000",
    "lineItems": [
        {
            "extLineItemNumber": 1,
            "offerId": "80004567CA01A12",
            "quantity": 1,
            "status": "1000",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
                    "code": "SUMMER_SALE_123",
                    "result": "SUCCESS"
                }
            ]
        },
        {
            "extLineItemNumber": 2,
            "offerId": "80004561CA02A12",
            "quantity": 11,
            "status": "1000",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55522355-313b-476c-9d0b-7a710f4h83s4",
                    "code": "WINTER_SALE_123",
                    "result": "SUCCESS"
                }
            ]
        }
    ],
    "links": { // As existing response fields } 
    },
```

### HTTP Status Codes

The same as the standard [Get Order API](../order-management/get-order.md).

## Get Order History of a Customer

The `Get Order History` API retrieves past orders for a customer, including any applied flexible discounts.

| Endpoint                             | Method |
|--------------------------------------|--------|
| `/v3/customers/<customer-id>/orders` | GET    |

### Request Header  

See [Headers](../references/api-headers.md) section.

### Request Body  

None.

### Response

```json
{
    "items": [
        {
            "referenceOrderId": "",
            "orderType": "NEW",
            "externalReferenceId": "759",
            "customerId": "9876543210",
            "orderId": "5120008001",
            "currencyCode": "USD",
            "creationDate": "2019-05-02T22:49:54Z",
            "status": "1000",
            "lineItems": [
                {
                    "extLineItemNumber": 1,
                    "offerId": "80004567CA01A12",
                    "quantity": 1,
                    "status": "1000",
                    "subscriptionId": "",
                    "currencyCode": "USD",
                    "flexDiscounts": [
                        {
                            "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
                            "code": "SUMMER_SALE_123",
                            "result": "SUCCESS"
                        }
                    ]
                },
                {
                    "extLineItemNumber": 2,
                    "offerId": "80004561CA02A12",
                    "quantity": 11,
                    "status": "1000",
                    "subscriptionId": "",
                    "currencyCode": "USD",
                    "flexDiscounts": [
                        {
                            "id": "55522355-313b-476c-9d0b-7a710f4h83s4",
                            "code": "WINTER_SALE_123",
                            "result": "SUCCESS"
                        }
                    ]
                }
            ],
            "links": { // As existing response fields } 
            }
        ]
    }
```

### HTTP Status Codes

The same as the standard [Get Order History API](../order-management/get-order.md).

## Preview renewal with flexible discount code

Eligibility for flexible discounts is validated in both automated preview renewal and manual preview renewals.

**Note:** The flexible discount codes included in the response apply only to renewal scenarios. They cannot be used when creating a new order.

### Preview automated renewal

The `POST /v3/customers/<customer-id>/orders` API with the `orderType` as `PREVIEW_RENEWAL` is used in the request to verify the eligibility of the order, including validation of customer eligibility for the flexible discount code that is currently applied on the subscription.

**Request**

```json
{
  "orderType": "PREVIEW_RENEWAL"
}
```

**Response**

```json
{
    "referenceOrderId": "",
    "orderType": "PREVIEW_RENEWAL",
    "externalReferenceId": "759",
    "customerId": "9876543210",
    "orderId": "5120008001",
    "currencyCode": "USD",
    "creationDate": "2019-05-02T22:49:54Z",
    "status": "",
    "lineItems": [
        {
            "extLineItemNumber": 1,
            "offerId": "80004567CA01A12",
            "quantity": 1,
            "status": "1000",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
                    "code": "ABCD-XV54-HG34-78YT",
                    "result": "SUCCESS"
                }
            ]
        },
        {
            "extLineItemNumber": 2,
            "offerId": "80004561CA02A12",
            "quantity": 11,
            "status": "1000",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55522355-313b-476c-9d0b-7a710f4h83s4",
                    "code": "ABCD-XV54-HG34-78YT",
                    "result": "SUCCESS"
                }
            ]
        }
    ],
    "links": { // As existing response fields }
}
```

### Manual Preview Renewal Order with flexible discount code

Use the `POST /v3/customers/<customer-id>/orders` API with the `orderType` as `PREVIEW_RENEWAL` to manually preview the renewal order, including the eligibility of the customer for the flexible discount code included in the request.

**Request**

```json

{
  "orderType": "PREVIEW_RENEWAL",
  "externalReferenceId": "759",
  "currencyCode": "USD",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "80004567CA01A12",
      "quantity": 1,
      "currencyCode": "USD",
      "subscriptionId": " e0b170437c4e96ac5428364f674dffNA",
      "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
    },
    {
      "extLineItemNumber": 2,
      "offerId": "80004561CA02A12",
      "quantity": 11,
      "currencyCode": "USD",
      "subscriptionId": " fff170437c4e96ac5428364f674dfggg",
      "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
    }
  ]
}
```

**Response**

```json
{
    "referenceOrderId": "",
    "orderType": "PREVIEW_RENEWAL",
    "externalReferenceId": "759",
    "customerId": "9876543210",
    "orderId": "5120008001",
    "currencyCode": "USD",
    "creationDate": "2019-05-02T22:49:54Z",
    "status": "1002",
    "lineItems": [
        {
            "extLineItemNumber": 1,
            "offerId": "80004567CA01A12",
            "quantity": 1,
            "status": "1002",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
                    "code": "ABCD-XV54-HG34-78YT",
                    "result": "SUCCESS"
                }
            ]
        },
        {
            "extLineItemNumber": 2,
            "offerId": "80004561CA02A12",
            "quantity": 11,
            "status": "1002",
            "subscriptionId": "",
            "currencyCode": "USD",
            "flexDiscounts": [
                {
                    "id": "55522355-313b-476c-9d0b-7a710f4h83s4",
                    "code": "ABCD-XV54-HG34-78YT",
                    "result": "SUCCESS"
                }
            ]
        }
    ],
    "links": { // As existing response fields }
}
```

## Apply flexible discounts when placing manual renewal orders

You can apply a flexible discount to renewal orders, including late renewals, by specifying flexible discount codes at the line item level in the `Create Order` request. To do this, set `orderType` to `RENEWAL` and include `flexDiscountCodes` for the applicable line items.

| Endpoint                             | Method |
|--------------------------------------|--------|
| `/v3/customers/<customer-id>/orders` | POST   |

**Request:**

The following sample request shows how to apply a flexible discount code to a Create Order request to get a discounted price:

```json
{
  "orderType": "RENEWAL",
  "externalReferenceId": "759",
  "currencyCode": "USD",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "80004567EA01A12",
      "subscriptionId": " e0b170437c4e96ac5428364f674dffNA ",
      "flexDiscountCodes": ["SUMMER_SALE_12"],
      "quantity": 1
    }
  ]
}
```

**Response:**

```json
{
  "referenceOrderId": "",
  "orderType": "RENEWAL",
  "externalReferenceId": "759",
  "customerId": "9876543210",
  "orderId": "5120008001",
  "currencyCode": "USD",
  "creationDate": "2019-05-02T22:49:54Z",
  "status": "1002",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "status": "1002",
      "subscriptionId": " e0b170437c4e96ac5428364f674dffNA ",
      "flexDiscounts": [
                {
                    "id": "55555555-313b-476c-9d0b-6a610d5b91e0",
                    "code": "SUMMER_SALE_12",
                    "result": "SUCCESS"
                }
      ]
    }
  ],
  "links": { ... }
}
```

## Apply flexible discounts on subscriptions

- [Create Scheduled Subscription with flexible discount](#create-a-scheduled-subscription-with-flexible-discount)
- [Update Subscription with a flexible discount code](#update-a-subscription-with-flexible-discount-code)
- [Remove  flexible discount from a subscription](#remove-a-flexible-discount-from-a-subscription)


### Create a scheduled subscription with flexible discount

You can use the `POST /v3/customers/<customer-id>/subscriptions` API with `flexDiscountCodes` in the request to create a subscription for a specific customer.

**Note:**

- Flexible discount codes are not validated while creating a subscription. Verification of customer eligibility occurs exclusively through the Preview Renewal API.
- A flexible discount code can be redeemed only once for a customer, thereby reducing the risk of unwanted code redemptions and thus preventing abuse.

#### Request

```json

{
  "offerId": "65304470CA01012",
  "autoRenewal": {
    "enabled": true,
    "renewalQuantity": 100,
    "flexDiscountCodes": ["ABCD-XV54-HG34-78YT"]
  }
}
```

#### Response

```json
{
  "subscriptionId": "cc8efgh8bc4354a4b38006c87804ceNA",
  "currentQuantity": 0,
  "offerId": "65304470CA01012",
 
  "autoRenewal": {
    "enabled": true,
    "renewalQuantity": 5,
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

### Update a subscription with flexible discount code

Use the `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>` API with `flexDiscountCodes` in the request to update a subscription with the corresponding flexible discount.

**Note:** Flexible discount codes are not validated while updating a subscription. Verification of customer eligibility occurs exclusively through the Preview Renewal API.

#### Request

The `flexDiscountCodes` parameter indicates the flexible discounts applicable for the subscription.

```json
{
  "autoRenewal": {
    "enabled": true, // If Auto Renew is OFF, it must be turned ON to apply Discount Code. If it is already ON, this field is OPTIONAL.
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

### Remove a flexible discount from a subscription

Use the `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>` with the query parameter `reset-flex-discount-codes=true` to remove a flexible discount from a subscription.

#### Request

- **Request URL:** `PATCH /v3/customers/<customer-id>/subscriptions/<subscription-id>?reset-flex-discount-codes=true`
- **Request body:** Empty or applicable body for update subscription

Sample empty request body:

```json
{}
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
