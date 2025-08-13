# Business Wallet API
This API allows API clients to manage wallets for a business. All endpoints require authentication using a client ID and client secret. The business is identified via the authenticated API client.

## Authentication
All endpoints require:
- `x-client-id` header with the client ID.
- `Authorization` header as `Bearer <client_secret>`.

## Endpoints

### Create Wallet for Business
- **Endpoint**: `POST /business/wallets`
- **Description**: Creates a new wallet for a customer under the business associated with the authenticated API client.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`CreateWalletForBusinessCustomerDTO`):
    ```json
    {
      "first_name": "string",
      "last_name": "string",
      "name_on_account": "string",
      "middlename": "string",
      "dob": "string", // ISO date format
      "gender": "string",
      "title": "string",
      "address_line_1": "string",
      "address_line_2": "string",
      "city": "string",
      "country": "string",
      "phone_number": "string",
      "email": "string",
      "bvn": "string"
    }
    ```
    - `first_name`: Customer's first name (required, string).
    - `last_name`: Customer's last name (required, string).
    - `name_on_account`: Name on the account (optional, string).
    - `middlename`: Customer's middle name (optional, string).
    - `dob`: Date of birth in ISO format (required, string).
    - `gender`: Customer's gender (required, string).
    - `title`: Customer's title (required, string).
    - `address_line_1`: Primary address line (required, string).
    - `address_line_2`: Secondary address line (optional, string).
    - `city`: City (required, string).
    - `country`: Country (required, string).
    - `phone_number`: Valid phone number (required, string).
    - `email`: Valid email address (required, string).
    - `bvn`: Bank Verification Number (required, 11-digit string).
- **Response**:
  - **Status**: `201 Created`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Wallet created successfully",
      "data": {
        "wallet_id": "string",
        "account_number": "string",
        "bank_name": "string",
        "owners_fullname": "string",
        "frozen": boolean,
        "business_id": "string",
        "isFloat": boolean,
        "created_at": "string", // ISO date string
        "updated_at": "string" // ISO date string
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the request payload is invalid or the BVN is not an 11-digit string.
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.

### Get All Customer Sub-Wallets
- **Endpoint**: `GET /business/wallets/all`
- **Description**: Retrieves all customer sub-wallets for the business associated with the authenticated API client. Excludes business float wallets.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Sub wallets fetched successfully",
      "data": [
        {
          "wallet_id": "string",
          "account_number": "string",
          "bank_name": "string",
          "owners_fullname": "string",
          "frozen": boolean,
          "business_id": "string",
          "isFloat": boolean,
          "created_at": "string", // ISO date string
          "updated_at": "string" // ISO date string
        }
      ]
    }
    ```
- **Errors**:
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.

### Get Sub-Wallet Details
- **Endpoint**: `GET /business/wallets/:walletId`
- **Description**: Retrieves details of a specific sub-wallet (customer or float) for the business associated with the authenticated API client.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Path**:
    - `walletId`: The ID of the wallet (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Wallet fetched successfully",
      "data": {
        "wallet_id": "string",
        "account_number": "string",
        "bank_name": "string",
        "owners_fullname": "string",
        "frozen": boolean,
        "business_id": "string",
        "isFloat": boolean,
        "created_at": "string", // ISO date string
        "updated_at": "string" // ISO date string
      }
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the wallet ID is invalid.
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.
  - `404 Not Found`: If the wallet is not found.

### Get Business Wallet Balance
- **Endpoint**: `GET /business/wallets/wallet_balance`
- **Description**: Retrieves the balance of the primary wallet for the business associated with the authenticated API client.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Wallet balance fetched successfully",
      "data": {
        "businessId": "string",
        "businessName": "string",
        "balance": number
      }
    }
    ```
- **Errors**:
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.
  - `404 Not Found`: If the business or its wallet is not found.

  ### Get Business Float Wallets
- **Endpoint**: `GET /business/wallets/float`
- **Description**: Retrieves all float wallets for the business associated with the authenticated API client.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Business float wallets fetched successfully",
      "data": [
        {
          "wallet_id": "string",
          "account_number": "string",
          "bank_name": "string",
          "owners_fullname": "string",
          "frozen": boolean,
          "business_id": "string",
          "isFloat": true,
          "created_at": "string", // ISO date string
          "updated_at": "string" // ISO date string
        }
      ]
    }
    ```
- **Errors**:
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.
  - `404 Not Found`: If the business is not found.

### Get Parent Wallet Details
- **Endpoint**: `GET /business/:businessId/wallet`
- **Description**: Retrieves details of the parent wallet for a business, accessible by an authenticated user.
- **Authentication**: JWT (`Bearer <jwt_token>`)
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Path**:
    - `businessId`: Business ID (required, string).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Wallet retrieved successfully",
      "data": {
        "wallet_id": "wallet_54321",
        "account_number": "1234567890",
        "business": {
          "id": "business_12345",
          "name": "Business Inc"
        },
        "owner": {
          "user_id": "user_12345",
          "email": "user@example.com"
        },
        "frozen": false,
        "created_at": "2025-07-26T04:22:00.000Z",
        "updated_at": "2025-07-26T04:22:00.000Z"
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid `businessId`.
  - 401 Unauthorized: Invalid JWT token.
  - 404 Not Found: Wallet or business not found.