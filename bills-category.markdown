# Business Bills Categories API Documentation

## Overview
The Business Bills Categories API provides endpoints for retrieving services and category billers for business bills services. This controller handles both API client and dashboard access patterns.

## Base URL
```
/business/vas
```

## Authentication
- **API Routes**: Use `x-client-id` header and `Bearer <client_secret>` in Authorization header
- **Dashboard Routes**: Use `Bearer <jwt_token>` in Authorization header and `wallet_pin` in request body

---

## Endpoints

### 1. Get Services

Retrieves all available services for business bills.

**Endpoint:** `GET /business/vas/services`

**Authentication:** API Client Bearer Guard

**Headers:**
```
x-client-id: your_client_id
Authorization: Bearer your_client_secret
```

**Response:**

#### Success Response (200)
```json
{
    "success": true,
    "message": "Services retrieved successfully",
    "data": [
        {
            "id": "1",
            "service": "airtime"
        },
        {
            "id": "2",
            "service": "data"
        },
        {
            "id": "3",
            "service": "electricity"
        },
        {
            "id": "4",
            "service": "cable_tv"
        }
    ]
}
```

#### Error Response (400)
```json
{
    "success": false,
    "message": "Bad Request",
    "error": "Error details"
}
```

---

### 2. Get Category Billers

Retrieves billers for a specific service.

**Endpoint:** `GET /business/vas/{service_id}/billers`

**Authentication:** API Client Bearer Guard

**Headers:**
```
x-client-id: your_client_id
Authorization: Bearer your_client_secret
```

**Path Parameters:**
- `service_id` (string, required): The ID of the service

**Available Service IDs:**
- `1` - Airtime
- `2` - Data
- `3` - Electricity
- `4` - Cable TV

**Response:**

#### Success Response (200)
```json
{
    "success": true,
    "message": "Service billers retrieved successfully",
    "data": [
        {
            "id": "61efac19b5ce7eaad3b405d4",
            "biller": "BEDC"
        },
        {
            "id": "61efac27da92348f9dde5f74",
            "biller": "EKEDC"
        },
        {
            "id": "61efac35da92348f9dde5f77",
            "biller": "AEDC"
        }
    ]
}
```

#### Error Response (400)
```json
{
    "success": false,
    "message": "Bad Request",
    "error": "Error details"
}
```

---

## Data Models

### Service
```typescript
interface Service {
  id: string;        // Unique identifier for the service
  service: string;   // Service name (airtime, data, electricity, cable_tv)
}
```

### Category Biller
```typescript
interface CategoryBiller {
  id: string;        // Unique identifier for the biller
  biller: string;    // Biller name (e.g., BEDC, EKEDC, AEDC)
}
```

---

## Usage Examples

### Example 1: Get All Services
```bash
curl -X GET "https://your-api-domain/business/vas/services" \
  -H "x-client-id: your_client_id" \
  -H "Authorization: Bearer your_client_secret"
```

### Example 2: Get Electricity Billers
```bash
curl -X GET "https://your-api-domain/business/vas/3" \
  -H "x-client-id: your_client_id" \
  -H "Authorization: Bearer your_client_secret"
```

### Example 3: Get Cable TV Billers
```bash
curl -X GET "https://your-api-domain/business/vas/4" \
  -H "x-client-id: your_client_id" \
  -H "Authorization: Bearer your_client_secret"
```

---

## Business Logic Flow

1. **API Request**: Client sends authenticated request to the controller
2. **Guard Validation**: `ApiClientBearerGuard` validates the API client credentials
3. **Service Call**: Controller delegates to `BusinessBillsMediatorService`
4. **Data Retrieval**: Service fetches data from the appropriate business bills service
5. **Response Formatting**: Controller formats the response with success status and message
6. **Client Response**: Formatted response is returned to the client

---

## Error Handling

The API uses standard HTTP status codes and returns error responses in the following format:

```json
{
    "success": false,
    "message": "Error message",
    "error": "Detailed error information"
}
```

Common error scenarios:
- **400 Bad Request**: Invalid request parameters or data
- **401 Unauthorized**: Missing or invalid authentication credentials
- **403 Forbidden**: Insufficient permissions
- **500 Internal Server Error**: Server-side processing errors

---

## Notes

- All endpoints require valid API client authentication
- The service IDs are predefined and correspond to specific services
- Biller data is provider-specific and may vary based on the selected service provider
- Response data is cached and updated periodically from external provider APIs