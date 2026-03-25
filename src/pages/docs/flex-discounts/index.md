# Manage Flexible Discounts

The Flexible Discount feature enables partners to easily adjust product prices, making them more attractive to different customer segments. These discounts are primarily intended to boost customer retention or acquisition. You can view flexible discount details for a product in a specific market segment and country, and apply them while placing an order or while creating or modifying a subscription.

Flexible discounts also support discount reusability, which allows eligible discounts to be reused for renewals and additional purchases until a defined discount reusability date. This capability helps customers renew with confidence by avoiding sudden price increases after an introductory offer expires.

Key advantages of flexible discounts include:

- **Discounts to drive new product adoption:**

  - Provides selective discounts for enterprise accounts to encourage early adoption of new products.
  - Helps facilitate faster market penetration.

- **Quick flexible discount launches for seasonal sales:**

  - Allows partners to activate discounts within days for timely seasonal discounts like Black Friday.

- **Renewals with reusable discounts**

   [*Note: This feature is not yet released, expected release - April, 2026*]
  
  - Allows customers to reuse an eligible discount for renewals and seat additions until a configured discount lock end date.
  - Ensures customers who use a discount during its active period can continue to benefit from it for a defined time, reducing churn caused by sharp price increases.
  - Enables customers to review the applied reusable discount using Preview Renewal.
  - Eliminates the need for customers to explicitly opt in again using Update Subscription when a reusable discount has already been applied to an eligible subscription.
  - Supports fixed-price introductory offers that span multiple terms, enabling better budget planning for customers.

## Flexible discount types

The following types of flexible discounts are available:

1. **Percentage Discount (% discount):** A percentage reduction is applied to the base price of the product.
2. **Fixed Discount ($ discount):** A fixed monetary reduction is applied to the base price of the product.
3. **Fixed Price:** Offers a product at a fixed price, regardless of its original price.

The discounts applied based on these discount types are explained in the following tables:

**Percentage Discount – Example: 20% discount for Acrobat Pro**

| Part Number      | Original Price | Discounted Price |
|------------------|----------------|------------------|
| 30006208CA01A12  | $89.99         | $71.99           |
| 30006208CA02A12  | $87.99         | $70.39           |
| 30006208CA03A12  | $85.99         | $68.79           |
| 30006208CA04A12  | $83.99         | $67.19           |
| 30006208CA012A12 | $79.99         | $63.99           |
| 30006208CA013A12 | $77.99         | $62.39           |
| 30006208CA014A12 | $75.99         | $60.79           |

**Fixed Amount Discount – Example: $20 discount for Acrobat Express**

| Part Number      | Original Price | Discount Amount | Discounted Price |
|------------------|----------------|-----------------|----------------|
| 30006208CA01A12  | $89.99         | $20.00          | $69.99         |
| 30006208CA02A12  | $87.99         | $20.00          | $67.99         |
| 30006208CA03A12  | $85.99         | $20.00          | $65.99         |
| 30006208CA04A12  | $83.99         | $20.00          | $63.99         |
| 30006208CA012A12 | $79.99         | $20.00          | $59.99         |
| 30006208CA013A12 | $77.99         | $20.00          | $57.99         |
| 30006208CA014A12 | $75.99         | $20.00          | $55.99         |

**Fixed Price – Examples**

| Part Number     | Original Price | Discounted Price |
|-----------------|----------------|------------------|
| 65322535CA04A12 | $89.99         | $49.99           |
| 86322535CA04A12 | $87.99         | $45.00           |

These discounts are applied to a part number using a discount code. Partners must use this discount code while placing an order to provide a discounted price for their customers.

## Eligibility criteria

Flexible discounts are available to all VIP Marketplace customers, regardless of their existing volume discounts, including those with Linked Memberships. However, eligibility is determined by other factors, such as the customer's market segment, country, and so on.

Introductory offers apply only to customers who are purchasing a product for the first time.

For reusable discounts, customers must use the discount at least once during the discount’s active period. Once used, the discount can be reused for renewals and eligible order updates until the configured discount reusability date, provided at least one qualifying order remains active.

## Partner integration process  

Partners need to facilitate the discovery and application of flexible discounts. There are two ways to discover flexible discounts today:

- **Open Discounts:** These discounts are returned through the Flexible Discounts API and are primarily used to acquire new customers who meet specific eligibility criteria.

  Example: 20% off Creative Cloud Pro Plus for Black Friday

- **Closed Discounts:** Discounts shared with partners through Customer Account Manager (CAM) or Sales Agents.  These discounts are intended to retain customers who are at risk of attrition or to target customers who are eligible for an upgrade. Although these discount codes are applied to orders in the same way as Open Discounts, these codes are not available through the [Get Flexible Discounts API](./apis.md#get-flexible-discounts).

  Example: 15% off discounts for customers with Acrobat Pro licenses who renew to Acrobat Studio

Both Open and Closed Discounts can leverage the discount types specified in the [Flexible discount types section](#flexible-discount-types).

Follow the steps illustrated in the figure below to apply flexible discounts and obtain the discounted price:

![Partner integration process](../image/flex_7.png)

## What's next?

Read more about:

- [Managing Flexible Discounts using APIs](./apis.md)
- [Error codes specific to Flexible Discounts](./error-codes.md)
- [How to test flexible discounts in Sandbox](../../sandbox/sandbox-portal/flex-discounts/index.md)
