# Error codes for data exports

These errors apply to the [Data export APIs](./apis.md):

| HTTP status | Error code | Message or meaning | When | Applicable operations |
|---|---|---|---|---|
| 400 | `1117` | Some Fields are Invalid | Invalid scope level, dataset, report type, format, filter values, limit, or offset; blank reseller IDs; or a `datasets` array that does not contain exactly one item. | Create report, List reports |
| 400 | `1121` | Unexpected Fields | Unknown field or a filter unsupported for the selected dataset and type. | Create report |
| 400 | `1122` | Missing Required Fields | Missing `scope`, `scope.level`, `datasets`, `datasets[].dataset`, or `datasets[].type`; missing `scope.ids` for reseller scope. | Create report |
| 400 | `4122` | Idempotent Request | A matching report from the same partner is already `INITIATED` or `IN_PROGRESS`. The response identifies the existing report in `additionalDetails`; no new report is created. | Create report |
| 400 | `4119` | Correlation ID is Invalid or Missing | Missing `x-correlation-id`. | Create report |
| 401 / 403 | Existing authentication errors | Token or API key missing or invalid. | Authentication or authorization failure. | All |
| 404 | `1181` | Invalid Report ID | Report ID not found. | Get report status |
| 500 | `1124` | Internal Server Error | Unexpected error. | All |
| 503 | `1172` | Service is unavailable | Report service unavailable or timing out. | All |

## Error response examples

### Matching report in progress

HTTP `400`:

```json
{
  "code": "4122",
  "message": "This request is already being processed. Please wait a moment and try again.",
  "additionalDetails": ["reportId: 550e8400-e29b-41d4-a716-446655440000"]
}
```

Use the report ID in `additionalDetails` to poll the existing report. Duplicate detection applies only to `INITIATED` and `IN_PROGRESS` reports. A matching completed, failed, or archived report does not block a new submission. For more information, see [Duplicate request handling](./apis.md#duplicate-request-handling).

### Invalid dataset count

HTTP `400`:

```json
{
  "code": "1117",
  "message": "Some Fields are Invalid",
  "additionalDetails": ["datasets: exactly one item is allowed"]
}
```

### Report not found

An unknown report ID returns HTTP `404`, code `1181`, with message `Invalid Report ID`. Status lookups are limited to reports owned by the authenticated partner.

Incorrect reseller IDs and IDs that do not belong to the partner are skipped and contribute no rows, without a reseller-recognition error. They never expose another partner's data. Blank reseller IDs, however, are invalid.

## HTTP status and report states

HTTP status indicates the outcome of an API request, not asynchronous report generation. HTTP `202` means that a new report was accepted for processing, not that the report is ready for download. `INITIATED`, `IN_PROGRESS`, `COMPLETED`, `FAILED`, and `ARCHIVED` are report states, not application error codes.

A status request returns HTTP `200` even if the report is `FAILED` or `ARCHIVED`. A failed report includes partner-facing reasons in `additionalDetails` and no `downloadLink`:

```json
{
  "reportId": "550e8400-e29b-41d4-a716-446655440000",
  "status": "FAILED",
  "source": "Bridge",
  "updatedDate": "2026-10-22T10:20:10Z",
  "additionalDetails": ["Report generation failed. Create a new report to retry."],
  "links": {
    "self": {
      "uri": "/v3/reports/550e8400-e29b-41d4-a716-446655440000",
      "method": "GET",
      "headers": []
    }
  }
}
```

Failure details do not expose internal service names, stack traces, or storage errors. Submit a new report to retry a failed or archived report. An error fetching status does not, by itself, mean the underlying report failed.

Only a `COMPLETED` report includes `downloadLink`. If the URL reaches `downloadLink.expiresAt` while the report remains completed, request status again for a fresh link. An `ARCHIVED` report requires a new submission.

Report retention starts at completion: 1 month for churn and 7 days for seat expansion. Retention is separate from link expiry. No-data output and rate or concurrency limits are not specified; do not assign error codes to those cases.
