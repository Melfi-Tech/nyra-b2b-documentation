 # Business Fees API
 
 This document describes the Business Fees API endpoints for managing business-specific fee configurations.
 
 ## Currency Units
 
 **Important:** Different fee types use different currency units:
 - **Wallet Transfer & Wallet Funding**: Fees are in **kobo** 
   - Example: `flat_fee: 2500` = ₦25.00
 - **Card Creation & Card Funding**: Fees are in **USD**
   - Example: `flat_fee: 4` = $4.00

## Create Business Fees

- *Endpoint*: POST /business-fees
- *Description*: Creates business fee configuration for a specific business. Each business can have custom fee settings for different transaction types.
- *Authentication*: JWT token with ADMIN or SUPER_ADMIN role

## Request Parameters

- *Headers*:
  - Authorization: Bearer <jwt_token> (required, string).

 - *Body* (all optional except business_id):

   - business_id: The business ID to create fees for (required, string).
   - wallet_transfer: Wallet transfer fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in kobo  (number, >= 0).
     - cap_fee: Maximum fee cap in kobo  (number, >= 0).
   - wallet_funding: Wallet funding fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in kobo  (number, >= 0).
     - cap_fee: Maximum fee cap in kobo  (number, >= 0).
   - card_creation: Card creation fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in USD (number, >= 0).
     - cap_fee: Maximum fee cap in USD (number, >= 0).
   - card_funding: Card funding fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in USD (number, >= 0).
     - cap_fee: Maximum fee cap in USD (number, >= 0).

 ## Response
 
 - *Status*: 200 OK
 - *Body*:
 ```json
 {
     "statusCode": 200,
     "status": "success",
     "success": true,
     "error": "",
     "message": "Business fees created successfully",
     "data": {
         "fee_id": "65e6e6d18d41d876944861da",
         "wallet_transfer": {
             "percentage_charge": 0,
             "flat_fee": 0,
             "cap_fee": 0
         },
         "wallet_funding": {
             "percentage_charge": 1,
             "flat_fee": 2500,
             "cap_fee": 10000
         },
         "card_creation": {
             "percentage_charge": 2,
             "flat_fee": 4,
             "cap_fee": 10
         },
         "card_funding": {
             "percentage_charge": 1.5,
             "flat_fee": 0.5,
             "cap_fee": 2
         },
         "business": {
             "created_at": "2025-07-30T12:45:21.638Z",
             "updated_at": "2025-07-30T12:45:21.638Z",
             "_v": 1,
             "id": "65e6e6d18d41d876944861da",
             "name": "PICKSON",
             "business_type": "REGISTERED_COMPANY",
             "address": "House 79, Larix Home Apo, Abuja",
             "city": null,
             "state": null,
             "lga": null,
             "email": null,
             "registration_number": null,
             "date_of_registration": null,
             "verification_status": "NOT_STARTED"
         },
         "created_at": "2025-08-07T15:19:05.894Z",
         "updated_at": "2025-08-07T15:19:05.894Z",
         "_v": 1
     }
 }
 ```

## Errors

- 400 Bad Request: If business fees already exist for the business or invalid data.
- 401 Unauthorized: If the JWT token is invalid.
- 403 Forbidden: If the user doesn't have ADMIN or SUPER_ADMIN role.

## Example Request

```http
POST /business-fees
Authorization: Bearer your-jwt-token
Content-Type: application/json

 {
   "business_id": "business123",
   "wallet_transfer": {
     "percentage_charge": 1.5,
     "flat_fee": 50,
     "cap_fee": 1000
   },
   "card_creation": {
     "percentage_charge": 2.0,
     "flat_fee": 100,
     "cap_fee": 2000
   }
 }
```

---

# Get All Business Fees

- *Endpoint*: GET /business-fees
- *Description*: Retrieves all business fee configurations with cursor-based pagination. Supports filtering by business ID and date range.
- *Authentication*: JWT token with ADMIN or SUPER_ADMIN role

## Request Parameters

- *Headers*:
  - Authorization: Bearer <jwt_token> (required, string).

- *Query Parameters* (all optional):
  - cursor: Cursor for pagination (string).
  - from: From date in ISO format (string, e.g., "2025-01-01").
  - to: To date in ISO format (string, e.g., "2025-01-31").
  - page_size: Number of results to return (number, default: 20).
  - lean: Lean option for the query (boolean).

## Response

- *Status*: 200 OK
- *Body*:
```json
{
  "statusCode": 200,
  "status": "success",
  "success": true,
  "error": "",
  "message": "Business fees retrieved successfully",
  "data": [
    {
      "fee_id": "65e6e6d18d41d876944861da",
      "wallet_transfer": {
        "percentage_charge": 0,
        "flat_fee": 0,
        "cap_fee": 0
      },
      "wallet_funding": {
        "percentage_charge": 1,
        "flat_fee": 2500,
        "cap_fee": 10000
      },
      "card_creation": {
        "percentage_charge": 2,
        "flat_fee": 4,
        "cap_fee": 10
      },
      "card_funding": {
        "percentage_charge": 1.5,
        "flat_fee": 0.5,
        "cap_fee": 2
      },
      "business": {
        "id": "65e6e6d18d41d876944861da",
        "name": "PICKSON"
      },
      "created_at": "2025-08-07T15:19:05.894Z",
      "updated_at": "2025-08-07T15:19:05.894Z",
      "_v": 1
    }
  ],
  "meta": {
    "next_cursor": "65e6e6d18d41d876944861da"
  }
}
```

## Errors

- 401 Unauthorized: If the JWT token is invalid.
- 403 Forbidden: If the user doesn't have ADMIN or SUPER_ADMIN role.

## Example Request

```http
GET /business-fees?page_size=10&from=2025-01-01&to=2025-01-31
Authorization: Bearer your-jwt-token
```

## Pagination

This endpoint uses cursor-based pagination:
- Use the `cursor` parameter to get the next page of results
- The `meta.next_cursor` field contains the cursor for the next page
- When `next_cursor` is `undefined`, there are no more results

---

# Get Business Fees

- *Endpoint*: GET /business-fees/{businessId}
- *Description*: Retrieves business fee configuration for a specific business.
- *Authentication*: JWT token with ADMIN or SUPER_ADMIN role

## Request Parameters

- *Headers*:
  - Authorization: Bearer <jwt_token> (required, string).

- *Path*:
  - businessId: The business ID to retrieve fees for (required, string).

 ## Response
 
 - *Status*: 200 OK
 - *Body*:
 ```json
 {
   "statusCode": 200,
   "status": "success",
   "success": true,
   "error": "",
   "message": "Business fees retrieved successfully",
   "data": {
     "fee_id": "65e6e6d18d41d876944861da",
     "wallet_transfer": {
       "percentage_charge": 0,
       "flat_fee": 0,
       "cap_fee": 0
     },
     "wallet_funding": {
       "percentage_charge": 1,
       "flat_fee": 2500,
       "cap_fee": 10000
     },
     "card_creation": {
       "percentage_charge": 2,
       "flat_fee": 4,
       "cap_fee": 10
     },
     "card_funding": {
       "percentage_charge": 1.5,
       "flat_fee": 0.5,
       "cap_fee": 2
     },
     "business": {
       "created_at": "2025-07-30T12:45:21.638Z",
       "updated_at": "2025-07-30T12:45:21.638Z",
       "_v": 1,
       "id": "65e6e6d18d41d876944861da",
       "name": "PICKSON",
       "business_type": "REGISTERED_COMPANY",
       "address": "House 79, Larix Home Apo, Abuja",
       "city": null,
       "state": null,
       "lga": null,
       "email": null,
       "registration_number": null,
       "date_of_registration": null,
       "verification_status": "NOT_STARTED"
     },
     "created_at": "2025-08-07T15:19:05.894Z",
     "updated_at": "2025-08-07T15:19:05.894Z",
     "_v": 1
   }
 }
 ```

## Errors

- 401 Unauthorized: If the JWT token is invalid.
- 403 Forbidden: If the user doesn't have ADMIN or SUPER_ADMIN role.
- 404 Not Found: If no business fees found for the business ID.

## Example Request

```http
GET /business-fees/business123
Authorization: Bearer your-jwt-token
```

---

# Update Business Fees

- *Endpoint*: PUT /business-fees/{businessId}
- *Description*: Updates business fee configuration for a specific business. Only provided fee types will be updated.
- *Authentication*: JWT token with ADMIN or SUPER_ADMIN role

## Request Parameters

- *Headers*:
  - Authorization: Bearer <jwt_token> (required, string).

- *Path*:
  - businessId: The business ID to update fees for (required, string).

 - *Body* (all optional):

   - wallet_transfer: Wallet transfer fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in kobo  (number, >= 0).
     - cap_fee: Maximum fee cap in kobo  (number, >= 0).
   - wallet_funding: Wallet funding fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in kobo  (number, >= 0).
     - cap_fee: Maximum fee cap in kobo  (number, >= 0).
   - card_creation: Card creation fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in USD (number, >= 0).
     - cap_fee: Maximum fee cap in USD (number, >= 0).
   - card_funding: Card funding fee configuration (optional, object).
     - percentage_charge: Percentage charge (number, 0-100).
     - flat_fee: Flat fee amount in USD (number, >= 0).
     - cap_fee: Maximum fee cap in USD (number, >= 0).


 ## Response
 
 - *Status*: 200 OK
 - *Body*:
 ```json
 {
     "statusCode": 200,
     "status": "success",
     "success": true,
     "error": "",
     "message": "Business fees updated successfully",
     "data": {
         "fee_id": "65e6e6d18d41d876944861da",
         "wallet_transfer": {
             "percentage_charge": 0.5,
             "flat_fee": 1000,
             "cap_fee": 5000
         },
         "wallet_funding": {
             "cap_fee": 10000,
             "flat_fee": 2500,
             "percentage_charge": 1
         },
         "card_creation": {
             "cap_fee": 10,
             "flat_fee": 4,
             "percentage_charge": 2
         },
         "card_funding": {
             "percentage_charge": 2,
             "flat_fee": 1,
             "cap_fee": 3
         },
         "business": {
             "created_at": "2025-07-30T12:45:21.638Z",
             "updated_at": "2025-07-30T12:45:21.638Z",
             "_v": 1,
             "id": "65e6e6d18d41d876944861da",
             "name": "PICKSON",
             "business_type": "REGISTERED_COMPANY",
             "address": "House 79, Larix Home Apo, Abuja",
             "city": null,
             "state": null,
             "lga": null,
             "email": null,
             "registration_number": null,
             "date_of_registration": null,
             "verification_status": "NOT_STARTED"
         },
         "created_at": "2025-08-07T15:19:05.894Z",
         "updated_at": "2025-08-07T15:20:30.123Z",
         "_v": 1
     }
 }
 ```

## Errors

- 401 Unauthorized: If the JWT token is invalid.
- 403 Forbidden: If the user doesn't have ADMIN or SUPER_ADMIN role.
- 404 Not Found: If no business fees found for the business ID.

 ## Example Request
 
 ```http
 PUT /business-fees/business123
 Authorization: Bearer your-jwt-token
 Content-Type: application/json
 
 {
   "wallet_transfer": {
     "percentage_charge": 0.5,
     "flat_fee": 1000,
     "cap_fee": 5000
   },
   "card_funding": {
     "percentage_charge": 2.0,
     "flat_fee": 1,
     "cap_fee": 3
   }
 }
 ```

---

# Delete Business Fees

- *Endpoint*: DELETE /business-fees/{businessId}
- *Description*: Deletes business fee configuration for a specific business.
- *Authentication*: JWT token with ADMIN or SUPER_ADMIN role

## Request Parameters

- *Headers*:
  - Authorization: Bearer <jwt_token> (required, string).

- *Path*:
  - businessId: The business ID to delete fees for (required, string).

## Response

- *Status*: 200 OK
- *Body*:
```json
{
  "statusCode": 200,
  "status": "success",
  "success": true,
  "error": "",
  "message": "Business fees deleted successfully"
}
```

## Errors

- 401 Unauthorized: If the JWT token is invalid.
- 403 Forbidden: If the user doesn't have ADMIN or SUPER_ADMIN role.
- 404 Not Found: If no business fees found for the business ID.

## Example Request

```http
DELETE /business-fees/business123
Authorization: Bearer your-jwt-token
```

---

 # Compute Business Fee
 
 - *Endpoint*: POST /business-fees/{businessId}/{feeType}/compute
 - *Description*: Calculates the fee amount for a specific business and fee type based on the transaction amount.
 - *Authentication*: JWT token with ADMIN or SUPER_ADMIN role
 
 ## Available Fee Types
 
   The following fee types are supported for computation:
  - **walletTransfer**: Wallet transfer fees (in kobo)
  - **walletFunding**: Wallet funding fees (in kobo)  
  - **cardCreation**: Card creation fees (in USD)
  - **cardFunding**: Card funding fees (in USD)

## Request Parameters

- *Headers*:
  - Authorization: Bearer <jwt_token> (required, string).

- *Path*:
  - businessId: The business ID to compute fees for (required, string).
     - feeType: The fee type to compute (required, string). One of: walletTransfer, walletFunding, cardCreation, cardFunding.

- *Body*:
  - amount: The transaction amount to compute fees for (required, number).

## Response

- *Status*: 200 OK
- *Body*:
```json
{
  "statusCode": 200,
  "status": "success",
  "success": true,
  "error": "",
  "message": "Fee computed successfully",
    "data": {
      "business_id": "65e6e6d18d41d876944861da",
      "fee_type": "walletTransfer",
      "amount": 10000,
      "computed_fee": 1050,
      "fee_unit": "kobo"
      }
}
```

## Errors

- 400 Bad Request: If the fee type is invalid or amount is invalid.
- 401 Unauthorized: If the JWT token is invalid.
- 403 Forbidden: If the user doesn't have ADMIN or SUPER_ADMIN role.
- 404 Not Found: If no business fees found for the business ID.

## Example Request

```http
 POST /business-fees/business123/walletTransfer/compute
Authorization: Bearer your-jwt-token
Content-Type: application/json

{
  "amount": 10000
}
```

## Fee Calculation Logic

The fee is calculated using the following formula:
```
Fee = min((percentageCharge / 100 * amount) + flatFee, capFee)
```

If no custom business fees are configured, the system falls back to control panel default fees.
