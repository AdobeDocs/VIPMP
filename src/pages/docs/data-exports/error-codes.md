# Error codes for data exports

These errors apply to the [Data export APIs](./apis.md):

| HTTP status | Error code | Message or meaning | When | Applicable operations |
|---|---|---|---|---|
| 400 | `1117` | Some Fields are Invalid | Invalid scope level, dataset, report type, format, filter values, limit, or page; blank reseller IDs; or a `datasets` array that does not contain exactly one item. | Create report, List reports |
| 400 | `1121` | Unexpected Fields | Unknown field or a filter unsupported for the selected dataset and type. | Create report |
| 400 | `1122` | Missing Required Fields | Missing `scope`, `scope.level`, `datasets`, `datasets[].dataset`, or `datasets[].type`; missing `scope.ids` for reseller scope. | Create report |
| 400 | `4122` | Idempotent Request | A matching report from the same partner is already `PENDING` or `RUNNING`. The response identifies the existing report in `additionalDetails`; no new report is created. | Create report |
| 400 | `4119` | Correlation ID is Invalid or Missing | Missing `x-correlation-id`. | Create report |
| 401 / 403 | Existing authentication errors | Token or API key missing or invalid. | Authentication or authorization failure. | All |
| 406 | `406` | Not Acceptable | Missing or invalid `X-Distributor-Org-Id` for the allowlisted Bridge client only. | All |
| 400 | `5122` | Invalid Request | Report not found for the authenticated partner. Unknown IDs and another partner's report return the same response. | Get report status |
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

Use the report ID in `additionalDetails` to poll the existing report. Duplicate detection applies only to `PENDING` and `RUNNING` reports. For more information, see [Duplicate request handling](./apis.md#duplicate-request-handling).

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

HTTP `400` is returned for both an unknown report ID and a report that belongs to another partner:

```json
{
  "code": "5122",
  "message": "Invalid Request",
  "additionalDetails": ["reportId: Report not found"]
}
```

Reseller IDs that do not belong to the partner return no data and are not treated as request errors. Blank reseller IDs, however, are invalid.

## HTTP status and report states

HTTP status indicates the outcome of an API request, not asynchronous report generation. HTTP `202` means that a new report was accepted for processing, not that the report is ready for download. `PENDING`, `RUNNING`, `COMPLETED`, `FAILED`, and `EXPIRED` are report states, not application error codes.

A status request returns HTTP `200` even if the report is `FAILED` or `EXPIRED`. A failed report includes partner-facing reasons in `additionalDetails` and no `downloadUrl`:

```json
{
  "reportId": "550e8400-e29b-41d4-a716-446655440000",
  "status": "FAILED",
  "lastUpdatedDate": "2026-10-22T10:20:10Z",
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

Failure details do not expose internal service names, stack traces, or storage errors. Submit a new report to retry a failed or expired report. An error fetching status does not, by itself, mean the underlying report failed.

Only a `COMPLETED` report includes `downloadUrl`. If the link expires while the report remains completed, request status again for a fresh link. An `EXPIRED` report requires a new submission.

The contract does not specify behavior for no-data output, link and retention lifetimes, or rate and concurrency limits. Do not assign error codes to these unspecified cases.
