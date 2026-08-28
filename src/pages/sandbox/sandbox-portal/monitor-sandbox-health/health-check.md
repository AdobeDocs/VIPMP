# Health check API

The CPAPI Health Check API enables you to determine whether Adobe Commerce Partner API (CPAPI) is available to serve requests before you send them. Instead of waiting for a customer transaction to fail, you can call this endpoint to verify whether the service, or a specific business component, is currently operational.

The health check reports status by the following business components, rather than individual backend services:

- Customer management
- Order management
- Subscription management
- Sandbox Partner Portal backend

For details about each component, see [Business components](#business-components).

## What it's for

Use the Health Check API for the following scenarios:

- Performing a pre-flight validation before submitting a batch of customer, order, or subscription requests.
- Determining whether to display a "service temporarily unavailable" message to end customers instead of allowing an operation to fail during processing.
- Supporting automated monitoring and alerting workflows. Integrate the endpoint into your dashboards or configure alerts when repeated 503 Service Unavailable responses occur.

## What it's not

The Health Check API is not intended for the following purposes:

- Diagnosing or troubleshooting service issues. The API indicates which business component is unavailable, but it does not provide details about the underlying cause.
- Replacing standard error handling in your application. A healthy status indicates that the service is currently available, but it does not guarantee that all subsequent API requests will succeed.

The API deliberately exposes only a small set of business-oriented components. This abstraction allows Adobe to change underlying infrastructure and implementation details without affecting the API contract.

Regardless of backend changes, the Health Check API always reports the same four business components and one of two possible states: `UP` or `DOWN`.

## Endpoint

```
GET /v1/health
```

Sandbox: `https://partnersandbox-stage.adobe.io/v1/health?api_key=<your API key>`

## Authentication

The endpoint uses API key authentication through Adobe I/O Gateway. Use the same API key that you use for other Sandbox CPAPI endpoints when calling this endpoint.

## Response codes

| HTTP status | Meaning |
|---|---|
| 200 | All business components are healthy (UP). You can proceed with API requests. |
| 503 | At least one business component is unhealthy (DOWN). Requests to the affected component may fail until service is restored. |

## Response body

```json
{
  "status": "UP",
  "components": {
    "customerManagement": "UP",
    "orderManagement": "UP",
    "subscriptionManagement": "UP",
    "sandboxPortal": "UP"
  }
}
```

`status` represents the overall health of CPAPI. `components` provides a health status for each business component, allowing you to identify the affected area without exposing underlying service dependencies.

## Business components

| Component | Covers | If `DOWN`, recommended action |
|---|---|---|
| `customerManagement` | Customer and account management capabilities | Account creation and lookup operations are unavailable. Pause customer signup and account calls. |
| `orderManagement` | Order creation and order processing capabilities, including transfer orders  | Order placement and processing are unavailable. Pause order submission and queue retries. |
| `subscriptionManagement` | Subscription and entitlement capabilities | Subscription and entitlement changes are unavailable. Pause renewal, upgrade, and downgrade calls. |
| `sandboxPortal` | Availability of the Sandbox Partner Portal backend | The sandbox portal is unavailable. Sandbox testing may be impacted; there is no production impact. |

A component reports `DOWN` when one or more of its underlying dependencies are unavailable.

**Note:** `customerManagement`, `orderManagement`, and `subscriptionManagement` share common underlying dependencies, such as authentication and the core CPAPI datastore. As a result, an outage in a shared dependency may cause multiple components to report `DOWN` simultaneously.

## Recommended client behavior

- Call `/v1/health` before bulk operations rather than before every individual API request.
- Use a minimum polling interval, such as 30 to 60 seconds. Avoid repeatedly calling the endpoint during a service disruption.
