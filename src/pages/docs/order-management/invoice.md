
# Pricing and invoicing data

Partners in the VIP Marketplace can view invoicing and pricing details for customer orders through the [Preview Order](order-scenarios.md#preview-an-order), [Preview Renewal](order-scenarios.md#preview-renewal-orders), and [Get Order APIs](get-order.md).

Pricing data returned through these APIs reflects partner pricing, discounts, proration logic, and totals as stored in Adobe systems. Pricing is available only for single‑currency orders and for orders with completed status (1000).

## How partners can use these APIs

The Preview and Get Order APIs return a defined subset of pricing fields, including:

- `partnerPrice`
- `discountedPartnerPrice`
- `netPartnerPrice`
- `lineItemPartnerPrice`
- `pricingSummary`
- `isDealRegistered` when Instant Deal Registration was auto-injected for the line item
- `earnedDealRegPerUnit` and `earnedDealRegAmount` for qualifying line items
- `totalEarnedDealRegAmount` in the pricing summary

Adobe Instant Deal Registration amounts are additive and separate from flexible discounts. They do not reduce `discountedPartnerPrice`, `lineItemPartnerPrice`, or `totalLineItemPartnerPrice`. Adobe evaluates eligible line items automatically, independently of whether a flexible discount code is supplied; partners never send or receive an Instant Deal Registration code or ID.

On Return orders, `earnedDealRegPerUnit`, `earnedDealRegAmount`, and `totalEarnedDealRegAmount` are not represented as negative values.

The functionality allows partners to:

- View invoicing details for a specific order by calling the [Get Order by ID](get-order.md#get-details-of-a-specific-order) API with the required query parameter.
- View invoicing details across a customer’s order history by calling the [Get Order History](get-order.md#get-the-order-history-of-a-customer) API with the required query parameter.
- Retrieve pricing during order preview and renewal preview by using the [Preview Order](order-scenarios.md) and [Preview Renewal](order-scenarios.md#preview-renewal-orders) APIs with the pricing parameter.

## Preview with pricing

Partners can retrieve the pricing details in the `Preview Order` and `Preview Renewal` APIs by setting the `fetch-price` query parameter to `true` in the request URL. This returns real-time partner pricing details for Adobe products, helping partners and resellers better estimate how much Adobe will invoice for an order.

**Note:** This section provides an example for Preview Order API. The example for Preview Renewal is not listed as the pricing details returned in the response are the same.

**Sample request URL:** `<ENV>/v3/customers/{customer-id}/orders?fetch-price=true`

- Pricing returns the partner price, discount calculations, prorated values, and total line item pricing.
- Pricing is based on the Pacific Standard Time (PST) time zone at the moment the preview is created.
- Prices returned are estimates. Final pricing may differ if the order is submitted at a different date or time.
- Pricing returned reflects the price between Adobe and the direct partner.
- Multi‑currency preview pricing is not supported.

**Sample request body:**

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

#### Sample response

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

#### Pricing details in lineitems (lineItems[].pricing)

| Field                  | Description                                                                                                                                         |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| partnerPrice           | Non-prorated full-term unit price for the given offer, including any applicable volume discounts, but before applying flexible discounts and taxes. |
| discountedPartnerPrice | Unit price after applying discount. \<br /\>                                                                                                          |
| netPartnerPrice        | Prorated unit price after discount.                                                                                                                 |
| lineItemPartnerPrice   | Prorated price of the item after discount and before tax. This is the price that the partner needs to pay to Adobe for this item.                   |
| earnedDealRegPerUnit | Deal registration amount per unit. Included only for a qualifying line item when `fetch-price=true`. |
| earnedDealRegAmount | Total deal registration amount for the line item: `earnedDealRegPerUnit` multiplied by quantity. Included only for a qualifying line item when `fetch-price=true`. |

**Note:** The `proratedDays` parameter in the response specifies the number of days for which the order will be invoiced. This parameter appears only when the `fetch-price` parameter is set to `true` in the request.

#### Pricing Summary (pricingSummary[])

| Field                     | Description                                                                                    |
|---------------------------|------------------------------------------------------------------------------------------------|
| totalLineItemPartnerPrice | Sum of all line item prices in the order.                                                      |
| totalEarnedDealRegAmount      | Sum of `earnedDealRegAmount` across qualifying line items when `fetch-price=true`. |
| currencyCode              | Currency used for pricing. This is specified in ISO 4217 currency code. Examples: USD and EUR. |

For complete set of request and response parameter descriptions, refer to [Order resource](../references/resources.md#order-top-level-resource).

## Pricing in Order APIs

Pricing appears in the following two order APIs when the `fetch-price=true` query parameter is included in the request:

- [Get Order by ID](#get-order-by-id)
- [Get Order History](#get-order-history)

Pricing is returned only when:

- The request includes the pricing query parameter
- The order is a single‑currency order
- The order status is 1000
- The order was placed after the pricing feature was introduced

### Get Order by ID

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
    },
  ],
}
```

### Get Order History

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
                            "discountedPartnerPrice": 345.00,  
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
