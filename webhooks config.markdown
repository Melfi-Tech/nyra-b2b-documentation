# Webhooks API
This API allows logged-in users and API clients to manage webhook configurations for businesses and resend webhook notifications for specific transactions. Webhooks are used to receive real-time notifications for events like wallet funding and transfers.

## Authentication
- **Logged-in User Endpoints** (`/business/:businessId/webhooks/*`):
  - Requires a valid JWT token in the `Authorization` header as `Bearer <token>`.
- **API Client Endpoints** (`/business/1/resend-webhook/:reference`):
  - Requires the `x-client-id` header with the client ID and the `Authorization` header as `Bearer <client_secret>`.

## Webhook Payloads
Webhooks are sent to configured URLs for subscribed events (`managed_wallet.funded`, `managed_wallet.transfer`). The payload is signed with HMAC-SHA256 using the webhook’s secret for verification.

### Webhook Payload Schema
```json
{
  "event": "string", // e.g., "managed_wallet.funded" or "managed_wallet.transfer"
  "data": {
    "account_number": "string",
    "amount": number,
    "currency": "string", // e.g., "NGN"
    "narration": "string",
    "wallet_id": "string",
    "timestamp": "string", // ISO date string
    "reference": "string", // Transaction ID
    "sender_name": "string | null",
    "sender_account_number": "string | null",
    "sender_bank": "string | null",
    "business_id": "string",
    "transaction_date": "string", // ISO date string
    "credit_account_name": "string"
  }
}
```

### Supported Events
- `managed_wallet.funded`: Triggered when a wallet is funded.
- `managed_wallet.transfer`: Triggered when a transfer is made from a wallet.

## Endpoints

### List Webhook Configurations
- **Endpoint**: `GET /business/:businessId/webhooks`
- **Description**: Retrieves all webhook configurations for a specific business owned by the authenticated user. The `secret` field is not included in the response for security.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <token>` (required, string).
  - **Path**:
    - `businessId`: The ID of the business (required, string, UUID).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Webhook configurations retrieved successfully.",
      "data": [
        {
          "id": "string",
          "name": "string | null",
          "url": "string",
          "subscribed_events": ["string"],
          "created_at": "string", // ISO date string
          "updated_at": "string" // ISO date string
        }
      ]
    }
    ```
- **Errors**:
  - `401 Unauthorized`: If the JWT token is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business is not found.

### Create Webhook Configuration
- **Endpoint**: `POST /business/:businessId/webhooks`
- **Description**: Creates a new webhook configuration for a specific business owned by the authenticated user. The response includes a `secret` for HMAC-SHA256 signing, which is shown only once.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <token>` (required, string).
  - **Path**:
    - `businessId`: The ID of the business (required, string, UUID).
  - **Body** (`CreateWebhookConfigDTO`):
    ```json
    {
      "name": "string",
      "url": "string",
      "subscribed_events": ["string"]
    }
    ```
    - `name`: Descriptive name for the webhook (optional, string, max 100 characters).
    - `url`: Secure HTTPS URL for webhook payloads (required, string, valid URL).
    - `subscribed_events`: Array of event types to subscribe to (required, array of `managed_wallet.funded` or `managed_wallet.transfer`).
- **Response**:
  - **Status**: `201 Created`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Webhook configuration set successfully, Please save the secret securely, it will not be shown again.",
      "data": {
        "id": "string",
        "business_id": "string",
        "name": "string | null",
        "url": "string",
        "subscribed_events": ["string"],
        "secret": "string",
        "created_at": "string", // ISO date string
        "updated_at": "string" // ISO date string
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the request payload is invalid (e.g., invalid URL or events).
  - `401 Unauthorized`: If the JWT token is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business is not found.
  - `409 Conflict`: If a webhook with the same URL already exists for the business.

### Regenerate Webhook Secret
- **Endpoint**: `POST /business/:businessId/webhooks/:configId/regenerate-secret`
- **Description**: Regenerates the secret for a specific webhook configuration. The new secret is shown only once in the response.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <token>` (required, string).
  - **Path**:
    - `businessId`: The ID of the business (required, string, UUID).
    - `configId`: The ID of the webhook configuration (required, string, UUID).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Secret regenerated successfully. Please save this new secret, it will not be shown again.",
      "data": {
        "id": "string",
        "url": "string",
        "new_secret": "string"
      }
    }
    ```
- **Errors**:
  - `401 Unauthorized`: If the JWT token is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business or webhook configuration is not found.

### Update Webhook Configuration
- **Endpoint**: `PUT /business/:businessId/webhooks/:configId`
- **Description**: Updates an existing webhook configuration for a specific business owned by the authenticated user. The `secret` field is not included in the response.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <token>` (required, string).
  - **Path**:
    - `businessId`: The ID of the business (required, string, UUID).
    - `configId`: The ID of the webhook configuration (required, string, UUID).
  - **Body** (`UpdateWebhookConfigDTO`):
    ```json
    {
      "name": "string",
      "url": "string",
      "subscribed_events": ["string"]
    }
    ```
    - `name`: New descriptive name (optional, string, max 100 characters).
    - `url`: New secure HTTPS URL (optional, string, valid URL).
    - `subscribed_events`: New array of event types (optional, array of `managed_wallet.funded` or `managed_wallet.transfer`).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Webhook endpoint updated successfully.",
      "data": {
        "id": "string",
        "name": "string | null",
        "url": "string",
        "subscribed_events": ["string"],
        "created_at": "string", // ISO date string
        "updated_at": "string" // ISO date string
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the request payload is invalid (e.g., invalid URL or events).
  - `401 Unauthorized`: If the JWT token is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business or webhook configuration is not found.
  - `409 Conflict`: If the new URL already exists for another webhook configuration.


### Delete Webhook Configuration
- **Endpoint**: `DELETE /business/:businessId/webhooks/:configId`
- **Description**: Deletes a webhook configuration for a specific business owned by the authenticated user. Use [List Webhook Configurations](#list-webhook-configurations) to retrieve available configurations.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <token>` (required, string).
  - **Path**:
    - `businessId`: The ID of the business (required, string, UUID).
    - `configId`: The ID of the webhook configuration (required, string, UUID).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Webhook configuration deleted successfully."
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the `configId` is invalid.
  - `401 Unauthorized`: If the JWT token is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business or webhook configuration is not found.


### Resend Webhook
- **Endpoint**: `GET /business/1/resend-webhook/:reference`
- **Description**: Resends a webhook for a specific transaction to all configured webhook URLs for the business associated with the authenticated API client. Supports `managed_wallet.funded` and `managed_wallet.transfer` events.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Path**:
    - `reference`: The transaction reference (required, string, UUID).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "data": true,
      "message": "Webhook resent successfully"
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the transaction reference is invalid or the transaction category is unsupported.
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.
  - `404 Not Found`: If the transaction or business is not found.

  

  