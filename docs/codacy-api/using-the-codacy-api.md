# Using the Codacy API

The Codacy API allows you to programmatically retrieve and analyze data from Codacy and perform a few configuration changes.

Codacy supports two API versions but we strongly recommend using the new API v3 when possible since it's the version being actively developed. Import the OpenAPI 2.0 definition provided below into your development tools to help bootstrap your integration with Codacy.

<table>
  <thead>
    <tr>
      <td></td>
      <th><strong>API v3 (recommended)</strong></th>
      <th><strong>API v2</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Endpoint documentation</th>
      <td><a href="https://api.codacy.com/api/api-docs">https://api.codacy.com/api/api-docs</a></td>
      <td><a href="https://api.codacy.com/api-docs">https://api.codacy.com/api-docs</a></td>
    </tr>
    <tr>
      <th>OpenAPI 2.0 definition</th>
      <td><a href="https://api.codacy.com/api/api-docs/swagger.yaml">https://api.codacy.com/api/api-docs/swagger.yaml</a></td>
      <td>-
          <!--NOTE
              See https://github.com/codacy/docs/pull/1058#discussion_r810889139 for why we decided to postpone publishing the definition file URL for the API v2.
              <a href="https://api.codacy.com/api-docs/swagger.yaml">https://api.codacy.com/api-docs/swagger.yaml</a>
          --></td>
    </tr>
    <tr>
      <th>Base URL</th>
      <td><code>https://api.codacy.com/api/v3</code></td>
      <td><code>https://api.codacy.com/</code></td>
    </tr>
    <tr>
      <th>Overview</th>
      <td>
        <p>Use the new endpoints to access and manipulate the following resources, among others:<p>
        <ul>
          <li><a href="https://api.codacy.com/api/api-docs#codacy-api-analysis">Analysis</a> details, issue and ignored issue details, repository quality settings</li>
          <li><a href="https://api.codacy.com/api/api-docs#codacy-api-account">Account</a> details and API token management</li>
          <li><a href="https://api.codacy.com/api/api-docs#codacy-api-organization">Organization</a> details and join request management</li>
          <li><a href="https://api.codacy.com/api/api-docs#codacy-api-people">People</a> management</li>
          <li><a href="https://api.codacy.com/api/api-docs#codacy-api-repository">Repository</a> management and file details</li>
          <li><a href="https://api.codacy.com/api/api-docs#codacy-api-tools">Tool</a> and code pattern details</li>
        </ul>
      </td>
      <td>
        <p>Use the legacy endpoints to access and manipulate the following resources:</p>
          <ul>
            <li><a href="https://api.codacy.com/swagger#codacy-api-commit">Commit</a> code quality details and deltas</li>
            <li><a href="https://api.codacy.com/swagger#codacy-api-project">Project</a> details and configurations, file code quality and issue details</li>
          </ul>
      </td>
    </tr>
  </tbody>
</table>

!!! important
    **If you're using Codacy Self-hosted** you must use your own Codacy instance domain name in the API URLs to access the endpoint documentation matching your Codacy Self-hosted version and to call the endpoints on your Codacy instance.

    For example, use the following URLs for the API v3 endpoint documentation and endpoints:

    ```
    https://<your Codacy instance domain name>/api/api-docs
    
    https://<your Codacy instance domain name>/api/v3
    ```

## Authenticating requests

Most API endpoints require that you authenticate using an API token. After [obtaining the necessary tokens](api-tokens.md), include them in your request headers using the format `api-token: <your account API token>` or `project-token: <your project API token>`.

!!! note
    Currently, all API v3 endpoints that require authentication must use **account API tokens**, while the API v2 endpoints require either **account or project API tokens**.

    Performing `GET` requests for public repositories doesn't require authentication.

For example, to make a request to an API v3 endpoint that requires an account API token:

```bash
curl -X GET 'https://api.codacy.com/api/v3/user/organizations/gh' \
     -H 'api-token: <your account API token>'
```

Or to make a request to an API v2 endpoint that requires a project API token:

```bash
curl -X GET 'https://api.codacy.com/2.0/commit/da275c14ffab6e402dcc6009828067ffa44b7ee0' \
     -H 'project-token: <your project API token>'
```

## Using parameters in requests

Most API endpoints require that you specify parameters.

**For `GET` requests**, specify parameters directly as path segments of the endpoint URLs. Some endpoints also accept optional query string parameters.

For example, to call the endpoint [getRepositoryWithAnalysis](https://api.codacy.com/api/api-docs#getrepositorywithanalysis) with the parameters:

-   provider: `gh`
-   remoteOrganizationName: `codacy`
-   repositoryName: `docs`
-   branch (query string): `api-overview`

```bash
curl -X GET 'https://app.codacy.com/api/v3/analysis/organizations/gh/codacy/repositories/docs?branch=api-overview' \
     -H 'api-token: <your account API token>'
```

**For `POST`, `PATCH`, and `DELETE` requests**, besides the parameters included in the URL you may also need to include a JSON body.

For example, to call the endpoint [searchRepositoryIssues](https://api.codacy.com/api/api-docs#searchrepositoryissues) specifying the issue levels `Error` and `Warning` in the body:

```bash
curl -X POST 'https://app.codacy.com/api/v3/analysis/organizations/gh/codacy/repositories/docs/issues/search' \
     -H 'api-token: <your account API token>' \
     -H 'Content-Type: application/json' \
     -d '{"levels": ["Error", "Warning"]}'
```

## Using pagination

Endpoints that return lists containing a potential large number of results use cursor-based pagination to return the results in small batches.

These endpoints return the results together with a `pagination` object:

-   If the `pagination` object **includes** a `cursor`, obtain the next page of results by calling the endpoint again using the `cursor` from the previous response as a query string parameter

    If the `pagination` object **doesn't include** a `cursor`, the endpoint returned the last page of results

-   Use the query string parameter `limit` to configure the number of results that the endpoint returns in each page. The maximum `limit` is 1000 and the default is 100
-   The `total` is the total number of results that the endpoint can return

!!! note
    To make sure that you receive all results when calling an endpoint with pagination, repeat the process above until the response doesn't include the cursor to obtain another page of results.

For example, the following command requests the first 10 repositories in the Codacy GitHub organization:

```bash
curl -X GET 'https://app.codacy.com/api/v3/organizations/gh/codacy/repositories?limit=10'
     -H 'api-token: <your account API token>'
```

The response includes the first 10 results, as well as the cursor to obtain the next page of results:

```json
{
  "data": [
    ...
  ],
  "pagination": {
      "cursor": "codacy_2",
      "limit": 10,
      "total": 156
  }
}
```

To obtain the next page of results, it's necessary to include the `cursor` from the previous page as a parameter:

```bash
curl -X GET 'https://app.codacy.com/api/v3/organizations/gh/codacy/repositories?limit=10&cursor=codacy_2'
     -H 'api-token: <your account API token>'
```

If you continue requesting more pages the endpoint will eventually return a `pagination` object that doens't include a `cursor`. This means that you've reached the last page of results:

```json
{
  "data": [
    ...
  ],
  "pagination": {
      "limit": 10,
      "total": 156
  }
}
```

## Request rate limit

**On Codacy Cloud** the number of requests that you can perform to the Codacy API is rate limited to help us provide a reliable service:

-   The limit is **2500 requests per 5 minutes and per source IP address**
-   When a request is rate limited, Codacy responds with an HTTP 503 or 504 error code and you should wait before attempting the request again

Although it's possible for you to perform short bursts of requests to the Codacy API, you should always use a delay between requests to ensure that your API client doesn't hit the rate limits.

The request rate limit doesn't apply to Codacy Self-hosted.
