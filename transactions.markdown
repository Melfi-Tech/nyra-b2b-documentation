# Business Transactions API Documentation

## Overview
This API manages transaction-related operations for business wallets, including setting and updating a 4-digit transaction PIN required for dashboard-initiated transfers [Dashboard Transfer](./business-transfers.markdown). A PIN must be set before dashboard transactions can proceed.

## Authentication
- **User Routes**: Require JWT token in `Authorization` header (`Bearer <jwt_token>`).

## Prerequisites
- A 4-digit transaction PIN must be set via `POST /business/:businessId/wallet/create-pin` before initiating dashboard transactions.
- PINs are validated as 4-digit numbers (e.g., `1234`).

## Endpoints

### Create Transaction PIN
- **Endpoint**: `POST /business/:businessId/wallet/create-pin`
- **Description**: Sets a 4-digit transaction PIN for the business wallet, required for dashboard transfers.
- **Authentication**: JWT (`Bearer <jwt_token>`)
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Path**:
    - `businessId`: Business ID (required, string).
  - **Body** (`CreateTransactionPinDTO`):
    ```json
    {
      "new_pin": "1234"
    }
    ```
    - `new_pin`: 4-digit PIN (required, string, must be exactly 4 digits).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "pin created successfully!",
      "data": true
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid PIN format (must be 4 digits).
  - 401 Unauthorized: Invalid JWT token.
  - 404 Not Found: Business not found.

### Update Transaction PIN
- **Endpoint**: `POST /business/:businessId/wallet/update-pin`
- **Description**: Updates the 4-digit transaction PIN for the business wallet. Requires control panel settings (`CHANGE_PIN`) to be enabled.
- **Authentication**: JWT (`Bearer <jwt_token>`), Control Panel Guard
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Path**:
    - `businessId`: Business ID (required, string).
  - **Body** (`UpdateTransactionPinDTO`):
    ```json
    {
      "old_pin": "1234",
      "new_pin": "5678"
    }
    ```
    - `old_pin`: Current 4-digit PIN (required, string, must be exactly 4 digits).
    - `new_pin`: New 4-digit PIN (required, string, must be exactly 4 digits).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "pin updated successfully!",
      "data": true
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid PIN format or incorrect old PIN.
  - 401 Unauthorized: Invalid JWT token or control panel settings not enabled.
  - 404 Not Found: Business not found.