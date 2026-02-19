# Get order details

The following two API endpoints are available to get order details:

- [Get details of a specific order](#get-details-of-a-specific-order)
- [Get the order history of a customer](#get-the-order-history-of-a-customer)

## Get details of a specific order

Use the `GET /v3/customers/<customer-id>/orders/<order-id>` API endpoint to get details of a specific order.

### Request header

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving a response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

### Request body

None.

### Response body

```json
{
  "externalReferenceId": "759",
  "orderId": "0123456789",
  "customerId": "9876543210",
  "orderType": "NEW",
  "referenceOrderId": "",
  "referencedOrderId": "",
  "currencyCode": "USD",
  "creationDate": "2019-05-02T22:49:54Z",
  "status": "1000",
  "source": "API",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "subscriptionId": "86756309",
      "status": "1000",
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

### HTTP status codes

| Status code | Description                         |
| ----------- | ----------------------------------- |
| 200         | Order details successfully returned |
| 400         | Bad request                         |
| 401         | Invalid Authorization token         |
| 403         | Invalid API Key                     |
| 404         | Invalid customer or order ID        |

## Get the order history of a customer

Use the `GET /v3/customers/<customer-id>/orders` API endpoint to get a customer's order history.

### Assumptions

Ensure that you are aware of the following before trying out this API endpoint:

- All parameters included in the request, besides limit and offset parameters, are queried together using `AND`.
- Any parameters that are not included will default to the value in the default column.
- For `start-date` and `end-date`, a date without timestamp is evaluated to midnight UTC.
  - Dates with timestamps are only accepted in ISO-8601 format with "Zulu" (UTC) time zone. This is the same format that all dates and times are in CPAPI responses.
- The `order-type`, `status`, and `offer-id` parameters can be included multiple times to specify multiple values. Including multiple values for a parameter results in an OR operation for those values in the query.
  - **Example:** To query for orders that are either status 1000 or 1002:
    `/v3/customers/<customer-id>/orders?status=1000&status=1002`
- Results will be sorted in descending order by `creationDate`.
- The maximum value for offset is the total number of results. Values higher than the total count return an error.
- If a `limit` greater than the maximum (100) is sent, it will default to the maximum value (100).
- Links to `next` and `prev` will be included in the response if there is a valid next or previous page in the result set.

### Request header

The request body is the same as mentioned in the [previous endpoint](#request-header).

### Query parameters

| Parameters              | Values                           | Default                 | Description                                                                                |
|--------------------|----------------------------------|-------------------------|--------------------------------------------------------------------------------------------|
| order-type         | NEW, TRANSFER, or RENEWAL        | All                     |                                                                                            |
| reseller-id        | Valid reseller IDs               | All                     | Regardless of reseller id, only orders for the partner making the request will be returned. |
| status             | 1000, 1002, 1004, or 1026           | All                     |                                                                                            |
| reference-order-id | Valid order IDs                  | All                     |                                                                                            |
| offer-id           | Valid offer IDs                  | All                     |                                                                                            |
| start-date         | 2019-05-02, 2019-05-02T22:49:54Z | Current term start date | Date without timestamp or with timestamp in Zulu time.                                      |
| end-date           | 2019-05-02, 2019-05-02T22:49:54Z | now                     | Date without timestamp or with timestamp in Zulu time.                                      |
| limit              | 1…100                            | 25                      | Page size (max number of orders to return).                                                 |
| offset             | 0…N                              | 0                       | Where to start the page.                                                                 |

### Response body

```json
{
  "totalCount": 9,
  "count": 3,
  "offset": 3,
  "limit": 3,
  "items": [
    { Order resource },
    { Order resource }
  ],
  "links": {
    "self": {
      "uri": "/v3/customers/<customer-id>/orders?offset=3&limit=3",
      "method": "GET",
      "headers": []
    },
    "next": {
      "uri": "/v3/customers/<customer-id>/orders?offset=6&limit=3",
      "method": "GET",
      "headers": []
    },
    "prev": {
      "uri": "/v3/customers/<customer-id>/orders?offset=0&limit=3",
      "method": "GET",
      "headers": []
    }
  }
}
```

If `promotions` array indicates the promotions applied to the Order. For example:

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
   "source": "API",
    
"lineItems": [ 
     { 
       "extLineItemNumber": 1, 
       "offerId": "80004567CA01A12", 
       "quantity": 1, 
       "status": "1002", 
       "subscriptionId": "", 
       "currencyCode": "USD", 
       "promotions": [ 
                  { 
                      "code": "SUMMER_SALE_123", 
                      "result": "SUCCESS", 
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
       "promotions": [ 
                  { 
                      "code": "WINTER_SALE_123", 
                      "result": "SUCCESS", 
                   } 
         ] 
    } 
   ],  "links": { // As existing response fields } 
 } 
```

### HTTP status codes

| Status code | Description                         |
| ----------- | ----------------------------------- |
| 200         | Order history successfully returned |
| 400         | Bad request                         |
| 401         | Invalid Authorization token         |
| 403         | Invalid API Key                     |
| 404         | Invalid customer ID                 |

## Pricing and invoicing data in order details

Partners can view invoicing and pricing details for customer orders through the [Get Order](get-order.md), [Preview Order](order-scenarios.md#preview-an-order), and [Preview Renewal APIs](order-scenarios.md#preview-renewal-orders).

Pricing data returned through these APIs reflects partner pricing, discounts, proration logic, and totals as priced and stored in Adobe systems.

**Note:** 

- These APIs do not return tax information.
- Pricing information is not available in these APIs for exceptional return orders that are not refunded.

### How partners can use these APIs

Pricing appears in the following two order APIs when the `fetch-price=true` query parameter is included in the request:

- Get Order by ID
- Get Order History

The Preview and Get Order APIs return a defined subset of pricing fields, including:

- `partnerPrice`
- `discountedPartnerPrice`
- `netPartnerPrice`
- `lineItemPartnerPrice`
- `pricingSummary`

**Usage instructions:**

The pricing data is returned only when:

- The request includes the `fetch-price=true` query parameter
- The order is a single‑currency order
- The order status is 1000
- The order was placed after the pricing feature was introduced

### Pricing data in GET Order by ID API

**Sample request:**

`<ENV>/v3/customers/11233441871/orders/02938476?fetch-price=true`

**Sample response:**

```json
{
  "referenceOrderId": "",
  "externalReferenceId": "9c6a5425-c6e0-480b-b075-7b439a6375d",
  "orderId": "02938476",
  "customerId": "1005831871",
  "currencyCode": "USD",
  "orderType": "NEW",
  "status": "1000",
  "creationDate": "2025-05-22T12:51:06Z",
  "lineItems": [
     
    {
      "extLineItemNumber": 1,
      "offerId": "69804578CA02A12",
      "quantity": 10,
      "subscriptionId": "", 
      "status": "", 
      "currencyCode": "USD",
      "proratedDays" : 90,
      "pricing": {
            "partnerPrice": 365.00,              
            "discountedPartnerPrice": 365.00,    
            "netPartnerPrice": 85.068,      
            "lineItemPartnerPrice": 850.68,             
      }
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 1660.68, 
      "currencyCode": "USD"                
    },
  ],
}
```

#### Pricing details in lineitems (lineItems[].pricing)

| Field                  | Description                                                                                                                                         |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| partnerPrice           | Non-prorated full-term unit price for the given offer, including any applicable volume discounts, but before applying flexible discounts and taxes. |
| discountedPartnerPrice | Unit price after applying discount. \<br /\>                                                                                                          |
| netPartnerPrice        | Prorated unit price after discount.                                                                                                                 |
| lineItemPartnerPrice   | Prorated price of the item after discount and before tax. This is the price that the partner needs to pay to Adobe for this item.                   |

**Note:** The `proratedDays` parameter in the response specifies the number of days for which the order will be invoiced. This parameter appears only when the `fetch-price` parameter is set to `true` in the request.

#### Pricing Summary (pricingSummary[])

| Field                     | Description                                                                                    |
|---------------------------|------------------------------------------------------------------------------------------------|
| totalLineItemPartnerPrice | Sum of all line item prices in the order.                                                      |
| currencyCode              | Currency used for pricing. This is specified in ISO 4217 currency code. Examples: USD and EUR. |

For complete set of request and response parameter descriptions, refer to [Order resource](../references/resources.md#order-top-level-resource).

### Pricing data in GET Order History API

**Sample request URL:**

`<Env>/v3/customers/{{customerId}}/orders?offset=0&limit=25&fetch-price=true`

**Sample response:**

```json
{
    "totalCount": 7,
    "count": 7,
    "offset": 0,
    "limit": 25,
    "items": [
        {
            "referenceOrderId": "9204879218",
            "externalReferenceId": "8da1e2fd-5695-4812-a4a7-ed09844763f",
            "orderId": "550135947",
            "customerId": "1007008907",
            "currencyCode": "USD",
            "orderType": "RETURN",
            "status": "1000",
            "source": "API",
            "lineItems": [
                {
                    "extLineItemNumber": 2,
                    "offerId": "65322435CAT1A12",
                    "quantity": 2500,
                    "subscriptionId": "cc92f750c3455ea60250bf20e7814dNA",
                    "status": "1000",
                    "currencyCode": "USD",
                    "pricing": {
                            "partnerPrice": 365.00,              
                            "discountedPartnerPrice": 365.00,  
                            "netPartnerPrice": 85.068,  
                            "lineItemPartnerPrice": 850.68, 
      }
    }
                }
            ],
            "pricingSummary": [
                {
                    "totalLineItemPartnerPrice": 1660.68,       
                    "currencyCode": "USD"                
            }
            ]
            "creationDate": "2026-01-07T06:30:12Z",
            "links": {
                "self": {
                    "uri": "/v3/customers/122334455/orders/550135947",
                    "method": "GET",
                    "headers": []
                }
            }
        }
    ],
    "links": {
        "self": {
            "uri": "/v3/customers/122334455/orders?limit=25&offset=0&fetch-recommendations=false&recommendation-language=MULT",
            "method": "GET",
            "headers": []
        }
    }
}
```

For complete set of request and response parameter descriptions, refer to [Order resource](../references/resources.md#order-top-level-resource).
