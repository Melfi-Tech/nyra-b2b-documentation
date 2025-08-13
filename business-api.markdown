# Business API
This API allows logged-in users and API clients to manage businesses. Endpoints for logged-in users require JWT authentication, while API client endpoints require authentication using a client ID and client secret.

## Authentication
- **Logged-in User Endpoints** (`/business/register`, `/business/all`, `/business/:businessId`):
  - Requires a valid JWT token in the `Authorization` header as `Bearer <token>`.
- **API Client Endpoints** (`/business/1/:businessId`, `/business/1/resend-webhook/:reference`):
  - Requires the `x-client-id` header with the client ID and the `Authorization` header as `Bearer <client_secret>`.

## Endpoints

### Register a Business
- **Endpoint**: `POST /business/register`
- **Description**: Registers a new business for the authenticated user.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Body** (`CreateBusinessDTO`):
    ```json
    {
      "business_name": "string",
      "address": "string",
      "business_type": "string" // Must be one of the predefined BusinessType values
    }
    ```
    - `business_name`: The name of the business (required, string).
    - `address`: The business address (required, string).
    - `business_type`: The type of business (required, string, must match predefined `BusinessType` enum).
- **Response**:
  - **Status**: `201 Created`
  - **Body**:
    ```json
    {
      "success": true,
      "data": {
        "id": "string",
        "name": "string",
        "address": "string",
        "business_type": "string",
        "creator": {
          "user_id": "string"
        }
      },
      "message": "Business registered successfully"
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the request payload is invalid or business creation fails.
  - `401 Unauthorized`: If the JWT token is invalid.

### Get All Businesses
- **Endpoint**: `GET /business/all`
- **Description**: Retrieves all businesses owned by the authenticated user.
- **Authentication**: JWT token
- **Request Parameters**: None
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "data": [
        {
          "id": "string",
          "name": "string",
          "address": "string",
          "business_type": "string",
          "creator": {
            "user_id": "string"
          }
        }
      ],
      "message": "All businesses fetched successfully"
    }
    ```
- **Errors**:
  - `400 Bad Request`: If retrieval fails.
  - `401 Unauthorized`: If the JWT token is invalid.

### Get a Business (User)
- **Endpoint**: `GET /business/:businessId`
- **Description**: Retrieves details of a specific business by ID for a logged-in user.
- **Authentication**: JWT token
- **Request Parameters**:
  - **Path**:
    - `businessId`: The ID of the business (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "data": {
        "id": "string",
        "name": "string",
        "address": "string",
        "business_type": "string",
        "creator": {
          "user_id": "string"
        }
      },
      "message": "Business fetched successfully"
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the business ID is invalid.
  - `401 Unauthorized`: If the JWT token is invalid.
  - `403 Forbidden`: If the user does not own the business.
  - `404 Not Found`: If the business is not found.

### Get a Business (API Client)
- **Endpoint**: `GET /business/1/:businessId`
- **Description**: Retrieves details of a specific business by ID for an API client.
- **Authentication**: Client ID and secret
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: The client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Path**:
    - `businessId`: The ID of the business (required, string).
- **Response**:
  - **Status**: `200 OK`
  - **Body**:
    ```json
    {
      "success": true,
      "data": {
        "id": "string",
        "name": "string",
        "address": "string",
        "business_type": "string",
        "creator": {
          "user_id": "string"
        }
      },
      "message": "Business fetched successfully"
    }
    ```
- **Errors**:
  - `400 Bad Request`: If the business ID is invalid.
  - `401 Unauthorized`: If the client ID or secret is invalid.
  - `403 Forbidden`: If the API client is not authorized for the business.
  - `404 Not Found`: If the business is not found.