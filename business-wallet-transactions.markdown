# Get Transactions for Business

- **Endpoint**: `GET /business/transactions`
- **Description**: Retrieves a list of transactions for the business associated with the authenticated API client. Supports filtering by wallet, type, method, status, and date range, as well as pagination and cursor-based navigation.
- **Authentication**: Client ID and secret

## Request Parameters

- **Headers**:
  - `x-client-id`: The client ID (required, string).
  - `Authorization`: `Bearer <client_secret>` (required, string).

- **Query** (all optional unless otherwise stated):

  - `wallet_id`: Filter by a specific customer's wallet ID (string).
  - `type`: Filter by transaction type. One of: `INFLOW`, `OUTFLOW`, `INTERNAL`.
  - `method`: Filter by transaction method. One of: `API`, `DASHBOARD`.
  - `status`: Filter by transaction status (e.g., `PENDING`, `COMPLETED`, etc.).
  - `from`: Start date for transactions (ISO format, e.g., `2024-07-01`).
  - `to`: End date for transactions (ISO format, e.g., `2024-07-21`).
  - `cursor`: A transaction reference to paginate results before this transaction (string).
  - `page_size`: Number of transactions to return (default: 20, minimum: 20).

## Response

- **Status**: `200 OK`
- **Body**:
  ```json
  {
    "statusCode": 200,
    "status": "success",
    "success": true,
    "error": "",
    "message": "Transactions fetched successfully",
    "data": [
        {
            "created_at": "2025-07-21T11:28:10.652Z",
            "updated_at": "2025-07-21T11:28:10.652Z",
            "transaction_id": "TXN-01K0PF9EHMHXTW3NECYJC9H41Y",
            "transaction_type": "CREDIT",
            "transaction_category": "wallet funding",
            "transaction_status": "successful",
            "balance_before": "20000.00",
            "balance_after": "30000.00",
            "transaction_reference": "TXN-01K0PF9EHMHXTW3NECYJC9H41Y",
            "description": " Payment for invoice #INV001",
            "amount": "10000.00",
            "payment_provider": "Polaris",
            "charge": "0.00",
            "meta": {
                "data": {
                    "sender": {
                        "name": "John Doe",
                        "merchant": "Polaris",
                        "bank_name": "GTBank",
                        "account_no": "0987654321"
                    }
                }
            }
        },
        {
            "created_at": "2025-07-21T11:26:07.956Z",
            "updated_at": "2025-07-21T11:26:07.956Z",
            "transaction_id": "TXN-01K0PF5PQEP9MED7YARMN9TC47",
            "transaction_type": "CREDIT",
            "transaction_category": "wallet funding",
            "transaction_status": "successful",
            "balance_before": "10000.00",
            "balance_after": "20000.00",
            "transaction_reference": "TXN-01K0PF5PQEP9MED7YARMN9TC47",
            "description": " Payment for invoice #INV001",
            "amount": "10000.00",
            "payment_provider": "Polaris",
            "charge": "0.00",
            "meta": {
                "data": {
                    "sender": {
                        "name": "John Doe",
                        "merchant": "Polaris",
                        "bank_name": "GTBank",
                        "account_no": "0987654321"
                    }
                }
            }
        },
        {
            "created_at": "2025-07-17T20:06:21.563Z",
            "updated_at": "2025-07-17T20:06:21.563Z",
            "transaction_id": "TXN-01K0D3BCYP1QNQFX0RQ0BE9S8K",
            "transaction_type": "CREDIT",
            "transaction_category": "wallet funding",
            "transaction_status": "successful",
            "balance_before": "0.00",
            "balance_after": "10000.00",
            "transaction_reference": "TXN-01K0D3BCYP1QNQFX0RQ0BE9S8K",
            "description": " Payment for invoice #INV001",
            "amount": "10000.00",
            "payment_provider": "Polaris",
            "charge": "0.00",
            "meta": {
                "data": {
                    "sender": {
                        "name": "John Doe",
                        "merchant": "Polaris",
                        "bank_name": "GTBank",
                        "account_no": "0987654321"
                    }
                }
            }
        },
        {
            "created_at": "2025-07-17T20:03:45.442Z",
            "updated_at": "2025-07-17T20:03:45.442Z",
            "transaction_id": "TXN-01K0D36ME7XHWKA78EHE2YH8TC",
            "transaction_type": "CREDIT",
            "transaction_category": "wallet funding",
            "transaction_status": "successful",
            "balance_before": "49999999950669.73",
            "balance_after": "49999999960669.73",
            "transaction_reference": "TXN-01K0D36ME7XHWKA78EHE2YH8TC",
            "description": " Payment for invoice #INV001",
            "amount": "10000.00",
            "payment_provider": "Polaris",
            "charge": "0.00",
            "meta": {
                "data": {
                    "sender": {
                        "name": "John Doe",
                        "merchant": "Polaris",
                        "bank_name": "GTBank",
                        "account_no": "0987654321"
                    }
                }
            }
        }
    ]
}      
  ```

## Errors

- `400 Bad Request`: If any query parameter is invalid.
- `401 Unauthorized`: If the client ID or secret is invalid.
- `403 Forbidden`: If the API client is not authorized for the business.

## Example Request

```http
GET /business/transactions?wallet_id=abc123&type=INFLOW&method=API&from=2024-07-01&to=2024-07-21&page_size=10
x-client-id: your-client-id
Authorization: Bearer your-client-secret
```



---

# Get Transaction by ID for Business

- **Endpoint**: `GET /business/transactions/{transaction_id}`
- **Description**: Retrieves a specific transaction by its ID for the business associated with the authenticated API client.
- **Authentication**: Client ID and secret

## Request Parameters

- **Headers**:
  - `x-client-id`: The client ID (required, string).
  - `Authorization`: `Bearer <client_secret>` (required, string).

- **Path Parameters**:
  - `transaction_id`: The unique identifier of the transaction to retrieve (required, string).

## Response

- **Status**: `200 OK`
- **Body**:
  ```json
  {
    "statusCode": 200,
    "status": "success",
    "success": true,
    "error": "",
    "message": "Transaction fetched successfully",
    "data": {
        "created_at": "2025-07-21T11:28:10.652Z",
        "updated_at": "2025-07-21T11:28:10.652Z",
        "transaction_id": "TXN-01K0PF9EHMHXTW3NECYJC9H41Y",
        "transaction_type": "CREDIT",
        "transaction_category": "wallet funding",
        "transaction_status": "successful",
        "balance_before": "20000.00",
        "balance_after": "30000.00",
        "transaction_reference": "TXN-01K0PF9EHMHXTW3NECYJC9H41Y",
        "description": " Payment for invoice #INV001",
        "amount": "10000.00",
        "payment_provider": "Polaris",
        "charge": "0.00",
        "meta": {
            "data": {
                "sender": {
                    "name": "John Doe",
                    "merchant": "Polaris",
                    "bank_name": "GTBank",
                    "account_no": "0987654321"
                }
            }
        }
    }
  }
  ```

## Errors

- `400 Bad Request`: If the transaction ID is invalid.
- `401 Unauthorized`: If the client ID or secret is invalid.
- `403 Forbidden`: If the API client is not authorized for the business.
- `404 Not Found`: If the transaction with the specified ID does not exist.

## Example Request

```http
GET /business/transactions/TXN-01K0PF9EHMHXTW3NECYJC9H41Y
x-client-id: your-client-id
Authorization: Bearer your-client-secret
```


