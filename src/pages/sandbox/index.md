# Introduction

Sandbox is a development environment where Adobe Partners can test their integrations with Adobe’s Commerce Partner APIs and marketplaces. Sandbox replicates the production environment, allowing the Partner to fully test and implement Adobe’s Commerce Partner API (CPAPI) calls without incurring any costs or impacting production data.

Interaction with Sandbox can occur through API calls, the [Sandbox Partner Portal](https://partnerportal-sandbox.adobe.com/), or both, as illustrated in the matrix below:

| CPAPI Call                                                           | API calls | Portal |
|----------------------------------------------------------------------|-----------|--------|
| Direct Partner Management                                            |           |        |
| Enable/Disable Global Sales                                          |           | ✓      |
| Reseller Management                                                  |           |        |
| Create Reseller Account                                              | ✓         | ✓      |
| Get Reseller (View Reseller details)                                 | ✓         | ✓      |
| Update Reseller Information                                          | ✓         |        |
| Edit Reseller Status                                                 |           | ✓      |
| Customer Management                                                  |           |        |
| Create Customer Account                                              | ✓         | ✓      |
| Get Customer Account / View Customer details                         | ✓         | ✓      |
| Update Customer Account                                              | ✓         |        |
| Edit Anniversary/Co-Term Date                                        |           | ✓      |
| Edit Customer Status                                                 |           | ✓      |
| Edit Global Sales Status                                             | ✓         | ✓      |
| Trigger Auto Renewal                                                 |           | ✓      |
| Linked Membership                                                    |           |        |
| Creating Linked Account Groups                                       | ✓         |        |
| Viewing Linked Membership Details Linked Membership terms acceptance | ✓         |        |
| Generating approval code                                             |           | ✓      |
| Enroll as a member to Linked Membership                              |           | ✓      |
| Refresh Discount Levels                                              | ✓         | ✓      |
| Deployment Management                                                |           |        |
| Update Deployment                                                    | ✓         | ✓      |
| Get Deployment Details                                               | ✓         | ✓      |
| Get Deployments for Customer                                         | ✓         | ✓      |
| Order Management                                                     |           |        |
| Create Order                                                         | ✓         | ✓      |
| Get Order Details                                                    | ✓         | ✓      |
| Cancel Order                                                         | ✓         | ✓      |
| Get Orders for Customer                                              | ✓         | ✓      |
| Edit Order Status (other than cancelling order)                      |           | ✓      |
| Subscription Management                                              |           |        |
| Get Subscription Details                                             | ✓         | ✓      |
| Get Subscription for Customer                                        | ✓         | ✓      |
| Update Subscription Auto-Renewal                                     | ✓         | ✓      |
| Transfers                                                            |           |        |
| Preview Offers                                                       | ✓         |        |
| Transfer Subscriptions                                               | ✓         |        |
| Get Transfer Details                                                 | ✓         | ✓      |
| Update Transfer Status                                               |           | ✓      |
| Create Membership ID                                                 |           | ✓      |
| View Created Memberships                                             |           | ✓      |
| Delete Membership IDs                                                |           | ✓      |

In addition to CPAPI interaction, the Portal offers other capabilities, such as:

- A list of ineligible membership IDs for testing
- A list of all offers available to your distributor
- A list of all available distributor regions, along with their associated countries, country regions, postal codes, and currencies
- An address validator
- Search, filter, and sort capabilities within reseller, customer, order, transfers, and membership data
- Direct partner management
- 3YC flow management
- Additional links
- Partner CPAPI support
