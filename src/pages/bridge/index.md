# The Bridge

The Bridge is a partner-facing marketplace UI built directly on top of the VIP Marketplace Commerce Partner APIs (CPAPI). It gives direct partners, including distributors and platinum resellers, a direct, browser-based interface to VIP Marketplace capabilities, including purchasing, renewals, upgrades, account management, and pricing discovery, without requiring them to build or maintain their own API integration.

Everything the Bridge does is powered by the same Commerce Partner APIs described elsewhere in this documentation. If you have already built your own integration with these APIs, the Bridge is not intended to replace it. It is a complementary, ready-to-use UI that:

- Gives your team immediate access to new VIP Marketplace capabilities when they are released, without waiting for your own development cycle.
- Serves as a fail-safe for revenue continuity, helping you continue transactions if your own platform integration is temporarily unavailable.
- Provides a standardized reference implementation for the API-driven workflows described in this documentation.

## Who can access and use the Bridge

The Bridge is available only to distributor and platinum partner organizations. Adobe enables access at the organization level, and individual users are then assigned access by their organization's Admin Console administrators.

**Important:** You must be granted Bridge access before you can sign in. Accessing the URL alone does not provide access to the application. See [Getting started](./getting-started.md) for the complete access process.

After your organization administrator grants you access to the Bridge, sign in using the following production URL:

| Environment | URL |
|-------------|-----|
| Production | https://bridge.marketplace.adobe.com/ |

Sign in using your Adobe ID, or IMS, credentials, which are the same credentials used elsewhere in the VIP Marketplace ecosystem.

## What you can do in the Bridge

The Bridge covers a growing subset of the workflows available through the Commerce Partner APIs, including reseller and customer management, catalog browsing, cart and checkout, Three-Year Commit (3YC) enrollment, mid-term changes, and flexible discounts. Some capabilities are currently API-only and are not yet exposed in the Bridge UI. See [Supported features](./supported-features.md) for the current breakdown, including the UI path for each supported feature.

## Next steps

Use the following resources to continue:

- [Getting started](./getting-started.md): Learn how your organization and users get access, and how to sign in.
- [Supported features](./supported-features.md): Review what you can do in the Bridge today, and what still requires direct API integration.
