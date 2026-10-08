# Test Adobe Instant Deal Registration in Sandbox

The following steps provide the test workflow:

1. Discover Instant Deal Registration opportunities by using the `GET /v3/flex-discounts?categories=DEAL_REGISTRATION` endpoint.
2. Based on the response, submit a Preview Order or Create Order request for the applicable product. No need to pass any code or ID for applying the deal registration.
3. From the Get Order API response, verify whether `isDealRegistered=true` to confirm that Instant Deal Registration was successfully applied.
4. When `fetch-price=true`, you will also see parameters such as `earnedDealRegPerUnit` and `earnedDealRegAmount` for qualifying line items and `totalEarnedDealRegAmount` in the pricing summary.

Interpret `isDealRegistered` as follows:

- `true`: Instant Deal Registration was auto-injected, all conditions passed, and the deal registration amount was applied.
- `false`: Instant Deal Registration was auto-injected, but qualification failed. Deal registration amount fields are omitted.
- Absent: Instant Deal Registration was not injected or evaluated for the line item.

## Test scenario

Use this scenario for customers who have Acrobat Standard or Acrobat Pro and purchase Acrobat Studio. It supports Commercial (COM), Government (GOV), and Education (EDU) market segments.

To qualify, the customer must renew at least **90% of their prior-term Acrobat-family licenses**. These licenses include Acrobat Standard, Acrobat Pro, and Acrobat Studio. Both previously renewed licenses and licenses included in the current order count toward the renewal requirement.

**Eligible purchase combinations**

- Customers with Acrobat Standard Team or Acrobat Pro Team can purchase either Acrobat Studio Team or Acrobat Studio Enterprise.
- Customers with only Acrobat Standard Enterprise or Acrobat Pro Enterprise can purchase Acrobat Studio Enterprise only.

**Additional Acrobat Studio requirement**

- If the customer already has Acrobat Studio licenses, the total Acrobat Studio licenses renewed must exceed the prior-term Acrobat Studio license count. Include both previously renewed licenses and licenses in the current order. Count Acrobat Studio Team and Acrobat Studio Enterprise licenses together.
- A customer who holds only Acrobat Studio cannot receive this Instant Deal Registration for an Acrobat Studio-to-Acrobat Studio renewal or change. The customer must also hold Acrobat Standard or Acrobat Pro.

**Discovery and other conditions**

- Use [discovery APIs](../../../docs/instant-deal-registration/apis.md) to get the applicable Acrobat Studio offer and its Instant Deal Registration amount.
- Renewal orders can qualify during the customer's renewal period. Orders for adding licenses can qualify after the anniversary date, within the customer's renewal period.
- For a three-year commitment (3YC), Instant Deal Registration fails qualification if a flexible discount code is included on the same line item. This restriction applies even if the flexible discount fails qualification. Without a flexible discount code, the service evaluates the deal against the qualification requirements.
- For a customer without a three-year commitment (non-3YC), the service can evaluate the deal with or without a flexible discount code on the same line item. A permitted combination does not guarantee an Instant Deal Registration.
- No need to send an Instant Deal Registration code. The service adds the applicable deal automatically. Read the order response to find the result.

### Case matrix

Use these scenarios to test Instant Deal Registration qualification and the order response. Set `fetch-price=true` to check Instant Deal Registration amounts.

| Test case | Customer setup and order | Expected response                                                                                                                                                                                               |
|---|---|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Credit applied | Customer holds 100 Acrobat Pro Team licenses. Renew 90 licenses as Acrobat Studio Team. Retention is 90%. | `isDealRegistered: true`. Instant Deal Registration amount fields contain the credit calculated at the applicable rate.                                                                                                            |
| Retention below 90% | Customer holds 100 Acrobat Pro Team licenses. Renew 85 licenses as Acrobat Studio Team. Retention is 85%. | The deal is added automatically, but qualification fails. `isDealRegistered: false`. Instant Deal Registration amount fields are omitted.                                                                                          |
| Acrobat renewal without an Acrobat Studio purchase | Customer holds 100 Acrobat Pro licenses. Renew all 100 licenses as Acrobat Pro. Do not include Acrobat Studio in the order. | `isDealRegistered` is absent from the Acrobat Pro line item. Instant Deal Registration amount fields are omitted.                                                                                             |
| Acrobat Studio-only customer | Customer holds 100 Acrobat Studio Team licenses and no Acrobat Standard or Acrobat Pro licenses. Renew 110 licenses as Acrobat Studio Team. | No Instant Deal Registration. An Acrobat Studio-to-Acrobat Studio renewal is not supported for an Acrobat Studio-only customer. `isDealRegistered: false`. Instant Deal Registration amount fields are omitted.                                                                 |
| Order outside the renewal period | Customer holds 100 Acrobat Pro Team licenses. Order 90 Acrobat Studio Team licenses outside the customer's renewal period. | No Instant Deal Registration. If the deal is added automatically, `isDealRegistered: false`; otherwise, the field is absent. Instant Deal Registration amount fields are omitted.                                                                     |
| Enterprise customer purchases Acrobat Studio Team | Customer holds 100 Acrobat Pro Enterprise licenses and no Acrobat Standard or Acrobat Pro Team licenses. Renew 90 licenses as Acrobat Studio Team. | Qualification fails because Acrobat Studio Team is not a permitted target. If the deal is added automatically, `isDealRegistered: false`; otherwise, the field is absent. Instant Deal Registration amount fields are omitted.             |
| Three-year commitment (3YC) with a flexible discount and a deal | Customer has a three-year commitment (3YC) and holds 100 Acrobat Pro Team licenses. Renew 90 licenses as Acrobat Studio Team with a flexible discount code on the Acrobat Studio line item. The deal is added automatically. | `isDealRegistered: false`, even if the flexible discount fails qualification. Instant Deal Registration amount fields are omitted. |
| Customer without a three-year commitment (non-3YC) with a flexible discount and a deal | Customer has no three-year commitment and holds 100 Acrobat Pro Team licenses. Renew 90 licenses as Acrobat Studio Team with a flexible discount code on the Acrobat Studio line item. The deal is added automatically. | The service evaluates the deal. `isDealRegistered: true` if qualification succeeds, or `false` with Instant Deal Registration amount fields omitted if qualification fails. |
| Three-year commitment (3YC) with a deal and no flexible discount | Customer has a three-year commitment (3YC) and holds 100 Acrobat Pro Team licenses. Renew 90 licenses as Acrobat Studio Team without a flexible discount code. The deal is added automatically. | The service evaluates the deal. `isDealRegistered: true` if qualification succeeds, or `false` with Instant Deal Registration amount fields omitted if qualification fails. |

## Validate responses

- Validate the offer and the details returned by discovery across COM, GOV, and EDU. Do not infer the percentage from the segment.
- `isDealRegistered` is `true` when qualification passed, `false` when qualification failed, and absent when Instant Deal Registration was not injected or evaluated.
- For an applied line item, `earnedDealRegAmount` equals `earnedDealRegPerUnit` x `quantity`.
- `totalEarnedDealRegAmount` equals the sum of the qualifying line item amounts.
- The deal registration amount does not change `discountedPartnerPrice`, `lineItemPartnerPrice`, or `totalLineItemPartnerPrice`.
- On a return, the deal registration amount applied to the original transaction is reversed; returned amount fields are not negative.

For production API details, see [Use Adobe Instant Deal Registration APIs](../../../docs/instant-deal-registration/apis.md).
