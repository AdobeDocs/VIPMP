# Order creation scenarios

This topic provides details of the following scenarios related to order creation and management:

- [New order](#new-order)
- [Return or cancellation of order](#return-or-cancellation-of-order)
- [Preview an order](#preview-an-order)
- [Preview renewal orders](#preview-renewal-orders)
- [Renewal of order](#renewal-orders)
- [Preview switch order](#order-scenarios-corresponding-to-mid-term-upgrade)
- [Create switch order](#order-scenarios-corresponding-to-mid-term-upgrade)
- [Preview revert switch order](#order-scenarios-corresponding-to-mid-term-upgrade)
- [Revert switch order](#order-scenarios-corresponding-to-mid-term-upgrade)

## New order

This section lists the sample requests and responses of an order with `orderType` - NEW.

**Notes:**

- The `subscriptionId` may be blank while the order is processing. Once the order is complete (status 1000), then all `subscriptionIds` will be populated and will not change.
- The [Create Order](create-order.md) call is used to add seats for the current term.
- Order ID is created by this service and returned synchronously.
- Order may fail asynchronously, and status will be updated to reflect the failure.

### Sample request

```json
{
  "orderType": "NEW",
  "externalReferenceId": "759",
  "currencyCode": "USD",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "currencyCode": "USD",
      "deploymentId": "12345"
    }
  ]
}
```

### Sample response

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
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "status": "1002",
      "subscriptionId": "",
      "currencyCode": "USD",
      "deploymentId": "12345"
    }
  ],
  "links": { ... }
}
```

## Return or cancellation of order

**Notes:**

- `referenceOrderId` is the order that is being returned.
- `referenceOrderId` must be a valid, returnable order.
  - Either NEW or RENEWAL.
- Line items being returned must match `extLineItemNumber`, `offerId`, and quantity of the original order:
  - Line items can be cancelled independently in the same or different RETURN order.
  - No partial line item cancellations.
- Same 1000 | 1002 | 1004 statuses.
- As line items from an order get cancelled, the line item status on the original order changes from 1000 to 1008.
  - When all line items for an order are cancelled, the status changes to 1008 for the original order.

### Sample request

```json
{
  "referenceOrderId": "0123456789",
  "orderType": "RETURN",
  "externalReferenceId": "759",
  "currencyCode": "USD",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "currencyCode": "USD",
      "deploymentId": "12345"
    }
  ]
}
```

### Sample response

```json
{
  "referenceOrderId": "0123456789",
  "orderType": "RETURN",
  "externalReferenceId": "759",
  "orderId": "911000833",
  "customerId": "9876543210",
  "currencyCode": "USD",
  "creationDate": "2019-05-02T22:49:54Z",
  "status": "1002",
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
  "links": { ... }
}
```

## Preview an order

The  `Create Order` API with `orderType` PREVIEW is a simulated order request that helps partners validate and prepare an order before actually placing it.

| Endpoint | Method |
|----------|--------|
| `/v3/customers/<customer-id>/orders`         |   `POST`     |

The API returns structural and eligibility-related information about the order, but does not include pricing details unless explicitly requested.

A few of the advantages for previewing an order before placing an order include:

- Verifies customer eligibility for the requested offer.
Automatically adjusts the offer ID if a better volume discount is available.
- Prevents order failures by catching issues early, for example,  invalid discount codes, ineligible offers, and so on.
- Helps partners prepare a quote structure (line items, quantities, deployment IDs) before calculating pricing.
- Allows partners to test different configurations and offers.

 **Preview Order with pricing**

You can choose to include pricing in the Preview Order API response by setting the `fetch-price` query parameter to `true` in the request URL. This returns real-time partner pricing details for Adobe products, helping partners and resellers better estimate how much Adobe will invoice for an order.

Pricing data is sourced directly from Adobe’s systems, reflecting official price lists, discounts, and proration logic.

**Notes:**

- Prices returned in the  `Preview Order` and `Preview Renewal` calls are calculated based on the Pacific Standard Time (PST) time zone.
- Prices returned in the `Preview Order` and `Preview Renewal` calls are an estimate based on the request made at that date and time. Orders placed at a later date and time may not result in the same return amount.
- Pricing returned via this API is pricing between Adobe and the direct partner. Any pricing presented to end customers is set by end customer’s reseller.
- Pricing details are unavailable in `Preview Order` and `Preview Renewal` scenarios for global sales involving multiple currencies.

### Usage instructions for Preview Order API

- Set `orderType` to PREVIEW
- A successful response can be used to place a new order request, if the `orderType` is changed to NEW.
- Include the query parameter `fetch-price=true` to retrieve pricing details.
- Returns the best available offer ID for the customer and the order.
  - Input Offer ID can be any level representing the same product.
  - If the Offer IDs in the request provides a better discount than customer is eligible for, then the correct lower-level Offer IDs are returned.
    - For a PREVIEW order, the request gets rejected if the customer is not eligible for an Offer ID.
- If the `PREVIEW` order is rejected, then the `NEW` order will also fail with the same error.
- The `discountCode` is applicable only to High Volume Discount customers who have migrated from VIP to VIP MP. You can use the discount code only if their discount level in VIP is between 17 and 22.

### Request

**Sample request URL:** `<ENV>/v3/customers/{customer-id}/orders?fetch-price=true`

**Query parameters:**

| Parameter | Type |Description|
|--|--|--|
|customer-id |String | A unique identifier for the customer for whom the Order Preview request is being submitted. |
|fetch-price | Boolean| A flag indicating whether pricing details should be included in the response. Possible values are: `true` or `false`  |

**Request body:**

```json
{
  "externalReferenceId": "22739ace-0da5-41a4-b475-12f677a4cac",
  "orderType": "PREVIEW",
  "currencyCode": "USD",
  "lineItems": [
    {
      "currencyCode": "USD",
      "extLineItemNumber": 1,
      "offerId": "11073058CA01A12",
      "quantity": 10,
      "flexDiscountCodes": [
        "BLACK_FRIDAY_10_PERCENT_OFF"
      ]
    },
    {
      "currencyCode": "USD",
      "extLineItemNumber": 2,
      "offerId": "69804578CA02A12",
      "quantity": 10,
      "flexDiscountCodes": [
        "BLACK_FRIDAY_20_DOLLAR_OFF"
      ]
    }
  ]
}
```

### Sample response

```json
{
  "referenceOrderId": "",
  "externalReferenceId": "aad516e6-8945-4531-8572-75bed01c445",
  "orderId": "",
  "customerId": "1006370764",
  "currencyCode": "USD",
  "orderType": "PREVIEW",
  "creationDate": "2025-05-02T22:49:54Z",
  "status": "",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "11073058CA02A12",
      "quantity": 10,
      "subscriptionId": "",
      "status": "",
      "currencyCode": "USD",
      "flexDiscounts": [
        {
          "id": "55555555-5bb8-4476-bd3f-5562299ab3f5",
          "code": "BLACK_FRIDAY_10_PERCENT_OFF",
          "result": "SUCCESS"
        }
      ],
      "proratedDays" : 90,
      "pricing": {
        "partnerPrice": 365.00,              
        "discountedPartnerPrice": 328.50, 
        "netPartnerPrice": 81.00,       
        "lineItemPartnerPrice": 810.00,    
      }
    },
    {
      "extLineItemNumber": 2,
      "offerId": "69804578CA02A12",
      "quantity": 10,
      "subscriptionId": "", 
      "status": "", 
      "currencyCode": "USD",
      "flexDiscounts": [
        {
          "id": "55555555-5bb8-4476-bd3f-5562299ab3f5",
          "code": "BLACK_FRIDAY_20_DOLLAR_OFF",
          "result": "SUCCESS"
        }
      ],
      "proratedDays" : 90,
      "pricing": {
        "partnerPrice": 365.00,      
        "discountedPartnerPrice": 345.00,    
        "netPartnerPrice": 85.068,     
        "lineItemPartnerPrice": 850.68,    
      }
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 1660.68,        
      "currencyCode": "USD"   
    }
  ]
}
```

### Pricing details in lineitems (lineItems[].pricing)

| Field                       | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| partnerPrice                | Non-prorated full-term unit price for the given offer, including any applicable volume discounts, but before applying flexible discounts and taxes.|
| discountedPartnerPrice     | Unit price after applying discount. \<br /\> |
| netPartnerPrice                 | Prorated unit price after discount. |
| lineItemPartnerPrice      | Prorated price of item after discount and before tax. This is the price partner need to pay to Adobe for this item.  |

**Note:** The `proratedDays` parameter in the response specifies the number of days for which the order will be invoiced. This parameter appears only when the `fetch-price` parameter is set to `true` in the request. It is relevant for mid-term purchases.

### Pricing Summary (pricingSummary[])

| Field                       | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| totalLineItemPartnerPrice               | Sum of all line item prices in the order.                 |
| currencyCode                 | Currency used for pricing. This is specified in ISO 4217 currency code. Example, USD and EUR.                                    |

For complete set of request and response parameter descriptions, refer to [Order resource](../references/resources.md#order-top-level-resource).

#### Sample request and response for HVD customers

**Request:**

```json
{

  "orderType": "PREVIEW",
  "externalReferenceId": "759",
  "currencyCode": "USD",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "currencyCode": "USD",
      "deploymentId": "12345",
      "discountCode": "HVD_L18_PRE"
    }
  ]
}
```

**Response:**

**Note:** Pricing details is included in the response as the query parameter `fetch-price` was set to `true` in the request URL.

```json
{
  "referenceOrderId": "",
  "orderType": "PREVIEW",
  "externalReferenceId": "759",
  "orderId": "",
  "customerId": "9876543210",
  "currencyCode": "USD",
  "creationDate": "2019-05-02T22:49:54Z",
  "status": "",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "subscriptionId": "",
      "status": "",
      "currencyCode": "USD",
      "deploymentId": "12345",
      "discountCode": "HVD_L18_PRE",
      "proratedDays": 30,
      "pricing": {
        "partnerPrice": 299.99,
        "discountedPartnerPrice": 299.99,
        "proratedPartnerPrice": 24.65,
        "lineItemPartnerPrice": 24.65
      }
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 24.65,
      "currencyCode": "USD"
    }
  ]
}
```

## Preview renewal orders

The `Create Order` API with `orderType` set to PREVIEW_RENEWAL allows partners to simulate a renewal order before the actual renewal is processed. This helps validate renewal eligibility, pricing, and offer availability in advance.

| Endpoint | Method |
|----------|--------|
| `/v3/customers/<customer-id>/orders`         |   `POST`     |

A few of the benefits of previewing a renewal order include:

- Validates renewal eligibility for existing subscriptions based on auto-renewal preferences.
- Returns the best available offer IDs for renewal, including High Growth Offers.
- Provides accurate pricing details for renewal scenarios, including discounts and proration logic.

### Usage instructions

- No `orderId`, `status`, and `links` in the request.
- In case of no `lineItems` in the request, the response indicates what would be in the RENEWAL order based on the auto-renewal preferences (`autoRenewal.enabled` and `autoRenewal.renewalQuantity`) on the customer’s subscriptions.
- In case of `lineItems` in the request, the response indicates the RENEWAL order initiated for the selected line items, supporting both Early Renewal (before anniversary date) and Late Renewal (after anniversary date).
- If the customer does not have any subscriptions with autoRenewal enabled, then an error is returned.
- Returns the best available offer IDs for the renewal order.
- The `eligibleOffers` section lists the High Growth Offers available for the customer. Read more about the [High Growth Offers](../customer-account/high-growth.md).
- The `discountCode` indicates the discount code applicable to the HVD customers migrating from VIP to VIP Marketplace. This parameter does not apply to non-HVD customers.
- The `lineItems []  → status` parameter in the response adheres to standard status codes, for example, 1000 = Active, 1004 = Inactive, and so on. However, its interpretation is specific to the context of Preview Renewal Orders. For example:

  - **1000** indicates that the subscription is either active or scheduled and is expected to renew successfully.
  - **1004** indicates that the subscription is active or scheduled, but the associated product has expired, so the renewal will not proceed.
- Include the query parameter `fetch-price=true` to retrieve pricing details. 
- Pricing details are not available in Preview Order and Preview Renewal scenarios for global sales involving multiple currencies.
- `proratedDays` in the response indicates the number of days for which order will be invoiced. This applies in the case of mid-term purchases. 

### Sample request

**Request URL:** `<ENV>/v3/customers/{customer-id}/orders?fetch-price=true`

**Request sample:**

```json
{
  "orderType" : "PREVIEW_RENEWAL"
}
```

### Response

```json
{
  "referenceOrderId": "",
  "externalReferenceId": "",
  "orderId": "",
  "customerId": "1006370655",
  "currencyCode": "USD",
  "orderType": "PREVIEW_RENEWAL",
  "creationDate": "2025-05-02T22:49:54Z",
  "status": "",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "11083117CA03A12",
      "quantity": 10,
      "subscriptionId": "3d0630693446f8bdff9cbd08f4b68bNA",
      "status": "1000",
      "currencyCode": "USD",
      "proratedDays": 365,
      "pricing": {
        "partnerPrice": 350.50,
        "discountedPartnerPrice": 350.50,
        "netPartnerPrice": 350.50,
        "lineItemPartnerPrice": 3505.00
      }
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 3505.00,
      "currencyCode": "USD"
    }
  ],
}
```

For more information on the pricing details returned in the response, see [Preview Order](#preview-an-order).

#### Sample request and response for HVD customers

**Request:**

```json
{
  "orderType": "PREVIEW_RENEWAL"
}
```

OR

```json
{
  "orderType": "PREVIEW_RENEWAL",
  "lineItems": [
    {
      "extLineItemNumber": 1,
      "offerId": "80004567EA01A12",
      "discountCode": "HVD_L18_PRE",
      "subscriptionId": "e0b170437c4e96ac5428364f674dffNA"
    }
  ]
}
```

**Response:**

**Note:** Pricing details is included in the response as the query parameter `fetch-price` was set to `true` in the request URL.

```json
{
  "referenceOrderId": "",
  "orderType": "PREVIEW_RENEWAL",
  "externalReferenceId": "759",
  "orderId": "",
  "customerId": "9876543210",
  "currencyCode": "USD",
  "creationDate": "2019-05-02T22:49:54Z",
  "status": "",
  "lineItems": [
    {
      "extLineItemNumber": 4,
      "offerId": "80004567EA01A12",
      "quantity": 1,
      "subscriptionId": " e0b170437c4e96ac5428364f674dffNA",
      "discountCode": "HVD_L18_PRE",
      "status": "1000",
      "currencyCode": "USD",
      "deploymentId": "12345",
      "proratedDays": 365,
      "pricing": {
        "partnerPrice": 299.99,
        "discountedPartnerPrice": 299.99,
        "proratedPartnerPrice": 299.99,
        "lineItemPartnerPrice": 299.99
      }
    },
    {
      "extLineItemNumber": 1,
      "offerId": "65322447CA01A12",
      "quantity": 25,
      "subscriptionId": "4392d721a543929afb871a4c140435NA",
      "discountCode": "HVD_L18_PRE",
      "status": "1004",
      "currencyCode": "USD",
      "deploymentId": "12345",
      "proratedDays": 365,
      "pricing": {
        "partnerPrice": 299.99,
        "discountedPartnerPrice": 299.99,
        "proratedPartnerPrice": 299.99,
        "lineItemPartnerPrice": 7499.75
      }
    }
  ],
  "pricingSummary": [
    {
      "totalLineItemPartnerPrice": 7799.74,
      "currencyCode": "USD"
    }
  ],
  "eligibleOffers": [
    {
      "offerId": "65324918CA14X12",
      "renewalCode": "MOQ_100",
      "eligibilityCriteria": {
        "minQuantity": 100,
        "additionalCriteria": [
          "THREE_YEAR_COMMIT"
        ],
        "deploymentId": "1450043516"
      }
    },
    {
      "offerId": "65324918CA14Y12",
      "renewalCode": "MOQ_250",
      "eligibilityCriteria": {
        "minQuantity": 250,
        "additionalCriteria": [
          "THREE_YEAR_COMMIT"
        ],
        "deploymentId": "1450043516"
      }
    },
    {
      "offerId": "65324918CA14Z12",
      "renewalCode": "MOQ_500",
      "eligibilityCriteria": {
        "minQuantity": 500,
        "additionalCriteria": [
          "THREE_YEAR_COMMIT"
        ]
      }
    }
  ]
}
```

## Renewal orders

**Notes:**

- RENEWAL order type is used for late renewals, which are initiated after the anniversary date.
- Subscription ID is required in the request line item.
- The license quantities must be less than or equal to customer’s current subscription current quantities.
- Order ID is created by this service and returned synchronously.
- Partner Marketplaces are expected to check the status of the order for success.
- You can select the expired subscriptions for manual renewal by using the [Get All Subscriptions for a Customer](../subscription-management/get-details-for-customers.md) API. Subscriptions that can be selected for manual renewal are indicated by the `allowedActions`": `["MANUAL_RENEWAL"]` parameter of the Get All Subscriptions of a Customer API response.

### Sample request

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
      "quantity": 1
    }
  ]
}
```

### Sample response

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
      "subscriptionId": " e0b170437c4e96ac5428364f674dffNA "
    }
  ],
  "links": { ... }
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

## Assumptions

- Renewals are managed at the subscription level. No new order is expected from the marketplace. If a customer has subscriptions set to auto-renew, Adobe creates a renewal order on the customer's `cotermDate`. The renewal Order ID can be retrieved by parsing the response from the [Get Order History](get-order.md) API with `orderType` as RENEWAL.
- Offer IDs are defined and communicated in published price lists.
- Each Offer ID is valid only for certain currencies. The currency code sent in the request must match the Offer IDs as well as the currency for which the distributor is configured.
- `externalReferenceId` is used to pass in an ID for financial reconciliation.
  - The `externalReferenceId` does not need to be unique and is an optional field.

**Restrictions:**

- Maximum quantity per line item:
  - 10,000 for Team products
  - 200,000 for Enterprise products
- Maximum line items per order:
  - 499
- Maximum length of `externalReferenceId`:
  - 35 characters
- Maximum `extLineItemNumber`:
  - 999999

## Order scenarios corresponding to mid-term upgrade

The Mid-Term Upgrade capability in VIP Marketplace enables partners to upgrade a  customer's product subscriptions during the active term rather than waiting for the renewal cycle. It allows transitions to higher-tier offerings such as moving from Teams to Enterprise products. Read more about [mid-term upgrade](../mid-term/index.md).

Depending upon the corresponding values for the `orderType` parameter of the `Create Order` API, the following  mid-term upgrade and reversal scenarios are supported:

| Scenario | orderType | Description | Link to Request and Response |
|--|--|--|--|
|Preview switch order |PREVIEW_SWITCH | Generates a quote for the upgrade, including pricing and eligibility details.|[Preview switch order](#preview-switch-order) |
| Create switch order|SWITCH |Places the actual upgrade order. | [Create switch order](#create-switch-order)|
| Preview revert switch order|PREVIEW_REVERT_SWITCH|Previews the reversal of a previously placed upgrade order. | [Preview revert switch order](#preview-revert-switch-order)|
|Revert switch order|REVERT_SWITCH |Executes the reversal of an upgrade order and restores the original subscription. | [Revert switch order](#revert-switch-order)|

### Preview switch order

The newly introduced `Preview Switch` option in the `OrderType` parameter of the Create Order API helps partners to generate upgrade quotes prior to placing a switch order.

|Endpoint | Method |
|--|--|
|`<env root url>/v3/customers/{{customerId}}/orders` | POST|

### Request

**Query parameters**

| Parameter | Required | Description |
|---|--|--|
|reassign-users|Optional |Specifies whether to automatically reassign users from the original subscription to the upgraded product. \<br /\>**Note:** Automatic user reassignment is not supported for upgrades from Teams to Enterprise or between Enterprise subscriptions. |
|fetch-price|Optional| Specifies whether to fetch pricing details while previewing the mid-term upgrade offers.|

**Sample Request URL:** `POST https://partners-stage.adobe.io/v3/customers/1005944528/orders?fetch-price=true`

**Request body**

```json
{
    "orderType" : "PREVIEW_SWITCH",
    "currencyCode" : "USD",
    "lineItems" : [
        {
            "extLineItemNumber" : 1,
            "offerId" : "65322651CA02A12",
            "quantity" : 15,
            "discountCode": "HVD_L17PRE"
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
            "discountCode": "HVD_L17PRE",
            "quantity" : 15,
        }
    ]   
}
```

**Request parameters:**

|Parameter|Required|Data Type|Description|
|--|--|--|--|
|externalReferenceId|Not Required|String|This is used to link the order with partner passed id|
|referenceOrderId|Optional(Required for revert switch)|String|Original order id of switch order, in case of revert |
|orderType|Required|String (Enum)|Indicates the order type customer is trying to place. Possible values corresponding to the mid-term upgrade: PREVIEW_SWITCH or SWITCH|
|currencyCode                      | Required                            | String (Enum)           | Currency code for order, must be supported by the partner.                      |
| lineItems                         | Required                            | List     | Specifies the line items the customer intends to switch.                     |
| lineItems.extLineItemNumber       | Required                            | String                   | Unique index for line item.                                                 |
| lineItems.subscriptionId          | Required                            | String                   | Indicates which subscription customer is trying to switch.                  |
| lineItems.offerId                 | Required                            | String                   | Indicates which product customer is switching to                           |
| lineItems.quantity                | Required                            | String                   | Quantity from subscription to be switched.                                  |
| lineItems.discountCode            | Optional                            | String                   | Discount code applied to the line item                                     |
| cancellingItems                   | Required for Switch type Order      | List | List of items the customer intends to cancel as part of the switch process.                                |
| cancellingItems.extLineItemNumber | Required                            | String                   | A unique identifier for the line item being canceled.                                      |
| cancellingItems.referenceLineItemNumber | Required                     | String                   | Reference line item number of the item being canceled.                                 |
| cancellingItems.subscriptionId    | Required                            | String                   | Indicates subscription to be canceled.                                           |
| cancellingItems.discountCode      | Optional                            | String                   | The discount code applied to the item being canceled, if any.                                   |

### Response

```json
{
    "referenceOrderId": "",
    "externalReferenceId": "a96ee8fe-c440-4d1c-ae5b-a90e1825aef",
    "orderId": "",
    "customerId": "1005944528",
    "currencyCode": "USD",
    "orderType": "PREVIEW_SWITCH",
    "status": "",
    "pricingSummary": [
    {
        "totalLineItemPrice": 810.00,
        "currencyCode": "USD"
        }
    ],
    "lineItems": [
        {
 
                "extLineItemNumber": 1,
                "offerId": "65304479CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": "",
                "proratedDays": 90,
                "pricing": {
                    "partnerPrice": 365.00,
                    "discountedPartnerPrice": 328.50,
                    "netPartnerPrice": 81.00,
                    "lineItemPrice": 810.00
                }
        }
    ],
    "cancellingItems":[
        {
                "offerId": "65322651CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
                "pricing": {
                    "partnerPrice": 300.00,
                    "discountedPartnerPrice": 0.00,
                    "netPartnerPrice": -300.00,
                    "lineItemPrice": 300.00
                },
                "referenceLineItemNumber": 1,
        }
    ]
    "creationDate": "2025-03-17T11:42:29Z"
}
```

**Response parameters:**

The `cancellingItems` object lists the switch plan with corresponding pricing details. The following table lists the parameters in the cancellingItems object. For more details on the entire parameters of the Order source, refer to [Order object](../references/resources.md#order-top-level-resource).

|Parameter|Not Null|Data Type|Description|Included in Response by Default|
|--|--|--|--|--|
| cancellingItems.offerId               | YES      | String                   | Part number of the item being canceled.                          | Yes                              |
| cancellingItems.quantity              | YES      | Integer                  | Quantity being canceled.                                        | Yes                              |
| cancellingItems.discountCode          | NO       | String                   | Discount code applied to the item being canceled.                   | Yes                              |
| cancellingItems.subscriptionId        | YES      | String                   | Subscription ID associated with the item being canceled.                             | Yes                              |
| cancellingItems.pricing.partnerPrice | YES      | Decimal                  | Partner price of the item being canceled.                         | Yes                              |
| cancellingItems.pricing.discountedPartnerPrice | YES | Decimal          | Partner price after applying discounts.                      | Yes                              |
| cancellingItems.pricing.netPartnerPrice | YES    | Decimal                  | Net partner price of the item being canceled after discounts.                               | Yes                              |
| cancellingItems.pricing.lineItemPrice | YES      | Decimal                  | Final price of the item being canceled.                                | Yes                              |
| cancellingItems.referenceLineItemNumber | YES    | Integer                  | Reference line item number.                                | Yes                              |
| creationDate                          | YES      | DateTime                 | Timestamp of the order creation.                               | Yes                              |

### Create switch order

Use the `Create Order` API with `orderType` as SWITCH to switch from the current order to a new one. Creating a switch order is functionally similar to a preview request, but it does not include pricing details in the response. Once placed, the order appears in the order history, and the same logic applies for tracking and managing orders.

This API facilitates upgrade orders with "From" and "To" product details.

#### Request

Sample request URL: `POST https://partners-stage.adobe.io/v3/customers/1005944528/orders?`

Request body:

```json
{
    "orderType" : "SWITCH",
    "currencyCode" : "USD",
    "lineItems" : [
        {
            "extLineItemNumber" : 1,
            "offerId" : "65322651CA02A12",
            "quantity" : 15,
            "discountCode": "HVD_L17PRE"
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
            "discountCode": "HVD_L17PRE",
            "quantity" : 15,
        }
    ]
}
```

#### Response

```json
{
    "referenceOrderId": "",
    "externalReferenceId": "a96ee8fe-c440-4d1c-ae5b-a90e1825aef",
    "orderId": "123432123",
    "customerId": "1005944528",
    "currencyCode": "USD",
    "orderType": "SWITCH",
    "status": "",
    "lineItems": [
        {
 
                "extLineItemNumber": 1,
                "offerId": "65304479CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": ""
                 
        }
    ],
    "cancellingItems":[
		{
				"offerId": "65322651CA02A12",
				"extLineItemNumber": 1,
                "quantity": 15,
				"discountCode": "HVD_L17PRE",
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
				"referenceLineItemNumber": 1,
		}
	],
    "creationDate": "2025-03-17T11:42:29Z"
}
```

### Preview Revert Switch Order

Use `PREVIEW_REVERT_SWITCH` as the `orderType` in the Create Order API to get the required details and to check the validity of the reversal.

#### Request

**Query parameters**

| Parameter | Required | Description |
|---|--|--|
|reassign-users|Optional |Specifies whether to automatically reassign users from the original subscription to the upgraded product. \<br /\>**Note:** Automatic user reassignment is not supported for upgrades from Teams to Enterprise or between Enterprise subscriptions.|
|fetch-price|Optional| Specifies whether to fetch pricing details while previewing the mid-term upgrade offers.|

**Sample request URL:** POST `https://partners-stage.adobe.io/v3/customers/1005944528/orders?fetch-price=true`

Request body:

```json
{
    "orderType" : "PREVIEW_REVERT_SWITCH",
    "currencyCode" : "USD",
    "referenceOrderId": "987654334",
    "lineItems" : [
        {
            "extLineItemNumber" : 1,
            "offerId" : "65322651CA02A12",
            "quantity" : 15,
            "discountCode": "HVD_L17PRE",
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
            "discountCode": "HVD_L17PRE",
            "quantity" : 15,
        }
    ]   
}
```

#### Response

```json
{
    "referenceOrderId": "",
    "externalReferenceId": "a96ee8fe-c440-4d1c-ae5b-a90e1825aef",
    "orderId": "",
    "customerId": "1005944528",
    "currencyCode": "USD",
    "orderType": "PREVIEW_REVERT_SWITCH",
    "referenceOrderId": "987654334",
    "status": "",
    "pricingSummary": [
    {
        "totalLineItemPrice": 810.00,
        "currencyCode": "USD"
        }
    ],
    "lineItems": [
        {
 
                "extLineItemNumber": 1,
                "offerId": "65304479CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": "werb5a4ctrew879af7204c7daee1NA",
                "proratedDays": 90,
                "pricing": {
                    "partnerPrice": 365.00,
                    "discountedPartnerPrice": 328.50,
                    "netPartnerPrice": 81.00,
                    "lineItemPrice": 810.00
                }
        }
    ],
    "cancellingItems":[
        {
                "offerId": "65322651CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
                "pricing": {
                    "partnerPrice": -300.00,
                    "discountedPartnerPrice": 0.00,
                    "netPartnerPrice": -300.00,
                    "lineItemPrice": -300.00
                },
                "referenceLineItemNumber": 1,
        }
    ]
    "creationDate": "2025-03-17T11:42:29Z"
}
```

### Revert Switch Order

Use `REVERT_SWITCH` as the `orderType` in the Create Order API to revert to the plan from which you upgraded.

#### Request

```json
{
    "orderType" : "REVERT_SWITCH",
    "currencyCode" : "USD",
    "referenceOrderId": "987654334",
    "lineItems" : [
        {
            "extLineItemNumber" : 1,
            "offerId" : "65322651CA02A12",
            "quantity" : 15,
            "discountCode": "HVD_L17PRE"
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
            "discountCode": "HVD_L17PRE",
            "quantity" : 15,
        }
    ]   
}
```

#### Response

```json
{
    "referenceOrderId": "",
    "externalReferenceId": "a96ee8fe-c440-4d1c-ae5b-a90e1825aef",
    "orderId": "",
    "customerId": "1005944528",
    "currencyCode": "USD",
    "orderType": "REVERT_SWITCH",
    "referenceOrderId": "987654334",
    "status": "",
    "pricingSummary": [
    {
        "totalLineItemPrice": 810.00,
        "currencyCode": "USD"
        }
    ],
    "lineItems": [
        {
 
                "extLineItemNumber": 1,
                "offerId": "65304479CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": "werb5a4ctrew879af7204c7daee1NA",
                "proratedDays": 90,
                "pricing": {
                    "partnerPrice": 365.00,
                    "discountedPartnerPrice": 328.50,
                    "netPartnerPrice": 81.00,
                    "lineItemPrice": 810.00
                }
        }
    ],
    "cancellingItems":[
        {
                "offerId": "65322651CA02A12",
                "quantity": 15,
                "discountCode": "HVD_L17PRE",
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
                "pricing": {
                    "partnerPrice": -300.00,
                    "discountedPartnerPrice": 0.00,
                    "netPartnerPrice": -300.00,
                    "lineItemPrice": -300.00
                },
                "referenceLineItemNumber": 1,
        }
    ]
    "creationDate": "2025-03-17T11:42:29Z"
}
```
