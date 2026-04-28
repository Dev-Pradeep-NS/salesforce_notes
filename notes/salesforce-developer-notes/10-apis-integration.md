# 10 - APIs and Integration (Detailed)

## Learning goals
- Understand Salesforce API landscape and integration choices.
- Build custom Apex REST endpoints with clear contracts.
- Implement secure and testable outbound callouts.

## Why APIs matter
- Sync Salesforce with ERP, billing, support, and analytics systems.
- Expose Salesforce business capabilities externally.
- Enable event-driven and near-real-time enterprise workflows.

## Salesforce API types (quick map)
- REST API: modern JSON-first CRUD/query integrations.
- SOAP API: enterprise/WSDL-heavy scenarios.
- Bulk API: high-volume async data loads.
- Metadata API: deploy/customization operations.
- Tooling API: dev-tooling and metadata introspection.

## REST fundamentals (clean structure)
- Resource-oriented URLs (for example `/v1/accounts`).
- Stateless communication.
- JSON request/response as default exchange format.
- Status codes communicate outcome (`200`, `201`, `400`, `401`, `404`, `500`).

## HTTP methods (core)
- `GET`: read data
- `POST`: create data
- `PUT/PATCH`: update data
- `DELETE`: remove data

## Apex REST endpoint
```apex
@RestResource(urlMapping='/v1/accounts/*')
global with sharing class AccountApi {
    @HttpGet
    global static List<Account> getAccounts() {
        return [SELECT Id, Name, Industry FROM Account LIMIT 50];
    }
}
```

POST pattern:
```apex
@HttpPost
global static Id createAccount() {
    RestRequest req = RestContext.request;
    Map<String, Object> body = (Map<String, Object>)JSON.deserializeUntyped(req.requestBody.toString());
    Account a = new Account(Name = (String)body.get('name'));
    insert a;
    return a.Id;
}
```

## Outbound callout basics
```apex
HttpRequest req = new HttpRequest();
req.setEndpoint('callout:ExternalService/orders');
req.setMethod('GET');
req.setTimeout(120000);
HttpResponse res = new Http().send(req);
Integer statusCode = res.getStatusCode();
```

## Request/response design tips
- Validate request body fields before DML.
- Return consistent response wrapper with `success`, `message`, `data`.
- Do not leak internal exception stack traces in API responses.

## Authentication (high-level)
- Use OAuth-based Named Credentials for modern integrations.
- Avoid hardcoding credentials in Apex.
- Choose per-integration principal strategy based on security requirements.

## Security and reliability rules
- Use Named Credentials (never hardcode secrets).
- Validate and sanitize inbound payloads.
- Return consistent error shape and HTTP status.
- Add retries/backoff where business-safe.

## Testing callouts
- Use `HttpCalloutMock` with `Test.setMock()`.
- Verify both success and failure status handling.

## Common mistakes
- Directly exposing internal exception messages in API response.
- No idempotency strategy for repeated requests.
- No structured logging/correlation IDs.

## Interview quick Q&A
- **Q:** REST vs SOAP in Salesforce integrations?
- **A:** REST is lightweight/JSON and generally preferred; SOAP used in legacy contract-heavy systems.

- **Q:** Why Named Credentials?
- **A:** Secure auth management and endpoint abstraction without hardcoded tokens.

## Practice set
1. Build REST POST endpoint returning response wrapper (`success`, `message`, `data`).
2. Add validation and custom error response for missing required fields.
3. Implement and test callout service with mock success and 500 error cases.
