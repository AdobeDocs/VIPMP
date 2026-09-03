# Manage market segments

Market segmentation is an internal convention used to inform the internal stakeholders about the product or business intent, to have one or more of the following differentiations:

* **Price range**

    **Example**: Pricing for the Education segment is typically lower than that for the Commercial pricing.
* **Product certification**

    **Example**: Education-ready and Government-ready software and services are required to meet more stringent Adobe legal, safety, privacy, and trust requirements.
* **Purchasing qualification**

    **Example**: Customers are typically required to prove their association with government-related agencies or educational institutions to buy the product at a different price range. For individuals buying education plans on Adobe-direct, Adobe verifies their education status with a third-party identity verification service.

These are the three market segments available for Adobe products:

| Value | Description                 |
|-------|-----------------------------|
| COM   | Commercial \<br /\> (default) |
| EDU   | Education                   |
| GOV   | Government                  |

The education market segment mentioned in the above table is further divided into K-12, higher education, and non-profit, as listed in the following table:

| Value      | Description                               | Offers available to purchase                                                    |
|------------|-------------------------------------------|---------------------------------------------------------------------------------|
| K_12       | K-12 School (Primary or Secondary School) | - EDU offers without a sub-segment.  \<br /\> - EDU offers with K-12 sub-segment. |
| HIGHER_ED  | Higher Education Institution              | - EDU offers without a sub-segment. \<br /\> - EDU offers with HED sub-segment.   |
| NON_PROFIT | Non-profit Organization                   | - EDU offers without a sub-segment.                                             |

**Note:** The Additional Detail (Column P) in the price list file contains information on the sub-segment of an offer as well as the minimum purchase quantity, if applicable.

* If the offer has a sub-segment, the Additional Detail text begins with that subsegment (K-12 or HED).
* If the offer has a minimum purchase quantity, the Additional Detail text ends with that information.

**Example Additional Detail**: K-12 Shared Device Site Education License Lab and Classroom (100+)

* This offer is associated with the K-12 sub-segment and has a minimum purchase quantity of 100.

## Market segment workflows

The following market segment workflows are available:

1. Set market segments for resellers

   * Reseller market segments can be set as part of a create or update request.
   * A reseller’s market segments may be changed at any point, but removing a market segment may cause future orders or renewals to fail.
   * All existing resellers with no market segment can be initialized with only the COM market segment.
   * API endpoints for managing this workflow:
     * [POST Create Reseller API](../reseller-account/create-reseller-account.md)
     * [PATCH Update Reseller API](../reseller-account/update-reseller-account.md)

2. Set market segment for customers

   * Customer market segment can be set as part of a create or update request.
   * All existing customers with no market segment will be initialized to COM.
   * API endpoints for managing this workflow:
     * [POST Create Customer API](create-customer-account.md)
     * [PATCH Update Customer API](update-customer-account.md)

3. Set market sub-segment for customers

   * A customer’s market sub-segment can only be set by the customer in the Adobe Admin Console.
   * Sandbox for testing the sub-segment can be set in the sandbox partner portal. Read more about [Sandbox user guide](../../technical-assets/index.md).
   * API endpoints available to manage this workflow: [GET Customer API](get-customer-account.md).

4. Transfer customers to your marketplace (Customer only)

   * Customers with any market segment can be transferred as long as the new reseller is enabled to sell into the customer’s market segment. API endpoints available to manage this workflow:
     * [POST Create Transfer API](../reseller-change/commit-transfer.md)
     * [GET Customer API](get-customer-account.md)

5. Purchase  offers for a market segment or sub-segment

   * Customers may only purchase offers in their market segment or subsegment.
   * Resellers may only purchase offers in a market segment they are enabled for.
   * Requests to purchase offers in a different segment returns the  error 2129 – Reason Code: `INELIGIBLE_MARKET_SEGMENT`.
   * API endpoint to manage this workflow: [POST Create/Preview Order API](../migration/preview-offers.md)

New or updated error codes are listed in the following table:

| Error Code                        | Error Message                                 | Applicable APIs                  | Change Details                                                  |
|-----------------------------------|-----------------------------------------------|----------------------------------|-----------------------------------------------------------------|
| 2135 \<br /\> (new error code)       | Invalid market segment is used for the customer.      | Create Customer, Update Customer | New Error Code                                                  |
| 2129 \<br /\> (existing error code) | Customer is not eligible to purchase an offer.  | Preview Order, Create Order      | New Additional Details Reason Code: `INELIGIBLE_MARKET_SEGMENT` |

## Market segment API changes

### Changes to Reseller Resource

* Market Segments: New field `companyProfile.marketSegments`

  * Defines the list of market segments that the reseller can sell into.
  * Can be set during the create reseller or update reseller processes.
  * Default is `COM`.

### Changes to Customer Resource

* Market Segment: New field - `companyProfile.marketSegment`
  * Can be set during Create Customer and Update Customer.
  * A customer may only have a single market segment.
  * Default is `COM`.

* Market sub-segments: New field -  `companyProfile.marketSubSegments`

  * Read-only: Market sub-segments are set by the customer in the Adobe Admin Console.
  * Currently sub-segments are only applicable for the EDU market segment.
