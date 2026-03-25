# Upcoming releases

The following features are scheduled for release:

**Important:** The release timelines stated herein are indicative only and are provided for guidance purposes. Actual release dates may vary.

## Early Renewals – Subscription renewal ahead of anniversary date

**Expected release:** April, 2026.

Facilitates the renewal of customer subscriptions before the Anniversary Date (AD). This capability enables customers to renew early while maintaining uninterrupted access to their existing subscription.

### New capabilities

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

## Promotion Reusability support

**Expected release:** April, 2026.

The promotion reusability allows eligible promotions to be reused beyond their initial end date under defined conditions. With this enhancement, a promotion can be configured for extended usage if it meets the required eligibility criteria. Partners can continue to apply a reusable promotion until the configured reusableUntilDate, provided the promotion is used at least once before the promotion end date.

**Key details**

- Allows customers to reuse an eligible discount for renewals and seat additions until a configured discount lock end date.
- Ensures customers who use a discount during its active period can continue to benefit from it for a defined time, reducing churn caused by sharp price increases.
- Enables customers to review the applied reusable discount using Preview Renewal.
- Eliminates the need for customers to explicitly opt in again using Update Subscription when a reusable discount has already been applied to an eligible subscription.
- Supports fixed-price introductory offers that span multiple terms, enabling better budget planning for customers.

**Sandbox changes**

The **Portal Resources > View Available Flex Discounts** page in the Sandbox UI lists the available flexible discounts. The **Edit Reusable Flex Discounts** tab provides the option to change the end date of a discount and test the use of reusable flexible discounts. For more information, see [Edit reusable flexible discounts](../../sandbox/sandbox-portal/flex-discounts/index.md#edit-reusable-flexible-discounts).
