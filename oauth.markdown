# OAuth API
This API allows API clients to obtain access tokens using client credentials. This endpoint is for API clients, not logged-in users.

## Authentication
- No authentication header is required; the client provides credentials in the request body.

## Endpoints

### Issue Access Token
- **Endpoint**: `POST /oauth/token`
- **Description**: Issues a JWT access token for an API client using the client_credentials grant type.
- **Request Parameters**:
  - **Body** (`OauthTokenDTO`):
    ```json
    {
      "grant_type": "client_credentials",
      "client_id": "string",
      "client_secret": "string"
    }
    ```
    - `grant_type`: Must be `"client_credentials"` (required, string).
    - `client_id`: The public client identifier (required, string).
    - `client_secret`: The confidential client secret (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "access_token": "string",
      "token_type": "Bearer",
      "expires_in": 3600
    }
    ```
- **Errors**:
  - `400 Bad Request`: If `grant_type` is not `client_credentials` or credentials are missing.
  - `401 Unauthorized`: If the client credentials are invalid.