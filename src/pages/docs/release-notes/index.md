# Release notes

- [API changes](#api-changes)
- [Sandbox changes](#sandbox-changes)

**Important:** This section lists only features that are currently available in production. For planned enhancements and upcoming changes, see [Upcoming releases](./upcoming-releases.md).

## API changes

### April 09, 2026

#### Support for automatic discount continuity across renewals

The discount reusability allows eligible discounts to be reused beyond their initial end date under defined conditions. With this enhancement, a discount can be configured for extended usage if it meets the required eligibility criteria. Partners can continue to apply a reusable discount until the configured reusableUntilDate, provided the discount is used at least once before the discount end date.

**Key details**

- Allows customers to reuse an eligible discount for renewals and seat additions until a configured discount lock end date.
- Ensures customers who use a discount during its active period can continue to benefit from it for a defined time, reducing churn caused by sharp price increases.
- Enables customers to review the applied reusable discount using Preview Renewal.
- Eliminates the need for customers to explicitly opt in again using Update Subscription when a reusable discount has already been applied to an eligible subscription.
- Introduced the following changes to [Get Flexible Discounts](../flex-discounts/apis.md#get-flexible-discounts) API:
  - The `include-eligible-reusable-discounts` query parameter fetches reusable flexible discounts in the response.
  - The newly introduced `REUSABLE` value of the `status` parameter indicates whether a flexible discount is reusable.
  - The `discountLockEndDate` parameter in the response indicates the date through which a flexible discount may continue to be applied beyond its initial offering period (start/end date). Reusable discounts are identified by the presence of the discountLockEndDate field in the response.

For more information, see [Manage Flexible Discounts](../flex-discounts/index.md).

**Sandbox changes**

The **Portal Resources > View Available Flex Discounts** page in the Sandbox UI lists the available flexible discounts. The **Edit Reusable Flex Discounts** tab allows you to change a discount's end date and test reusable flexible discounts. For more information, see [Edit reusable flexible discounts](../../sandbox/sandbox-portal/flex-discounts/index.md#edit-reusable-flexible-discounts).

### March 30, 2026

#### Early Renewals – Subscription renewal ahead of anniversary date

Facilitates the renewal of customer subscriptions before the Anniversary Date (AD). This capability enables customers to renew early while maintaining uninterrupted access to their existing subscription.

##### New capabilities

- **Early renewal window (AD‑30 to AD-1)**  
  Partners can now place renewal orders up to 30 days before the subscription’s anniversary date. Orders placed within this window are treated as renewal orders and are invoiced immediately.

- **Pricing based on date of renewal**

  Customers can benefit from the old price if they renew before a price change.

- **Three-year commitment (3YC) support**  
  For three-year commitment customers, price continues to be based on the 3YC commitment start date. Early renewals maintain compliance and may help non-compliant customers regain compliant status. Early renewals are not supported if the 3YC customer is in the last term.

- **Return policy enhancements**  
  Early renewal orders can be returned within the standard 14-day return window, even if the return occurs before the anniversary date. The anniversary date does not roll back after a return.

- **Auto-renew compatibility**  
  
  - Customers with auto-renewal enabled can still renew early. On the renewal date, any remaining quantities not covered by early renewals are automatically renewed.
  - Early renewing customers are not expected to turn off their auto-renewal configuration. Adobe intelligently handles the auto renewals accordingly and subscription states and attributes are updated as part of the standard renewal cycle.

For more information, see:

- [Renewals - Overview](../renewals/overview.md)
- [Managing early renewals using APIs](../renewals/manual-renewals.md)
- [Managing auto-renewals using APIs](../renewals/auto-renewals.md)
- [Error codes specific to early renewals](../renewals/error-codes.md)

**Sandbox changes**

- The Customer records in the Sandbox UI displays early renewal details of subscriptions. For more information, see: [View customer details in Sandbox](../../sandbox/sandbox-portal/customer-management/get-customer-details.md).

### March 23, 2026

#### Pricing and invoicing data available in Get Order APIs

The VIP Marketplace now provides partners with enhanced access to pricing and invoicing information through the `GET Order by ID` and `GET Order History` APIs.

These enhancements allow partners to view the same pricing elements that appear in the invoice spreadsheets, which improves transparency and reconciliation accuracy.

#### New capabilities

- Pricing is now available in the following APIs when the `fetch-price=true` query parameter is included in the request:

  - Get Order by ID
  - Get Order History

- Partners can retrieve detailed pricing and invoicing data directly through these API responses, including:

  - `partnerPrice`
  - `discountedPartnerPrice`
  - `netPartnerPrice`
  - `lineItemPartnerPrice`
  - `pricingSummary`

- Pricing is returned only for:

  - Single‑currency orders
  - Orders with completed status (1000)
  - Orders placed after the pricing feature was introduced

- Additional details

  - The `proratedDays` field appears when pricing is fetched for mid‑term purchases.
Multi‑currency pricing for previews is not supported.

For more information, see:

- [Preview with Pricing](../order-management/order-scenarios.md)
- [Get order by ID](../order-management/get-order.md#get-details-of-a-specific-order)
- [Get order history for a customer](../order-management/get-order.md#get-the-order-history-of-a-customer)

**Sandbox changes**

- The Order and Customer records in the Sandbox UI display pricing information. For more information, see: [View order details in Sandbox](../../sandbox/sandbox-portal/order-management/view-order-details.md) and [View customer details in Sandbox](../../sandbox/sandbox-portal/customer-management/get-customer-details.md).

### March 16, 2026

#### Flexible Discounts – Introductory offers support

This release enhances the Flexible Discounts capability in VIP Marketplace to support Introductory Offers as a new discount category. The introductory offer is a type of discount that helps partners apply a fixed-price discount to a customer’s first purchase or first-time use of a specific product. Some benefits include:

- Incentivizing early onboarding of new Adobe products
- Transitioning from SKU-based promotions to promotional codes

**New capabilities:**

- **Category filtering:**  
  The newly introduced `categories` query parameter of the [Fetch Flexible Discounts](../flex-discounts/apis.md#get-flexible-discounts) endpoint supports filtering by category. Supported values:
  - `STANDARD`: Regular Flexible Discounts (% off, $ off)
  - `INTRO`: Introductory Offers

- **New discount type:**  
  The `type` parameter with value `FIXED_PRICE` allows partners to offer a product at a fixed price, regardless of its original price.
  *Example:* Adobe Express at $39.99 for new customers.

- **Eligibility enhancements:**  
  Flexible Discounts for Introductory Offers can be applied only if the customer has never owned the product previously.

For more information, see:

- [Flexible Discounts](../flex-discounts/index.md)
- [Manage Flexible Discounts using APIs](../flex-discounts/apis.md)

### February 11, 2026

#### Modified Price List documentation

- Added a disclaimer in the Price List documentation, stating that using the `includeOfferAttributes` parameter in the request, the API will attempt to return the specified attributes for each offer. However, it is not guaranteed that every offer in the response will contain all the requested attributes. The availability of each attribute depends on the underlying data applicability and validity for the specific offer. If an attribute is not relevant or does not have a valid (non-null) value for a given offer, it may be omitted from that offer’s attribute section in the response.

  For more information, see [Manage Price Lists](../manage-pricing/index.md).

### January 08, 2026

#### Flexible Discounts for subscription renewals

Partners can now access details about flexible discounts for a customer and apply them in the subscription’s auto-renewal settings while creating or updating the subscription.

**New capabilities:**

- [Preview Renewal with flexible discount code](../flex-discounts/apis.md#preview-renewal-with-flexible-discount-code)
- [Manual preview renewal with flexible discount code](../flex-discounts/apis.md#manual-preview-renewal-order-with-flexible-discount-code)
- [Create a scheduled Subscription with flexible discount](../flex-discounts/apis.md#create-a-scheduled-subscription-with-flexible-discount)
- [Update Subscription with a flexible discount code](../flex-discounts/apis.md#update-a-subscription-with-flexible-discount-code)
- [Remove  flexible discount from a subscription](../flex-discounts/apis.md#remove-a-flexible-discount-from-a-subscription)

**Other API documentation changes:**

- Updated [Create Subscription](../subscription-management/create-subscription.md) and [Update Subscription](../subscription-management/update-subscription.md) endpoint documentation to include the newly introduced `flexDiscountCodes` parameter in request and response.
- Updated [Subscription (top-level resource)](../references/resources.md#subscription-top-level-resource) section to include the `flexDiscountCodes` parameter.

**Sandbox changes:**

- In **Manage Records > Customers**, you can now view the subscription details, including any flexible discounts that have been applied.
- For more information, see [Manage Flexible Discounts](../../sandbox/sandbox-portal/flex-discounts/index.md#view-flexible-discounts-applied-to-a-subscription).

### January 06, 2026

#### The Fetch Price List API now supports price lists for Large Government Agencies (LGA)

To retrieve LGA price lists, use the `priceListType` request parameter with the value `STD-LGA`, which specifies that the Price List is intended for LGA customers. Read more about [Fetch Price List API](../manage-pricing/index.md).

### December 11, 2025

#### Mid-term upgrades

Partners can upgrade customer subscriptions during the active term, without waiting for the renewal date.

**New capabilities**

- [Upgrade path discovery](../mid-term/apis.md#discover-upgrade-path)
  Partners can retrieve valid upgrade paths using the new [GET Offer Switch Paths](../mid-term/apis.md#discover-upgrade-path) API, filtered by market segment, country, and language.

- [Preview switch order](../mid-term/apis.md#2-preview-switch-order)
  A new `orderType` value, **`PREVIEW_SWITCH`**, has been added to the **Create Order API**. This allows partners to generate upgrade quotes before placing a switch order.

- [Switch order execution](../mid-term/apis.md#apply-switch-plan)
  Partners can place upgrade orders using the `SWITCH` order type, specifying both the "From" and "To" product details. The API supports automatic user reassignment via the `reassign-users=true` query parameter.

- [Upgrade reversion](../mid-term/apis.md#revert-switch-order)
  Partners can revert a switch order within 14 days using the new `REVERT_SWITCH` and `PREVIEW_REVERT_SWITCH` order types. This restores the original subscription and de-provisions the upgraded product.

- [Partial and full upgrades](../mid-term/apis.md#discover-upgrade-path)
  The system supports both full and partial upgrades, including seat expansions and product transitions (example: Acrobat Standard to Acrobat Pro).

- [Enhanced Error Handling](../mid-term/error-codes.md)

  Several new error codes have been introduced to provide clear feedback for mid-term upgrade scenarios, including:

  - 2149: Quantity mismatch
  - 2150: Switch path validity check failed
  - 2151: Quantity exceeded
  - 2152: Multiple line items not supported
  - 2153: Line item and cancelling line item mismatch
  - 2154: Upgrade not supported
  - 3115: Invalid subscription

Read more about [Mid-term upgrades](../mid-term/index.md).

### November 11, 2025

#### Change to reseller change approval code validity

- The reseller approval code is now valid for 7 days, an increase from the previous 72 hours. This code is emailed to all customer administrators, who then share it with the new reseller to authorize the transfer. Read more about [Reseller change process](../reseller-change/index.md).

#### Display source of the Order in Create Order and Get Order API responses

- Added the `source` parameter in the responses of [Create Order](../order-management/create-order.md) and [Get Order](../order-management/get-order.md) APIs to indicate the origin of the order. Possible values are: `API` or `System`.

#### Changes to Get Customer List and Get Reseller List APIs

- Added the `companyProfile.companyName` parameter to [Get Customer List](../customer-account/get-customer-list.md) and [Get Reseller List](../reseller-account/get-reseller-list.md) API responses to display the customer name and reseller name, respectively.

#### Display deploymentID and currencyCode in Reseller Transfer API responses

- Added `deploymentId` and `currencyCode` parameters to the responses of [Preview Reseller Transfer](../reseller-change/preview-transfer.md), [Commit Transfer](../reseller-change/commit-transfer.md), and [Get Reseller Transfer](../reseller-change/get-transfer.md) APIs.

#### Base part number in the GET Open Acquisitions API response

- Introduced the `baseOfferId` parameter in the response payload of the GET Open Acquisitions (aka GET Licenses Pending Partner Order) API. This parameter in the API response allows partners to match the correct part number using product name details from notification emails and proceed with order placement.

  Read more about [Get licenses pending partner order API](../customer-account/get-licenses.md).

### August 22, 2025

#### Preview with pricing

- Partners can now retrieve the pricing details in the Preview Order and Preview Renewal APIs by setting the `fetch-price` query parameter to `true` in the request URL. This returns real-time partner pricing details for Adobe products, helping partners and resellers better estimate how much Adobe will invoice for an order.
  - Introduced `pricing` object in the `lineItem` level of the Preview Order and Preview Renewal API responses.
  - Added `pricingSummary` object to provide summarized pricing details of all line items.
  - Introduced new `fetch-price` query parameter for both Preview Order and Preview Renewal scenarios.
  - Introduced new `proratedDays` parameter in the response of Preview Order and Preview Renewal APIs to indicate the number of days for which the order will be invoiced. This parameter appears only when the `fetch-price` parameter is set to `true` in the request.
  - A [new error code (2148)](../references/error-handling.md) has been introduced for the `Preview Order` and `Preview Renewal` APIs to handle pricing requests involving multiple currencies.

  Read more about [preview with pricing](../order-management/order-scenarios.md).

#### Get Customer List and Get Reseller List APIs

- The [Get Reseller List API](../reseller-account/get-reseller-list.md) enables partners to retrieve a list of resellers linked to a distributor.
  - The API supports filtering by reseller status and sorting by creation date or reseller name. \<br /\>Read more about [Get Reseller List API](../reseller-account/get-reseller-list.md).
- The [Get Customer List API](../customer-account/get-customer-list.md) enables partners to retrieve customer accounts associated with a specific reseller.
  - This API is essential for partners who manage customer portfolios and require precise, real-time access to customer data for quoting, reporting, and renewal planning. \<br /\>Read more about [Get Customer List API](../customer-account/get-customer-list.md).

### June 19, 2025

#### Fetch Price List

- Partners can leverage the `Fetch Price List` API to retrieve up-to-date pricing information for Adobe products. This API modernizes Adobe’s price list distribution process. Read more about [how to access price lists using Fetch Price List API](../manage-pricing/index.md).
  - Introduced new [error codes specific to the Price List API](../manage-pricing/index.md#error-codes-specific-to-fetch-price-list-api).

### May 09, 2025

#### Manage Flexible Discounts

- Partners can access flexible discounts tailored to specific products, market segments, and countries, and apply them during the order process. Read more about [how to manage flexible discounts](../flex-discounts/index.md). \<br /\>The major [API changes](../flex-discounts/apis.md) include:

  - Introduced the `GET /v3/flex-discounts` API to get the list of available flexible discounts.
  - Preview Order API returns the applicable flexible discounts for the customer.
  - Partners can place the order by applying the  flexible discount code received.
  - Introduced [new error codes specific to Flexible Discounts](../flex-discounts/error-codes.md).

#### Fetch Recommendations

- Recommendations API enables VIP Marketplace partners to deliver intelligent, personalized, and in-context product recommendations, enhancing customer experience through upsell, cross-sell, and add-on opportunities. Read more about [how to manage recommendations](../recommendations/index.md). \<br /\> Major [API changes](../recommendations/apis.md) include:
  - Partners can use `POST /v3/recommendations` API to fetch the relevant product recommendations.
  - Both Preview Order and Preview Renewal APIs fetch the applicable recommendations.
  - Both Get Subscriptions and Get Order APIs can fetch recommendations.
  - Introduced new [error codes specific to recommendations](../recommendations/error-codes.md).

### January 23, 2025

#### Migrate High Volume Discount (HVD) customers from VIP

- High Volume Discount (HVD) customers in VIP can now migrate to VIP Marketplace. See [Migrate High Volume Discount customers from VIP to VIP Marketplace](../migration/migrate-hvd.md) for more details.

#### Manage High Growth Offers

- Introduced High Growth Offers for existing Acrobat Pro customers. These offers will replace the current High Volume Discounting program that exists in VIP.  See [Manage High Growth Offers](../customer-account/high-growth.md) for more details.

### Earlier releases from 2024

- Included customer's VIP renewal status within migration preview API response. See [Preview Offer](../migration/preview-offers.md) for more details.
- Ability to place renewal orders after anniversary date

  Added allowedActions field in the Get All Subscriptions for a Customer API to indicate the subscriptions that can be selected for manual renewals. See [Get Subscription Details](../subscription-management/get-details.md) for more information.

- The newly introduced `RENEWAL` order type in the Create Order API is used for renewals that are initiated after the anniversary date. See [Create Order Scenarios](../order-management/order-scenarios.md) for more details.
- Enabling Global customers for Create Subscription.

  Global customers can now use Create Subscription API to create scheduled subscriptions. See [Create Subscriptions](../subscription-management/create-subscription.md) for more details.

- Create and manage linked memberships

  Adding support for creating and managing linked memberships that facilitates combining purchases across linked customer accounts to achieve better volume discounts. See [Manage linked memberships](../customer-account/linked-membership.md) for more information.

- “Late renewals” or “Renewal” Order Type

  Introducing a new Order Type for partners to place “late renewal” after anniversary date. See  [Create Order](../order-management/create-order.md) for more information.

- Create orders

  Implemented a validation to prevent partners from placing new orders before the subscription’s anniversary date. If a partner attempts to place an order before this date, an error message will appear as the response.

- Worldwide offers:

  - Added ability to enable a customer for worldwide (global) sales
  - Added ability to create “Deployments” for a customer to purchase worldwide offers
  - Added an optional currencyCode to the order lineItem level to support global deployments
  - Added deploymentId to order lineItem (read/write)
  - Added deploymentId and currencyCode to subscription resource (read only)
  - Added deploymentId and currencyCode to transfer resource (read only)

- Included 3yc info in preview migration and preview/execution of reseller change API calls.

- Create Subscription:

  - Added ability to create future dated subscription that would become active on next anniversary date. See [Create Subscription](../subscription-management/create-subscription.md) for more information.

- Added a new API to fetch all customers (under a given reseller) who have added seats via Adobe’s Admin console.
- Added a new API to fetch all seats/licenses added by a given customer via admin console.
- These APIs will inform our partners to place corresponding orders backing the seats added by the customers via admin console.
- Added new Error Code for Preview / Create Transfer response. See [Status Codes & Error Handling](../references/error-handling.md) for more information.
- Added self-service capability for a customer and partner to move between resellers/partners. See [Reseller Change Process](../reseller-change/index.md) for more information.

## Sandbox changes

### January 08, 2026

Changes to support on-demand flexible discount release:

- In **Manage Records > Customers**, you can now view the subscription details, including any flexible discounts that have been applied.
- For more information, see [Manage Flexible Discounts](../../sandbox/sandbox-portal/flex-discounts/index.md#view-flexible-discounts-applied-to-a-subscription).

### Earlier releases

- [View pricing data in order details](../../sandbox/sandbox-portal/order-management/view-order-details.md) and [View pricing details in customer records](../../sandbox/sandbox-portal/customer-management/get-customer-details.md)
- [Manage Flexible Discounts](../../sandbox/sandbox-portal/flex-discounts/index.md)
- [Manage Product Recommendations](../../sandbox/sandbox-portal/recommendations/index.md)
- [Manage High Growth Offers](../../sandbox/sandbox-portal/high-growth-offer/high-growth.md)
- [Migrate HVD customers from VIP to VIP Marketplace](../../sandbox/sandbox-portal/migrate-hvd-customers/migrate-hvd-customers.md)
- [Manage Linked Memberships](../../sandbox/sandbox-portal/linked-memberships/index.md)
- [View renewal status of memberships](../../sandbox/sandbox-portal/transfer-memberships/index.md)
