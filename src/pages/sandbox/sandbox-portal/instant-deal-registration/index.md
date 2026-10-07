# Test Adobe Instant Deal Registration in Sandbox

The following steps provide the step workflow:

1. Discover Instant Deal Registration opprtunities by using the `GET /v3/flex-discounts?categories=DEAL_REGISTRATION` endpoint.
2. Based on the response, place the Preview Order or Create Order for the applicable product. No need to pass any code or ID for applying the deal registration.
3. From the response of Get Order API, verify whether `isDealRegistered= true` to understand that it is successfully applied.
4. When `fetch-price=true`, you will also see parameters such as `earnedDealRegPerUnit` and `earnedDealRegAmount` for qualifying line items and `totalEarnedDealRegAmount` in the pricing summary.

Interpret `isDealRegistered` as follows:

- `true`: Instant Deal Registration was auto-injected, all conditions passed, and the deal registration amount was applied.
- `false`: Instant Deal Registration was auto-injected, but qualification failed. Deal registration amount fields are omitted.
- Absent: Instant Deal Registration was not injected or evaluated for the line item.

## Test scenario

Use this scenario for customers who have Acrobat Standard or Acrobat Pro and purchase Acrobat Sudio. It supports the Commercial (COM), Government (GOV), and Education (EDU) market segments.

The customer must renew at least 90% of their prior-term Acrobat-family licenses. Eligible licenses include Acrobat Standard, Acrobat Pro, and Acrobat Sudio. Licenses that have already been renewed, along with licenses included in the current order, count toward the renewal requirement.

Customers with Acrobat Standard Team or Acrobat Pro Team subscriptions can purchase Acrobat Sudio Team or Acrobat Sudio Enterprise. Customers with only Acrobat Standard Enterprise or Acrobat Pro Enterprise subscriptions can purchase Acrobat Sudio Enterprise, but not Acrobat Sudio Team.

If the customer already has Acrobat Sudio licenses, the total number of renewed Acrobat Sudio licenses must exceed the prior-term Acrobat Sudio license count. Include licenses that have already been renewed and licenses in the current order. Count Acrobat Sudio Team and Acrobat Sudio Enterprise licenses together when calculating the total.

Use Discovery to determine the applicable Acrobat Sudio offer and Instant Deal Registration rate.

Renewal orders qualify during the customer's renewal period. Add-licenses orders qualify after the anniversary date, provided the order is placed within the customer's renewal period.

Do not submit an Instant Deal Registration code. The service automatically applies the eligible promotion. Check the order response to verify the promotion result.

### Case matrix

For the first two cases, use a renewal order within the customer’s renewal period. The customer has no Acrobat Sudio licenses. Set `fetch-price=true` to check Instant Deal Registration amounts.

| Test case | Customer setup and order | Expected response |
|---|---|---|
| Instant Deal Registration applied | Customer holds 100 Acrobat Pro licenses. Renew 90 licenses as Acrobat Sudio. Retention is 90%. | `isDealRegistered: true`. Instant Deal Registration amount fields contain the deal registration amount calculated at the applicable rate. |
| Retention below 90% | Customer holds 100 Acrobat Pro licenses. Renew 85 licenses as Acrobat Sudio. Retention is 85%. | The promotion is added automatically, but qualification fails. `isDealRegistered: false`. Instant Deal Registration amount fields are omitted. |
| Acrobat renewal without an Acrobat Sudio purchase | Customer holds 100 Acrobat Pro licenses. Renew all 100 licenses as Acrobat Pro. Do not include Acrobat Sudio in the order. | `isDealRegistered` is absent from the Acrobat Pro line. Instant Deal Registration amount fields are omitted. |


## Validate responses

- Validate the offer and the details returned by discovery across COM, GOV, and EDU. Do not infer the percentage from the segment.
- `isDealRegistered` is `true` when qualification passed, `false` when qualification failed, and absent when Instant Deal Registration was not injected or evaluated.
- For an applied line item, `earnedDealRegAmount` equals `earnedDealRegPerUnit` x `quantity`.
- `totalEarnedDealRegAmount` equals the sum of the qualifying line item amounts.
- The deal registration amount does not change `discountedPartnerPrice`, `lineItemPartnerPrice`, or `totalLineItemPartnerPrice`.
- On a return, the deal registration amount applied to the original transaction is reversed; returned amount fields are not negative.

For production contract details, see [Use Adobe Instant Deal Registration APIs](../../../docs/instant-deal-registration/apis.md).
