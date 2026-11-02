# Manage data exports using APIs

Data exports use the following three report service operations:

| Operation | Endpoint | Method |
|---|---|---|
| [Create report](#create-report) | `/v3/reports` | POST |
| [Get report status](#get-report-status) | `/v3/reports/{report-id}` | GET |
| [List reports](#list-reports) | `/v3/reports` | GET |

## Integration guide

1. Authenticate and prepare the [request headers](#authentication-and-request-headers).
2. Use [Create report](#create-report) to specify the scope, dataset, and optional filters. Save the `reportId` returned with HTTP `202`. If the service returns `400` with code `4122`, use the existing report ID from `additionalDetails` instead.
3. Use [Get report status](#get-report-status) with that identifier. Continue polling while the report is `PENDING` or `RUNNING`. Check the report state even when the status request returns HTTP `200`.
4. When the report is `COMPLETED`, [download the file](#download-the-file) using `downloadUrl`. For `FAILED` or `EXPIRED` reports, submit a new report if another export is needed.
5. Use [List reports](#list-reports) to find previous reports. Follow `links.next` when more results are available.

For request failures, see [HTTP status codes](#http-status-codes) and [Error codes for data exports](./error-codes.md).

## Authentication and request headers

See [API authentication and access](../authentication/index.md) for authentication requirements and [API request header](../references/api-headers.md) for platform header conventions. The report contract specifies the following requirements:

| Header | Requirement |
|---|---|
| `Authorization` | Required authentication token. The partner is derived from the token. |
| `X-Api-Key` | Required API key for the integration. |
| `X-Request-Id` | Recommended unique identifier for each request. |
| `x-correlation-id` | Required for POST; optional for GET. Missing on POST returns HTTP `400`, code `4119`. |
| `Content-Type: application/json` | Required for POST. |
| `X-Distributor-Org-Id` | Required only for the allowlisted Bridge client. Identifies the partner IMS organization; missing or invalid values return HTTP `406`. |

Do not supply a partner identity in the request body to select another partner's data. Status and list operations expose only the authenticated partner's reports.

## Create report

**Sample request URL:** `POST <env root url>/v3/reports`

Submit the scope, dataset selection, and optional filters. Report processing is asynchronous. A successful request returns HTTP `202 Accepted` with a `reportId` and a `PENDING` status. The response does not contain the report file.

### Request body

The following example requests a reseller-scoped churn report:

```json
{
  "scope": {
    "level": "reseller",
    "ids": ["R123"]
  },
  "datasets": [
    {
      "dataset": "propensity",
      "type": "churn",
      "filters": {
        "probability": ["HIGH", "MEDIUM"]
      }
    }
  ],
  "format": "csv"
}
```

#### Request parameters

| Field | Required | Type | Description |
|---|---|---|---|
| `scope` | Yes | Object | Defines whose data to export. |
| `scope.level` | Yes | String (enum) | `partner` or `reseller`. |
| `scope.ids` | For reseller scope | String array | One or more reseller IDs. Duplicate IDs are removed. Blank IDs are invalid. IDs that do not belong to the partner do not contribute rows to the report. Ignored for partner scope, where identity comes from the token. |
| `datasets` | Yes | Object array | Exactly one item is allowed. |
| `datasets[].dataset` | Yes | String (enum) | `propensity`. |
| `datasets[].type` | Yes | String (enum) | `churn` or `seat_expansion`. |
| `datasets[].filters` | No | Object | Allowed keys depend on the dataset and type. |
| `datasets[].filters.probability` | No | String array (enum values) | For propensity: `HIGH`, `MEDIUM`, `LOW`. If omitted, all values are included. Duplicates are removed. |
| `format` | No | String (enum) | `csv`. Defaults to `csv`. |

For partner scope, use `"scope": {"level": "partner"}`. Do not include a force-refresh field in the request. It is not defined in version 1.7 of the contract. Unknown fields and filters unsupported for the selected dataset and type return code `1121`.

### Response body

HTTP `202 Accepted`:

```json
{
  "reportId": "550e8400-e29b-41d4-a716-446655440000",
  "scope": {
    "level": "reseller",
    "ids": ["R123"]
  },
  "datasets": [
    {
      "dataset": "propensity",
      "type": "churn",
      "filters": {
        "probability": ["HIGH", "MEDIUM"]
      }
    }
  ],
  "format": "csv",
  "status": "PENDING",
  "creationDate": "2026-10-22T10:15:30Z",
  "links": {
    "self": {
      "uri": "/v3/reports/550e8400-e29b-41d4-a716-446655440000",
      "method": "GET",
      "headers": []
    }
  }
}
```

#### Response parameters

| Field | Type | Description |
|---|---|---|
| `reportId` | String | Identifier of the accepted report. Keep this value for polling. |
| `scope` | Object | Request scope after defaults are applied and duplicates are removed. |
| `datasets` | Object array | Requested dataset selection after defaults are applied and duplicates are removed. |
| `format` | String (enum) | Output format after defaults are applied: `csv`. |
| `status` | String (enum) | `PENDING` when a new report is accepted. |
| `creationDate` | String (UTC timestamp) | Time the report was created. |
| `links.self` | Object | Link to the report's status operation, with `uri`, `method`, and `headers`. |

### Duplicate request handling

A matching request from the same partner is rejected if an existing matching report is `PENDING` or `RUNNING`. HTTP `400` returns code `4122` and the existing report identifier:

```json
{
  "code": "4122",
  "message": "This request is already being processed. Please wait a moment and try again.",
  "additionalDetails": ["reportId: 550e8400-e29b-41d4-a716-446655440000"]
}
```

No new report is created in this case. Use [Get report status](#get-report-status) to poll the existing report using the identifier in `additionalDetails`.

If the matching report is `COMPLETED`, `FAILED`, or `EXPIRED`, or there is no match, the service creates a new report and returns HTTP `202`.

## Get report status

**Sample request URL:** `GET <env root url>/v3/reports/{report-id}`

Poll the report status using the `reportId` returned by [Create report](#create-report). The operation returns only reports owned by the authenticated partner.

### Path parameters

| Field | Required | Type | Description |
|---|---|---|---|
| `report-id` | Yes | String | The `reportId` returned when the report was accepted, or the existing report ID returned with code `4122`. |

### Response body

#### Completed response

HTTP `200`, with response header `Cache-Control: no-store`:

```json
{
  "reportId": "550e8400-e29b-41d4-a716-446655440000",
  "status": "COMPLETED",
  "lastUpdatedDate": "2026-10-22T10:20:10Z",
  "downloadUrl": "https://s3.amazonaws.com/bucket/path/to/report?X-Amz-Signature=...",
  "links": {
    "self": {
      "uri": "/v3/reports/550e8400-e29b-41d4-a716-446655440000",
      "method": "GET",
      "headers": []
    }
  }
}
```

`lastUpdatedDate` is the time of the last status change in UTC. `downloadUrl` is a short-lived pre-signed link, included only for `COMPLETED` reports. Call [Get report status](#get-report-status) again to obtain a fresh link. Do not cache the completed status response.

#### Failed response

HTTP `200` means the status request succeeded, even when report generation failed:

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

`additionalDetails` is included only for `FAILED` reports and contains partner-facing failure reasons. It does not include internal service names, stack traces, or storage errors. Use [Create report](#create-report) to submit a new report and retry.

#### Response parameters

| Field | Type | Description |
|---|---|---|
| `reportId` | String | Identifier of the report. |
| `status` | String (enum) | `PENDING`, `RUNNING`, `COMPLETED`, `FAILED`, or `EXPIRED`. |
| `lastUpdatedDate` | String (UTC timestamp) | Time of the last status change. |
| `downloadUrl` | String | Short-lived pre-signed file URL. Included only for `COMPLETED` reports. |
| `additionalDetails` | String array | Partner-facing failure reasons. Included only for `FAILED` reports. |
| `links.self` | Object | Link to this status operation, with `uri`, `method`, and `headers`. |

#### Status handling

| Report state or lookup result | Response |
|---|---|
| `COMPLETED` | HTTP `200` with `downloadUrl`. |
| `PENDING`, `RUNNING`, or `EXPIRED` | HTTP `200` without `downloadUrl`. |
| `FAILED` | HTTP `200` without `downloadUrl`, with failure reasons in `additionalDetails`. |
| Unknown report ID or another partner's report | HTTP `400`, code `5122`, message `Invalid Request`, and `additionalDetails: ["reportId: Report not found"]`. |

See the [job lifecycle](./index.md#job-lifecycle) and [Error codes for data exports](./error-codes.md) to distinguish report state from request errors.

## List reports

**Sample first-page request URL:** `GET <env root url>/v3/reports?limit=10`

List the authenticated partner's reports. Results are ordered by `creationDate`, with the newest reports first. Sorting is not configurable.

### Query parameters

| Field | Required | Type | Description |
|---|---|---|---|
| `limit` | No | Integer | Page size; defaults to `10`, maximum `50`. |
| `page` | No | String | Opaque token from `links.next`. Omit for the first page. |

### Response body

HTTP `200`. The following example is for `GET <env root url>/v3/reports?limit=1` and shows one result with another page available:

```json
{
  "totalCount": 2,
  "count": 1,
  "limit": 1,
  "items": [
    {
      "reportId": "550e8400-e29b-41d4-a716-446655440000",
      "scope": { "level": "reseller", "ids": ["R123"] },
      "datasets": [
        {
          "dataset": "propensity",
          "type": "churn",
          "filters": { "probability": ["HIGH", "MEDIUM"] }
        }
      ],
      "format": "csv",
      "status": "COMPLETED",
      "creationDate": "2026-10-22T10:15:30Z",
      "fileSize": 4096,
      "links": {
        "self": {
          "uri": "/v3/reports/550e8400-e29b-41d4-a716-446655440000",
          "method": "GET",
          "headers": []
        }
      }
    }
  ],
  "links": {
    "self": { "uri": "/v3/reports?limit=1", "method": "GET", "headers": [] },
    "next": {
      "uri": "/v3/reports?limit=1&page=eyJwYWdlIjoyfQ",
      "method": "GET",
      "headers": []
    }
  }
}
```

#### Response parameters

| Field | Type | Description |
|---|---|---|
| `totalCount` | Integer | Total number of reports for the authenticated partner. |
| `count` | Integer | Number of reports returned on this page. |
| `limit` | Integer | Page size for the request. |
| `items` | Object array | Reports on this page, ordered by `creationDate` descending. |
| `items[].reportId` | String | Identifier to use with the status operation. |
| `items[].scope` | Object | Scope of the report. |
| `items[].datasets` | Object array | Dataset selection and filters for the report. |
| `items[].format` | String (enum) | Report format: `csv`. |
| `items[].status` | String (enum) | `PENDING`, `RUNNING`, `COMPLETED`, `FAILED`, or `EXPIRED`. |
| `items[].creationDate` | String (UTC timestamp) | Time the report was created. |
| `items[].fileSize` | Integer | File size in bytes. Included only for completed reports. |
| `items[].links.self` | Object | Link to the report's status operation. |
| `links.self` | Object | Link to the current list page. |
| `links.next` | Object | Link to the next page. Omitted on the last page. |

Link objects contain `uri`, `method`, and `headers`. Use [Get report status](#get-report-status) for a selected report to obtain its download link.

### Pagination

Follow `links.next` to retrieve the next page. The `links.next` property is omitted on the last page. Pagination is forward-only. There is no `prev` link or offset parameter. Treat the page token as opaque; do not construct or decode it.

## Download the file

After [Get report status](#get-report-status) returns `COMPLETED` and provides a `downloadUrl`, download the CSV separately:

```http
GET <downloadUrl>
```

Use the returned pre-signed URL as provided. Do not construct or modify the hostname, filename, signature, or expiration information. Protect the link as a credential. If it expires while the report remains `COMPLETED`, call [Get report status](#get-report-status) again to obtain a fresh link. If the report is `EXPIRED`, use [Create report](#create-report) to submit a new report instead.

## HTTP status codes

The following statuses apply to the report service APIs, not the separate file download:

| HTTP status | Meaning | Applicable operations |
|---|---|---|
| `202 Accepted` | A new report was accepted for asynchronous processing. | Create report |
| `200 OK` | The request succeeded. For the status operation, inspect `status` to determine the report outcome, including `FAILED` or `EXPIRED`. | Get report status, List reports |
| `400 Bad Request` | Validation failure, missing correlation ID, matching report in progress, or report not found for the partner. Inspect the error code and `additionalDetails`. | Create report, Get report status, List reports, depending on the error |
| `401 Unauthorized` and `403 Forbidden` | Authentication or authorization failure. | All |
| `406 Not Acceptable` | Missing or invalid `X-Distributor-Org-Id` for the allowlisted Bridge client. | All |
| `500 Internal Server Error` | Unexpected error. | All |
| `503 Service Unavailable` | Report service unavailable or timing out. | All |

See [Error codes for data exports](./error-codes.md) for the application codes, operation-specific mappings, and error response examples.

