# Data exports

Data exports provide an asynchronous way to retrieve a partner-scoped dataset as a downloadable file. Instead of combining individual customer API responses, partners can request an export of their book of business at the partner or reseller scope.

The current API contract supports the propensity dataset, with churn and seat expansion report types. Each report includes exactly one dataset selection.

## Export workflow

![Data export workflow: submit a report, poll its status, and download the CSV when completed. Continue polling pending or running reports; submit a new report if failed or expired.](../image/data-export-workflow.png)

1. **Create report:** Select the dataset, scope, and applicable filters. The service accepts the create report request, creates a report, and returns a `reportId` instead of the file.
2. **Get report status:** Use the `reportId` to check the report status until processing is complete.
3. **Download report:** When the report is completed, retrieve the file using the returned download URL. Currently only CSV format is supported for reports.

All exports are asynchronous, including small exports. Use the list operation to find previous reports or check for work already in progress. For endpoints, request fields, and examples, see [Manage data exports using APIs](./apis.md).

## Scope and access

Partner identity is determined by authentication rather than by a partner identifier in the request. Partners can view only their own reports.

Set `scope.level` to `partner` to export the authenticated partner's data, or set it to `reseller` with one or more reseller IDs in `scope.ids`. Reseller IDs that do not belong to the partner return no rows and do not provide access to another partner's data. For partner scope, `scope.ids` is ignored.

The dataset determines the available filters and the contents of the resulting file. This capability overview does not define dataset-specific columns, refresh schedules, or snapshot guarantees.

## Report lifecycle

The API returns the following report states:

| State | Meaning | Next action |
|---|---|---|
| `PENDING` | The request has been accepted, but processing has not yet started. | Wait for completion. |
| `RUNNING` | The export is being generated. | Wait for completion. |
| `COMPLETED` | The export is ready, and the status operation returns a download link. The report transitions to `EXPIRED` when the file is no longer available. | Download the file while it is retained. |
| `FAILED` | The export could not be completed. | Submit a new request to attempt the export again. |
| `EXPIRED` | The file retention period has elapsed, and the export is no longer available. | Submit a new export request. The old file cannot be revived. |

A successful status API response does not necessarily mean that the export was completed successfully. HTTP `200` with `status: "FAILED"` means the status request succeeded but report generation failed. Partner-facing failure reasons are returned in `additionalDetails`. See [Error codes for data exports](./error-codes.md).

## Repeated requests and report creation

Before creating a report, the service checks for a matching request for the authenticated partner.

| Matching report state | Create response |
|---|---|
| `PENDING` or `RUNNING` | HTTP `400`, code `4122`, with the existing `reportId` in `additionalDetails`. No new report is created. |
| `COMPLETED`, `FAILED`, or `EXPIRED` | HTTP `202`. A new report is created. |
| No matching report | HTTP `202`. A new report is created. |

## What's next?

- [Manage data exports using APIs](./apis.md)
- [Error codes for data exports](./error-codes.md)
