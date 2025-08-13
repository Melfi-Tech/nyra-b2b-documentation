# Business Identity Verification API

This API allows logged-in users to verify business identities and upload related documents. All endpoints require JWT authentication for a logged-in user.

## Authentication

- **AuthGuard('jwt')**: All endpoints require a valid JWT token in the `Authorization` header as `Bearer <token>`.

## Endpoints

### Verify BVN

- **Endpoint**: `POST /business/identities/bvn/verify`

- **Description**: Verifies the user's BVN for the business.

- **Request Parameters**:

  - **Body** (`BvnVerificationDTO`):

    ```json
    {
      "bvn": "string",
      "dob": "string"
    }
    ```

    - `bvn`: The Bank Verification Number (required, string).
    - `dob`: The correct date of birth of the bvn holder - (ISO Format -> 2000-07-15) (required, string)

- **Response**:

  - **Status**: `200 OK`

  - **Body**:

    ```json
    {
      "success": true,
      "data": true,
      "message": "bvn verified successfully"
    }
    ```

- **Errors**:

  - `400 Bad Request`: If the BVN is invalid or verification fails.
  - `403 Forbidden`: If the user lacks permission.

### Verify NIN

- **Endpoint**: `POST /business/identities/nin/verify`

- **Description**: Verifies the user's NIN for the business.

- **Request Parameters**:

  - **Body** (`NinVerificationDTO`):

    ```json
    {
      "nin": "string"
    }
    ```

    - `nin`: The National Identification Number (required, string).

- **Response**:

  - **Status**: `200 OK`

  - **Body**:

    ```json
    {
      "success": true,
      "data": true,
      "message": "nin verified successfully"
    }
    ```

- **Errors**:

  - `400 Bad Request`: If the NIN is invalid or verification fails.
  - `403 Forbidden`: If the user lacks permission.

### Verify CAC

- **Endpoint**: `POST /business/identities/cac/verify`

- **Description**: Verifies the business's CAC registration number.

- **Request Parameters**:

  - **Body** (`CACVerificationDTO`):

    ```json
    {
      "registration_number": "string",
      "business_id": "string"
    }
    ```

    - `registration_number`: The CAC registration number (required, string).
    - `business_id`: The ID of the business (required, string).

- **Response**:

  - **Status**: `200 OK`

  - **Body**:

    ```json
    {
      "success": true,
      "data": true,
      "message": "cac verified successfully"
    }
    ```

- **Errors**:

  - `400 Bad Request`: If the registration number is invalid, already linked, or does not match the business name.
  - `404 Not Found`: If the business is not found.
  - `403 Forbidden`: If the user does not own the business.

### Verify BVN Phone Number

- **Endpoint**: `POST /business/identities/bvn/verify-phone`

- **Description**: Verifies the phone number associated with the user's BVN.

- **Request Parameters**:

  - **Body** (`BvnPhoneNumberVerificationDTO`):

    ```json
    {
      "phone_number": "string"
    }
    ```

    - `phone_number`: The phone number to verify (required, string).

- **Response**:

  - **Status**: `200 OK`

  - **Body**:

    ```json
    {
      "success": true,
      "message": "phone number validated"
    }
    ```

- **Errors**:

  - `400 Bad Request`: If the phone number is invalid or verification fails.
  - `403 Forbidden`: If the user lacks permission.

### Upload Business Identity Documents

1. **Endpoint**: `POST /business/identities/docs/upload`

2. **Description**: Uploads business identity documents (e.g., certificate of incorporation).

3. **Request Parameters**:

   - **Body** (`UploadBusinessDocsDto`, multipart/form-data):

     ```json
     {
       "business_id": "string"
     }
     ```

     - `business_id`: The ID of the business (required, string).

   - **Files** (multipart/form-data, max 1 file per field, max size 1GB):

     - `certificate_of_incorporation`: Certificate of incorporation (optional, file).
     - `application_for_registration`: Application for registration (optional, file).
     - `memorandum_of_association`: Memorandum of association (optional, file).
     - `proof_of_address`: Proof of address (optional, file).

4. **Response**:

   - **Status**: `201 Created`

   - **Body**:

     ```json
     {
       "success": true,
       "data": {
         "businessId": "string",
         "verificationStatus": "VERIFIED" | "PENDING",
         "documentsProcessed": ["string"]
       },
       "message": "document uploaded successfully!"
     }
     ```

5. **Errors**:

   1. `400 Bad Request`: If the files or business ID are invalid.
   2. `403 Forbidden`: If the user does not own the business.

- `404 Not Found`: If the business is not found.