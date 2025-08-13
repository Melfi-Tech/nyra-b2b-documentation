# API Client Management API
This API allows logged-in users to manage API clients for their businesses. All endpoints require JWT authentication for a logged-in user.

## Authentication
- **AuthGuard('jwt')**: All endpoints require a valid JWT token in the `Authorization` header as `Bearer <token>`.

## Endpoints

### Create or Regenerate API Client
- **Endpoint**: `POST /business/:businessId/api-clients`
- **Description**: Creates a new API client for the specified business or regenerates the client secret if one already exists for the given environment. The client secret is returned only in this response and should be securely stored by the user.
- **Request Parameters**:
  - **Path**:
    - `businessId`: The ID of the business (required, string).
  - **Body** (`CreateApiClientDTO`):
    ```json
    {
      "app_name": "string"
    }
    ```
    - `app_name`: The name of the application or service (required, string).
- **Response**:
  - **Status**: `201 Created`
  - **Body**:
    ```json
    {
      "message": "API client created successfully. Please save your secret key, it will not be shown again." | "API client secret regenerated successfully. Please save your new secret key, it will not be shown again.",
      "data": {
        "client_id": "string",
        "client_secret": "string"
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the request payload is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business is not found.

### Get API Client
- **Endpoint**: `GET /business/:businessId/api-clients`
- **Description**: Retrieves the API client details for the specified business. The client secret is not included in this response for security reasons.
- **Request Parameters**:
  - **Path**:
    - `businessId`: The ID of the business (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "message": "API client retrieved successfully.",
      "data": {
        "appName": "string",
        "clientId": "string",
        "environment": "LIVE" | "TEST",
        "isActive": boolean,
        "lastUsedAt": "string" | null,
        "businessId": "string",
        "businessName": "string",
        "createdAt": "string",
        "_v": number
      }
    }
    ```
- **Errors**:
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business or API client is not found.