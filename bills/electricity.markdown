# Business Electricity Bills API Documentation

## Overview
This API manages business electricity bill payments, including validating meter data, retrieving provider options, and processing payments. Electricity payments can be initiated via API or dashboard, with dashboard payments requiring a 4-digit transaction PIN set via [Set Transaction Pin Endpoint](./transactions.markdown).

## Authentication
- **API Routes** (e.g., `/business/bills/electricity/validate`, `/business/bills/electricity/provider/items`, `/business/bills/electricity/pay`):
  - `x-client-id` header (lowercase, required, string).
  - `Authorization` header: `Bearer <client_secret>` (required, string).
- **Dashboard Routes** (`/business/bills/electricity/pay/dashboard`):
  - `Authorization` header: `Bearer <jwt_token>` (required, string).

## Prerequisites
- For `POST /business/bills/electricity/pay/dashboard`, a 4-digit transaction PIN must be set using the [Set Transaction Pin Endpoint](./transactions.markdown).
- The source account must be a business float wallet.

## Endpoints

### Validate Electricity Data
- **Endpoint**: `POST /business/bills/electricity/validate`
- **Description**: Validates electricity meter data and retrieves customer information.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`ElectricityValidateInputDTO`):
    ```json
    {
      "biller_id": "1",
      "payment_type_id": "VT01",
      "meter_number": "12345678901",
      "amount": "5000"
    }
    ```
    - `biller_id`: Electricity provider ID (required, string).
    - `payment_type_id`: Payment type ID (required, string).
    - `meter_number`: Meter number (required, string, minimum 10 digits).
    - `amount`: Amount to validate (optional, string).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Electricity data validated successfully",
        "data": {
            "customer_name": "John Doe",
            "customer_info": "Test Customer",
            "outstanding": "1500",
            "amount": "5000"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid meter number or biller ID.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Meter not found.

### Get Electricity Provider Items
- **Endpoint**: `GET /business/bills/electricity/provider/items?biller_id=1`
- **Description**: Retrieves available payment types for an electricity provider.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `biller_id`: Electricity provider ID (required, string).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Electricity provider items retrieved successfully",
        "data": {
            "payment_type_id": {
                "items": [
                    {
                        "label": "PREPAID",
                        "id": "VT01",
                        "amount": 0
                    },
                    {
                        "label": "POSTPAID",
                        "id": "VT02",
                        "amount": 0
                    }
                ]
            }
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid biller ID.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Provider not found.

### Pay Electricity Bill (API)
- **Endpoint**: `POST /business/bills/electricity/pay`
- **Description**: Processes electricity bill payment via API.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`BusinessElectricityPaymentDTO`):
    ```json
    {
      "biller_id": "1",
      "payment_type_id": "VT01",
      "meter_number": "12345678901",
      "amount": 5000,
      "phone_number": "08012345678",
      "customer_name": "John Doe",
      "source_account_number": "1234567890",
      "description": "Electricity payment for July"
    }
    ```
    - `biller_id`: Electricity provider ID (required, string).
    - `payment_type_id`: Payment type ID (required, string).
    - `meter_number`: Meter number (required, string, minimum 10 digits).
    - `amount`: Payment amount (required, number, minimum 100).
    - `phone_number`: Contact phone number (optional, string).
    - `customer_name`: Customer name (required, string).
    - `source_account_number`: Business float account number (required, string).
    - `description`: Payment description (optional, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Electricity payment successful",
        "data": {
            "transaction_id": "TXN-01K1WZSME40SNY9F0HXEM3BW8H",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": "429225.60",
            "balance_after": 424225.6,
            "transaction_reference": "TXN-01K1WZSME40SNY9F0HXEM3BW8H",
            "description": "PICKSON electricity payment for 12345678901",
            "amount": "-5000.00",
            "channel": "bank_transfer",
            "payment_provider": "9PSB",
            "mode": "electronic",
            "charge": "0.00",
            "provider_charge": "0.00",
            "meta": {
                "data": {
                    "source": "api",
                    "biller_id": "1",
                    "business_id": "65e6e6d18d41d876944861da",
                    "meter_number": "12345678901",
                    "phone_number": "08012345678",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T10:27:49.667Z",
            "updated_at": "2025-08-05T10:27:49.667Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### Pay Electricity Bill (Dashboard)
- **Endpoint**: `POST /business/bills/electricity/pay/dashboard`
- **Description**: Processes electricity bill payment via the dashboard. Requires a 4-digit transaction PIN.
- **Authentication**: JWT (`Bearer <jwt_token>`), BusinessParentWalletGuard
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Body** (`BusinessElectricityPaymentDashboardDTO`):
    ```json
    {
      "biller_id": "1",
      "payment_type_id": "VT01",
      "meter_number": "12345678901",
      "amount": 5000,
      "phone_number": "08012345678",
      "customer_name": "John Doe",
      "source_account_number": "1234567890",
      "description": "Electricity payment for July",
      "wallet_pin": "1234"
    }
    ```
    - `biller_id`: Electricity provider ID (required, string).
    - `payment_type_id`: Payment type ID (required, string).
    - `meter_number`: Meter number (required, string, minimum 10 digits).
    - `amount`: Payment amount (required, number, minimum 100).
    - `phone_number`: Contact phone number (optional, string).
    - `customer_name`: Customer name (required, string).
    - `source_account_number`: Business float account number (required, string).
    - `description`: Payment description (optional, string).
    - `wallet_pin`: 4-digit PIN (required, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Electricity payment successful",
        "data": {
            "transaction_id": "TXN-01K1XZ7Z46AM8JFKK4CGCB0BQX",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": "429225.60",
            "balance_after": 424225.6,
            "transaction_reference": "TXN-01K1XZ7Z46AM8JFKK4CGCB0BQX",
            "description": "PICKSON electricity payment for 12345678901",
            "amount": "-5000.00",
            "channel": "bank_transfer",
            "payment_provider": "9PSB",
            "mode": "electronic",
            "charge": "0.00",
            "provider_charge": "0.00",
            "meta": {
                "data": {
                    "source": "dashboard",
                    "biller_id": "1",
                    "business_id": "65e6e6d18d41d876944861da",
                    "meter_number": "12345678901",
                    "phone_number": "08012345678",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T19:37:26.234Z",
            "updated_at": "2025-08-05T19:37:26.234Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid PIN, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid JWT token or missing PIN.
  - 404 Not Found: Business or wallet not found. 
