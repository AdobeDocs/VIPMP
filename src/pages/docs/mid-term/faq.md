# Frequently asked questions

This detailed FAQ covers common questions about Adobe’s mid-term upgrade feature, also referred to as Switch Orders. Use this resource to learn the upgrade process, resolve issues, and efficiently implement switch order workflows.

## Table of contents

- [General concepts](#general-concepts)
- [Switch paths and eligibility](#switch-paths-and-eligibility)
- [Order types and workflow](#order-types-and-workflow)
- [Quantities and pricing](#quantities-and-pricing)
- [Revert switch](#revert-switch)
- [User reassignment](#user-reassignment)
- [Special customer types](#special-customer-types)
- [API implementation](#api-implementation)
- [Error handling](#error-handling)
- [Miscellaneous](#miscellaneous)

## General concepts

### What is a mid-term upgrade?

**Answer:** A mid-term upgrade allows partners to upgrade a customer's product subscription to a higher-tier product at any time during the current term, without waiting for renewal. This is handled through the [Orders API](../order-management/create-order.md) with the order type set to “SWITCH.”

- **Why mid-term upgrade:**

  | Previous limitation. | Advantage with mid-term upgrade. |
  |---|---|
  | Customers wait months for anniversary date. | Immediate access to advanced features. |
  | Lost revenue opportunities for partners. | Immediate upsell revenue realization. |
  | 14-day return limit prevents mid-term changes. | Prorated upgrades with automatic refunds. |

- **Key benefits:**

  - **Customer flexibility:** Access advanced features immediately when business needs change.
  - **Pricing transparency:** Prorated pricing with automatic refunds for unused time on original subscription.
  - **Seamless transition:** Maintains subscription continuity with automatic quantity adjustments.

- **Example:** A customer with 50 Acrobat Pro licenses can upgrade 20 of them to Acrobat Sign mid-term, keeping 30 Acrobat Pro licenses active.

### What is the difference between a switch and a regular order?

**Answer:**

|                         | Regular order                          | Switch order                           |
|-------------------------|----------------------------------------|----------------------------------------|
| Timing                 | Can be placed anytime                  | Mid-term upgrade during active subscription. |
| Existing subscription   | Creates new subscription               | Modifies existing subscription.         |
| Pricing                 | Full term pricing                      | Prorated pricing for remaining term.    |
| Original product        | Not affected                           | Quantity reduced or subscription cancelled. |
| Flexible promotion      | Allowed                                | Not allowed.                            |
| Use case                | New purchase or add-on                 | Upgrade to higher-tier product.         |

### What is the difference between a full switch and a partial switch?

**Answer:**

- **Full switch:**

  - The entire quantity of the original subscription is upgraded to the new product.
  - Original subscription is completely cancelled (quantity becomes 0).
  - Only the new product subscription renews at term end.

- **Example:** Switch all 100 Photoshop licenses to Creative Cloud All Apps.

- **Partial switch:**

  - Only a portion of the original subscription quantity is upgraded.
  - Original subscription remains active with reduced quantity.
  - Both original and new product subscriptions renew at term end.

- **Example:** Switch 30 out of 100 Photoshop licenses to Creative Cloud All Apps (70 Photoshop remain).

- **Important:** After a full switch, only the new product renews. After a partial switch, both products renew at their respective quantities.

### Can I switch between any two products?

**Answer:** No. Switch paths are predefined and directional. You can only switch between products that have an established upgrade path.

- **Key points:**

  - Switch paths are one-way (A→B doesn't mean B→A is valid).
  - Use the [GET Product Switch Paths API](apis.md#discover-upgrade-path) to discover valid paths.
  - Paths vary by market segment (COM, GOV, EDU), country, and language.
  - Some products (like High Growth Offers) have restricted switch eligibility.

- Examples for upgrade paths:

  - Photoshop → Creative Cloud All Apps.
  - Acrobat Sign → Acrobat Pro (not a valid upgrade path).

### Which products are not eligible for mid-term upgrades?

**Answer:** Stock Credit Packs (SCP) and Adobe Sign are not eligible for mid-term upgrades.

## Switch paths and eligibility

### How do I discover available upgrade paths for my customer?

**Answer:** Use one of two API methods:

- **Method 1: By market segment (general discovery).**

  GET `{env root url}/v3/offer-switch-paths?market-segment=COM&country=US&language=MULT`

  Returns all possible switch paths for the specified market segment.

- **Query parameters:**

  - market-segment (required): COM, GOV, or EDU.
  - country (required): ISO country code (e.g., US, GB, CA).
  - language (optional): Language code. Default is MULT (multi-language).

- **Method 2: By subscription (specific customer).**

  GET `{env root url}/v3/offer-switch-paths?subscription-id={{subscriptionId}}&customer-id={{customerId}}`

  Returns valid switch targets for a specific customer's subscription.

- **Recommendation:** Use Method 2 when working with a specific customer subscription to ensure the path is valid for their exact configuration.

### How do I discover available upgrade paths for an offer?

**Answer:** Use the [GET Offer Switch Paths](apis.md#discover-upgrade-path) API to discover upgrade paths at the product level using the base offer ID.

- **Example request:**

  GET `{env root url}/v3/offer-switch-paths?offer-id=65304479CA01A12&market-segment=COM&country=US&language=MULT`

- **Example response:**

```json
{
    "totalCount": 3,
    "count": 3,
    "offset": 0,
    "limit": 20,
    "productUpgrades": [
            {
                "sourceBaseOfferId": "65304479CA01A12",
                "targetList": [
                        {
                                "targetBaseOfferId": "65324898CA01A12",
                                "sequence": 1,
                                "switchType": "PARTIAL_ALLOWED"
                        },
                        {
                                "targetBaseOfferId": "30005296CA01A12",
                                "sequence": 2,
                                "switchType": "PARTIAL_ALLOWED"
                        },
                        {
                               "targetBaseOfferId": "65324888CA01A12",
                               "sequence": 3,
                               "switchType": "FULL_ONLY"
                        }
                    ]
            }
        ]
}
```

**Response fields explained:**

| Field                | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| `totalCount`         | Total number of upgrade paths available.                                    |
| `count`              | Number of upgrade paths returned in this response.                          |
| `offset`             | Pagination offset (start index).                                            |
| `limit`              | Maximum number of items per response.                                       |
| `productUpgrades`    | Array of upgrade paths for the source offer.                                |
| `sourceBaseOfferId`  | The base offer ID you are upgrading from.                                   |
| `targetList`         | List of possible upgrade targets.                                           |
| `targetBaseOfferId`  | The base offer ID you can upgrade to.                                       |
| `sequence`           | Order in which upgrade paths are listed.                                    |
| `switchType`         | Indicates if partial upgrades are allowed (`PARTIAL_ALLOWED`) or only full upgrades (`FULL_ONLY`). |

**Example interpretation:**

- You can upgrade from offer `65304479CA01A12` to:
        - `65324898CA01A12` (partial switch allowed)
        - `30005296CA01A12` (partial switch allowed)
        - `65324888CA01A12` (full switch only)
- Use `switchType` to determine if you can upgrade part of the quantity or must upgrade all licenses.
- Use the `targetBaseOfferId` in your switch order API requests.

### Why does the API show a switch path exists, but my switch order fails?

**Answer:** Several factors beyond the general switch path may affect eligibility:

- **Subscription status:** Original subscription must be active (not expired, canceled, or in a grace period).
- **Discount level mismatch:** Customers on 3YC plans must maintain the same discount level.
- **Market segment mismatch:** The customer’s market segment must align with the switch path.
- **Country restrictions:** Global customers have limitations on cross-country switches.
- **Product-specific rules:** High Growth Offers have special restrictions.
- **Quantity constraints:** Switching quantity cannot exceed the available subscription quantity.

**Troubleshooting steps:**

1. Verify subscription status using `GET /customers/{customerId}/subscriptions/{subscriptionId}`.
2. Check whether the customer's market segment matches the switch path.
3. Confirm discount levels match (for 3YC customers).
4. Verify the switch path using subscription-specific API to ensure it's valid for this customer.

### Why am I getting "Switch path validity check failed" (Error 2150)?

**Answer:** This error indicates the requested switch path is not valid. Common causes:

- **No switch path exists:** The source→target combination is not configured.
- **Wrong market segment:** Using COM path for GOV customer (or vice versa).
- **Directional path:** Trying reverse direction (B→A when only A→B exists).
- **High Growth Offer restrictions:** Source or target is a High Growth Offer with limitations.
- **Country mismatch:** Global customer trying cross-country switch.

**Resolution:**

- **Step 1:** Verify with subscription-based API.  
GET `{env root url}/v3/customers/{customerId}/subscriptions/{subscriptionId}/offer-switch-paths`

- **Step 2:** If no paths returned, check general paths.  
GET `{env root url}/v3/offer-switch-paths?market-segment=COM&country=US&language=MULT`

- **Step 3:** If path exists in general but not for subscription.  
→ Review subscription status, discount level, and market segment.

## Order types and workflow

### What are the different order types for mid-term upgrades?

**Answer:** There are four order types:

   | Type                  | Description                    | Preview | Execute | Shows pricing/refund |
   |-----------------------|-------------------------------|---------|---------|---------------------|
   | PREVIEW_SWITCH        | Preview upgrade before execution | Yes     | No      | Shows prorated pricing |
   | SWITCH                | Execute the upgrade              | Yes     | Yes     | Charges prorated amount |
   | PREVIEW_REVERT_SWITCH | Preview reverting an upgrade     | Yes     | No      | Shows refund/credit |
   | REVERT_SWITCH         | Reverse a previous upgrade       | Yes     | Yes     | Processes refund/credit |

**Recommended workflow:**

1. PREVIEW_SWITCH → Review pricing → Get customer approval.
2. SWITCH → Execute upgrade.
3. (If needed) PREVIEW_REVERT_SWITCH → Review impact.
4. (If needed) REVERT_SWITCH → Reverse upgrade.

### Is PREVIEW_SWITCH required before executing a SWITCH?

**Answer:** No, [PREVIEW_SWITCH](apis.md#2-preview-switch-order) is optional but highly recommended.

**Benefits of using PREVIEW_SWITCH:**

- Shows exact prorated pricing before charging customer.
- Validates switch path and quantity availability.
- Identifies potential errors before execution.
- Provides transparency for customer approval.
- Returns pricing details with fetch-price=true parameter.

**Best practice:** Always use `PREVIEW_SWITCH` for customer-facing scenarios to avoid billing disputes.

### What is the complete workflow for a mid-term upgrade?

**Answer:** Follow these steps:

- **Step 1:** Verify switch path.  
GET `/v3/customers/{customerId}/subscriptions/{subscriptionId}/offer-switch-paths`
- **Step 2:** Check current subscription.  
GET `/v3/customers/{customerId}/subscriptions/{subscriptionId}`  
*Verify:* status, currentQuantity, and offer ID.
- **Step 3:** Create preview (recommended).  

```json
POST /v3/customers/{customerId}/orders?fetch-price=true
{
    "orderType": "PREVIEW_SWITCH",
    "currencyCode": "USD",
    "lineItems": [{
        "offerId": "{target-offer-id}",
        "quantity": 10,
        "extLineItemNumber": 1
    }],
    "cancellingItems": [{
        "subscriptionId": "{subscription-id}",
        "quantity": 10,
        "extLineItemNumber": 1,
        "referenceLineItemNumber": 1
    }],
    "externalReferenceId": "preview-12345"
}
```

- **Step 4:** Submit the switch order.

```json
 POST /v3/customers/{customerId}/orders
{
    "orderType": "SWITCH",
    "currencyCode": "USD",
    "lineItems": [{
        "offerId": "{target-offer-id}",
        "quantity": 10,
        "extLineItemNumber": 1
    }],
    "cancellingItems": [{
        "subscriptionId": "{subscription-id}",
        "quantity": 10,
        "extLineItemNumber": 1,
        "referenceLineItemNumber": 1
    }],
    "externalReferenceId": "switch-12345"
}
```

- **Step 5:** Monitor order status.  
GET `/v3/customers/{customerId}/orders/{orderId}`  
Poll every 30 seconds until status = "1000" (Success).  
Typical processing time: 2-8 minutes.

- **Step 6:** Retrieve new subscription ID.

  ```json
  {
    "orderId": "order-123",
    "status": "1000",
    "lineItems": [{
        "subscriptionId": "new-sub-456",  // ← New subscription
        "offerId": "target-offer-id",
        "quantity": 10
    }]
  }
  ```

- **Step 7:** Verify subscription changes.  
GET `/v3/customers/{customerId}/subscriptions/{original-subscription-id}`  
GET `/v3/customers/{customerId}/subscriptions/{new-subscription-id}`  

Verify whether the quantities updated correctly.

### How long does a switch order take to process?

**Answer:**

| Type           | Typical time  | Max time | Response | Processing                           |
|----------------|--------------|----------|----------|--------------------------------------|
| PREVIEW_SWITCH | Instant      | 1-2s     | 200      | Synchronous                          |
| SWITCH         | 2-8 minutes  | 10 min   | 202      | Poll for 1000                        |
| REVERT_SWITCH  | 2-8 minutes  | 10 min   | 202      | Poll for 1000                        |

**Status codes:**

- 1000: Success (order completed)
- 1002: Processing (order in progress)

**Best practice:** Poll every 30 seconds for up to 10 minutes. If status remains 1002 after 10 minutes, escalate to support.

### Can I place multiple switch orders for the same subscription simultaneously?

**Answer:** No. Only one switch order can be processed for a subscription at a time.

**Behavior:**

- If you submit a second switch while the first is processing (status 1002), the second will fail.
- Wait for the first switch to complete (status 1000) before submitting another.
- For concurrent switches of different subscriptions, each subscription can have one active switch.

**Example scenario:**

Subscription A (100 licenses):

- Switch 30 licenses to Product B → Wait for completion.
- Switch 20 more licenses to Product C → OK after step 1 completes.

## Quantities and pricing

### What quantity constraints apply to switch orders?

**Answer:**

**Key rules:**

- Switch quantity ≤ Current subscription quantity  
  - Cannot switch more licenses than you have  
  - Error 2151: "Quantity exceeded"
- lineItems.quantity = cancellingItems.quantity
  - Must be equal (1:1 switch)  
  - Error 2149: "Quantity mismatch"
- Minimum quantity: 1  
  - Cannot switch 0 licenses
- No maximum limit  
  - Can switch entire subscription (full switch)

**Examples:**

Valid: Switching 10 licenses:

```json
"lineItems": [{"quantity": 10}],
"cancellingItems": [{"quantity": 10}]
```

Invalid: Quantities don't match:

```json
"lineItems": [{"quantity": 10}],
"cancellingItems": [{"quantity": 5}]  // Error 2149
```

Invalid: Exceeds subscription.

// Current subscription has 8 licenses.

```json
"lineItems": [{"quantity": 10}],
"cancellingItems": [{"quantity": 10}]  // Error 2151
```

### How is pricing calculated for mid-term upgrades?

**Answer:** Pricing is prorated based on the remaining term of the original subscription.

- **Formula:**

     Prorated price = (New product price at current discount level - Original product price at current discount level) × (Days remaining / Total days in term).

- **Example:**

  - Original: Acrobat Pro @ $180/year (100 days remaining out of 365).
  - Target: Acrobat Sign @ $300/year.
  - Prorated charge: ($300 - $180) × (100/365) = $32.88.

- **Key points:**

  - If new product is more expensive, the customer pays the prorated difference.
  - Pricing includes any applicable discounts.
  - Use `fetch-price=true` to see detailed pricing breakdown.

### How do I get detailed pricing information before executing a switch?

**Answer:** Use the fetch-price=true query parameter with PREVIEW_SWITCH:

```json
POST /v3/customers/{customerId}/orders?fetch-price=true
{
    "orderType": "PREVIEW_SWITCH",
    ...
}
```

**Response includes:**

```json
{
    "lineItems": [{
        "pricing": {
            "partnerPrice": 300.00,
            "discountedPartnerPrice": 270.00,
            "netPartnerPrice": 270.00,
            "lineItemPartnerPrice": 73.97
        },
        "proratedDays": 100
    }],
    "cancellingItems": [{
        "pricing": {
            "partnerPrice": 180.00,
            "discountedPartnerPrice": 162.00,
            "netPartnerPrice": 162.00,
            "lineItemPartnerPrice": 44.38
        }
    }],
    "pricingSummary": [{
        "totalLineItemPartnerPrice": 29.59,  // Net charge
        "currencyCode": "USD"
    }]
}
```

**Fields explained:**

- partnerPrice: Base price per unit.
- discountedPartnerPrice: After discount applied.
- netPartnerPrice: Final price per unit.
- lineItemPartnerPrice: Total for this line item (quantity × netPartnerPrice × proration).
- totalLineItemPartnerPrice: Net amount to charge (new - cancelled).
- proratedDays: Number of days remaining in term.

### Why am I getting "Quantity exceeded" (Error 2151) when I have enough licenses?

**Answer:** Common causes:

- Previous partial switch reduced quantity
  - Original: 100 licenses
  - Previous switch: 30 licenses → Now only 70 remain
  - Trying to switch 80 → Error 2151
- Concurrent operations
  - Another switch or return order is processing
  - Temporarily locks quantity
- Subscription status changed
  - Subscription partially cancelled
  - Some licenses returned

**Resolution:**

**Step 1:** Check current subscription quantity.  

```json
GET /v3/customers/{customerId}/subscriptions/{subscriptionId}

# Response shows actual available quantity
{
    "subscriptionId": "sub-123",
    "currentQuantity": 70,  // ← Actual available
    "offerId": "..."
}
```

**Step 2:** Adjust switch quantity to ≤ currentQuantity.

## Revert switch

### What is a revert switch and when should I use it?

**Answer:** A revert switch reverses a previous mid-term upgrade and restores the subscription to its original state.

**What happens:**

- The upgraded subscription is canceled.
- Original subscription quantity is restored.
- You must include the original switch order ID in the request.

**Time limitation:**

- Revert switch must be executed within 14 days of the original switch order date.
- After 14 days, revert is not allowed.
- Monitor switch orders closely if revert might be required.

### Can I perform a partial revert switch?

**Answer:** No. When you perform a revert switch, you must revert the exact quantity as the original switch.

**Example:**

**Original switch:**

```json
// Switched 30 licenses from Acrobat Pro to Acrobat Sign
{
    "orderType": "SWITCH",
    "lineItems": [{"quantity": 30}],
    "cancellingItems": [{"quantity": 30}]
}
```

**Valid revert:**

```json
// Must revert all 30 licenses
{
    "orderType": "REVERT_SWITCH",
    "referenceOrderId": "original-switch-order-id",
    "lineItems": [{"quantity": 30}],  // Same as original
    "cancellingItems": [{"quantity": 30}]  // Same as original
}
```

**Invalid revert:**

```json
// Cannot revert only 15 licenses
{
    "orderType": "REVERT_SWITCH",
    "referenceOrderId": "original-switch-order-id",
    "lineItems": [{"quantity": 15}],  // ← Error!
    "cancellingItems": [{"quantity": 15}]
}
```

**Workaround for partial revert:** If you need to keep some licenses on the new product:

1. Revert the entire switch (all 30 licenses).
2. Place a new switch order for the desired quantity (e.g., 15 licenses).

### How do I perform a revert switch?

**Answer:** Follow this workflow:

- **Step 1:** Get original switch order details.  
GET `/v3/customers/{customerId}/orders/{original-switch-order-id}`

- Extract:

  - Original offer ID (from cancellingItems).
  - New offer ID (from lineItems).
  - Switch subscription ID (from lineItems.subscriptionId).
  - Quantity switched.

- **Step 2:** Create preview revert (recommended).  

```json
POST /v3/customers/{customerId}/orders?fetch-price=true
{
    "orderType": "PREVIEW_REVERT_SWITCH",
    "currencyCode": "USD",
    "referenceOrderId": "{original-switch-order-id}",
    "lineItems": [{
        "offerId": "{original-offer-id}",  // Back to original
        "quantity": 30,  // Same as original switch
        "extLineItemNumber": 1
    }],
    "cancellingItems": [{
        "subscriptionId": "{switch-subscription-id}",  // New subscription to cancel
        "quantity": 30,  // Same as original switch
        "extLineItemNumber": 1,
        "referenceLineItemNumber": 1
    }],
    "externalReferenceId": "preview-revert-12345"
}
```

- **Step 3:** Execute revert.  

```json
POST /v3/customers/{customerId}/orders
{
    "orderType": "REVERT_SWITCH",
    "currencyCode": "USD",
    "referenceOrderId": "{original-switch-order-id}",
    "lineItems": [{
        "offerId": "{original-offer-id}",
        "quantity": 30,
        "extLineItemNumber": 1
    }],
    "cancellingItems": [{
        "subscriptionId": "{switch-subscription-id}",
        "quantity": 30,
        "extLineItemNumber": 1,
        "referenceLineItemNumber": 1
    }],
    "externalReferenceId": "revert-12345"
}
```

- **Step 4:** Verify revert success.  
GET `/v3/customers/{customerId}/orders/{revert-order-id}`  
Wait for status "1000".

- **Step 5:** Confirm subscription quantities.  
GET `/v3/customers/{customerId}/subscriptions/{original-subscription-id}`  
Verify `currentQuantity` restored to pre-switch value.

### Can I revert a switch multiple times?

**Answer:** No. Each switch can only be reverted once.

**Scenario:**

1. Original: Acrobat Pro (100 licenses).
2. Switch: 30 to Acrobat Sign → Creates sub-B.
3. Revert: Back to Acrobat Pro → Cancels sub-B, restores 30 to original.
4. Attempt second revert: Fails (sub-B already cancelled).

**If you need to switch again:** After reverting, you can create a new switch order (not a revert):

1. Revert completed → Back to Acrobat Pro (100 licenses).
2. New switch: 30 to Acrobat Sign → Creates sub-C (different subscription).

### What happens if I try to revert a switch that's already been reverted?

**Answer:** The order will fail with an error.

- **Error:** Subscription not found or already cancelled.
- **Reason:** The switch subscription was already cancelled by the first revert, so there's nothing to revert again.

- **Prevention:**

  - Track which switches have been reverted.
  - Check subscription status before attempting revert.
  - Use order history to verify revert hasn't already occurred.

### Can I return licenses from a reverted subscription?

**Answer:** No. After a revert switch, you cannot place a return order on the switch subscription (it's already cancelled).

- **Valid return scenarios:**

  - Before revert: Return licenses from the switch subscription.
  - After revert: Return licenses from the original subscription.
  - After revert: Return licenses from the (cancelled) switch subscription.

- **Example:**

  1. Switch: Acrobat Pro → Acrobat Sign (30 licenses).
  2. Revert: Back to Acrobat Pro.
  3. Return 10 Acrobat Sign licenses: Fails (subscription cancelled by revert).
  4. Return 10 Acrobat Pro licenses: Works (original subscription active).

### Can I perform concurrent revert switches on the same order?

**Answer:** No. Only one revert switch will succeed.

- **Scenario:**

  - Thread 1: Submit revert switch for order-123.
  - Thread 2: Submit revert switch for order-123 (simultaneously).

- **Result:**

  - Thread 1: Success (status 1000).
  - Thread 2: Fails (subscription already cancelled).

- **Prevention:**

  - Implement locking mechanism in your application.
  - Check order status before submitting revert.
  - Handle concurrent request errors gracefully.

## User reassignment

### Can users be automatically reassigned during a mid-term upgrade?

**Answer:** Yes, but only for Teams-to-Teams upgrades.

- **Supported:**

  - Acrobat Pro Teams → Acrobat Sign Teams.
  - Creative Cloud Teams → Creative Cloud Pro Teams.

- **Not supported:**

  - Teams → Enterprise.
  - Enterprise → Enterprise.
  - Single App → All Apps.

- **How to enable:** Add the reassign-users=true query parameter:

  ```json
  POST /v3/customers/{customerId}/orders?reassign-users=true
  {
    "orderType": "SWITCH",
    ...
  }
  ```

- **What happens:**

  - Users assigned to original subscription are automatically reassigned. Users will be reassigned in LIFO manner with undelegated licenses given preference.
  - User access transitions seamlessly.
  - No manual reassignment required.
  - User data and settings preserved.

### What happens to user assignments if I don't use reassign-users=true?

**Answer:** Users will get removed from the original license.

- **Impact:**

  - Original subscription quantity reduced.
  - Users may lose access if original subscription quantity < assigned users.
  - Manual reassignment required to new subscription.

- **Example:**

  - Original: 50 Acrobat Pro licenses, 50 users assigned.
  - Switch: 30 licenses to Acrobat Sign (without reassign-users=true).

- **Result:**

  - Acrobat Pro: 20 licenses, 50 users still assigned → 30 users lose access!
  - Acrobat Sign: 30 licenses, 0 users assigned → Manual assignment needed.

- **Best practice:** Always use reassign-users=true for Teams-to-Teams switches to avoid user access issues.

## Special customer types

### Can government (LGA) customers perform mid-term upgrades?

**Answer:** Yes, but with specific requirements:

- **Requirements:**

  - Discount levels: Often require specific discount levels (e.g., 06).

- **Example:**

   GET `/v3/offer-switch-paths?market-segment=GOV&country=US&language=MULT`

### Can global customers perform mid-term upgrades?

**Answer:** Yes, with significant limitations on cross-country switches.

- **Allowed:**

  - Within same deployment (same country).

- **Not allowed:**

  - Home country → Deployment country.
  - Deployment country → Home country.
  - Between different deployment countries.

- **Error:** Error 2150 or 2154 with details like "Invalid deploymentId" or "Invalid currency."

## API implementation

### What are the required fields for a switch order?

**Answer:**

- **Required fields:**

```json
{
    "orderType": "SWITCH",  // Required: SWITCH, PREVIEW_SWITCH, REVERT_SWITCH
    "currencyCode": "USD",  // Required: ISO currency code
    "lineItems": [{  // Required: Array with exactly 1 item
        "offerId": "65325063CA01A12",  // Required: Target offer ID
        "quantity": 10,  // Required: Number of licenses to switch
        "extLineItemNumber": 1  // Required: Must be 1
    }],
    "cancellingItems": [{  // Required: Array with exactly 1 item
        "subscriptionId": "sub-123",  // Required: Subscription to switch from
        "quantity": 10,  // Required: Must match lineItems.quantity
        "extLineItemNumber": 1,  // Required: Must be 1
        "referenceLineItemNumber": 1  // Required: Must match lineItems.extLineItemNumber
    }],
    "externalReferenceId": "switch-12345"  // Required: Unique identifier
}
```

- **Optional fields:**
  - discountCode: For applying specific discounts.
  - referenceOrderId: Required for REVERT_SWITCH only.

### Why do I get "Line item and cancelling line item mismatch" (Error 2153)?

**Answer:** This error occurs when extLineItemNumber doesn't match referenceLineItemNumber.

- **Cause:**

  ```json
  // Invalid
  {
    "lineItems": [{
        "extLineItemNumber": 1
    }],
    "cancellingItems": [{
        "referenceLineItemNumber": 2  // ← Must be 1
    }]
  }
  ```

- **Fix:**

   ```json
   // Valid
  {
    "lineItems": [{
        "extLineItemNumber": 1
    }],
    "cancellingItems": [{
        "referenceLineItemNumber": 1  // ← Matches
    }]
  }
    ```

### Can I include multiple line items in a switch order?

**Answer:** No. Switch orders support only ONE line item.

- **Error 2152:** "Multiple line items not supported."

- **Invalid:**

  ```json
  {
    "orderType": "SWITCH",
    "lineItems": [
        {"offerId": "offer-A", "quantity": 10},
        {"offerId": "offer-B", "quantity": 5}  // ← Error!
    ]
  }
  ```

- **Workaround:** To switch to multiple products, create separate switch orders:

  1. Switch order 1: 10 licenses to Product A.
  2. Switch order 2: 5 licenses to Product B.

### What is the difference between extLineItemNumber and referenceLineItemNumber?

**Answer:**

| Field                  | In    | Description                               | Value for switch orders |
|------------------------|-------|-------------------------------------------|------------------------|
| extLineItemNumber      | lineItems     | Identifies the line item in the current order     | Always 1               |
| extLineItemNumber      | cancellingItems        | Identifies the cancelling item               | Always 1               |
| referenceLineItemNumber| cancellingItems        | References which line item is being cancelled | Must match lineItems.extLineItemNumber |

**For switch orders:** All three must be 1 (since only one line item is allowed).

### What query parameters are available for switch orders?

**Answer:**

| Parameter         | For            | Purpose                                       | Value       |
|-------------------|----------------|-----------------------------------------------|-------------|
| fetch-price       | PREVIEW_SWITCH \<br /\>PREVIEW_REVERT_SWITCH  | Get detailed pricing information              | true, false |
| reassign-users    | SWITCH         | Automatically reassign users (Teams only)     | true, false |

**Examples:**

```shell
# Get pricing details
POST /v3/customers/{customerId}/orders?fetch-price=true

# Auto-reassign users
POST /v3/customers/{customerId}/orders?reassign-users=true

# Both parameters
POST /v3/customers/{customerId}/orders?fetch-price=true&reassign-users=true
```

## Error handling

### What are the common error codes for switch orders?

**Answer:**

| Code  | Error     | Cause/resolution                                   |
|-------|-----------|----------------------------------------------------|
| 2149  | Quantity mismatch | lineItems.quantity ≠ cancellingItems.quantity. \<br /\> Ensure quantities match exactly. |
| 2150  | Switch path validity check failed. | Invalid switch path. \<br /\> Verify path exists with GET switch-paths API. |
| 2151  | Quantity exceeded. | Switch quantity > subscription quantity. \<br /\>Check subscription currentQuantity. |
| 2152  | Multiple line items not supported. | More than 1 line item. \<br /\> Use single line item only. |
| 2153  | Line item and cancelling line item mismatch. | extLineItemNumber ≠ referenceLineItemNumber. \<br /\> Set both to 1. |
| 2154 | Upgrade not supported. | High Growth Offer restrictions, net refund scenario, or restricted product. \<br /\> Check product eligibility and pricing. |
| 3115 | Invalid subscription. | Subscription expired/cancelled. \<br /\> Verify subscription status. |

**General troubleshooting:**

- Check error code in response.
- Review error message and additionalDetails.
- Verify request payload against requirements.
- Use PREVIEW_SWITCH to validate before SWITCH.

## Miscellaneous

### What are High Growth Offers and how do they affect switches?

**Answer:** High Growth Offers are special promotional subscriptions with restricted switch eligibility.

**Restrictions:**

- High Growth Offer subscriptions often cannot be switched FROM (only TO).
- Limited upgrade paths available.
- May require specific approval or conditions.
- Error code 2154 indicates High Growth Offer restrictions.

**Example:**

- Acrobat Pro (High Growth Offer) → Acrobat Sign: Not allowed.
- Acrobat Pro (standard) → Acrobat Pro (High Growth Offer): May be allowed.

### Can I switch products across different regions?

**Answer:** Very limited. For global customers:

|                | Allowed | Not allowed |
|----------------|---------|-------------|
| Within same deployment | Yes | Same country, same deployment |
| Home country to deployment country | No | Error 2150 or 2154 |
| Deployment country to home country | No | Error 2150 or 2154 |
| Between different deployments | No | Error 2150 or 2154 |

**Error message:** "Switch path validity check failed" or "Upgrade not supported" with additional details like "Invalid deploymentId" or "Invalid currency."
