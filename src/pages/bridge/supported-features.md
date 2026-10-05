# Supported features

The Bridge supports a growing subset of the workflows available through the Commerce Partner APIs. This page lists the features that are currently available in the Bridge UI, the navigation path for each feature, and the capabilities that still require direct API integration.

This list will expand as the Bridge adds more API workflow coverage.

## Supported in the Bridge UI

### Access and navigation

| Feature                            | UI path                                                                                                          |
|-------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Sign in and select a distribution  | Sign in at **bridge.marketplace.adobe.com** > **Personal Account** > select organization > select distribution   |
| Switch between distributions       | Organization or distribution picker, top right, available from any screen                                        |

**Watch video on logging in and selecting the right organization**

<Embed slots="video" />

[Logging in and selecting the right organization](../assets/logging-in.mp4)

**Watch video on essential navigation** 

This video covers ain navigation, switching organizations and distributions, and navigating the interface

<Embed slots="video" />

[Bridge - Essential Navigation](../assets/essential-navigation.mp4)

### Reseller and customer management

| Feature                                                                                                                                                      | UI path                                                        |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| View all resellers under your organization                                                                                                                     | **Resellers** from the left navigation menu                     |
| View all customers under a reseller                                                                                                                            | **Resellers** > select a reseller                                |
| Search for a reseller or a customer                                                                                                                            | **Resellers** > search bar, or **Resellers** > reseller > customer search bar |
| Create a customer                                                                                                                                               | Cart > **Add customer details** > create a new customer         |
| View customer account details, including reseller ID, company name, address, admins, discount level, 3YC level, linked membership, and next anniversary date | **Resellers** > reseller > customer > **Account Details** tab   |
| Modify customer address and add an administrator                                                                                                               | **Resellers** > reseller > customer > **Account Details** tab   |

**Watch video on navigating the customer** 

This video covers topics such as finding resellers and customers, viewing customer information, products, and purchase history.

<Embed slots="video" />

[Bridge - Navigating the Customer](../assets/navigating-the-customer.mp4)

### Customer insights

| Feature                                                                              | UI path                                                                                     |
|----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| View a customer's active products and subscriptions                                    | **Resellers** > reseller > customer > **Products** tab                                        |
| Look up a customer's purchase history                                                  | **Resellers** > reseller > customer > **Purchase History** tab                                |
| Purchase a recommended product, or add a recommended product to an upcoming renewal    | Customer page > **Personalized recommendations** section > **Buy**, or **Add to renewal**     |

### Discounts and programs

| Feature                                                                | UI path                                                                                                                                                                              |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Discover and apply flexible discounts                                    | **Resellers** > reseller > customer > **Available Discounts** tab, or Checkout > **Review details** > discount selector, or **Available Discounts** from the left navigation menu     |
| Commence a Three-Year Commit, or 3YC, for Platinum partners               | Customer page > **Enroll** > enter subscription details > **Invite to enroll**                                                                                                         |
| Commence a Three-Year Commit, or 3YC, for Distributors                   | Customer page > **Enroll**. The Bridge opens a read-only modal that directs you to initiate the amendment from your own platform through the reseller.                                |

### Product catalog and ordering

| Feature                                                                                                | UI path                                                                                        |
|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Browse and access products from pricelists, filtered by market segment, category, type, and currency      | **Catalog** from the left navigation menu                                                         |
| Add a product to the cart                                                                                 | **Catalog** > select product > **Buy**                                                            |
| Start a new order for an existing customer                                                                | Cart > **Add customer details** > find existing customer by reseller and customer name or ID      |
| Place an order                                                                                             | Checkout > **Review details** > **Place Order**                                                   |
| Full or partial return of an order                                                                                            | Customer page > **Purchase History** tab > select order > **Return**                              |

**Watch video on ordering additional and new product licenses:** 

This video covers topics such as creating new customers, new product purchases, and additional license orders.

<Embed slots="video" />

[Bridge - Ordering Additional & New Product Licenses](../assets/ordering-additional-and-new-product-licenses.mp4)

### Subscription management

| Feature                              | UI path                                                                     |
|-----------------------------------------|--------------------------------------------------------------------------------|
| Modify subscription to add licenses     | Customer > **Products** tab > active product > **Add licenses**               |
| Place a mid-term upgrade order          | Customer > **Products** tab > active product > **Upgrade**                    |
| Change auto-renewal preferences         | Customer > **Products** tab > active product > **Auto-renewal settings**      |

**Watch video on anytime upgrades:** Mid-term upgrades and subscription changes

<Embed slots="video" />

[Bridge - Anytime Upgrades](../assets/anytime-upgrades.mp4)

### Renewals

| Feature                                | UI path                                                             |
|-------------------------------------------|-------------------------------------------------------------------------|
| Add a product to an upcoming renewal      | Catalog > Find existing Customer > Add products to Renewal              |
| Place an early renewal order              | Customer page > **Early renewal** > **Submit early renewal**            |
| Submit a late renewal order               | Customer page > **Late Renewal Banner** > **Submit late renewal**       |

**Watch video on renewals:** Auto-renewals, early renewals, late renewals, and product mix renewals

<Embed slots="video" />

[Renewals](../assets/renewals.mp4)

## API-only capabilities

The following capabilities are available through the Commerce Partner APIs, but are not yet exposed in the Bridge UI. Continue to use [direct API integration](../docs/index.md) for these workflows.

| Capability                                                                                         | Reference                  |
|------------------------------------------------------------------------------------------------------|-----------------------------|
| Create a reseller                                                                                     | [Reseller accounts](../docs/reseller-account/index.md)          |
| Create a customer as a standalone action, outside of the order flow                                  | [Customer accounts](../docs/customer-account/index.md)          |
| Manage Linked Memberships                                                                             | [Linked Memberships](../docs/customer-account/linked-membership.md)         |
| Manage worldwide, or WW, contracts through Deployments (only the simplified model of WW is supported)     | [Deployments](../docs/deployment-management/index.md)                |
| Migrate customers from VIP to VIP Marketplace                                                        | [Migrate to VIP Marketplace](../docs/migration/index.md) |
| Manage notifications, including converting Pending Actions, or PAs, to orders                        | [Notifications](../docs/notification-management/index.md)              |
| Access High Growth Offers                                                                             | [High Growth Offers](../docs/customer-account/high-growth.md)         |
| Manage Extended Term Customers                                                                        | [Extended Term Customers](../docs/customer-account/index.md)    |
| Manage 3YC commit revisions and recommitment                                                          | [Three-Year Commit (3YC)](../docs/customer-account/three-year-commit.md)    |

## Related

Use the following pages for more context:

- [Overview](./index.md)
- [Getting started](./getting-started.md)
