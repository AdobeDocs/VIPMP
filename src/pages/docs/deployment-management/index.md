# Manage deployments

For a global customer to purchase offers outside their home country, they must create and use deployment resources. If a partner is enabled for international sales, they can enable customers for global sales. Once a customer has been enabled for global sales, they can create deployments with addresses outside of the customer’s country. These deployments can then be used to place orders for offers and currencies that do not correspond to the customer’s address.

Global customers follow the [local currency cutover rules](../manage-pricing/index.md#local-currency-support) for the country associated with each deployment. During a currency change, renewal consolidation applies only to subscriptions with the same deployment and SKU. Subscriptions across different deployments are not consolidated, even when they have the same SKU. Continue to use `deploymentId` for deployment-specific requests.

Use the following APIs to manage your deployments:

- [Create deployment](create-deployment.md)
- [Get details of deployment](get-deployment.md)
- [Update deployment](update-deployment.md)
