# Test data exports in Sandbox

Data exports let partners test an asynchronous report integration by creating a report, checking its status, and downloading a CSV file. Sandbox uses synthetic sample data instead of production propensity data.

**Note:**

- Data exports in Sandbox are API-only. There are no Sandbox Partner Portal changes for creating, viewing, or downloading reports.
- **Set Propensity Level** under **Manage Records > Customers** in the Sandbox Partner Portal affects only the Recommendations API. It does not change data export files.

## Prerequisites

- Obtain Sandbox API credentials and a valid access token. See [API collection and interaction basics](../api-collection.md#api-interaction-basics).
- Use the Sandbox Partner API base URL, `https://partnersandbox-stage.adobe.io`, with the standard authentication and [API request headers](../../docs/references/api-headers.md). See [API authentication and access](../../docs/authentication/index.md) for environment URLs.
- Confirm that data exports are enabled in Sandbox.

The planned Sandbox scenarios use the `propensity` dataset, the `churn` report type, and the `HIGH` probability filter. Do not assume that other report types or probability filters described in the general API reference are enabled in Sandbox.

## API workflow

Sandbox uses the same report endpoints, request and response structures, report states, and error codes as the production environment. Use the general [Data exports API reference](../../docs/data-exports/apis.md) for the complete contract, applying the Sandbox differences on this page.

| Operation | Method and endpoint |
|---|---|
| Create report | `POST /v3/reports` |
| Get report status | `GET /v3/reports/{report-id}` |
| List reports | `GET /v3/reports` |

Once the feature is available, use the following workflow:

1. Create a report using the Sandbox base URL. Select the partner or reseller scope and use the Sandbox dataset and filter shown below. Save the `reportId` returned with HTTP `202`.
2. Get the report status using the saved `reportId`. Continue checking while the state is `INITIATED` or `IN_PROGRESS`. If a duplicate create request returns HTTP `400`, code `4122`, use the existing report ID from `additionalDetails`.
3. If the report is `COMPLETED`, download the sample CSV using `downloadLink.url`. If the report is `FAILED`, inspect `additionalDetails`. If the report is `FAILED` or `ARCHIVED`, create a new report if you need another export.
4. List reports to retrieve the authenticated partner's Sandbox reports, ordered from newest to oldest. Use paging as described in [List reports](../../docs/data-exports/apis.md#list-reports). `ARCHIVED` reports are excluded.

### Create request

Use this request body for a partner-scope test:

```json
{
  "scope": {
    "level": "partner"
  },
  "datasets": [
    {
      "dataset": "propensity",
      "type": "churn",
      "filters": {
        "probability": ["HIGH"]
      }
    }
  ],
  "format": "csv"
}
```

For reseller-scope tests, replace only the `scope` object with the scope shown in the scenario table below. Keep the dataset, report type, and probability filter unchanged.

## Sandbox differences and limitations

### Sample files and reseller IDs

The planned implementation selects one of three sample CSV files based on the request scope:

| Request scope | Sample file contents |
|---|---|
| Partner scope | Rows for several resellers. |
| Reseller scope with one reseller ID | Rows for one reseller. |
| Reseller scope with two or more reseller IDs | Rows for several resellers. |

The specific reseller ID values do not affect which sample file is selected. Unlike production, Sandbox does not validate whether the specified reseller IDs belong to the partner. Any nonblank reseller ID is accepted. Blank IDs remain invalid.

The reseller and customer IDs in the sample CSV files are sample IDs. They may not match Sandbox records or the reseller IDs supplied in the request. Use the files to test file download and CSV parsing, not customer data or reseller ownership validation. CSV format and columns match the general [Report file format](../../docs/data-exports/apis.md#report-file-format).

### Simulated processing and failure

Processing is simulated rather than based on report size. The simulation remains in progress for the first two status checks and reaches a final state on the third status check. Status checks performed during duplicate request handling also count. Therefore, the partner's third status request is not necessarily the simulation's third status check. Do not use this Sandbox behavior as a production timing guarantee.

For reseller scope, include `TEST_FAILURE` in `scope.ids` to simulate report-generation failure. Matching for `TEST_FAILURE` is case-insensitive. The report reaches `FAILED` no later than the third simulation status check, has no `downloadLink`, and includes the following message in `additionalDetails`:

```text
Report generation failed. Create a new report to retry.
```

Partner scope ignores reseller IDs, so `TEST_FAILURE` has no effect on a partner-scope report. An HTTP `200` status response with `status: "FAILED"` means the status request succeeded, not that report generation succeeded.

### Download links and accelerated retention

Only reports in the `COMPLETED` state include `downloadLink`. If a link expires while the report remains `COMPLETED`, get the report status again to obtain a fresh link. Download link expiration and report retention are independent.

A completed Sandbox report is treated as `ARCHIVED` five minutes after completion, rather than the production churn retention period of one month. An archived report has no download link and no longer appears in the List reports response. Create a new report to obtain the file again. Failed reports are not archived.

The planned behavior does not specify the lifetime of a download link. Test expired-link renewal only if `downloadLink.expiresAt` occurs before the report is archived; do not assume the five-minute retention window permits that test.

## Test scenarios

These scenarios describe the expected results after the Sandbox implementation has been deployed and verified. For report creation, use the request above with the indicated scope, then get the report status until the report reaches a final state.

| Scenario | Request or action | Expected result |
|---|---|---|
| Partner-scope report | `"scope": {"level": "partner"}` | `COMPLETED` by the third simulation status check at the latest, with `downloadLink`. The CSV contains sample rows for several resellers. |
| Single-reseller report | `"scope": {"level": "reseller", "ids": ["R123"]}` | `COMPLETED` with sample rows for one reseller. The file's reseller ID is a sample ID, not `R123`. |
| Multi-reseller report | `"scope": {"level": "reseller", "ids": ["R123", "R456"]}` | `COMPLETED` with sample rows for several resellers, using sample reseller IDs. |
| Failed report | `"scope": {"level": "reseller", "ids": ["TEST_FAILURE"]}` | `FAILED` by the third simulation status check at the latest, with the failure message in `additionalDetails` and no `downloadLink`. |
| Duplicate request | Submit a matching request while the existing report is still `INITIATED` or `IN_PROGRESS`. You can reorder or repeat reseller IDs to verify duplicate-request matching. | HTTP `400`, code `4122`, with the existing `reportId` in `additionalDetails`. Reseller ID order and repeated IDs do not affect matching. |
| Expired download link | Get report status after `downloadLink.expiresAt`, while the report remains `COMPLETED`. | The status response for the same report includes a fresh `downloadLink`. This test depends on link expiry occurring before archival. |
| Archived report | Get report status about five minutes after completion. | The report has the `ARCHIVED` status, has no `downloadLink`, and is excluded from the List reports response. A new report is required to download a file again. |
| List reports | `GET /v3/reports?limit=10&offset=0` | Returns the partner's Sandbox reports ordered from newest to oldest, excluding `ARCHIVED` reports. Paging follows the general API contract. |
| Blank reseller ID | Create with `"scope": {"level": "reseller", "ids": [""]}`. | HTTP `400`, code `1117`. |
| Report type not enabled | Create with a report type that is not enabled in Sandbox. | HTTP `400`, code `1117`. |
| Unknown field | Add an unknown field to a create request. | HTTP `400`, code `1121`. |
| Unknown report ID | Get report status using an unknown report ID. | HTTP `404`, code `1181`. |

## Related documentation

- [Data exports overview](../../docs/data-exports/index.md)
- [Manage data exports using APIs](../../docs/data-exports/apis.md)
- [Error codes for data exports](../../docs/data-exports/error-codes.md)
- [Sandbox support](../sandbox-support.md)
