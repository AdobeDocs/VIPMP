# API collection and interaction basics

As part of the onboarding process, Adobe provides a Postman collection that contains all preset API calls, making it easier to interact with CPAPI.

![API collection](./image/api_collection.png)

You can opt for an alternate method to interact with CPAPI. Read more about [getting started with Postman](https://learning.postman.com/docs/getting-started/introduction/).

## API interaction basics

### Get and renew an API OAuth access token

Having a valid access token is essential to get a successful response when making a call in the CPAPI collection. You can generate the access token by using the `POST Generate OAuth Token` call in the **Initialization** folder of the Postman collection. This token authenticates the principal and remains valid for 24 hours. Calls made without a valid token will result in a `403 Unauthorized error`.

### API documentation

You can interact with Sandbox using the preset calls available in the CPAPI collection. Read more about:

- [Detailed API documentation information on all the API calls](../docs/index.md)
- [CPAPI Collection](https://developer.adobe.com/vipmp/technical-assets/)

Additionally, you can use platforms other than Postman to interact with CPAPI.

### Sandbox Partner Portal interaction basics

The Sandbox Partner Portal (the Portal) is a website designed for viewing and interacting with data in the Sandbox environment of Adobe’s Commerce Partner API (CPAPI). The portal provides additional functionality to generate test cases and directly manipulate data, which is not available through the API or in any other environment. The portal will help develop and test your integration with CPAPI.

**Note:** The portal works only in the Sandbox environment, not in production.

**Sandbox portal URL:** [https://partnerportal-sandbox.adobe.com/](https://partnerportal-sandbox.adobe.com/)

Sign in to the portal using your Adobe ID. If you don't have one, create it. In case of any access issues, contact support at: `partnerapi-integration-support@adobe.com` for access.

![Login](./image/login.png)
