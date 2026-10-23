# Manage auto-renewals using APIs

Managing auto‑renewals through APIs allows you to programmatically control how subscriptions renew based on customer preferences and configured quantities. Partners can use the following APIs to configure auto-renewals for a subscription:

| Endpoint                                                      | Description               |
|---------------------------------------------------------------|----------------------------|
| `/v3/customers/<customer-id>/subscriptions`                   | POST   |
| `/v3/customers/<customer-id>/subscriptions/<subscription-id>` | PATCH  |

The system picks up the subscription on the Renewal Date.  Subscriptions are renewed based on the configured renewal quantity and the customer’s renewal preferences. If early renewal was previously applied:

- Auto‑renewal may renew the remaining quantities (if any).
- If all quantities were already renewed, auto‑renewal updates subscription states but does not add new line items.

Auto‑renewal process outcomes:

- Renew the subscription if auto‑renew is enabled.
- Expire the subscription if auto‑renew is turned off.
- Update the subscription attributes even when all seats were already early renewed.

## Renewals when the country currency changes

After a country transitions to a new currency, eligible subscriptions renew in the new currency at the next anniversary renewal. Active 3YC and HGO commitments remain in the previous currency until the commitment ends. See [Local currency support](../manage-pricing/index.md#local-currency-support) for cutover and commitment rules.

If subscriptions in both the previous and new currencies exist for the same deployment and SKU, the remaining eligible quantities are consolidated into a single renewal order in the new currency at anniversary renewal. If a new-currency subscription does not exist, the renewal creates one using the remaining renewal quantity from the previous-currency subscription. Auto-renewal for the previous-currency subscription is disabled, but the subscription remains active until it expires. Scheduled subscriptions transition with the active subscription.

### Calculate the combined renewal quantity

1. Determine each subscription's target independently: use `autoRenewal.renewalQuantity` when explicitly set, including an explicit zero; otherwise use `currentQuantity`.
2. Subtract each subscription's already early-renewed quantity from its own target.
3. Sum the remaining quantities for the renewal order in the new currency. If early renewals already cover both targets in full, no anniversary renewal order is placed.

For example, a USD subscription has a target of 100 seats, of which 40 were early renewed before cutover. A CAD subscription has a target of 20 seats, none early renewed. The anniversary renewal is **80 CAD seats**: (100 - 40) + (20 - 0).

[Preview Renewal](../order-management/order-scenarios.md#preview-renewal-orders) shows the consolidated renewal quantity. Partners can [update the renewal quantity](../subscription-management/update-subscription.md) on either subscription before renewal; review both targets to avoid counting the same intended seats twice.

When both subscriptions exist, the new-currency subscription's auto-renewal preference takes precedence at consolidation. Flexible discount codes and renewal/MOQ codes carry over to the new-currency subscription.

These rules do not move the renewal processing date when early renewal rolls the displayed anniversary date forward. The existing `renewalDate` continues to trigger renewal processing; see [Impact of early renewal on anniversary date and renewal date](manual-renewals.md#impact-of-early-renewal-on-anniversary-date-and-renewal-date).

## See also

- [Create Subscription](../subscription-management/create-subscription.md)
- [Update Subscription](../subscription-management/update-subscription.md)
- [Manage renewals (overview)](overview.md)
- [Managing early renewals using APIs](manual-renewals.md)
