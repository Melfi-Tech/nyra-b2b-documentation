# Business KYC API Documentation

## Overview
This API performs KYC verification operations, including Basic BVN verification, Advanced BVN verification, and NIN verification. Verifications are performed via API and require fees charged from the business float wallet.

## Authentication
- **API Routes** (e.g., `/business/kyc/bvn/basic`, `/business/kyc/bvn/advanced`, `/business/kyc/nin`):
  - `x-client-id` header (lowercase, required, string).
  - `Authorization` header: `Bearer <client_secret>` (required, string).

## Prerequisites
- Sufficient wallet balance to cover the applicable business KYC fees configured in Control Panel.

## Endpoints

### Basic BVN Verification 
- **Endpoint**: `GET /business/kyc/bvn/basic`
- **Description**: Performs a basic BVN verification and returns core identity details.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `bvn`: Bank Verification Number (required, string, e.g., `12345678901`).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Basic BVN verification completed successfully",
      "data": {
        "first_name": "john",
        "last_name": "doe",
        "middle_name": "junior",
        "date_of_birth": "1990-01-01",
        "phone_number": "08012345678"
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### Advance BVN Verification 
- **Endpoint**: `GET /business/kyc/bvn/advance`
- **Description**: Performs an advance BVN verification and returns extended identity details.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `bvn`: Bank Verification Number (required, string, e.g., `12345678901`).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "Advance BVN verification completed successfully",
      "data": {
        "first_name": "john",
        "last_name": "doe",
        "middle_name": "junior",
        "gender": "Male",
        "date_of_birth": "1990-01-01",
        "phone_number1": "08012345678",
        "phone_number2": "08098765432",
        "email": "john.doe@example.com",
        "enrollment_bank": "214",
        "enrollment_branch": "Main",
        "level_of_account": "1",
        "lga_of_origin": "Bende",
        "lga_of_residence": "Karu",
        "marital_status": "single",
        "name_on_card": "JOHN DOE",
        "nationality": "Nigeria",
        "registration_date": "19-Apr-2023",
        "residential_address": "123 test street",
        "state_of_origin": "Abia State",
        "state_of_residence": "Nasarawa State",
        "title": "mr",
        "watch_listed": "false",
        "base64_image": "/9j/4AAQSkZJRgABAgAAAQABAAD..."
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### NIN Verification 
- **Endpoint**: `GET /business/kyc/nin`
- **Description**: Performs NIN verification and returns identity details including photo and employment status.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `nin`: National Identity Number (required, string, e.g., `12345678901`).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
      "success": true,
      "message": "NIN verification completed successfully",
      "data": {
        "first_name": "jane",
        "last_name": "doe",
        "gender": "female",
        "middle_name": "junior",
        "photo": "/9j/4AAQSkZJRgABAgAAAQABAAD...",
        "date_of_birth": "1992-04-16",
        "email": "jane.doe@example.com",
        "phone_number": "08011112222",
        "employment_status": "unemployed",
        "marital_status": "single"
      }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.


