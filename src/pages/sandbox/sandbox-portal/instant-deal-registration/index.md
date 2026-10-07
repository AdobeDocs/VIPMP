# Test Adobe Instant Deal Registration in Sandbox

The following steps provide the test workflow:

1. Discover Instant Deal Registration opportunities by using the `GET /v3/flex-discounts?categories=DEAL_REGISTRATION` endpoint.
2. Based on the response, submit a Preview Order or Create Order request for the applicable product. No need to pass any code or ID for applying the deal registration.
3. From the response of Get Order API, verify whether `isDealRegistered=true` to to confirm that Instant Deal Registration was successfully applied..
4. When `fetch-price=true`, you will also see parameters such as `earnedDealRegPerUnit` and `earnedDealRegAmount` for qualifying line items and `totalEarnedDealRegAmount` in the pricing summary.

Interpret `isDealRegistered` as follows:

- `true`: Instant Deal Registration was auto-injected, all conditions passed, and the deal registration amount was applied.
- `false`: Instant Deal Registration was auto-injected, but qualification failed. Deal registration amount fields are omitted.
- Absent: Instant Deal Registration was not injected or evaluated for the line item.

## Test scenario

Use this scenario for customers who have Acrobat Standard or Acrobat Pro and purchase Acrobat Studio. It supports the Commercial (COM), Government (GOV), and Education (EDU) market segments.

The customer must renew at least 90% of their prior-term Acrobat-family licenses. Eligible licenses include Acrobat Standard, Acrobat Pro, and Acrobat Studio. Licenses that have already been renewed, along with licenses included in the current order, count toward the renewal requirement.

Customers with Acrobat Standard Team or Acrobat Pro Team subscriptions can purchase Acrobat Studio Team or Acrobat Studio Enterprise. Customers with only Acrobat Standard Enterprise or Acrobat Pro Enterprise subscriptions can purchase Acrobat Studio Enterprise, but not Acrobat Studio Team.

If the customer already has Acrobat Studio licenses, the total number of renewed Acrobat Studio licenses must exceed the prior-term Acrobat Studio license count. Include licenses that have already been renewed and licenses in the current order. Count Acrobat Studio Team and Acrobat Studio Enterprise licenses together when calculating the total.

Use Discovery to determine the applicable Acrobat Studio offer and Instant Deal Registration rate.

Renewal orders qualify during the customer's renewal period. Add-licenses orders qualify after the anniversary date, provided the order is placed within the customer's renewal period.

Do not submit an Instant Deal Registration code. The service automatically applies the eligible promotion. Check the order response to verify the promotion result.

### Case matrix

For the first two cases, use a renewal order within the customer’s renewal period. The customer has no Acrobat Studio licenses. Set `fetch-price=true` to check Instant Deal Registration amounts.

| Test case | Customer setup and order | Expected response |
|---|---|---|
| Instant Deal Registration applied | Customer holds 100 Acrobat Pro licenses. Renew 90 licenses as Acrobat Studio. Retention is 90%. | `isDealRegistered: true`. Instant Deal Registration amount fields contain the deal registration amount calculated at the applicable rate. |
| Retention below 90% | Customer holds 100 Acrobat Pro licenses. Renew 85 licenses as Acrobat Studio. Retention is 85%. | The promotion is added automatically, but qualification fails. `isDealRegistered: false`. Instant Deal Registration amount fields are omitted. |
| Acrobat renewal without an Acrobat Studio purchase | Customer holds 100 Acrobat Pro licenses. Renew all 100 licenses as Acrobat Pro. Do not include Acrobat Studio in the order. | `isDealRegistered` is absent from the Acrobat Pro line. Instant Deal Registration amount fields are omitted. |


## Validate responses

- Validate the offer and the details returned by discovery across COM, GOV, and EDU. Do not infer the percentage from the segment.
- `isDealRegistered` is `true` when qualification passed, `false` when qualification failed, and absent when Instant Deal Registration was not injected or evaluated.
- For an applied line item, `earnedDealRegAmount` equals `earnedDealRegPerUnit` x `quantity`.
- `totalEarnedDealRegAmount` equals the sum of the qualifying line item amounts.
- The deal registration amount does not change `discountedPartnerPrice`, `lineItemPartnerPrice`, or `totalLineItemPartnerPrice`.
- On a return, the deal registration amount applied to the original transaction is reversed; returned amount fields are not negative.

For production API details, see [Use Adobe Instant Deal Registration APIs](../../../docs/instant-deal-registration/apis.md).
