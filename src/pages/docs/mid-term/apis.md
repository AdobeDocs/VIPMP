# Manage mid-term upgrades through APIs

The Adobe Commerce Partner API provides comprehensive support for mid-term upgrade operations. The following functionalities are available:

- **Subscription-level upgrade eligibility**  
  Partners can retrieve eligible upgrade offers across all customer subscriptions.

- **Targeted subscription upgrade offers**  
  Partners can query upgrade eligibility for a specific subscription.

- **Upgrade path discovery by market segment, country, and language**  
  Partners can list all valid upgrade paths, filtered by market segment, country, and language.

- **Switch order reversion**  
  Partners can initiate reversal of switch orders to restore the original subscription state.

The following steps are involved in the upgrade process:

- [Discover upgrade paths and preview switch order](#discover-upgrade-path)
- [Create switch order](#apply-switch-plan)
- [Verify the upgrade](#verify-switch-order)
- [Revert switch order](#revert-switch-order)

## Discover upgrade path

You can use the following APIs to get the available switch plans and preview them before applying:

- [Get Offer Switch Paths](#1-retrieve-upgrade-paths)
- [Preview Switch Order](#2-preview-switch-order)

### 1. Retrieve upgrade paths

The `GET Offer Switch Paths` API enables Adobe partners to programmatically retrieve valid upgrade paths for customer subscriptions or offers, based on key business filters such as market segment, country, and language.

This API helps partners enable customers to upgrade their product subscriptions mid-term, rather than waiting until the subscription anniversary. This capability unlocks immediate access to higher-tier products and features, while allowing Adobe and its partners to capture revenue opportunities without delay.

| Path                                                                 | Request Method |
|----------------------------------------------------------------------|----------------|
| `<env root url>/v3/offer-switch-paths` | GET            |

#### Request

**Header:**

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

**Query parameters:**

| Parameter      | Required | Description                                                                                            |
|----------------|----------|--------------------------------------------------------------------------------------------------------|
| market-segment | Yes      | The market segment for which the upgrade path is applicable. |
| country        | Yes      | Specifies the country for which the upgrade path is applicable.                                        |
| offer-id       | No       | Fetches all upgrade paths available for the specified offer.                                               |
| subscription-id      | No       | See description corresponding to `customer-id`                                               |
| customer-id     | No       | If `subscription-id` and `customer-id`  query parameters are provided: \<br /\> - Partners do not need to pass other fields. \<br /\> - By default, the country will be taken from the customer’s country, or from the deployment’s country if the subscription has deployment details unless explicitly overridden by the partner. \<br /\> - The customer segment will be same as customer market segment. \<br /\> - The language will default to MULT, unless explicitly overridden by the partner by passing the corresponding query parameter.                                              |
| language       | No      | Language for which they want upgrade paths.                                                            |
| limit | No      | Specifies the maximum number of records (items) to return in a single response. Default value is 20. |
| offset | No     | Specifies the starting position in the dataset from which to return results. Default value is 0. |

**Request body**

None.

**Request URL:** `/v3/offer-switch-paths?market-segment=COM&country=US&language=MULT`

#### Response

```json
{
    "totalCount": 3,
    "count": 3,
    "offset": 0,
    "limit": 20,
    "productUpgrades": [
        {
            "sourceBaseOfferId": "30002139CC01A12",
            "targetList": [
                {
                    "targetBaseOfferId": "30002125CC01A12",
                    "sequence": 1,
                    "switchType": "PARTIAL_ALLOWED"
                },
                {
                    "targetBaseOfferId": "30013365CC01A12",
                    "sequence": 2,
                    "switchType": "PARTIAL_ALLOWED"
                },
                {
                    "targetBaseOfferId": "30002230CC01A12",
                    "sequence": 3,
                    "switchType": "PARTIAL_ALLOWED"
                }
            ]
        },
        {
            "sourceBaseOfferId": "30002994CC01A12",
            "targetList": [
                {
                    "targetBaseOfferId": "30005695CC01A12",
                    "sequence": 1,
                    "switchType": "PARTIAL_ALLOWED"
                },
                {
                    "targetBaseOfferId": "30002245CC01A12",
                    "sequence": 2,
                    "switchType": "PARTIAL_ALLOWED"
                },
                {
                    "targetBaseOfferId": "30002230CC01A12",
                    "sequence": 3,
                    "switchType": "PARTIAL_ALLOWED"
                }
            ]
        },
        {
            "sourceBaseOfferId": "30002125CC01A12",
            "targetList": [
                {
                    "targetBaseOfferId": "30002168CC01A12",
                    "sequence": 3,
                    "switchType": "PARTIAL_ALLOWED"
                }
            ]
        }
    ]
}
```

Response parameters:

| Parameter               | Required | Type    | Description |
|------------------------|----------|---------|-------------|
| productUpgrades        | No       | Object  | Contains the list of available upgrade paths. |
| sourceBaseOfferId      | Yes      | String  | The base offerId from which the customer can upgrade. |
| targetType             | Yes      | String  | Defines the scope of the target items (example: PRODUCT_LIST). |
| targetList             | Yes      | List    | Contains the list of target offers and their details. |
| targetList[].sequence  | Yes      | Integer | Defines the order in which upgrade paths should be presented. |
| targetList[].targetBaseOfferId | Yes | String | The base offerId to which customer can upgrade. |
| targetList[].switchType| Yes      | String  | Indicates whether the entire quantity of the original subscription can be upgraded to the new product or only a portion of it. |
| totalCount             | Yes      | Integer | The total number of items matching the query across all pages. Reflects the full dataset size regardless of pagination. |
| count                  | Yes      | Integer | The number of items included in the current response. Typically equals or is less than the limit. |
| offset                 | Yes      | Integer | The zero-based index of the first item in this response within the total result set. Indicates how many items were skipped. |
| limit                  | Yes      | Integer | The maximum number of items requested per response (page size). Defines how many items should be returned per request. |

### 2. Preview Switch Order

The newly introduced `Preview Switch` option in the `OrderType` parameter of the Create Order API helps partners to generate upgrade quotes prior to placing a switch order.

|Endpoint | Method |
|--|--|
|`<env root url>/v3/customers/{{customerId}}/orders` | POST|

### Request

**Query parameters**

| Parameter | Required | Description |
|---|--|--|
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
            "quantity" : 15
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
            "quantity" : 15
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
| lineItems.subscriptionId          | Optional \<br /\>  This parameter is relevant in the request if an existing subscription for the product exists.                          | String                   | Indicates which subscription customer is trying to switch.                  |
| lineItems.offerId                 | Required                            | String                   | Indicates which product customer is switching to                           |
| lineItems.quantity                | Required                            | String                   | Quantity from subscription to be switched.                                  |
| cancellingItems                   | Required for Switch type Order      | List | List of items the customer intends to cancel as part of the switch process.                                |
| cancellingItems.extLineItemNumber | Required                            | String                   | A unique identifier for the line item being canceled.                                      |
| cancellingItems.referenceLineItemNumber | Required                     | String                   | Reference line item number of the item being canceled.                                 |
| cancellingItems.subscriptionId    | Required                            | String                   | Indicates subscription to be canceled.                                           |

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
                "subscriptionId": "",
                "proratedDays": 90,
                "pricing": {
                    "partnerPrice": 365.00,
                    "discountedPartnerPrice": 328.50,
                    "netPartnerPrice": 81.00,
                    "lineItemPartnerPrice": 810.00
                }
        }
    ],
    "cancellingItems":[
        {
                "extLineItemNumber": 1,
                "referenceLineItemNumber": 1,
                "offerId": "65322651CA02A12",
                "quantity": 15,
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
                "status": "",
                "currencyCode": "USD",
                "pricing": {
                    "partnerPrice": 300.00,
                    "discountedPartnerPrice": 0.00,
                    "netPartnerPrice": -300.00,
                    "lineItemPartnerPrice": 300.00
                },
        }
    ]
    "creationDate": "2025-03-17T11:42:29Z"
}
```

Response parameters:

The `cancellingItems` object lists the switch plan with corresponding pricing details. The following table lists the parameters in the cancellingItems object. For more details on the entire parameters of the Order source, refer to [Order object](../references/resources.md#order-top-level-resource).

|Parameter|Not Null|Data Type|Description|Included in Response by Default|
|--|--|--|--|--|
| cancellingItems.extLineItemNumber              | YES      | String                   | Unique index for line item                          | Yes                              |
| cancellingItems.offerId               | YES      | String                   | Part number of the item being canceled.                          | Yes                              |
| cancellingItems.quantity              | YES      | Integer                  | Quantity being canceled.                                        | Yes                              |
| cancellingItems.subscriptionId        | YES      | String                   | Subscription ID associated with the item being canceled.                             | Yes                              |
| cancellingItems.status              | YES      | Integer                  | Indicates the status of the order                                        | Yes                              |
| cancellingItems.currencyCode              | YES      | Integer                  | Currency code for this order                                        | Yes                              |
| cancellingItems.pricing.partnerPrice | YES      | Decimal                  | Partner price of the item being canceled.                         | Yes                              |
| cancellingItems.pricing.discountedPartnerPrice | YES | Decimal          | Partner price after applying discounts.                      | Yes                              |
| cancellingItems.pricing.netPartnerPrice | YES    | Decimal                  | Net partner price of the item being canceled after discounts.                               | Yes                              |
| cancellingItems.pricing.lineItemPartnerPrice | YES      | Decimal                  | Final price of the item being canceled.                                | Yes                              |
| cancellingItems.referenceLineItemNumber | YES    | Integer                  | Reference line item number.                                | Yes                              |
| creationDate                          | YES      | DateTime                 | Timestamp of the order creation.                               | Yes                              |

## Apply switch plan

Use the `Create Order` API with `orderType` as SWITCH to switch from the current order to a new one. Creating a switch order is functionally similar to a preview request, but it does not include pricing details in the response. Once placed, the order appears in the order history, and the same logic applies for tracking and managing orders.

This API facilitates upgrade orders with "lineItems" and "cancellingItems" product details.

**Note:** If the partner has not explicitly set a specific renewal quantity, Adobe automatically sets the renewal quantity of the original subscription to its current value during any full or partial upgrade. This behavior ensures that partners do not need to manually update the renewal quantity after completing an upgrade transaction.

#### Request

**Query parameter**

|Parameter |Required | Description|
|--|--|--|
|reassign-users|Optional |Specifies whether to automatically reassign users from the original Teams subscription to the upgraded one. \<br /\>**Note:** Automatic user reassignment is not supported for upgrades from Teams to Enterprise or between Enterprise subscriptions.|

Sample request URL: `POST https://partners-stage.adobe.io/v3/customers/1005944528/orders?reassign-users=true`

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
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
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
            "subscriptionId": ""
                 
        }
    ],
    "cancellingItems":[
		{
			"offerId": "65322651CA02A12",
			"extLineItemNumber": 1,
            "quantity": 15,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
			"referenceLineItemNumber": 1,
		}
	],
    "creationDate": "2025-03-17T11:42:29Z"
}
```

## Verify Switch Order

You can use the following APIs to verify the upgrade and reversal of the upgrade:

- [Get order history](#get-order-history)
- [Get details of a specific order](#get-details-of-a-specific-order)

### Get Order History

#### Request

Sample request URL: `GET {{HOST}}/v3/customers/{{customerId}}/orders?offset=0&limit=25&order-type=SWITCH`

#### Response

Sample response:

```json
{
    "totalCount": 0,
    "count": 0,
    "offset": 0,
    "limit": 25,
    "items": [
{
    "referenceOrderId": "",
    "externalReferenceId": "a96ee8fe-c440-4d1c-ae5b-a90e1825aef",
    "orderId": "",
    "customerId": "1005944528",
    "currencyCode": "USD",
    "orderType": "SWITCH",
    "status": "1000",
    "lineItems": [
        {
 
                "extLineItemNumber": 1,
                "offerId": "65304479CA02A12",
                "quantity": 15,
                "subscriptionId": "asdfewaw1879af7204c7daee1NA"
        }
    ],
  "cancellingItems":[
		{
				"offerId": "65322651CA02A12",
				"extLineItemNumber": 1,
                "quantity": 15,
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
				"referenceLineItemNumber": 1,
		}
	],
    "creationDate": "2025-03-17T11:42:29Z"
}
],
    "links": {
        "self": {
            "uri": "/v3/customers/1005944528/orders?order-type=SWITCH",
            "method": "GET",
            "headers": []
        }
    }
}
```

### Get details of a specific order

#### Request

Sample request URL: `GET {{HOST}}/v3/customers/{{customerId}}/orders/{{orderId}}`

#### Response

Sample response:

```json
{
    "referenceOrderId": "",
    "externalReferenceId": "a96ee8fe-c440-4d1c-ae5b-a90e1825aef",
    "orderId": "",
    "customerId": "1005944528",
    "currencyCode": "USD",
    "orderType": "SWITCH",
    "status": "1000",
    "lineItems": [
        {
 
                "extLineItemNumber": 1,
                "offerId": "65304479CA02A12",
                "quantity": 15,
                "subscriptionId": "drger4cb14561879af7204c7daee1NA"
        }
    ],
    "cancellingItems":[
		{
				"offerId": "65322651CA02A12",
				"extLineItemNumber": 1,
                "quantity": 15,
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
				"referenceLineItemNumber": 1,
		}
	],
    "creationDate": "2025-03-17T11:42:29Z"
}
```

## Revert Switch Order

Partners can perform upgrade reversals within 14 days. It restores original product licenses and de-provisions upgraded ones. Revert logic selects original orders in a first-in, first-out (FIFO) manner.

**Note:** Refunds are calculated based on the current price, discount level, and quantity at the time of revert.

Reverting a switch order involves:

- [Preview Revert Switch](#preview-revert-switch)
- [Revert Switch Order](#revert-switch-order-1)

### Preview Revert Switch

Use `PREVIEW_REVERT_SWITCH` as the `orderType` in the Create Order API to get the required details and to check the validity of the reversal.

#### Request

**Query parameters**

| Parameter | Required | Description |
|---|--|--|
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
            "quantity" : 15
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
            "quantity" : 15
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
                "subscriptionId": "werb5a4ctrew879af7204c7daee1NA",
                "proratedDays": 90,
                "pricing": {
                    "partnerPrice": 365.00,
                    "discountedPartnerPrice": 328.50,
                    "netPartnerPrice": 81.00,
                    "lineItemPartnerPrice": 810.00
                }
        }
    ],
    "cancellingItems":[
        {
                "offerId": "65322651CA02A12",
                "quantity": 15,
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
                "pricing": {
                    "partnerPrice": -300.00,
                    "discountedPartnerPrice": 0.00,
                    "netPartnerPrice": -300.00,
                    "lineItemPartnerPrice": -300.00
                },
                "referenceLineItemNumber": 1,
        }
    ]
    "creationDate": "2025-03-17T11:42:29Z"
}
```

### Revert Switch Order

Use `REVERT_SWITCH` as the `orderType` in the Create Order API to revert to the plan from which you upgraded.

**Note:** You can use the `reassign-user=true` parameter in the request URL to automatically reassign users from the upgraded Teams subscription to the original Teams subscription.

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
            "quantity" : 15
        }
    ],
    "cancellingItems":[
        {
            "extLineItemNumber": 1,
            "referenceLineItemNumber": 1,
            "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
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
                "subscriptionId": "werb5a4ctrew879af7204c7daee1NA",
                "proratedDays": 90,
                "pricing": {
                    "partnerPrice": 365.00,
                    "discountedPartnerPrice": 328.50,
                    "netPartnerPrice": 81.00,
                    "lineItemPartnerPrice": 810.00
                }
        }
    ],
    "cancellingItems":[
        {
                "offerId": "65322651CA02A12",
                "quantity": 15,
                "subscriptionId": "abfb5a4cb14561879af7204c7daee1NA",
                "pricing": {
                    "partnerPrice": -300.00,
                    "discountedPartnerPrice": 0.00,
                    "netPartnerPrice": -300.00,
                    "lineItemPartnerPrice": -300.00
                },
                "referenceLineItemNumber": 1,
        }
    ]
    "creationDate": "2025-03-17T11:42:29Z"
}
```
