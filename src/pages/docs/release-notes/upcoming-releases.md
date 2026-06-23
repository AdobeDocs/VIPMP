# Upcoming releases

## Churn and seat expansion propensity are now surfaced in the Recommendations API

**Expected release:** July 2026

Partners can now see churn risk and seat expansion signals for each customer through the existing [Fetch Recommendations](../recommendations/apis.md#fetch-recommendations) (`POST /v3/recommendations`) API.

**What changed?**

Setting the request parameter `includePropensity` to `true` fetches  `churn` and `seatExpansion` arrays. Each contains a `probability` rating (`HIGH`, `MEDIUM`, or `LOW`), a `refreshDate`, and a `reasons` array with up to seven leading indicators ordered by relevance. 

Seat expansion also includes `predictedAddonSize` with the expected number of additional seats.

**Important:** An empty object `{}` for either node means propensity data is not available for that customer. Do not interpret `{}` as LOW risk or LOW expansion potential.

**Why it matters**

Partners can now identify at-risk customers and high-growth opportunities using data-driven signals, rather than relying on anecdotal indicators or waiting for account-manager outreach.

**Action required**

| Action | Details |
|---|---|
| Parse the new `propensity` object | It appear at the same level as `productRecommendations`. Handle three states: absent (feature not enabled), empty `{}` (data unavailable), and populated (data available). |
| Do not equate `{}` with LOW | An empty object means no data. Treat it as unknown, not low risk. |
| No changes needed for existing fields | `productRecommendations` and `overlayRecommendations` are unchanged. Existing integrations continue to work without modification. |

For more information, see [Propensity Intelligence](../recommendations/index.md#propensity-intelligence).
