# Business Airtime & Data Bills API Documentation

## Overview
This API manages business airtime and data purchases, including network lookup, data plan retrieval, and processing purchases. Airtime and data purchases can be initiated via API or dashboard, with dashboard purchases requiring a 4-digit transaction PIN set via [Set Transaction Pin Endpoint](./transactions.markdown).

## Authentication
- **API Routes** (e.g., `/business/bills/airtime-data/phone-number/network`, `/business/bills/airtime-data/data/data-plans`, `/business/bills/airtime-data/airtime/purchase`, `/business/bills/airtime-data/data/purchase`):
  - `x-client-id` header (lowercase, required, string).
  - `Authorization` header: `Bearer <client_secret>` (required, string).
- **Dashboard Routes** (`/business/bills/airtime-data/airtime/purchase/dashboard`, `/business/bills/airtime-data/data/purchase/dashboard`):
  - `Authorization` header: `Bearer <jwt_token>` (required, string).

## Prerequisites
- For dashboard purchase endpoints, a 4-digit transaction PIN must be set using the [Set Transaction Pin Endpoint](./transactions.markdown).
- The source account must be a business float wallet.

## Endpoints

### Get Network for Phone Number
- **Endpoint**: `GET /business/bills/airtime-data/phone-number/network?phone_number=08012345678`
- **Description**: Retrieves the network provider for a given phone number.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `phone_number`: Phone number to lookup (required, string, Nigerian format).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Network retrieved successfully",
      "data": {
        "network": "MTN",
        "network_id": "1"
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid phone number format.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Network not found.

### Get Data Plans
- **Endpoint**: `GET /business/bills/airtime-data/data/data-plans?network=MTN`
- **Description**: Retrieves available data plans for a network provider.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `network`: Network provider (required, string, e.g., MTN, Airtel, Glo, 9mobile).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Data plans retrieved successfully",
      "data": [
        {
          "id": "1",
          "name": "1GB - 30 Days",
          "amount": "1000",
          "validity": "30 days"
        },
        {
          "id": "2",
          "name": "2GB - 30 Days",
          "amount": "1500",
          "validity": "30 days"
        }
      ]
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid network provider.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Data plans not found.

### Purchase Airtime (API)
- **Endpoint**: `POST /business/bills/airtime-data/airtime/purchase`
- **Description**: Processes airtime purchase via API.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`BusinessAirtimePurchaseDTO`):
    ```json
    {
      "phone_number": "08012345678",
      "network": "MTN",
      "amount": 1000,
      "source_account_number": "9016997676",
      "description": "Airtime purchase for customer"
    }
    ```
    - `phone_number`: Phone number to purchase airtime for (required, string, Nigerian format).
    - `network`: Network provider (required, string, e.g., MTN, Airtel, Glo, 9mobile).
    - `amount`: Amount of airtime to purchase (required, number, minimum 100).
    - `source_account_number`: Business float account number (required, string).
    - `description`: Purchase description (optional, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Airtime purchase successful",
        "data": {
            "transaction_id": "TXN-01K1XDZEX2XADB254DPEJJGBE0",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": "429225.60",
            "balance_after": 428225.6,
            "transaction_reference": "TXN-01K1XDZEX2XADB254DPEJJGBE0",
            "description": "PICKSON airtime purchase for 08012345678",
            "amount": "-1000.00",
            "channel": "bank_transfer",
            "mode": "electronic",
            "charge": "0.00",
            "meta": {
                "data": {
                    "source": "api",
                    "business_id": "65e6e6d18d41d876944861da",
                    "phone_number": "08012345678",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T14:35:40.637Z",
            "updated_at": "2025-08-05T14:35:40.637Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### Purchase Data (API)
- **Endpoint**: `POST /business/bills/airtime-data/data/purchase`
- **Description**: Processes data purchase via API.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`BusinessDataPurchaseDTO`):
    ```json
    {
      "phone_number": "08012345678",
      "network": "MTN",
      "bundle_id": "1",
      "amount": 1000,
      "source_account_number": "9016997676",
      "description": "Data purchase for customer"
    }
    ```
    - `phone_number`: Phone number to purchase data for (required, string, Nigerian format).
    - `network`: Network provider (required, string, e.g., MTN, Airtel, Glo, 9mobile).
    - `bundle_id`: Data bundle ID (required, string).
    - `amount`: Amount for data purchase (required, number, minimum 100).
    - `source_account_number`: Business float account number (required, string).
    - `description`: Purchase description (optional, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Data purchase successful",
        "data": {
            "transaction_id": "TXN-01K1VVB97X0FC5B0XPN4JX47E8",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": 429225.6,
            "balance_after": 427725.6,
            "transaction_reference": "TXN-01K1VVB97X0FC5B0XPN4JX47E8",
            "description": "PICKSON data purchase for 08012345678",
            "amount": "-1500.00",
            "channel": "bank_transfer",
            "mode": "electronic",
            "charge": "0.00",
            "meta": {
                "data": {
                    "source": "api",
                    "bundle_id": "bundle_123",
                    "business_id": "65e6e6d18d41d876944861da",
                    "phone_number": "08012345678",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T14:35:40.637Z",
            "updated_at": "2025-08-05T14:35:40.637Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### Purchase Airtime (Dashboard)
- **Endpoint**: `POST /business/bills/airtime-data/airtime/purchase/dashboard`
- **Description**: Processes airtime purchase via the dashboard. Requires a 4-digit transaction PIN.
- **Authentication**: JWT (`Bearer <jwt_token>`), BusinessParentWalletGuard
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Body** (`BusinessAirtimePurchaseDashboardDTO`):
    ```json
    {
      "phone_number": "08012345678",
      "network": "MTN",
      "amount": 1000,
      "source_account_number": "9016997676",
      "description": "Airtime purchase for customer",
      "wallet_pin": "1234"
    }
    ```
    - `phone_number`: Phone number to purchase airtime for (required, string, Nigerian format).
    - `network`: Network provider (required, string, e.g., MTN, Airtel, Glo, 9mobile).
    - `amount`: Amount of airtime to purchase (required, number, minimum 100).
    - `source_account_number`: Business float account number (required, string).
    - `description`: Purchase description (optional, string).
    - `wallet_pin`: 4-digit PIN (required, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Airtime purchase successful",
        "data": {
            "transaction_id": "TXN-01K1XDZEX2XADB254DPEJJGBE0",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": "429225.60",
            "balance_after": 428225.6,
            "transaction_reference": "TXN-01K1XDZEX2XADB254DPEJJGBE0",
            "description": "PICKSON airtime purchase for 08012345678",
            "amount": "-1000.00",
            "channel": "bank_transfer",
            "mode": "electronic",
            "charge": "0.00",
            "meta": {
                "data": {
                    "source": "dashboard",
                    "business_id": "65e6e6d18d41d876944861da",
                    "phone_number": "08012345678",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T14:35:40.637Z",
            "updated_at": "2025-08-05T14:35:40.637Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid PIN, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid JWT token or missing PIN.
  - 404 Not Found: Business or wallet not found.

### Purchase Data (Dashboard)
- **Endpoint**: `POST /business/bills/airtime-data/data/purchase/dashboard`
- **Description**: Processes data purchase via the dashboard. Requires a 4-digit transaction PIN.
- **Authentication**: JWT (`Bearer <jwt_token>`), BusinessParentWalletGuard
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Body** (`BusinessDataPurchaseDashboardDTO`):
    ```json
    {
      "phone_number": "08012345678",
      "network": "MTN",
      "bundle_id": "1",
      "amount": 1000,
      "source_account_number": "9016997676",
      "description": "Data purchase for customer",
      "wallet_pin": "1234"
    }
    ```
    - `phone_number`: Phone number to purchase data for (required, string, Nigerian format).
    - `network`: Network provider (required, string, e.g., MTN, Airtel, Glo, 9mobile).
    - `bundle_id`: Data bundle ID (required, string).
    - `amount`: Amount for data purchase (required, number, minimum 100).
    - `source_account_number`: Business float account number (required, string).
    - `description`: Purchase description (optional, string).
    - `wallet_pin`: 4-digit PIN (required, string).
- **Response**:
  - **Status**: 201 Created
  - **Body**:
    ```json
    {
        "success": true,
        "message": "Data purchase successful",
        "data": {
            "transaction_id": "TXN-01K1VVB97X0FC5B0XPN4JX47E8",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": 429225.6,
            "balance_after": 427725.6,
            "transaction_reference": "TXN-01K1VVB97X0FC5B0XPN4JX47E8",
            "description": "PICKSON data purchase for 08012345678",
            "amount": "-1500.00",
            "channel": "bank_transfer",
            "mode": "electronic",
            "charge": "0.00",
            "meta": {
                "data": {
                    "source": "dashboard",
                    "bundle_id": "bundle_123",
                    "business_id": "65e6e6d18d41d876944861da",
                    "phone_number": "08012345678",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T14:35:40.637Z",
            "updated_at": "2025-08-05T14:35:40.637Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid PIN, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid JWT token or missing PIN.
  - 404 Not Found: Business or wallet not found. 
