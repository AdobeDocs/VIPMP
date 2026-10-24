# Manage subscriptions

A Subscription is an agreement between the customer and Adobe to provide, receive, or use Adobe paid and free products and services on a continuing or periodic basis. A subscription may contain one or more acquired offers.

## Subscriptions during a country currency change

After a country transitions to a new currency, new-currency orders and anniversary renewals create a subscription in the new currency if one does not already exist. The subscription in the previous currency remains active until it expires, with auto-renewal disabled as the subscription transitions to the new currency. As a result, two active subscriptions with different currencies can exist for the same SKU. Retain the separate subscription IDs and currencies in your integration.

For example, this behavior applies when Canada transitions from USD to CAD or Mexico transitions from USD to MXN.

At anniversary renewal, eligible subscriptions in the previous and new currencies for the same deployment and SKU are consolidated after excluding quantities that were renewed early. Active 3YC and HGO commitments remain in the previous currency until the commitment ends. See [Local currency support](../manage-pricing/index.md#local-currency-support) and [Currency-change auto-renewals](../renewals/auto-renewals.md#renewals-when-the-country-currency-changes). The consolidation rules take precedence over the independent subscription behavior described below.

## Auto-renewal scenarios

You can manage the auto-renewal preferences for each subscription independently through the subscription’s `autoRenewal` object, as shown in the following example:

```json
"autoRenewal": {
  "enabled": true,
  "renewalQuantity": 7
}
```

You can update these preferences through the [Update Subscription](update-subscription.md) endpoint.

The `enabled` field specifies whether a subscription will be auto-renewed, and the `renewalQuantity` determines the quantity of the subscription that will be renewed.

The `autoRenewal` preferences are not evaluated until the renewal time. You can enable or disable autorenewal and change the quantity throughout the duration of the subscription. On the `cotermDate` of the customer, all of the customer’s subscriptions with auto-renewal enabled will renew for the specified quantity.

At the time of renewal, there are two possible scenarios for each subscription:

- Enabled is set to `false` – Subscription becomes inactive (status 1004).
- Enabled is set to `true` – Subscription is marked for renewal (status will remain 1000) for the amount specified in the `renewalQuantity` object. During this brief period while waiting for the renewal order to be fulfilled, the `autoRenewal` preferences may not be updated.

To ensure that all subscriptions are renewed in accordance with the autorenewal preferences, set the autorenewal preferences to at least 3 days before the customer’s cotermDate (subscription’s `renewalDate`). The customer’s subscriptions will be renewed at some point soon after midnight UTC, but the exact time may change or vary by region.

If the `renewalQuantity` is higher than the current ordered quantity, then additional licenses will be provisioned after renewal. If the `renewalQuantity` is less than the current ordered quantity, then some licenses will be revoked upon renewal.

If the customer has subscriptions set to auto-renew, Adobe will create a renewal order on renewal date of subscription. The renewal order details and Order ID can be retrieved by parsing the response from the [Get Order History](../order-management/get-order.md) API. This ID can then be used in subsequent Get Order calls.

## APIs to manage subscriptions

The following API endpoints are available to manage subscriptions:

- [Create subscription](create-subscription.md)
- [Get details of a specific subscription](get-details.md)
- [Get details of all subscriptions of a customer](get-details-for-customers.md)
- [Update subscription](update-subscription.md)
