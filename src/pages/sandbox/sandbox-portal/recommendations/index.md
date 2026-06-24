# Product Recommendations

The Recommendations API empowers VIP Marketplace partners to provide intelligent, personalized, and context-aware product suggestions, enhancing the customer experience through upsell, cross-sell, and add-on opportunities. For more details, see [Manage recommendations using APIs](../../../docs/recommendations/apis.md) section in the Adobe Commerce Partner API documentation.

## Testing Recommendations in Sandbox

The standalone Recommendations API and certain existing APIs can fetch recommendations through query parameters, and these recommendations are now available in the Sandbox environment.

No updates have been made to the Sandbox UI, as recommendations will be delivered exclusively through the APIs.

Specific, hardcoded recommendations have been configured to facilitate integration and functional testing of the recommendations feature. These predefined rules are as follows:

**Note:** In production, recommendations will be context-aware and based on the customer's entitlements (products owned), and products that are already owned are not recommended.

| APIs                    | Context         | Rules for showing recommendations in sandbox                                                                                                                                                                                                 | Comments                                                                 |
|-------------------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Recommendations API (new) | GENERIC         | **Upsell Recommendation**\<br /\>1. CC All Apps - Pro for teams\<br /\>\<br /\>**cross-sell Recommendation**\<br /\>1. Adobe Express for teams\<br /\>\<br /\>**AddOn Recommendation**\<br /\>1. AI Assistant for Acrobat for enterprise                                  | These products will be recommended regardless of the customer and their current entitlements. |
| Recommendations API (new) | ORDER_PREVIEW   | **Upsell Recommendation**\<br /\>1. CC All Apps - Pro for teams while previewing Creative Cloud for teams All Apps\<br /\>2. Creative Cloud All Apps - Edition 4 for enterprise, while previewing Creative Cloud All Apps Enterprise\<br /\>\<br /\>**cross-sell Recommendation**\<br /\>1. Adobe Express for teams\<br /\>\<br /\>**AddOn Recommendation**\<br /\>1. AI Assistant for Acrobat for enterprise while previewing Acrobat Pro/Std for enterprise\<br /\>2. AI Assistant for Acrobat for the team while previewing Acrobat Pro/Std for the team | Recommendations are subject to the lineItem(s) that are previewed. |
| Create Order API        | Order Preview   | **Order Type: PREVIEW**\<br /\>\<br /\>**Upsell Recommendation**\<br /\>1. CC All Apps - Pro for teams while previewing Creative Cloud for teams All Apps\<br /\>2. Creative Cloud All Apps - Edition 4 for enterprise while previewing Creative Cloud All Apps Enterprise\<br /\>\<br /\>**cross-sell Recommendation**\<br /\>1. Adobe Express for teams\<br /\>\<br /\>**AddOn Recommendation**\<br /\>1. AI Assistant for Acrobat for enterprise while previewing Acrobat Pro/Std for enterprise\<br /\>2. AI Assistant for team while previewing Acrobat Pro/Std for team | Same recommendations as exposed for the standalone Recommendations API in the ORDER_PREVIEW context.\<br /\>\<br /\>Recommendations are subject to the lineItem(s) that are previewed. |
| Create Order API        | Renewal Preview | **Order Type: PREVIEW_RENEWAL without lineItems**\<br /\>\<br /\>**Upsell Recommendation**\<br /\>1. CC All Apps - Pro for teams\<br /\>\<br /\>**cross-sell Recommendation**\<br /\>1. Adobe Express for teams\<br /\>\<br /\>**AddOn Recommendation**\<br /\>1. AI Assistant for Acrobat for enterprise | Same recommendations as exposed for the standalone Recommendations API in the GENERIC context |
| Create Order API        | Renewal Preview | **Order Type: PREVIEW_RENEWAL with lineItems**\<br /\>\<br /\>**Upsell Recommendation**\<br /\>1. CC All Apps - Pro for teams while previewing Creative Cloud for teams All Apps\<br /\>2. Creative Cloud All Apps - Edition 4 for enterprise while previewing Creative Cloud All Apps Enterprise\<br /\>\<br /\>**cross-sell Recommendation**\<br /\>1. Adobe Express for teams\<br /\>\<br /\>**AddOn Recommendation**\<br /\>1. AI Assistant for Acrobat for enterprise while previewing Acrobat Pro/Std for enterprise\<br /\>2. AI Assistant for team while previewing Acrobat Pro/Std for team. | Same recommendations as exposed for the standalone Recommendations API in the ORDER_PREVIEW context.\<br /\>\<br /\>Recommendations are subject to the lineItem(s) that are previewed. |

## Testing Propensity Signals in Sandbox

In addition to product recommendations, the Sandbox returns agent-driven propensity signals under the `propensity` object for the [Fetch Recommendations API](../../../docs/recommendations/apis.md#fetch-recommendations). You can request these signals using the `includePropensity: true` request parameter.

Each signal type (`churn`, `seatExpansion`) is returned as an array of prediction objects. Every prediction shares a common shape — `category`, `probability`, `refreshDate`, `reasons`, and `additionalDetails`. The `category` field indicates the scope of the prediction; the Sandbox seeds currently use `"allOfferings"`.

**Note:**

- A set of 13 predefined test seeds is configured in the Sandbox so you can exercise every combination of rating level and empty-array behavior during integration testing. Each seed returns a fixed response, so results are deterministic and repeatable.
- Each customer is assigned one of the seeded responses randomly for testing. Once assigned, the seeded response does not change.

### Available test seeds

Each model produces a percentile score (0–100) that is mapped to a `HIGH` / `MEDIUM` / `LOW` rating

| Index | Scenario | Churn | Seat Expansion |
|---|---|---|---|
| 1 | Both HIGH | **HIGH** | **HIGH** |
| 2 | Churn HIGH + Expansion LOW | **HIGH** | LOW |
| 3 | Churn LOW + Expansion HIGH | LOW | **HIGH** |
| 4 | Both MEDIUM | **MEDIUM** | **MEDIUM** |
| 5 | Both LOW | LOW | LOW |
| 6 | Churn only HIGH | **HIGH** | — (empty array) |
| 7 | Expansion only HIGH | — (empty array) | **HIGH** |
| 8 | Churn MEDIUM + Expansion HIGH | **MEDIUM** | **HIGH** |
| 9 | Churn HIGH + Expansion MEDIUM | **HIGH** | **MEDIUM** |
| 10 | Both HIGH, large enterprise | **HIGH** | **HIGH** |
| 11 | Churn only MEDIUM | **MEDIUM** | — (empty array) |
| 12 | Expansion only LOW | — (empty array) | LOW |
| 13 | Both HIGH, Acrobat + CC activity | **HIGH** | **HIGH** |

### Empty and unavailable signals

| Scenario | Response |
|---|---|
| No churn data for the customer | `"churn": []` |
| No seat expansion data for the customer | `"seatExpansion": []` |
| Neither signal available | `"propensity": { "churn": [], "seatExpansion": [] }` |

**Note:** `predictedAddonSize` inside `additionalDetails` on a `seatExpansion` predictio is serializedonly  when a prediction is available. When no prediction exists, the key is omitted entirely from `additionalDetails`.  Treat a missing key as "no prediction available."

### Seed-by-seed API output

The following examples show the `propensity` node of the response for each seed.

#### Index 1 — Both HIGH

Churn **HIGH** · Seat Expansion **HIGH** (`predictedAddonSize` = 8)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "2" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "60" },
        { "reasonCode": "contract_acom_domain_visit_flag_in_last_30_days", "description": "Whether anyone from the customer's organisation has visited Adobe.com in the past 30 days.", "value": "0" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "18" },
        { "reasonCode": "team_num_assigned_seats_at_snapshot", "description": "Number of seats currently assigned to end users.", "value": "13" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.97" },
        { "reasonCode": "team_provisioned_quantity_rate_of_change_at_last_30_days", "description": "Rate of change in total provisioned seats over the past 30 days.", "value": "0.15" }
      ],
      "additionalDetails": { "predictedAddonSize": 8 }
    }
  ]
}
```

#### Index 2 — Churn HIGH + Expansion LOW

Churn **HIGH** · Seat Expansion **LOW** (`predictedAddonSize` = 2)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "2" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "90" },
        { "reasonCode": "contract_acom_domain_visit_flag_in_last_30_days", "description": "Whether anyone from the customer's organisation has visited Adobe.com in the past 30 days.", "value": "0" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "LOW",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.23" }
      ],
      "additionalDetails": { "predictedAddonSize": 2 }
    }
  ]
}
```

#### Index 3 — Churn LOW + Expansion HIGH

Churn **LOW** · Seat Expansion **HIGH** (`predictedAddonSize` = 6)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "LOW",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "11" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "3" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "22" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.92" },
        { "reasonCode": "team_provisioned_quantity_rate_of_change_at_last_30_days", "description": "Rate of change in total provisioned seats over the past 30 days.", "value": "0.18" }
      ],
      "additionalDetails": { "predictedAddonSize": 6 }
    }
  ]
}
```

#### Index 4 — Both MEDIUM

Churn **MEDIUM** · Seat Expansion **MEDIUM** (`predictedAddonSize` = 3)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "MEDIUM",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "6" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "30" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "MEDIUM",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "6" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.80" }
      ],
      "additionalDetails": { "predictedAddonSize": 3 }
    }
  ]
}
```

#### Index 5 — Both LOW

Churn **LOW** · Seat Expansion **LOW** (`predictedAddonSize` = 1)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "LOW",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "11" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "2" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "LOW",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.18" }
      ],
      "additionalDetails": { "predictedAddonSize": 1 }
    }
  ]
}
```

#### Index 6 — Churn only HIGH

Churn **HIGH** · Seat Expansion: no data → empty array

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "1" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "75" },
        { "reasonCode": "contract_acom_domain_visit_flag_in_last_30_days", "description": "Whether anyone from the customer's organisation has visited Adobe.com in the past 30 days.", "value": "0" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": []
}
```

#### Index 7 — Expansion only HIGH

Churn: no data → empty array · Seat Expansion **HIGH** (`predictedAddonSize` = 10)

```json
"propensity": {
  "churn": [],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "28" },
        { "reasonCode": "team_num_assigned_seats_at_snapshot", "description": "Number of seats currently assigned to end users.", "value": "50" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "1.00" },
        { "reasonCode": "team_provisioned_quantity_rate_of_change_at_last_30_days", "description": "Rate of change in total provisioned seats over the past 30 days.", "value": "0.20" }
      ],
      "additionalDetails": { "predictedAddonSize": 10 }
    }
  ]
}
```

#### Index 8 — Churn MEDIUM + Expansion HIGH

Churn **MEDIUM** · Seat Expansion **HIGH** (`predictedAddonSize` = 7)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "MEDIUM",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "6" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "20" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "7" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.93" },
        { "reasonCode": "team_provisioned_quantity_rate_of_change_at_last_30_days", "description": "Rate of change in total provisioned seats over the past 30 days.", "value": "0.10" }
      ],
      "additionalDetails": { "predictedAddonSize": 7 }
    }
  ]
}
```

#### Index 9 — Churn HIGH + Expansion MEDIUM

Churn **HIGH** · Seat Expansion **MEDIUM** (`predictedAddonSize` = 3)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "3" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "50" },
        { "reasonCode": "contract_acom_domain_visit_flag_in_last_30_days", "description": "Whether anyone from the customer's organisation has visited Adobe.com in the past 30 days.", "value": "0" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "MEDIUM",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "4" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.82" }
      ],
      "additionalDetails": { "predictedAddonSize": 3 }
    }
  ]
}
```

#### Index 10 — Both HIGH, large enterprise

Churn **HIGH** · Seat Expansion **HIGH** (`predictedAddonSize` = 15)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "2" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "45" },
        { "reasonCode": "contract_acom_domain_visit_flag_in_last_30_days", "description": "Whether anyone from the customer's organisation has visited Adobe.com in the past 30 days.", "value": "0" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "35" },
        { "reasonCode": "team_num_assigned_seats_at_snapshot", "description": "Number of seats currently assigned to end users.", "value": "100" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.98" },
        { "reasonCode": "team_provisioned_quantity_rate_of_change_at_last_30_days", "description": "Rate of change in total provisioned seats over the past 30 days.", "value": "0.12" }
      ],
      "additionalDetails": { "predictedAddonSize": 15 }
    }
  ]
}
```

#### Index 11 — Churn only MEDIUM

Churn **MEDIUM** · Seat Expansion: no data → empty array

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "MEDIUM",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "6" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "25" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": []
}
```

#### Index 12 — Expansion only LOW

Churn: no data → empty array · Seat Expansion **LOW** (`predictedAddonSize` = 1)

```json
"propensity": {
  "churn": [],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "LOW",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.08" }
      ],
      "additionalDetails": { "predictedAddonSize": 1 }
    }
  ]
}
```

#### Index 13 — Both HIGH, Acrobat + CC activity

Churn **HIGH** · Seat Expansion **HIGH** (`predictedAddonSize` = 5)

```json
"propensity": {
  "churn": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_months_to_renew", "description": "Number of months remaining until this customer's contract is up for renewal.", "value": "4" },
        { "reasonCode": "contract_num_days_since_last_desktop_app_launch_in_last_365_days", "description": "Number of days since the customer last launched any Adobe desktop app (within the past year).", "value": "55" },
        { "reasonCode": "contract_num_distinct_desktop_apps_installed_in_last_30_days", "description": "Number of distinct Adobe desktop apps installed across customer endpoints in the past 30 days.", "value": "0" }
      ],
      "additionalDetails": {}
    }
  ],
  "seatExpansion": [
    {
      "category": "allOfferings",
      "probability": "HIGH",
      "refreshDate": "2026-05-14T00:00:00Z",
      "reasons": [
        { "reasonCode": "contract_num_Acrobat_desktop_install_events_in_last_365_days", "description": "Acrobat desktop install events on customer endpoints in the past year.", "value": "14" },
        { "reasonCode": "team_num_assigned_seats_at_snapshot", "description": "Number of seats currently assigned to end users.", "value": "35" },
        { "reasonCode": "team_deployment_ratio_at_snapshot", "description": "Share of provisioned seats that are currently assigned to end users.", "value": "0.96" }
      ],
      "additionalDetails": { "predictedAddonSize": 5 }
    }
  ]
}
```
