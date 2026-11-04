# Manage data exports using APIs

Data exports use the following three report service operations:

| Operation | Endpoint | Method |
|---|---|---|
| [Create report](#create-report) | `/v3/reports` | POST |
| [Get report status](#get-report-status) | `/v3/reports/{report-id}` | GET |
| [List reports](#list-reports) | `/v3/reports` | GET |

## Integration guide

1. Use [Create report](#create-report) to specify the scope, dataset, and optional filters. Save the `reportId` returned in the HTTP `202 Accepted` response. If the service returns `400` with code `4122`, use the existing report ID from `additionalDetails` instead.
2. Use [Get report status](#get-report-status) with the `reportId`. Continue polling while the report is `INITIATED` or `IN_PROGRESS`. Check the report state even when the status request returns HTTP `200`.
3. When the report is `COMPLETED`, [download the file](#download-the-file) using `downloadLink.url`. For `FAILED` or `ARCHIVED` reports, create a new report if another export is required.
4. Use [List reports](#list-reports) to retrieve previously created reports. Follow `links.next` when more results are available.

For request failures, see [HTTP status codes](#http-status-codes) and [Error codes for data exports](./error-codes.md).

## Authentication and request headers

Use the standard authentication and request headers described in [API authentication and access](../authentication/index.md) and [API request header](../references/api-headers.md). No additional report-specific request headers are required.

Do not include a partner identifier in the request body. The authenticated partner can access only its own reports and data.

## Create report

**Sample request URL:** `POST <env root url>/v3/reports`

Submit the scope, a single dataset selection, and optional filters. Report processing is asynchronous. A successful request returns an HTTP `202 Accepted` response with a `reportId` and an `INITIATED` status. The response does not contain the report file.

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
| `scope.ids` | For reseller scope | String array | One or more reseller IDs. Duplicate IDs are removed. Blank IDs are invalid. Unrecognized reseller IDs and reseller IDs that do not belong to the authenticated partner are skipped and contribute no rows. The service does not return a reseller-recognition error for these IDs. Ignored for partner scope, where identity comes from the token. |
| `datasets` | Yes | Object array | Exactly one item is allowed. |
| `datasets[].dataset` | Yes | String (enum) | `propensity`. |
| `datasets[].type` | Yes | String (enum) | `churn` or `seat_expansion`. |
| `datasets[].filters` | No | Object | Allowed keys depend on the dataset and type. |
| `datasets[].filters.probability` | No | String array (enum values) | For the propensity dataset: `HIGH`, `MEDIUM`, or `LOW`. If omitted, all values are included. Duplicates are removed. |
| `format` | No | String (enum) | The supported value is `csv`, which is also the default. |

For partner scope, use `"scope": {"level": "partner"}`. The `source` field is read-only and must not be sent in the request. There is no force-refresh request field. Unknown fields and filters unsupported for the selected dataset and type return code `1121`.

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
  "status": "INITIATED",
  "source": "API",
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
| `status` | String (enum) | `INITIATED` when a new report is accepted. |
| `source` | String (enum) | Read-only origin set from the caller: `API` for a partner integration or `Bridge` for the Bridge UI. |
| `creationDate` | String (UTC timestamp) | Time the report was created. |
| `links.self` | Object | Link to the report's status operation, with `uri`, `method`, and `headers`. |

### Duplicate request handling

If a matching report for the authenticated partner is already `INITIATED` or `IN_PROGRESS`, the service does not create another report. HTTP `400` returns code `4122` and the existing report identifier:

```json
{
  "code": "4122",
  "message": "This request is already being processed. Please wait a moment and try again.",
  "additionalDetails": ["reportId: 550e8400-e29b-41d4-a716-446655440000"]
}
```

Use [Get report status](#get-report-status) to poll the existing report using the identifier in `additionalDetails`.

The service determines whether requests match by comparing the authenticated partner and the normalized request body:

- JSON field order is ignored.
- Array values are deduplicated and sorted, so their order is ignored.
- Defaults are applied before comparison. Omitting `format` matches `"format": "csv"`.
- `scope.ids` is ignored for partner scope.

If the matching report is `COMPLETED`, `FAILED`, or `ARCHIVED`, or there is no match, the service creates a new report and returns HTTP `202`. Completed reports are not automatically reused.

## Get report status

**Sample request URL:** `GET <env root url>/v3/reports/{report-id}`

Check the report status using the `reportId` returned by [Create report](#create-report). The operation returns only reports owned by the authenticated partner.

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
  "source": "API",
  "updatedDate": "2026-10-22T10:20:10Z",
  "downloadLink": {
    "url": "https://s3.amazonaws.com/bucket/path/to/report?X-Amz-Signature=...",
    "expiresAt": "2026-10-22T11:20:10Z"
  },
  "links": {
    "self": {
      "uri": "/v3/reports/550e8400-e29b-41d4-a716-446655440000",
      "method": "GET",
      "headers": []
    }
  }
}
```

`updatedDate` indicates the UTC time of the most recent status change. `downloadLink` is included only for `COMPLETED` reports. Its `url` is a short-lived pre-signed link, and `expiresAt` specifies when the link expires in UTC. The `expiresAt` value does not indicate when the report retention period ends. Call [Get report status](#get-report-status) again to obtain a fresh link. Do not cache the completed status response.

#### Failed response

An HTTP `200` response indicates that the status request succeeded. It does not indicate that report generation succeeded:

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

`additionalDetails` is included only for `FAILED` reports and contains partner-facing failure reasons. It does not include internal service names, stack traces, or storage errors. Use [Create report](#create-report) to submit a new report.

#### Response parameters

| Field | Type | Description |
|---|---|---|
| `reportId` | String | Identifier of the report. |
| `status` | String (enum) | `INITIATED`, `IN_PROGRESS`, `COMPLETED`, `FAILED`, or `ARCHIVED`. |
| `source` | String (enum) | Read-only origin: `API` or `Bridge`. |
| `updatedDate` | String (UTC timestamp) | Time of the last status change. |
| `downloadLink` | Object | Download information. Included only for `COMPLETED` reports. |
| `downloadLink.url` | String | Short-lived pre-signed file URL. Request status again to obtain a fresh link. |
| `downloadLink.expiresAt` | String (UTC timestamp) | Time the URL stops working. Separate from report retention. |
| `additionalDetails` | String array | Partner-facing failure reasons. Included only for `FAILED` reports. |
| `links.self` | Object | Link to this status operation, with `uri`, `method`, and `headers`. |

#### Status handling

| Report state or lookup result | Response |
|---|---|
| `COMPLETED` | HTTP `200` with `downloadLink`. |
| `INITIATED`, `IN_PROGRESS`, or `ARCHIVED` | HTTP `200` without `downloadLink`. |
| `FAILED` | HTTP `200` without `downloadLink`, with failure reasons in `additionalDetails`. |
| Unknown report ID | HTTP `404`, code `1181`, message `Invalid Report ID`. |

See the [report lifecycle](./index.md#report-lifecycle) and [Error codes for data exports](./error-codes.md) to distinguish report state from request errors.

## List reports

**Sample first-page request URL:** `GET <env root url>/v3/reports?limit=10&offset=0`

Use this operation to retrieve reports for the authenticated partner. Results are ordered by `creationDate`, with the newest reports first. Sorting is not configurable.

### Query parameters

| Field | Required | Type | Description |
|---|---|---|---|
| `limit` | No | Integer | Number of reports to return per page. The default is `10`, and the maximum is `50`. |
| `offset` | No | Integer | Number of reports to skip before the first result. The default is `0`. |

### Response body

HTTP `200`. The following example is for `GET <env root url>/v3/reports?limit=1&offset=0` and shows one result with another page available:

```json
{
  "totalCount": 2,
  "count": 1,
  "limit": 1,
  "offset": 0,
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
      "source": "API",
      "creationDate": "2026-10-22T10:15:30Z",
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
    "self": { "uri": "/v3/reports?limit=1&offset=0", "method": "GET", "headers": [] },
    "next": {
      "uri": "/v3/reports?limit=1&offset=1",
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
| `offset` | Integer | Offset applied to the response. |
| `items` | Object array | Reports on this page, ordered by `creationDate` descending. |
| `items[].reportId` | String | Identifier to use with the status operation. |
| `items[].scope` | Object | Scope of the report. |
| `items[].datasets` | Object array | Dataset selection and filters for the report. |
| `items[].format` | String (enum) | Report format: `csv`. |
| `items[].status` | String (enum) | `INITIATED`, `IN_PROGRESS`, `COMPLETED`, `FAILED`, or `ARCHIVED`. |
| `items[].source` | String (enum) | Read-only origin: `API` for a partner integration or `Bridge` for the Bridge UI. |
| `items[].creationDate` | String (UTC timestamp) | Time the report was created. |
| `items[].links.self` | Object | Link to the report's status operation. |
| `links.self` | Object | Link to the current list page. |
| `links.next` | Object | Link to the next page. Omitted on the last page. |
| `links.prev` | Object | Link to the previous page. Omitted on the first page. |

Link objects contain `uri`, `method`, and `headers`. Use [Get report status](#get-report-status) for a selected report to obtain its download link.

### Pagination

Pagination uses `limit` and `offset`. Follow `links.next` to retrieve the next page and `links.prev` to retrieve the previous page. These links are omitted when there is no corresponding page.

## Download the file

After [Get report status](#get-report-status) returns a `COMPLETED` status with `downloadLink`, download the CSV separately using `downloadLink.url`:

```http
GET <downloadLink.url>
```

Use the returned pre-signed URL as provided. Do not construct or modify the hostname, filename, signature, or expiration information. Treat the pre-signed URL as a credential and protect it accordingly. If the URL reaches `downloadLink.expiresAt` while the report remains `COMPLETED`, call [Get report status](#get-report-status) again with the same `reportId` to obtain a fresh link to the same retained file. Download link expiry does not affect report retention or cause the report to become `ARCHIVED`.

### Report retention

Retention begins when a report becomes `COMPLETED`:

| Report type | Retention |
|---|---|
| `churn` | 1 month |
| `seat_expansion` | 7 days |

When retention ends, the report becomes `ARCHIVED` and its file is no longer available. Use [Create report](#create-report) to request a new report. Download link expiry and report retention are independent. `downloadLink.expiresAt` indicates only when the download link expires.

## Report file format

Reports are UTF-8 CSV files with a header row and one row per customer. Columns depend on `datasets[].type`. Dates use `YYYY-MM-DD` in UTC. Each reasons field contains `reasonCode=value` pairs separated by semicolons and is enclosed in double quotation marks.

### Churn report

For `type: "churn"`:

| Column | Type | Description |
|---|---|---|
| `reseller_id` | String | Reseller ID. |
| `customer_id` | String | Customer ID. |
| `creative_cloud_probability` | Enum | `HIGH`, `MEDIUM`, or `LOW`. |
| `creative_cloud_reasons` | String | Creative Cloud churn reasons. |
| `document_cloud_probability` | Enum | `HIGH`, `MEDIUM`, or `LOW`. |
| `document_cloud_reasons` | String | Document Cloud churn reasons. |
| `anniversary_date` | Date | Customer anniversary date. |
| `refresh_date` | Date | Date the churn score was last refreshed. |

When a probability filter is supplied, a customer row is included if the Creative Cloud or Document Cloud probability matches the specified filter. Columns for a product that does not match the filter are left empty.

### Seat expansion report

For `type: "seat_expansion"`:

| Column | Type | Description |
|---|---|---|
| `reseller_id` | String | Reseller ID. |
| `customer_id` | String | Customer ID. |
| `probability` | Enum | `HIGH`, `MEDIUM`, or `LOW`. |
| `predicted_addon_size` | Integer | Predicted number of seats to add. |
| `reasons` | String | Seat expansion reasons. |
| `anniversary_date` | Date | Customer anniversary date. |
| `refresh_date` | Date | Date the seat expansion score was last refreshed. |

## HTTP status codes

The following HTTP status codes apply to the report service APIs, not to the separate file download:

| HTTP status | Meaning | Applicable operations |
|---|---|---|
| `202 Accepted` | A new report was accepted for asynchronous processing. | Create report |
| `200 OK` | The request succeeded. For the status operation, inspect `status` to determine the report outcome, including `FAILED` or `ARCHIVED`. | Get report status, List reports |
| `400 Bad Request` | Validation failure, missing correlation ID, or matching report in progress. Inspect the error code and `additionalDetails`. | Create report, List reports, depending on the error |
| `404 Not Found` | Report ID not found; code `1181`, `Invalid Report ID`. | Get report status |
| `401 Unauthorized` and `403 Forbidden` | Authentication or authorization failure. | All |
| `500 Internal Server Error` | An unexpected server error occurred. | All |
| `503 Service Unavailable` | The report service is unavailable or the request timed out. | All |

See [Error codes for data exports](./error-codes.md) for the application codes, operation-specific mappings, and error response examples.
