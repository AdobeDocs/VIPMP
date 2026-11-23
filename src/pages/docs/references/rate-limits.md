# API rate limits and throttling

The Adobe VIP Marketplace API applies client-level throttling over a rolling five-second window. The request count for each client is evaluated over the preceding five seconds rather than resetting at fixed five-second intervals. As time advances, older requests fall outside the window.

The soft limit is 1,000 requests and the hard limit is 1,500 requests within the rolling five-second window. Exceeding the soft limit introduces a random response delay, signaling the client to reduce its request rate. Exceeding the hard limit results in HTTP 429 errors.

| Requests in the preceding five seconds | Throttling behavior |
|:---|:---|
| Up to 1,000 | Neither threshold is exceeded. |
| More than 1,000 and up to 1,500 | A random response delay is introduced. Reduce the request rate. |
| More than 1,500 | HTTP 429 errors are returned. |

![Client-level throttling: up to 1,000 requests exceeds neither threshold; more than 1,000 through 1,500 introduces a random response delay; more than 1,500 returns HTTP 429. Two timeline snapshots show the preceding five-second window moving forward as time advances.](../image/api-throttling-limits.png)

These are rolling-window request counts, not requests-per-second guarantees. The duration of the random response delay is not specified.

## Handling throttling

Pace requests and reduce bursts when response delays indicate soft-limit throttling. For HTTP 429 handling and client retry recommendations, see [Throttling handling](error-handling.md#throttling-handling). Follow the [Idempotency - Correlation ID header](idempotency.md) rules when retrying.
