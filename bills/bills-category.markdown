# Business Bills Categories API Documentation

## Overview
This API manages business bill categories, including retrieving available bill categories, category contexts, and billers for specific categories. The API provides access to different bill payment categories such as electricity, cable TV, and betting services. All endpoints are accessible via API client authentication.

## Authentication
- **All Routes**: API Client authentication required
  - `x-client-id` header (lowercase, required, string)
  - `Authorization` header: `Bearer <client_secret>` (required, string)

## Prerequisites
- Valid API client credentials
- Active business account
- Business must be properly configured in the system

## Endpoints

### Get Bill Categories
- **Endpoint**: `GET /business/bills/categories/categories?bill_category_ctx=electricity`
- **Description**: Retrieves available bill categories for a specific category context (electricity, cable_tv, or betting).
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string)
    - `Authorization`: `Bearer <client_secret>` (required, string)
  - **Query Parameters**:
    - `bill_category_ctx`: Category context (required, string, one of: `electricity`, `cable_tv`, `betting`)
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Bill categories retrieved successfully",
      "data": [
        {
            "id": "1",
            "category": "ELECTRICITY",
            "provider": "9PSB"
        },
        {
            "id": "2",
            "category": "BETTING & LOTTERY",
            "provider": "9PSB"
        },
        {
            "id": "3",
            "category": "EXAMS & RESULTS",
            "provider": "9PSB"
        },
        {
            "id": "4",
            "category": "CABLE TV",
            "provider": "9PSB"
        },
        {
            "id": "7",
            "category": "INTERNET SERVICES",
            "provider": "9PSB"
        },
        {
            "id": "9",
            "category": "CHEAP DATA",
            "provider": "9PSB"
        }
    ]
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid category context or missing parameters
  - 401 Unauthorized: Invalid client credentials
  - 404 Not Found: Business not found
  - 500 Internal Server Error: Provider service unavailable

### Get Bill Categories Contexts
- **Endpoint**: `GET /business/bills/categories/categories-ctx`
- **Description**: Retrieves all available bill category contexts and their corresponding provider mappings.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string)
    - `Authorization`: `Bearer <client_secret>` (required, string)
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Bill categories contexts retrieved successfully",
      "data": {
        "cable_tv": "cable_tv",
        "electricity": "electricity",
      }
    }
    ```
- **Errors**:
  - 401 Unauthorized: Invalid client credentials
  - 404 Not Found: Business not found
  - 500 Internal Server Error: Service unavailable

### Get Category Billers
- **Endpoint**: `GET /business/bills/categories/categories/{category_id}/billers?bill_category_ctx=electricity`
- **Description**: Retrieves available billers for a specific category ID within a category context.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string)
    - `Authorization`: `Bearer <client_secret>` (required, string)
  - **Path Parameters**:
    - `category_id`: Category ID (required, string)
  - **Query Parameters**:
    - `bill_category_ctx`: Category context (required, string, one of: `electricity`, `cable_tv`)
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Category billers retrieved successfully",
      "data": [
        {
            "id": "BP-ABUJA",
            "biller": "AEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-EKO",
            "biller": "EKEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-IKEJA",
            "biller": "IKEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-IBADAN",
            "biller": "IBEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-ENUGU",
            "biller": "EEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-PH",
            "biller": "PHED",
            "provider": "9PSB"
        },
        {
            "id": "BP-JOS",
            "biller": "JEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-KADUNA",
            "biller": "KEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-KANO",
            "biller": "KEDCO",
            "provider": "9PSB"
        },
        {
            "id": "BP-BENIN",
            "biller": "BEDC",
            "provider": "9PSB"
        },
        {
            "id": "BP-YOLA",
            "biller": "YEDC",
            "provider": "9PSB"
        }
    ]
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid category ID or context, or no billers found
  - 401 Unauthorized: Invalid client credentials
  - 404 Not Found: Business, category, or billers not found
  - 500 Internal Server Error: Provider service unavailable

