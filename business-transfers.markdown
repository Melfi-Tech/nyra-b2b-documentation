# Business Transfers API Documentation

## Overview
This API manages business transfer operations, including initiating transfers, performing name enquiries, and listing available banks. Transfers can be initiated via API or dashboard, with dashboard transfers requiring a 4-digit transaction PIN set via [Set Transaction Pin Endpoint](./transactions.markdown).

## Authentication
- **API Routes** (e.g., `/business/transfers`, `/business/transfers/name-enquiry`, `/business/transfers/bank/list`):
  - `x-client-id` header (lowercase, required, string).
  - `Authorization` header: `Bearer <client_secret>` (required, string).
- **User Route** (`/business/transfers/dashboard`):
  - `Authorization` header: `Bearer <jwt_token>` (required, string).

## Prerequisites
- For `POST /business/transfers/dashboard`, a 4-digit transaction PIN must be set using the [Set Transaction Pin Endpoint](./transactions.markdown).
- The source account must be a business float wallet.

## Endpoints

### Initiate Business Transfer (API)
- **Endpoint**: `POST /business/transfers`
- **Description**: Initiates a transfer from a business float wallet via API.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`BusinessTransferDTO`):
    ```json
    {
      "beneficiary": {
        "account_name": "John Doe",
        "account_number": "1234567890",
        "bank_code": "044"
      },
      "source_account_number": "9876543210",
      "amount": 1000,
      "description": "Payment for services",
      "sender_name": "Business Inc",
    }
    ```
    - `beneficiary`: Beneficiary details (required, object).
      - `account_name`: Beneficiary name (required, string).
      - `account_number`: Beneficiary account number (required, string).
      - `bank_code`: Beneficiary bank code (required, string).
    - `source_account_number`: Business float account number (required, string).
    - `amount`: Transfer amount (required, number, minimum 100).
    - `description`: Transfer narration (optional, string).
    - `sender_name`: Custom sender name (optional, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Transfer initiated successfully",
      "data": {
        "transaction_id": "tx_123456789",
        "transaction_type": "DEBIT",
        "transaction_category": "WALLET_TRANSFER",
        "transaction_reference": "TX123456789",
        "amount": -1000,
        "charge": -50,
        "description": "Business Inc transfer to John Doe",
        "created_at": "2025-07-26T04:25:00.000Z"
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### Initiate Business Transfer (Dashboard)
- **Endpoint**: `POST /business/transfers/dashboard`
- **Description**: Initiates a transfer from a business float wallet via the dashboard. Requires a 4-digit transaction PIN.
- **Authentication**: JWT (`Bearer <jwt_token>`), BusinessParentWalletGuard
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Body** (`BusinessDashboardTransferDto`):
    ```json
    {
      "beneficiary": {
        "account_name": "John Doe",
        "account_number": "1234567890",
        "bank_code": "044"
      },
      "source_account_number": "9876543210",
      "amount": 1000,
      "description": "Payment for services",
      "sender_name": "Business Inc",
      "wallet_pin": "1234"
    }
    ```
    - `beneficiary`: Beneficiary details (required, object, same as above).
    - `source_account_number`: Business float account number (required, string).
    - `amount`: Transfer amount (required, number, minimum 100).
    - `description`: Transfer narration (optional, string).
    - `sender_name`: Custom sender name (optional, string).
    - `wallet_pin`: 4-digit PIN (required, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**: Same as API transfer response.
- **Errors**:
  - 400 Bad Request: Invalid PIN, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid JWT token or missing PIN.
  - 404 Not Found: Business or wallet not found.

### Name Enquiry
- **Endpoint**: `POST /business/transfers/name-enquiry`
- **Description**: Queries the account name for a beneficiary account number and bank code.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `account_number`: Beneficiary account number (required, string, e.g., `1234567890`).
    - `bank_code`: Bank code (required, string, e.g., `044`).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Name enquiry successful",
      "data": {
        "account": {
          "number": "1234567890",
          "name": "John Doe"
        },
        "sessionId": "session_12345",
        "bank_name": "Access Bank"
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid account number or bank code.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Account not found.

### List Available Banks
- **Endpoint**: `GET /business/transfers/bank/list`
- **Description**: Retrieves a list of banks available for transfers.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Banks listed successfully",
      "data": [
        {
          "bank_code": "044",
          "bank_name": "Access Bank"
        },
        {
          "bank_code": "011",
          "bank_name": "First Bank"
        }
      ]
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid request.
  - 401 Unauthorized: Invalid client credentials.