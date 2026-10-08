# Manage Three-Year Commits

Three-Year Commit (3YC) is a loyalty program that allows customers to get a greater discount level and a price lock for three terms (the current and two additional terms). Customers commit to purchasing and maintaining a minimum quantity throughout the 3-year period.

Using the [Preview Order](../order-management/order-scenarios.md#preview-an-order) API to determine the correct discount level is important. The Preview Order API uses the customer’s 3YC commitment and any accepted commitment requests to return the Offer ID with the best discount.

### Extended compliance window for 3YC commitments

Effective Date: June 13, 2025

The compliance window for fulfilling the 3-Year Commitment (3YC) license requirements has been extended. Previously, customers had 7 days from the date of accepting a 3YC agreement to meet their license commitment. This window has now been extended to 30 days.

- Customers now have up to 30 days from the acceptance date to ensure their contract reflects the agreed minimum number of licenses.
- If the customer fails to meet this requirement within the 30-day window, the 3YC agreement will become non-compliant, and the contract will no longer qualify for 3YC discounts.

### Term length calculation for VIP Marketplace 3YC agreements

Effective Date: June 1, 2025

The end date of a 3YC term depends on when the customer opts into the agreement relative to their annual renewal cycle:

- If a customer enters a 3YC agreement within 30 days before their anniversary date (AD-30): The 3YC term will end one day before the 4th anniversary of the original Join Date, thereby offering a full 3-year benefit.
- Customer enters a 3YC agreement at any other time during their annual term: The 3YC term will end one day before the 3rd anniversary of the 3YC agreement's start date, which may result in slightly less than 3 full years of benefit.

This distinction ensures fair benefit distribution while aligning with the annual contract renewal process.

### Extended-term customers and 3YC enrollment

Extended-term customers, created with a `cotermDate` more than one year in the future as described in [Create Customer Account](create-customer-account.md) cannot enroll in 3YC until they enter the final year of their extended term.

- **Within the last year of the term** (from one year before the anniversary date through the anniversary date): A 3YC enrollment request automatically converts the customer from an extended-term customer to a standard commitment and creates the 3YC clause in a single operation. No separate action is required.
- **More than one year before the anniversary date**: The 3YC enrollment request is rejected, and the customer remains an extended-term customer.

Use the [PATCH Update Customer API](update-customer-account.md) with a `commitmentRequest` to enroll an extended-term customer once the customer is within the final year of the term.

## 3YC Workflows

Customers can enroll in 3YC through one of three workflows. In all cases, customers must accept the commitment terms (minimum quantities and end date) in the Adobe Admin Console before receiving discounts.

1. New Customer: Partners can set the requested minimum quantities during customer creation. Once the account becomes active, this triggers the 3YC customer acceptance workflow. API endpoints:
   - [POST Create Customer API](create-customer-account.md)
   - [GET Customer API](get-customer-account.md)
2. Existing Customer: Partners can update existing customers (with or without an active 3YC) with new requested minimum quantities. This triggers the 3YC customer acceptance workflow once the update is processed. Extended-term customers can only enroll in 3YC once they reach the last year of their term; see [Extended-term customers and 3YC enrollment](#extended-term-customers-and-3yc-enrollment) for details. API endpoints:
   - [PATCH Update Customer API](update-customer-account.md)
   - [GET Customer API](get-customer-account.md)

3. Existing VIP Customer: Existing VIP customers with active 3YC can be transferred to VIP Marketplace and retain their 3YC commitment terms, which can be retrieved using the GET Customer API. API endpoints:

   - [POST Transfer Subscriptions API](../migration/transfer-subscription.md)
   - [GET Customer API](get-customer-account.md)

## 3YC discount levels

The customer can be enrolled in 3YC for either licenses, consumables, or both. If enrolled in only one, there is no minimum quantity for the other type, and the customer gets no discount for that type.

Different volume discount levels are applicable based on the market segment.

### For the GOV and COM market segments

Volume discounts apply only to qualifying licenses.

  | Discount Level | Minimum Number of Committed Licenses |
  |----------------|--------------------------------------|
  | 12             | 10-49                                |
  | 13             | 50-99                                |
  | 14             | 100+                                 |

### For the EDU market segment

  | Discount Level | Minimum Number of Committed Licenses |
  |----------------|--------------------------------------|
  | 02             | 10-49                                |
  | 03             | 50-99                                |
  | 04             | 100+                                 |
  
  For more information, refer to [Volume Discounts](https://cbconnection.adobe.com/en/vip-marketplace-guide/how-it-works/volume-discounts).
  
## Discount tier for consumables
  
  |Discount Tier | Minimum Number of Committed Transactions |
  |------|-------------------------|
  | TB   | 1,000–2,499             |
  | TC   | 2,500–4,999             |
  | TD   | 5,000–14,999            |
  | TE   | 15,000–49,999           |
  | TF   | 50,000–99,999           |
  | TG   | 100,000+                |

**Note:** When a 3-Year Commitment (3YC) contract expires after its designated end date, the volume discount levels are reset to the following defaults:

- **Licenses:** Level 01
- **Consumables:** Tier T1

## Arriving at partner price for 3YC customers

The diagram illustrates the decision-making process for determining partner pricing for 3YC customers.

![Partner pricing for 3YC customers](../image/3yc_pricing.png)

The flow ensures accurate pricing by considering factors such as customer type, offer matches, and applicable discounts before arriving at the final price charged by Adobe.

## Commitment request considerations

- Discount levels are updated whenever the commitment status changes, such as when a new commitment is made or an existing one expires.
- During the order preview, if the customer has met the minimum committed quantity, the preview response returns the discount level for that `commitmentRequest`.
- Placing the order for that quantity changes the  `commitmentRequest` status to COMMITTED and creates the corresponding `commitment` object.
  - If the order quantity exceeds the MPQ (Minimum Purchase Quantity):
    - The `commitmentRequest` will be null.
    - The `commitment` section will be populated with MPQ details.
  - If the orders are placed multiple times with smaller quantities, until the sum of fulfilled order quantities exceeds the MPQ, then:
    - The `commitmentRequest` will continue to have a value.
    - The `commitment` section will remain null.

- To apply 3YC for multiple offer types, the customer can enroll in one `offerType` (LICENSE or CONSUMABLE) first, and then add the other later. The committed `offerType` and the new `offerType` must be included in the `commitmentRequest`.

The following sections provide more details of the changes to the objects:

#### commitmentRequest object

**Example 1:** Sample request before placing the order or if the total order quantity is less than the Minimum Purchase Quantity (MPQ):

```json
{
  "companyProfile": {
    "companyName": "Fairmont",
    "preferredLanguage": "en-US",
    "address": {
      "country": "US",
      "region": "CA",
      "city": "San Jose",
      "addressLine1": "200 Fairmont Ave",
      "addressLine2": "Apt 123",
      "postalCode": "95110-1234",
      "phoneNumber": "800-123-4567"
    },
    "contacts": []
  },
  "benefits": [
    {
      "type": "THREE_YEAR_COMMIT",
      "commitmentRequest": {
        "minimumQuantities": [
          {
            "offerType": "LICENSE",
            "quantity": 10
          },
          {
            "offerType": "CONSUMABLES",
            "quantity": 1000
          }
        ]
      }
    }
  ]
}
```

**Example 2:** Response where the net fulfilled order quantities is more than MPQ:

```json

{
  "externalReferenceId": "18d9b63c-d81b-4e0a-b12c-9c3bc33f80d",
  "customerId": "P1005229864",
  "resellerId": "P1000086129",
  "status": "1000",
  "companyProfile": {
    ...
    ...
  },
  "discounts": [
    {
      "offerType": "LICENSE",
      "level": "14"
    }
  ],
  "tags": [],
  "cotermDate": "2026-07-07",
  "creationDate": "2025-07-07T04:58:17Z",
  "benefits": [
    {
      "type": "THREE_YEAR_COMMIT",
      "commitment": {
        "startDate": "2025-07-07",
        "endDate": "2028-07-05",
        "status": "COMMITTED",
        "minimumQuantities": [
          {
            "offerType": "LICENSE",
            "quantity": 100
          }
        ]
      },
      "commitmentRequest": null,
      "recommitmentRequest": null
    }
  ],
  "globalSalesEnabled": false,
  "links": {
    "self": {
      "uri": "/v3/customers/P1005229864",
      "method": "GET",
      "headers": []
    }
  }
}
```

**Example 3:** MPQ for Multiple offer types

If the total ordered quantity exceeds the MPQ for one offer type, for example, CONSUMABLES, while not exceeding the MPQ for another offer type (LICENSE), the system will behave as follows:

- Both `commitment` and `commitmentRequest` parameters will not be null.
- The offer type that has satisfied the MPQ will be reflected in the `commitment` section.
- The offer type that has not satisfied the MPQ will remain in the `commitmentRequest` section.

```json
{
    "externalReferenceId": "14b783eb-ba91-4f3b-ba38-417e96c5c92",
    "customerId": "P1005229895",
    "resellerId": "P1000086129",
    "status": "1000",
    "companyProfile": {
        ...
        ...
    },
    "discounts": [
        {
            "offerType": "LICENSE",
            "level": "13"
        },
        {
            "offerType": "CONSUMABLES",
            "level": "TC"
        }
    ],
    "tags": [],
    "cotermDate": "2026-07-07",
    "creationDate": "2025-07-07T05:38:37Z",
    "benefits": [
        {
            "type": "THREE_YEAR_COMMIT",
            "commitment": {
                "startDate": "2025-07-07",
                "endDate": "2028-07-05",
                "status": "COMMITTED",
                "minimumQuantities": [
                    {
                        "offerType": "CONSUMABLES",
                        "quantity": 1000
                    }
                ]
            },
            "commitmentRequest": {
                "startDate": "2025-07-07",
                "endDate": "2028-07-05",
                "status": "ACCEPTED",
                "minimumQuantities": [
                    {
                        "offerType": "LICENSE",
                        "quantity": 100
                    }
                ]
            },
            "recommitmentRequest": null
        }
    ],
    "globalSalesEnabled": false,
    "links": {
        "self": {
            "uri": "/v3/customers/P1005229895",
            "method": "GET",
            "headers": []
        }
    }
}
```

**Notes:**

- The `commitmentRequest` object is used for requesting 3YC for a customer without a commitment, and for requesting 3YC quantity increase for a customer with an existing commitment.
- Overwrites any existing `commitmentRequest`.
- Cannot be requested alongside `recommitmentRequest` or if customer has an existing `recommitmentRequest`.
- Does not need to include quantities for all `offerTypes`.
- If the customer does not have a `cotermDate` (they have not placed an order) at the time of acceptance, the `endDate` will be 3 years from the acceptance date and the `cotermDate` will be set at that point.

#### recommitmentRequest object

Sample request:

```json
{
  "companyProfile": {
    "companyName": "Fairmont",
    "preferredLanguage": "en-US",
    "address": {
      "country": "US",
      "region": "CA",
      "city": "San Jose",
      "addressLine1": "200 Fairmont Ave",
      "addressLine2": "Apt 123",
      "postalCode": "95110-1234",
      "phoneNumber": "800-123-4567"
    },
    "contacts": []
  },
  "benefits": [
    {
      "type": "THREE_YEAR_COMMIT",
      "recommitmentRequest": {
        "minimumQuantities": [
          {
            "offerType": "LICENSE",
            "quantity": 10
          },
          {
            "offerType": "CONSUMABLES",
            "quantity": 1000
          }
        ]
      }
    }
  ]
}
```

## 3YC flow diagrams

The diagram below shows the lifecycle of the commitmentRequest object. The same lifecycle applies to the `recommitmentRequest`, except it cannot move to COMMITTED status until the current commitment ends.

![3YC flow diagram](../image/3yc_flow_diagram.jpg)
