# Resources and fields

Please see the Validations/Regular Expressions section for any regular expressions for specific fields.

## Reseller (top-level resource)

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|externalReferenceId | String \<br /\> Optional| Marketplace’s ID for reseller. ID does not need to be unique.| Max: 35 characters|
|distributorId | String | Adobe-generated unique ID for distributor tied to this reseller| Max: 40 characters|
|resellerId (read only)| String | Unique ID for reseller created upon account creation |Max: 40 characters|
|companyProfile | CompanyProfile resource| Information about the reseller| |
|creationDate (read only)| String (datetime)| Date and time of account creation in UTC| |
|status (read only)| String | Status code for the reseller account  |4 characters |
|links (read only)| Links resource | Deep links to get customer account details| |

## Customer (top-level resource)

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
|status (read only)| String | Status code of customer account | 4 characters|
|links (read only)| **Links** resource | Deep links to get customer account details| |

### linkedMembership

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|id |String  |Unique ID of the Linked Membership.  | |
|name |String  |Name of the Linked Membership  | |
|type  |String  |Linked Membership type. Possible Values are:  \<br /\> - `STANDARD` \<br /\> - `CONSORTIUM` | |
|linkedMembershipType  |String  |Member type. Possible values are: \<br /\> - `MEMBER` \<br /\>- `OWNER`  | |
|creationDate  |Date  |Date of creation.  | |

## Deployment (top-level resource)

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|deploymentId |String | Unique ID for the deployment generated upon deployment creation| Max: 40 characters|
|companyProfile | `CompanyProfile` resource| Information about the customer| |
|creationDate (read only)| String (datetime)| Date and time of account creation in UTC| |
|status | String | Status code of customer account | 4 characters|
|links (read only)| `Links` resource | Deep links to get customer account details| |

## CompanyProfile

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|companyName | String | Name of company| Min: 4 characters \<br /\> Max: 80 characters|
|preferredLanguage | String | Preferred language for company (IETF hyphenated language tag)| Max: 40 characters|
|address | `Address` resource| Address of company | |
|contacts | Array of `Contact` resources| Points of contact for admin console | Min: One contact \<br /\> No limit|

## Address

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|country | String | Country of address  (ISO 3166 standard) | 2 or 3 characters|
|region | String | State/region of the address (ISO 3166-2 \<br /\> standard – only send code after hyphen). \<br /\> Example: US-CA → CA| Max: 255 characters|
|city | String | City | Max: 40 characters|
|addressLine1 | String | Line 1 of the address | Max: 60 characters|
|addressLine2 | String | Optional. Line 2 of the address | Max: 60 characters|
|postalCode | String | Zip code/postal code of address. Must match city/region/country.| Max: 40 characters|
|phoneNumber | String | Optional. Phone number associated with this address| Max: 40 characters|

## Contact

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|email | String | Email address of the contact | Max: 240 characters|
|firstName | String | First name of the contact | Max: 35 characters|
|lastName | String | Last name of the contact | Max: 35 characters|
|phoneNumber | String | Optional. Phone number of the contact | Max: 40 characters|

## Price List (top-level resource)

| **Field** | **Not Null** | **Type** | **Description** |
|-----------|--------------|----------|-----------------|
| priceListMonth | Yes | String | Price List published month |
| marketSegment | Yes | String (Enum) | Market segment applicable for the offers included in the response. Possible values: \<br /\> - COM \<br /\> - EDU \<br /\> - GOV |
| region | Yes | String (Enum) | Region where offer is sold. \<br /\> Possible values: \<br /\> - NA \<br /\> - PA \<br /\> - EE \<br /\> - MX \<br /\> - LA \<br /\> - WE \<br /\> - JP \<br /\> - AP |
| currency | Yes | String (Enum) | Currency applicable for the market segment and region. \<br /\> Possible Values: \<br /\> - AUD \<br /\> - EUR \<br /\> - GBP \<br /\> - JPY \<br /\> - USD |
| discountType | Yes | String (Enum) | The volume discount type applicable for the offers included in the response. \<br /\> Possible values:\<br /\> - STANDARD \<br /\> - 3YC |
| totalCount | Yes | Integer | Indicates the total items matched as per the search criteria. |
| count | Yes | Integer | Indicates the total number of offers returned in the response. |
| limit | Yes | Integer | The number of offers limited in the response. |
| offset | Yes | Integer | Specifies the starting point for retrieving items in a paginated list. It determines how many items to skip before beginning to return results. If not specified, the default value is 0, meaning the response will start from the first item. |
| offers | Yes | JSON List | List of Offers \<br /\> List will be empty if no Offers are available to return. |
| offer → offerId | Yes | String | Unique identifier of the Offer. Used as Offer ID in Order APIs |
| offer → productFamily | Yes | String | Indicates the grouping of products based on product code, product config, and so on. |
| offer → productType | Yes | String (Enum) | Possible values: \<br /\> - Enterprise Consumption (consumable) \<br /\> - Enterprise Hosted Subscription New \<br /\> - Feature Restricted Licensing Subscription New \<br /\> - Hosted Subscription \<br /\> - Subscription New \<br /\> - Team Consumption (consumable) \<br /\> - Transaction New (consumable) |
| offer → productTypeDetail | Yes | String (Enum) | Indicates whether the product is term base and if price is fixed or prorated. \<br /\> Possible values: \<br /\> - 12-month, no proration (fixed price) \<br /\> - Annual (proration based on duration) \<br /\> - No Proration (fixed price, no term) |
| offer → additionalDetail | No | String | Additional details for the product offer, such as the number of credits in a pack, the number of license bundles in a subscription, and so on. |
| offer → operatingSystem | Yes | String (Enum) | Possible values: \<br /\> - Multiple Platforms \<br /\> - Other \<br /\> - Windows |
| offer → language | Yes | String (Enum) | Language offered by Offer \<br /\> The following are the possible values: \<br /\> - EU English \<br /\> - Japanese \<br /\> - Multi Asian Languages \<br /\> - Multi European Languages \<br /\> - Multi Language Australia \<br /\> - Multi Latin American Languages \<br /\> - Multi NorthAmerican Language |
| offer → version | Yes | String | Possible values: \<br /\> - ALL |
| offer → users | Yes | String (Enum) | License type \<br /\> The Following are the possible values: \<br /\> - 1 User \<br /\> - Named \<br /\> - Per Credit Pack \<br /\> - Per Server \<br /\> - Per Transaction \<br /\> - Per Workstation \<br /\> - Subscription |
| offer → metric | No | String (Enum) | Unit of measure. The following are the possible values: \<br /\> - 1000 \<br /\> - 10000 \<br /\> - 15000 \<br /\> - 20000 \<br /\> - 30000 \<br /\> - 40 Images \<br /\> - 50 TRS INTRO NC \<br /\> - 5000 \<br /\> - 50000 \<br /\> - Transaction - USER |
| offer → bridge | No | String |  |
| offer → upcEanCode | No | String | Barcode formats used to identify products in retail sales. |
| offer → gtinCode | No | String | 13-digit code that identifies products for sale in retail stores or online. |
| offer → acdIndicator | No | String (Enum) | Used to indicate if the offer is NEW, CHANGED, or DELETED. \<br /\> Possible values: \<br /\> - Add \<br /\> - Change \<br /\> - Delete |
| offer → acdEffectiveDate | No | Date | Effective date for the ACD indicator (ADD, CHANGE, and DELETE). This date is in UTC format. |
| offer → acdDescription | No | String | This field provides additional context or details about the ACD status of the offer. |
| offer → levelDetails | Yes | String | Level Description of Min and Max range for the price point. |
| offer → firstOrderDate | Yes | Date | The first date for the item's sale. |
| offer → lastOrderDate | Yes | Date | The last date for the item's sale. |
| offer → partnerPrice | Yes | String | List Price for Partner \<br /\> |
| offer → estimatedStreetPrice | Yes | String | Estimated retail price \<br /\>  |
| offer → discountCode | No | String | High volume discount code will be provided here. When the discount code is available, the estimatedStreetPrice and partnerPrice will reflect the discounted price. |
| offer → estimatedShipDate | Yes | Date | Estimated Ship Date. This date is in UTC format. |
| offer → publicAnnounceDate | Yes | Date | Public Announce Date. This date is in UTC format. |
| offer → rmaRequestDeadline | Yes | Date | RMA Request Deadline. This date is in UTC format. |
| offer → pool | Yes | String (Enum) | The category or grouping to which the offer belongs. This field helps in identifying the broader classification of the offer. Possible values are: \<br /\> - Application \<br /\> - Discount1 \<br /\> - Discount2 \<br /\> - Pricing Version 23 \<br /\> - Pricing Version 24 \<br /\> - Pricing Version 25 \<br /\>|
| offer → duration | Yes | String | The time period for which the offer is valid or applicable. This field specifies the length of time the offer is effective. |

## Order (top-level resource)

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|externalReferenceId | String \<br /\>Optional |Reconciliation ID for this order (Example: PO Number, Order ID, and so on). \<br /\>ID does not need to be unique.| Max: 35 characters|
|orderId \<br /\> (read only)| String | Unique identifier for order that is created upon order creation| Max: 40 characters|
|customerId \<br /\> (read only)| String | ID of the customer that placed this order | Max: 40 characters|
|referenceOrderId | String \<br /\> required for RETURN orders| The ID of the order being returned in a RETURN order.| Max: 40 characters|
|orderType | String \<br /\> (enum)| Type of the order. Valid values: NEW, PREVIEW, PREVIEW_RENEWAL, RETURN, TRANSFER, RENEWAL, PREVIEW_SWITCH, SWITCH, PREVIEW_REVERT_SWITCH, REVERT_SWITCH | |
|lineItems | `lineItem` resources| Itemized list of the offers and their quantity for this order| Min: 1 item \<br /\>Max: 499 items|
|pricingSummary | `pricingSummary` resources| A summary of prices of offers included in the line item.| |
|eligibleOffers | `eligibleOffers` resource | The details of High Growth Offers available to the customer.||
|recommendations | `recommendations` resource | Lists products available for the customer in the upsell, cross-sell, and add-on motions.||
|currencyCode | String | Currency used for placing the order (ISO 4217 format).| 3 characters|
|creationDate \<br /\> (read only)| String (datetime)| Date and time the order was created in UTC| |
|status (read only, except for canceling)| String | Status code of the order | 4 characters|
|Source| String | Indicates the origin of the order. Possible values are: \<br /\> - **API:** Order placed through APIs \<br /\> - **System:** Order generated by the system.| 4 characters|
|links (read only)| `Links` resource | Deep links to get order details| |

### eligibleOffers

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|offerId |String |The unique ID of the offer. |Max: 40 characters |
|renewalCode |String |Unique identifier of the Minimum Order Quantity Offer. Available values are: \<br /\> - MOQ_100 \<br /\> - MOQ_250 \<br /\> - MOQ_500 |Max: 30 characters |
|eligibilityCriteria |Array |The eligibility criteria for availing the MOQ offer. | |
|minQuantity |Integer |The minimum quantity for which this offer is applicable, also the minimum quantity that the customer needs to commit for a 3YC term to be eligible for this offer. |Min: 0 \<br /\> Max: 999999 |
|additionalCriteria |String |The additional criteria list for availing the High Growth Offer. Currently, THREE_YEAR_COMMIT is the only supported value, indicating that 3YC is required to avail the High Growth Offers. |Min: 1 item \<br /\> Max: 499 items |
|deploymentId |String |Unique ID of the deployment. |Max: 40 characters |

### lineItem

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|extLineItemNumber| integer | Marketplace’s unique index for item in order| Min: 0 \<br /\> Max: 999999 \<br /\> Must be unique|
|offerId | String | The ID of the offer being purchased| |
|quantity | integer | Quantity of this offer purchased in this order| Min: 1 \<br /\> Max: 10,000 for Team / 599,000 for Enterprise|
|discountCode|String|The discount code applicable to the HVD customers migrating from VIP to VIP Marketplace.|Max: 40 characters|
|subscriptionId (read only)| String | ID of the Subscription resource associated with this line item.| Max: 40 characters|
|status (read only)| String | Status code of the line item | 4 characters|
|currencyCode | String | Currency code applicable for the offer | 3 characters|
|proratedDays | Integer |  The number of days for which order will be invoiced. This applies in the case of mid-term purchases.| |
|remainingQuantity | Integer |  The quantity available after the returns and mid-term switch plan cancellations against the same line item.| |
| flexDiscounts        | Object | Details of the flexible discount applied to that lineItem             | |
| flexDiscounts[].id  | String | A unique identifier for the flexible discount. Used to retrieve or reference a specific flexible discount.          | |
| flexDiscounts[].code  | String | The flexible discount code that was applied to that lineItem          | |
| flexDiscount[].result| String | The “SUCCESS" indicates that the flexible discount code applicability was successful. | |
| pricing       | Object |  Pricing details.            | |
| pricing[].partnerPrice                |Integer | Non-prorated full-term unit price for the given offer, including any applicable volume discounts, but before applying flexible discounts and taxes.| |
| pricing[].discountedPartnerPrice     |Integer  | Unit price after applying discount. \<br /\> | |
| pricing[].netPartnerPrice             | Integer    | Prorated unit price after discount. | |
| pricing[].lineItemPrice   | Integer   | Prorated price of item after discount and before tax. This is the price partner need to pay to Adobe for this item.  | |

### pricingSummary

| Field                       | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| totalLineItemPrice               | Sum of all line item prices in the order.                 |
| currencyCode                 | Currency used for pricing. This is specified in ISO 4217 currency code. Examples: USD and EUR.                                    |

### recommendations

| Property               | Type                    | Description                                                                                                                                           |
|------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| productRecommendations | Object                  | Contains different categories of recommended products.                                                                                                |
| upsells                | Array of Recommendations| List of recommended products that offer more value by providing a higher-tier, premium, or enhanced version of the selected product or offer. Example: Adobe Photoshop to Adobe Creative Cloud All Apps |
| crossSells             | Array of Recommendations| List of recommended products that complement the selected product by offering additional functionality or benefits. Example: Adobe Stock to Adobe Photoshop |
| addOns                 | Array of Recommendations| List of recommended products to extend or enhance the functionality of a base product. These products are not standalone and must be used with the base product. Example: AI Assistant for Adobe Acrobat |

#### overlayRecommendations object

| **Parameter**          | **Type**                 | **Description**                                                                                                                                                                                          |
|------------------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| overlayRecommendations | Object                   | Contains overlay recommendation opportunities created by Adobe agents during overlay interactions. Present only when opportunities exist for the customer. For more information, see [Overlay recommendations](./index.md#overlay-recommendations).                                                               |
| new                    | Array of opportunities           | List of opportunities representing new purchase intent identified during overlay interactions.                                                                                                                    |
| renew                  | Array of opportunities           | List of opportunities representing renewal intent identified during overlay interactions.                                                                                                                         |
| createdAt              | String (ISO-8601)        | Timestamp when the opportunity was created.                                                                                                                                                                     |
| expiresAt              | String (ISO-8601)        | Timestamp when the opportunity expires if not acted upon.                                                                                                                                                        |
| status                 | String                   | Opportunity lifecycle state. Possible values: `OPEN` and `EXPIRED`.                                                                                                                                                                     |
| items                  | Array of Items           | List of products and quantities in the opportunity.                                                                                                                                                             |
| offerId                | String                   | Offer identifier (Part Number) for the opportunity item.                                                                                                                                                        |
| quantity               | Integer                  | Number of units of the product in the opportunity.                                                                                                                                                              |

#### flexibleDiscountCodes object

| Parameter                       | Not Null | Data Type        | Description                                                                                                   | Included in response by default |
|----------------------------------|----------|------------------|---------------------------------------------------------------------------------------------------------------|-------------------------------|
| lineItems[].flexDiscountCodes    | No       | Array of strings | Flexible discount codes that were applied to the line item.                                                    | Yes                           |
| lineItems[].flexDiscounts        | No       | Object           | Details of the flexible discount applied to the line item.                                                     | Yes                           |
| lineItems[].flexDiscounts[].id   | No       | String           | A unique identifier for the promotion. Used to retrieve or reference a specific flexible discount.             | Yes                           |
| lineItems[].flexDiscounts[].code | No       | String           | The flexible discount code that was applied to the line item.                                                  | Yes                           |
| lineItems[].flexDiscounts[].result | No     | String           | Indicates applicability result. SUCCESS means the flexible discount code was applied successfully.             | Yes                           |

### cancellingItems

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

## Subscription (top-level resource)

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|subscriptionId (read only)| String | Unique identifier of the subscription resource | Max: 40 characters|
|currentQuantity (read only)| integer | Total quantity of the subscription that a customer has |Max: 10,000 for Team \<br /\> 599,000 for Enterprise|
|usedQuantity | integer | Total used quantity for the license or the consumable| |
|offerId | String | The base discount level offer id. \<br /\> **Note:** This attribute is included for developers to identify the offer and NOT the discount level. Partners need to use “Get customer Account” and “Get Order history” APIs to see discount level information.| |
|autoRenewal | `AutoRenewal` object| Resource to manage auto-renewal flag and quantity| |
|creationDate (read only)| String (datetime)| Date and time of subscription creation in UTC| |
|status (read only)| String | Current status code of the subscription | 4 characters|
|links (read only)| `Links` resource | Deep links to get subscription details| |

### autoRenewal

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|enabled| Boolean | Indicates whether the auto renewal is enabled. Set `enabled` to `false` to disable auto renewal. | Max: 40 characters|
|renewalQuantity | Integer | If the `renewalQuantity` is higher than `currentQuantity` at the renewal time, then the additional licenses will be included in renewal. If it is lower, then licenses will be removed at renewal. | Max: 40 characters|
|renewalCode| String | Unique identifier of the Minimum Order Quantity Offer. Available values are: \<br /\> - MOQ_100 \<br /\> - MOQ_250 \<br /\> - MOQ_500 | Max: 40 characters|
|flexDiscountCodes| String | Indicates the flexible discounts applicable for the subscription. | Max: 40 characters|

## Recommendations (top-level resource)

| **Parameter**          | **Type**                 | **Description**                                                                                                                                                                                          |
|------------------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| productRecommendations | Object                   | Contains different categories of recommended products.                                                                                                                                                   |
| upsells                | Array of Recommendations | List of recommended products that offer more value by providing a higher-tier, premium, or enhanced version of the selected product or offer. Example: Adobe Photoshop to Adobe Creative Cloud All Apps  |
| crossSells             | Array of Recommendations | List of recommended products that complement the selected product by offering additional functionality or benefits. Example: Adobe Stock to Adobe Photoshop                                              |
| addOns                 | Array of Recommendations | List of recommended products to extend or enhance the functionality of a base product. These products are not standalone and must be used with the base product. Example: AI Assistant for Adobe Acrobat |
| rank                   | Integer                  | The ranking position of the recommended product within its category.                                                                                                                                     |
| product                | Object                   | Details about the recommended product.                                                                                                                                                                   |
| baseOfferId            | String                   | The base offer ID associated with the product.                                                                                                                                                           |
| source                 | Object                   | Indicates the source of the recommendation.                                                                                                                                                              |
| sourceType             | String                   | Specifies the type of the source entity. Currently only `OFFER` is supported.                                                                                                                              |
| offerIds               | String Array             | List of offer IDs that contributed to this recommendation.                                                                                                                                               |

## Flexible discounts (top-level resource)

| Parameter                       | Type             | Description                                                                 |
|---------------------------------|------------------|-----------------------------------------------------------------------------|
| limit                            | String           |  Number of items to be included in the current response.                 |
| offset                            | String |Offset applied for the current response.                                                 |
| count                            | String           | The count of flexible discount entities included in the current response.                                                       |
| totalCount                            | String           |   Total count of flexible discount entities, if no limit was applied.                                                   |
| category                               | String           | Filter promotions by category. Possible values are: \<br /\>\<br /\>  - **STANDARD:** Represents all regular Flexible Discounts available in the VIP Marketplace. These discounts can be applied to eligible orders as long as they meet the defined qualification criteria. These discounts are not restricted to new customers or first-time purchases. \<br /\> **Example:** Seasonal discounts like Black Friday or volume-based discounts for enterprise accounts.\<br /\> \<br /\> - **INTRO:** Represents Introductory Offers designed to help acquire new customers or encourage existing customers to adopt products that are new to their subscription. These discounts are typically limited to a customer's first purchase or first-time use of a specific product.  \<br /\> **Example:** Launch offer for Adobe Express at $39.99 for new customers. |
| name                            | String           | Name of the flexible discount.                                                       |
| description                     | String           | Description of the flexible discount. It also provides additional details about the eligibility criteria for the flexible discount. For example, "Exclusive 20% off for Teams customers of CC All Apps in US"                                               |
| code                            | String           | The code that needs to be used in the order and will reflect in the invoice. It will be unique across flexible discounts. |
| endDate                         | String (Date)    | Final date when the flexible discount can be used.                                   |
| startDate                       | String (Date)    | First date when the flexible discount can be used                                   |
| status                          | String Enum      | Status of flexible discount. Possible values: ACTIVE, EXPIRED                       |
| qualification                   | Object           |                                                                             |
| qualification.baseOfferIds      | Array of Strings | List of Base Offer IDs of products eligible for flexible discount. Example: ["Offer ID 1", "Offer ID 2"] \<br /\>**Note**: The list of base Offer IDs will be empty if the flexible discount applies to all products. |
| outcomes[]                      | Array of Objects |                                                                             |
| outcomes[] → type               | String           | Type of flexible discount. Possible values are: PERCENTAGE_DISCOUNT, FIXED_DISCOUNT  |
| outcomes[].discountValues[]          | Array of Objects |                                                                             |
| outcomes[].discountValues[] → country| String           | Country Code: ISO 3166-1 alpha-2 code. Example: "US", "IN". Note: Not applicable for PERCENTAGE_DISCOUNT type. |
| outcomes[].discountValues[] → currency| String          | Currency Code: ISO 4217. Example: "USD", "EUR". Note: Not applicable for PERCENTAGE_DISCOUNT type. |
| outcomes[].discountValues[] → value  | Integer          | The discount value. For example, if the value is 15: 15% discount is applicable if the type is PERCENTAGE DISCOUNT. A discount of 15 USD, or any currency provided in the response, is applicable for the FIXED_DISCOUNT discount type. |

## productUpgrades (top-level resource)

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

## Notification (top-level resource)

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|limit | integer | Maximum number of notification items response can contain |0 to INTEGER_MAX|
|offset | integer | Number of notification items to skip before picking up the items| 0 to INTEGER_MAX|
|totalCount | integer | Total number of items present for a notification |0 to INTEGER_MAX|
|count | integer | Actual number of notification item response contains |0 to INTEGER_MAX|
|items | Array of `NotificationItem` resources| Itemized list of notifications| 0 to INTEGER_MAX|
|links(read only)| `Links` resource | Deep links to get notification details | |

### NotificationItem

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|customerId | String | Customer id for the account where partner order is pending| Max: 40 characters|
|resellerId | String | Reseller account id for the account whose partner order is still pending| Max: 40 characters|
|notificationType | String | Type of notification response| Max: 40 characters|
|links | `Links` resource | Deep links to get notification item details| |

### AutoRenewal

|Property | Type | Description | Range/Limits|
|:----|:----|:----|:----|
|enabled | boolean | Boolean flag denoting whether or not autoRenewal is enabled| true or false|
|renewalQuantity | integer | Optional. Total quantity to be renewed at the end of the contract period (can be higher than the current subscription quantity)| Min: 1 \<br /\> Max: 10,000 for Team / 599,000 for Enterprise|

### Links

|Property | Type | Description |
|:----|:----|:----|
|self | `Link` object | Link to get data about this resource |
|next | `Link` object | Link to get data about the next resource |
|prev | `Link` object | Link to get data about the previous resource |
|uri |String |The URI to access this link|
|method |String |The method used for the URI |
|headers|Array of Key:Value pairs |The headers for the link. \<br /\> **Note:** Any headers in this are in addition to the required headers specified in the Headers section above. |
