# Error codes specific to Flexible Discounts

The following error codes are specific to Flexible Discounts:

| Error Code | Message                                                                                                                                                                                                   | Applicable API calls | HTTP Status Code |
|------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------|------------------|
| 2141       | Customer is not qualified for the flexible discount. \<br /\> \<br /\> See [2141 Ineligible Flexible Discounts reason codes](#2141-ineligible-flexible-discounts-reason_code-list) for documented reasons. A generic qualification failure can also be returned. |    Create Order, \<br /\> Preview Order,  \<br /\>  Update Subscription, \<br /\>  Create Subscription               |    400              |
| 2142     | This Flexible Discount is for one-time use and is no longer available.                                                                 |    Get Flexible Discounts               |    400              |
| 2144       | Flexible Discount cannot be applied in combination with other discounts. \<br /\>  |       Create Order, \<br /\> Preview Order,  \<br /\>  Update Subscription, \<br /\>  Create Subscription             |  400                |
| 2145       | Flexible discount codes cannot be applied to non-base products.                 |    Create Order, \<br /\> Preview Order,  \<br /\>  Update Subscription, \<br /\>  Create Subscription                |    400              |
| 2146     | Invalid Flexible Discount Code |    Create Order, \<br /\> Preview Order,  \<br /\>  Update Subscription, \<br /\>  Create Subscription                |    400              |
| 2147      | Only one Flexible Discount Code is allowed per line item.   |    Create Order, \<br /\> Preview Order,   \<br /\>  Update Subscription, \<br /\>  Create Subscription               |    400              |

## 2141 Ineligible Flexible Discounts REASON_CODE list

The following documented REASON_CODE values can appear in the `additionalDetails` array for `2141` errors. Availability depends on the discount and API flow; do not assume every qualification failure includes a specific reason. Continue to handle the generic `Invalid Flexible Discount` reason shown below.

Returning additional [qualification criteria](./apis.md#evaluate-qualification-criteria), as proposed, does not add reason codes or change existing qualification-error behavior.

| Error Code | Error Reason | Action Required |
|---|---|---|
| `INELIGIBLE_COMMITMENT_STATUS_OR_PERCENT_SEATS` | Customer is not in eligible 3YC status or not migrating the required percentage of seats for this discount. | Verify that the customer is in the required commitment status and that the number of seats being migrated meets the minimum percentage required for the discount. |
| `INELIGIBLE_COMMITMENT_STATUS` | Customer is not in eligible 3YC status required for this discount. | Verify that the customer account is in the required commitment status before applying the discount. |
| `INELIGIBLE_COMMITMENT_STATUS_OR_COMMIT_QUANTITY` | Customer is not in eligible 3YC status or does not meet the minimum commit quantity required for this discount. | Verify that the customer is in the required commitment status and that the committed license quantity meets the minimum requirement for the discount. |
| `SEAT_UPGRADE_PERCENTAGE_NOT_MET` | The number of seats being upgraded does not meet the minimum percentage required for this discount. | Verify that the number of seats being upgraded meets the minimum percentage required for the discount before applying the discount code. |

## Sample Error Responses

1. Sample error response of API when customer tries to use a flexible discount code for which the customer is not eligible:

**HTTP Code:** 400 Bad Request

```json
{
  "code": "2141",
  "message": "Customer is not qualified for the Flexible Discount",
  "additionalDetails": [
    "Line Item: 1, Reason: Invalid Flexible Discount"
  ]
}
```

2. Sample error response of API when customer tries to use a flexible discount code if customer is not eligible for 3YC discount.

**HTTP Code:** 400 Bad Request

```json
{
    "code": "2141",
    "message": "Customer is not qualified for the Flexible Discount",
    "additionalDetails": [
        "Line Item: 1, Reason: INELIGIBLE_COMMITMENT_STATUS"
    ]
}
```

3. Sample error response of API when customer tries to use a flexible discount code that does not exist:

**HTTP Code:** 400 Bad Request

```json
{
    "code": "2146",
    "message": "Invalid Flexible Discount Code",
    "additionalDetails": [
        "Line Item: 1, Reason: NOT_FOUND"
    ]
}
```

4. Sample error response of API when customer tries to use multiple flexible discount codes within the same line item:

**HTTP Code:** 400 Bad Request

```json
{
    "code": "2147",
    "message": "Only one Flexible Discount Code is allowed per line item",
    "additionalDetails": [
        "Line Item: 1"
    ]
}
```
