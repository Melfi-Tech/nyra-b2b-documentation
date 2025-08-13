# Temporary Accounts API Documentation

## Overview
The Temporary Accounts API enables businesses to create and manage one-time virtual accounts for funding business wallets. These accounts are temporary, with a default expiration of 15 minutes (900 seconds), and are used to receive funds from external sources. Once funded, a webhook notification is sent if the business is subscribed to the `managed_wallet.temporary_account_funded` event (see [Webhooks](./webhooks.markdown#WebhookEvents)).

## Authentication
- **Endpoints**: All endpoints require API client authentication.
  - **Headers**:
    - `x-client-id`: Client ID (required, string, lowercase).
    - `Authorization`: `Bearer <client_secret>` (required, string).

## Endpoints

### Create Temporary Account
- **Endpoint**: `POST /business/wallets/temporary-account`
- **Description**: Creates a one-time virtual account for funding a business wallet. The account is valid for a specified duration (default 900 seconds) and includes a transfer fee (Contact support for more information). The `external_reference` must be unique.
- **Authentication**: API client (`x-client-id`, `Bearer <client_secret>`).
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`GetTemporaryAccountForFundingDTO`):
    ```json
    {
      "external_reference": "LIM45699343399438",
      "amount": 5000,
      "expiresIn": 900,
      "meta": {
        "customer_name": "John Doe",
        "customer_email": "john.doe@example.com"
      }
    }
    ```
    - `external_reference`: Unique reference for the account (required, string, 10-26 characters).
    - `amount`: Expected funding amount in NGN (required, number, minimum 300).
    - `expiresIn`: Account validity in seconds (optional, number, default 900).
    - `meta`: Additional metadata (optional, object).
      - `customer_name`: Customer name (optional, string).
      - `customer_email`: Customer email (optional, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Temporary account created successfully",
      "data": {
        "id": "01K22HK0MGGAPQFBNV0T045CBW",
        "business": "9718b4b64b70add2215210c7",
        "account_number": "6029414052",
        "account_name": "Nyra Wallet Checkout",
        "bank_name": "Safe Haven MFB",
        "amount": 5090.00,
        "bank_code": "090286",
        "expiry_date": "2025-08-07T16:51:00.000Z",
        "status": "pending",
        "expiresIn": 900,
        "external_reference": "LIM45699343399438",
        "created_at": "2025-08-07T16:36:00.000Z",
        "meta": {
          "customer_name": "John Doe",
          "customer_email": "john.doe@example.com"
        }
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: Invalid payload or duplicate `external_reference`.
  - `401 Unauthorized`: Invalid client credentials.
  - `500 Internal Server Error`: Failed to create the account.

### Get Temporary Account
- **Endpoint**: `GET /business/wallets/temporary-account/:id`
- **Description**: Retrieves details of a specific temporary account by its ID.
- **Authentication**: API client (`x-client-id`, `Bearer <client_secret>`).
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Path**:
    - `id`: Temporary account ID (required, string, UUID).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Temporary account fetched successfully",
      "data": {
        "id": "01K22HK0MGGAPQFBNV0T045CBW",
        "account_number": "6029414052",
        "account_name": "Nyra Wallet Checkout",
        "bank_name": "Safe Haven MFB",
        "amount": 5090.00,
        "bank_code": "090286",
        "expiry_date": "2025-08-07T16:51:00.000Z",
        "status": "pending",
        "expiresIn": 900,
        "external_reference": "LIM45699343399438",
        "created_at": "2025-08-07T16:36:00.000Z",
        "meta": {
          "customer_name": "John Doe",
          "customer_email": "john.doe@example.com"
        }
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: Missing or invalid `id`.
  - `401 Unauthorized`: Invalid client credentials.
  - `404 Not Found`: Temporary account not found.
  - `500 Internal Server Error`: Failed to retrieve the account.

### Get Temporary Account Transfer Status
- **Endpoint**: `GET /business/wallets/temporary-account/:sessionId/status`
- **Description**: Retrieves the transfer status of a temporary account by its session ID.
- **Authentication**: API client (`x-client-id`, `Bearer <client_secret>`).
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Path**:
    - `sessionId`: Session ID of the transfer (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Completed successfully",
      "data": {
        "id": "68714b6cc0f8770024c37a90",
        "business": "9718b4b64b70add2215210c7",
        "virtualAccount": {
          "id": "01K22HK0MGGAPQFBNV0T045CBW",
          "account_number": "6029414052",
          "account_name": "Nyra Wallet Checkout",
          "bank_name": "Safe MFB",
          "amount": 5090.00,
          "bank_code": "090286",
          "expiry_date": "2025-08-07T16:51:00.000Z",
          "status": "successful",
          "expiresIn": 900,
          "external_reference": "LIM45699343399438",
          "created_at": "2025-08-07T16:36:00.000Z",
          "meta": {
            "customer_name": "John Doe",
            "customer_email": "john.doe@example.com"
          }
        },
        "credit_account_number": "6025123067",
        "credit_account_name": "Nyra Wallet Checkout",
        "amount_paid": 5000,
        "external_reference": "LIZ45699343399438",
        "currency": "NGN",
        "provider": "NYRA WALLET",
        "narration": "Transfer from JANE DOE",
        "paid_at": "2025-08-07T16:45:00.000Z",
        "sender_bank": "Safe Haven MFB",
        "sender_name": "JANE DOE",
        "sender_account_number": "9138639500",
        "status": "Completed"
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: Missing or invalid `sessionId`.
  - `401 Unauthorized`: Invalid client credentials.
  - `404 Not Found`: Temporary account or session not found.
  - `500 Internal Server Error`: Failed to retrieve the status.

## Webhook Notification
When a temporary account is funded, a webhook is dispatched if the business is subscribed to the `managed_wallet.temporary_account_funded` event. The webhook payload includes details of the funding transaction. For more information, see [Temporary Account Funded Webhook](./webhooks.markdown#webhook-events).


## Best Practices
- **Unique References**: Ensure `external_reference` is unique to avoid conflicts.
- **Expiration Handling**: Inform users of the account's expiration time (`expiresIn`) to ensure timely funding.
- **Webhook Subscription**: Subscribe to the `managed_wallet.temporary_account_funded` event via [Create Webhook Configuration](./webhooks.markdown#CreateWebhookConfiguration) to receive funding notifications.
- **Status Monitoring**: Use [Get Temporary Account Transfer Status Endpoint](#get-temporary-account-transfer-status) to check the funding status periodically.
- **Error Handling**: Handle `400` and `404` errors gracefully, especially for duplicate references or expired accounts.