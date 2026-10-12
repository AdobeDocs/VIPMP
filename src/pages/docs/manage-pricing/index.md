# Access Price Lists

Partners can leverage the `Fetch Price List` API to retrieve up-to-date pricing information for Adobe products. This API not only modernizes Adobe’s price list distribution process but also delivers several key advantages:

- **Real-time pricing access**

  Partners can retrieve available price lists programmatically to always work with the latest version of VIP Marketplace price lists, ensuring accuracy and reducing delays.

- **Historical pricing support (3YC)**

  The API provides access to historical pricing, including Three-Year Commitment (3YC) prices. This is essential for serving customers with price-locked agreements.

- **Comprehensive global coverage**

  Partners can access current price lists across all products, countries, and currencies.

- **Country-specific pricing support**

  Partners can retrieve country-specific price lists by passing a supported country code in `region`, enabling accurate quoting where country-specific price lists are available. This feature introduces country-specific USD pricing without changing the currency.

## Fetch Price List API

| Endpoint      | Method |
|---------------|--------|
| /v3/pricelist | POST   |

**Sample request URL:** `POST <env root url>/v3/pricelist?offset=0&limit=10`

The `POST /v3/pricelist` API retrieves the price list information for the partners.

### Integration Guide

Partners will receive email notifications whenever updates are made to the Price List. Upon receiving the notification, partners can initiate a request to the Fetch Price List API by following the steps below:

1. Initiate API request

   Use the `POST /v3/pricelist` API. The request must include required parameters, with optional filters available to refine the response. See [Request](#request-body) for the complete list of supported parameters.

2. Handle API errors

   If the API returns an error, refer to the [Error Handling](#http-status-codes) section for guidance on interpreting and resolving error codes.

3. Parse the response

   The API returns a JSON response. Partners should parse this response and process the data outlined in the [Response](#response-body) section.

4. Pagination support

   The API supports pagination. Refer to the pagination guidelines to understand how to navigate through paged results efficiently.

5. Store Price List data

   Partners are expected to store the Price List data in their internal systems. Key attributes to store include:

   - `offerId` – Unique identifier for the offer; required when placing orders through [Order](../order-management/create-order.md) APIs.
   - `productFamily` – Helps categorize and group offers that belong to the same product family.

   Additional attribute details are available in the [Response](#response-body)  section.

6. Track Price List changes

   To identify changes to the Price List, review the following fields:

   - `acdIndicator`: Indicates whether the offer is new, changed, or deleted.
   - `acdEffectiveDate`: The date on which the acdIndicator status was set.

## Request

### Request header

| Parameter        | Description                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Request-Id     | A unique identifier for the call. The value should be reset for every single request. If this is not provided, then a request ID will be automatically generated. Using a duplicate request ID may return an error.              |
| X-Correlation-Id | **Required**. A unique identifier for the call. This is to ensure idempotency. In the case of a timeout, the retry call could include the same value. Upon receiving some response, the value should be reset for the next call. |
| Accept           | **Required**. Specifies the response type. Must be "application/json" for proper usage.                                                                                                                                          |
| Content-Type     | **Required**. Specifies the request type. Must be "application/json" for proper usage.                                                                                                                                           |
| Authorization    | **Required**. Authorization token in the form `Bearer <token>`                                                                                                                                                                   |
| X-Api-Key        | **Required**. The API Key for your integration                                                                                                                                                                                   |

### Query parameters

You can use the query parameters such as `limit` and `offset` in the request URL. For example: `POST <env root url>/v3/pricelist?offset=0&limit=10`

| **Field**                        | **Required** | **Type**           | **Description** |
|----------------------------------|--------------|--------------------|-----------------|
| `limit`            | No           | Integer            | Specifies the number of items to be returned per page, enabling pagination. The default value is 20.|
| `offset`       | No           | Integer            |  Specifies the starting point for retrieving items in a paginated list. It determines how many items to skip before beginning to return results. If not specified, the default value is 0, meaning the response will start from the first item. |

### Request body

A sample request is as follows:

```json
{
    "region": "NA",
    "marketSegment": "GOV",
    "priceListType": "3YC",
    "currency": "USD",
    "priceListMonth": "202601",
    "filters": {
        "offerId": "30001475CC01A12"
    },
    "includeOfferAttributes": [
        "productFamily",
        "productType",
        "productTypeDetail",
        "operatingSystem",
        "language",
        "version",
        "users",
        "metric",
        "bridge",
        "partNumber",
        "acdIndicator",
        "acdEffectiveDate",
        "levelDetails",
        "firstOrderDate",
        "lastOrderDate",
        "partnerPrice",
        "estimatedStreetPrice",
        "discountCode",
        "estimatedShipDate",
        "publicAnnounceDate",
        "rmaRequestDeadline",
        "acdDescription",
        "pool",
        "duration",
        "additionalDetail"
    ]
}
```

#### Request parameters

| **Field**                        | **Required** | **Type**           | **Description** |
|----------------------------------|--------------|--------------------|-----------------|
| `region` | Yes | String (Enum) | Specifies the region or country for which the Price List should be returned. The value must be supported for the Partner. \<br /\>- Supported regional codes: `AP`, `WE`, `LA`, `EE`, `PA`, `NA`. \<br /\> - Supported country codes: `JP`, `CN`, `MX`, `KR`, `TR`, `BR`, `IN`, for country-specific USD pricing. See [Local currencies support](#local-currency-support). |
| `marketSegment`                  | Yes          | String (Enum)      | Specifies the market segment. Must be one of the supported segments, such as: \<br /\> - COM \<br /\> - EDU \<br /\> - GOV. |
| `priceListType`                   | No           | String (Enum)      | Indicates the price list type. Possible values: \<br /\> - STANDARD \<br /\> - 3YC \<br /\> - STD-LGA (Standard Price List for Large Government Agencies)|
| `currency`                       | Yes          | String (Enum)      | Specifies the currency. Must be one of the supported currencies for the Partner. \<br /\>Possible Values: AUD, EUR, GBP, JPY, USD. |
| `priceListMonth`                | Yes          | String             | Specifies the month you want the Price List for, in `YYYYMM` format. Example: `202410`. |
| `filters`                        | No           | Object             | Filters to narrow down the Price List. All filters use AND logic. For example, if `offerId` and `productFamily` filters are included in the request, then the API response will include results that match `offerId` AND `productFamily` |
| `filters → offerId`             | No           | String             | Returns only offers matching the specified `offerId`. |
| `filters → productFamily`       | No           | String             | Returns only offers matching the exact, case-sensitive `productFamily`. |
| `filters → firstOrderDate`      | No           | Date               | Returns only those offers whose first order date is later than the specified date. |
| `filters → lastOrderDate`       | No           | Date               | Returns only those offers whose last order date is on or after the specified date. |
| `filters → discountCode`        | No           | String             | Returns only offers matching the specified discount code. |
| `includeOfferAttributes`        | No           | List  | List of additional offer attributes to include in the response. Unknown attributes will result in an error. Read more about how to [define offer data to be displayed in the response](#define-offer-data-to-be-displayed-in-the-response). |

### Filter

You can refine the data returned in the response by using the filters property in the request body. This allows you to narrow down the price lists based on specific criteria. All filter fields are combined using AND logic, meaning that only results matching all specified conditions will be returned if multiple filters are applied. Filters with null or empty values will be ignored. You can filter the data using the following parameters:

- `offerId`
- `productFamily`
- `firstOrderDate`
- `lastOrderDate`
- `discountCode`

### Define offer data to be displayed in the response

To customize the offer details returned in the response, use the `includeOfferAttributes` parameter in the request body. This allows you to specify which offer attributes should be included in the response.  For example:

- `productType`
- `productTypeDetail`
- `language`

**Notes:**

- The response will include only the attributes listed in `includeOfferAttributes`, plus these default attributes:

  - `totalCount`
  - `limit`
  - `offset`
  - `offers`
  - `offer → offerId`
  - `offer → discountCode`
  - `offer → productFamily`
  - `offer → firstOrderDate`
  - `offer → lastOrderDate`
  - `offer → partnerPrice`
- Any request parameter that has no value is omitted from the response.

## Response body

**Disclaimer – Offer Attributes in Response**

- When using the `includeOfferAttributes` parameter in the request, the API will attempt to return the specified attributes for each offer. However, it is not guaranteed that every offer in the response will contain all the requested attributes.
- The availability of each attribute depends on the underlying data applicability and validity for the specific offer. If an attribute is not relevant or does not have a valid (non-null) value for a given offer, it may be omitted from that offer’s attribute section in the response.
- Requesting all available `includeOfferAttributes` does not ensure that all attributes will be present for every offer. Consumers of the API should design their integrations to handle missing or null attribute values gracefully.

A sample response is as follows:

```json
{
    "priceListMonth": "202601",
    "marketSegment": "GOV",
    "region": "NA",
    "currency": "USD",
    "priceListType": "3YC",
    "totalCount": 2,
    "count": 2,
    "limit": 100,
    "offset": 0,
    "offers": [
        {
            "offerId": "30001475CC01A12",
            "productFamily": "Creative Cloud For Enterprise All Apps",
            "productType": "Subscription New",
            "productTypeDetail": "Annual",
            "additionalDetail": "GOV | First Order Date and Last Order Date=3-year commit accept. RMA Request Deadline=Last eligible order date.",
            "operatingSystem": "Multiple Platforms",
            "language": "Multi NorthAmerican Language",
            "version": "ALL",
            "users": "1 User",
            "levelDetails": "Level 1 1 - 9",
            "firstOrderDate": "2022-05-03T00:00:00.000+00:00",
            "lastOrderDate": "2023-11-01T00:00:00.000+00:00",
            "partnerPrice": "1206.84",
            "estimatedStreetPrice": "1283.88",
            "pool": "Application",
            "duration": "12 Months"
        },
        {
            "offerId": "30001475CC01A12",
            "productFamily": "Creative Cloud For Enterprise All Apps",
            "productType": "Subscription New",
            "productTypeDetail": "Annual",
            "additionalDetail": "GOV | First Order Date and Last Order Date=3-year commit accept. RMA Request Deadline=Last eligible order date.",
            "operatingSystem": "Multiple Platforms",
            "language": "Multi NorthAmerican Language",
            "version": "ALL",
            "users": "1 User",
            "levelDetails": "Level 1 1 - 9",
            "firstOrderDate": "2023-11-02T00:00:00.000+00:00",
            "lastOrderDate": "2024-08-01T00:00:00.000+00:00",
            "partnerPrice": "1263.24",
            "estimatedStreetPrice": "1343.88",
            "pool": "Application",
            "duration": "12 Months"
        }
    ]
}
```

### Response parameters

| **Field** | **Not Null** | **Type** | **Description** |
|-----------|--------------|----------|-----------------|
| `priceListMonth` | Yes | String | Price List published month |
| `marketSegment` | Yes | String (Enum) | Market segment applicable for the offers included in the response. Possible values: \<br /\> - COM \<br /\> - EDU \<br /\> - GOV |
| `region` | Yes | String (Enum) | For regional price list requests, the region where the offer is sold. \<br /\> Possible values: \<br /\> - NA \<br /\> - PA \<br /\> - EE \<br /\> - MX \<br /\> - LA \<br /\> - WE \<br /\> - JP \<br /\> - AP |
| `currency` | Yes | String (Enum) | Currency applicable for the market segment and region. \<br /\> Possible Values: \<br /\> - AUD \<br /\> - EUR \<br /\> - GBP \<br /\> - JPY \<br /\> - USD |
| `priceListType` | Yes | String (Enum) | The price list type. \<br /\> Possible values:\<br /\> - STANDARD \<br /\> - 3YC \<br /\> - STD-LGA (Standard Price List for Large Government Agencies) |
| `totalCount` | Yes | Integer | Indicates the total items matched as per the search criteria. |
| `count` | Yes | Integer | Indicates the total number of offers returned in the response. |
| `limit` | Yes | Integer | The number of offers limited in the response. |
| `offset` | Yes | Integer | Specifies the starting point for retrieving items in a paginated list. It determines how many items to skip before beginning to return results. If not specified, the default value is 0, meaning the response will start from the first item. |
| `offers` | Yes | JSON List | List of Offers \<br /\> List will be empty if no Offers are available to return. |
| `offer → offerId` | Yes | String | Unique identifier of the Offer. Used as Offer ID in Order APIs |
| `offer → productFamily` | Yes | String | Indicates the grouping of products based on product code, product config, and so on. |
| `offer → productType` | Yes | String (Enum) | Possible values: \<br /\> - Enterprise Consumption (consumable) \<br /\> - Enterprise Hosted Subscription New \<br /\> - Feature Restricted Licensing Subscription New \<br /\> - Hosted Subscription \<br /\> - Subscription New \<br /\> - Team Consumption (consumable) \<br /\> - Transaction New (consumable) |
| `offer → productTypeDetail` | Yes | String (Enum) | Indicates whether the product is term base and if price is fixed or prorated. \<br /\> Possible values: \<br /\> - 12-month, no proration (fixed price) \<br /\> - Annual (proration based on duration) \<br /\> - No Proration (fixed price, no term) |
| `offer → additionalDetail` | No | String | Additional details for the product offer, such as the number of credits in a pack, the number of license bundles in a subscription, and so on. |
| `offer → operatingSystem` | Yes | String (Enum) | Possible values: \<br /\> - Multiple Platforms \<br /\> - Other \<br /\> - Windows |
| `offer → language` | Yes | String (Enum) | Language offered by Offer \<br /\> The following are the possible values: \<br /\> - EU English \<br /\> - Japanese \<br /\> - Multi Asian Languages \<br /\> - Multi European Languages \<br /\> - Multi Language Australia \<br /\> - Multi Latin American Languages \<br /\> - Multi NorthAmerican Language |
| `offer → version` | Yes | String | Possible values: \<br /\> - ALL |
| `offer → users` | Yes | String (Enum) | License type \<br /\> The Following are the possible values: \<br /\> - 1 User \<br /\> - Named \<br /\> - Per Credit Pack \<br /\> - Per Server \<br /\> - Per Transaction \<br /\> - Per Workstation \<br /\> - Subscription |
| `offer → levelDetails` | Yes | String | Level Description of Min and Max range for the price point. |
| `offer → firstOrderDate` | Yes | Date | The first date for the item's sale. For a country-specific price list, the date the offer's country-specific price applies to orders. For 3YC pricing, use the customer's applicable partner price lock date. |
| `offer → lastOrderDate` | Yes | Date | The last date for the item's sale. |
| `offer → partnerPrice` | Yes | String | List Price for Partner \<br /\> |
| `offer → estimatedStreetPrice` | Yes | String | Estimated retail price \<br /\>  |
| `offer → pool` | Yes | String (Enum) | The category or grouping to which the offer belongs. This field helps in identifying the broader classification of the offer. Possible values are: \<br /\> - Application \<br /\> - Discount1 \<br /\> - Discount2 \<br /\> - Pricing Version 23 \<br /\> - Pricing Version 24 \<br /\> - Pricing Version 25 \<br /\>|
| `offer → duration` | Yes | String | The time period for which the offer is valid or applicable. This field specifies the length of time the offer is effective. |

## HTTP status codes

| Status code | Description                                       |
| ----------- | ------------------------------------------------- |
| 200         | Successfully retrieved the price list.            |
| 400         | Bad request                                       |
| 401         | Invalid Authorization token                       |
| 403         | Invalid API Key                                   |
| 404         | Price list not found for the given request/offset |

## Error codes specific to Fetch Price List API

The following table lists the error codes that are specific to the Fetch Price List API:

| Code | Message | Applicable API calls | HTTP Code |
|--|--|--|--|
|1159 |Inactive Partner Contract |Fetch Price Lists |400 |
|1160 |Invalid Market Segment for Partner |Fetch Price Lists |400 |
|1161 |Invalid Currency/Region for Partner |Fetch Price Lists |400 |
|1162 |Price List not found |Fetch Price Lists |404 |
|5122 |Invalid Request |Fetch Price Lists |400 |

For the complete set of error codes, see [Status codes and error handling](../references/error-handling.md).

## Local currency support

For supported countries, partners can retrieve country-specific USD price lists in addition to regional price lists. Local currencies support is available for South Korea (KR), Türkiye (TR), Brazil (BR), and India (IN). These countries move from regional to country-specific pricing on the  cutover date. A cutover is the date on which the country-specific USD price list becomes available and takes effect. For South Korea, Türkiye, Brazil, and India, the cutover date is December 1, 2026. The currency remains USD.

### Request country-specific prices

Use the required `region` request parameter in `POST /v3/pricelist` for either regional or country-specific prices. Supply a supported regional code (`AP`, `WE`, `LA`, `EE`, `PA`, or `NA`) for regional pricing, or a supported country code (`JP`, `CN`, `MX`, `KR`, `TR`, `BR`, or `IN`) for country-specific USD pricing.

For the same offer, currency, and price list month, a country-specific price can differ from the regional price. Use the applicable country-specific list for quoting after cutover, subject to the 3YC partner price locks described below.

Each offer's `firstOrderDate` indicates when its country-specific price applies to orders. The following request illustrates a country-specific lookup for Türkiye with `region` set to `TR`. The month and offer ID are illustrative values, not a cutover date or a guarantee of offer availability.

```json
{
    "region": "TR",
    "marketSegment": "COM",
    "priceListType": "STANDARD",
    "currency": "USD",
    "priceListMonth": "202610",
    "filters": {
        "offerId": "65324779CA01A12"
    }
}
```

Supply the applicable regional code in `region` to request the regional price list, including when retrieving historical regional pricing for a 3YC commitment accepted before cutover. For example, `region` set to `NA` requests North American regional pricing.

### Pricing at cutover to local currency

| Scenario | Pricing behavior |
|---|---|
| New orders, renewals, early renewals, mid-term license  additions, and `SWITCH_PLAN` on or after cutover | Use the country-specific USD price list, subject to the existing 3YC partner price lock described below. Mid-term purchases continue to modify the existing subscription. |
| 3YC commitment accepted before cutover | Retain the regional price list at the customer's partner price lock date until the commitment ends. Do not replace this locked price with the country-specific price. |
| 3YC commitment accepted on or after cutover | Lock to the country-specific USD price list at the commitment acceptance date. |
| Early renewal placed before cutover | Retain regional pricing. |
| Returns and plan reverts | Use the original order price, not the price currently available in the country-specific list. |
