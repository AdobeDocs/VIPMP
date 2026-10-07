# Adobe Instant Deal Registration

Adobe Instant Deal Registration is an automatically applied, codeless reseller credit. Unlike Flexible Discounts, partners do not provide or see an Instant Deal Registration code. Adobe automatically determines eligibility and applies the deal amount to qualifying order line items. Partners can discover opportunities through discovery APIs and view qualification outcomes through dedicated API response fields.

Instant Deal Registration:

- Applies independently of any partner-supplied flexible discount code.
- Is additive to flexible discounts and is reported separately from partner prices. It is not classified as a type of flexible discounts or recommendations.
- Does not reduce existing partner-price values.

## How it works

1. A partner can discover Instant Deal Registration opportunities using the Get Flexible Discounts API or the Recommendations API.
2. The partner previews or places an order through the existing order workflow. No registration request, code, or identifier is required.
3. Adobe evaluates eligible line items automatically at order time.
4. If a line item qualifies, Adobe applies the deal registration amount and reports it separately from the partner-price values.
5. Partners can view the outcome when they preview, create, or retrieve orders, including renewal and return orders. Upon a return, the deal amount applied to the original transaction is reversed.

Discovery does not guarantee that a deal registration amount will be applied. Adobe re-evaluates eligibility when the order is processed.

## Understand the outcome

An order response can indicate one of two outcomes for a line item:

- **Applied:** Adobe evaluated the line item, it qualified, and the deal registration amount was applied.
- **Not applied:** Instant Deal Registration was not applied to the line item.

Deal registration amount fields are returned only for qualifying line items when `fetch-price=true` is specified in the corresponding API endpoints. Deal Registration-related amounts are reported separately from partner-price fields.

## Partner experience

The Instant Deal Registration opportunities may be displayed during flexible discount, recommendations discovery, or during the order preview, the final financial outcome is determined only when the order is processed.

## What's next?

- [Use Instant Deal Registration APIs](./apis.md)
- [Test Adobe Instant Deal Registration in Sandbox](../../sandbox/sandbox-portal/instant-deal-registration/index.md)
