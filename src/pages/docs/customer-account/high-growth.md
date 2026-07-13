# Manage High Growth Offers

High growth offers are designed for customers who are either making new purchases or significantly expanding their existing Acrobat Pro seats. This will replace the current High Volume Discounting program (17-23 levels) that exists in VIP.

## What are High Growth Offers?

High Growth Offers are based on the Minimum Order Quantity (MOQ), which defines the purchase quantity and the corresponding discount rates. Any subsequent purchases will adhere to the customer’s High Growth MOQ Offer price.

The available High Growth Offers based on MOQ are:

- MOQ 100
- MOQ 250
- MOQ 500

**Note:** In this document, the terms "High Growth Offer" and "MOQ offer" are used interchangeably.

## Who is eligible for High Growth Offers?

Enterprise and Team customers of Acrobat Pro in the commerce, government, and education market segments can take advantage of High Growth Offers if they meet the following criteria:

1. **Enrollment:** Customers must be enrolled in the [3YC program](three-year-commit.md).
2. **MOQ requirement:** Customers require a minimum order quantity (MOQ) of 100 or more.

   - Customers can select from the available MOQ offers based on their seat requirements. For example, a customer with 110 seats can opt for either the 250 or 500 MOQ offer option, as shown in the following table.
   - MOQ offers are available to both new and existing customers with a certain number of licenses for specific product groups. For example, Acrobat Standard and Acrobat Pro are part of the Acrobat group, and customers with a combined quantity that falls into the range mentioned in the following table become eligible for the corresponding MOQ offers.

  | Current seats at renewal \<br/\> Acrobat Pro + Standard | Eligible Minimum Order Quantity (MOQ) |
  |-------------------------------------------------------|---------------------------------------|
  | ≤ 50 seats                                            | 100, 250, or 500             |
  | ≤ 125 seats                                           | 250 or 500                        |
  | ≤ 250 seats                                           | 500                                   |

3 - **MCQ requirement:** The number of seats committed (MCQ) must be greater than or equal to the number of subscriptions at the renewal date.

  | Current seats at renewal \<br /\> Acrobat Pro + Standard | Eligible Minimum Order Quantity (MOQ) | 3YC Minimum Commit Quantities (MCQ) - Needs to be increased based on the MOQ |
  |-------------------------------------------------------|---------------------------------------|-------------------------------------------------------------------------------|
  | ≤ 50 seats                                            | 100 \<br /\> 250 \<br /\> 500             | 100+                                                                          |
  | ≤ 125 seats                                           | 250 \<br /\> 500                        | 250+                                                                          |
  | ≤ 250 seats                                           | 500                                   | 500+                                                                          |

## High Growth Offer workflows

Customers can opt for an MOQ offer either at the anniversary or mid-term. Purchases made on the anniversary date apply to the next term, while mid-term purchases apply to the current term. All MOQ offer purchases on the anniversary date must be completed within the purchase window from AD-30 to AD-3.

The APIs required to obtain MOQ offers differ depending on various scenarios. The High Growth Offer workflow for these scenarios and the corresponding APIs are shown in the following figure:

![High Growth flow for Acro customers](../image/hg_flow.png)

Read more about [High Growth Offer scenarios](high-growth-scenarios.md).
