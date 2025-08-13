# Business TV Bills API Documentation

## Overview
This API manages business TV subscription payments, including validating smart card data, retrieving package options, and processing payments. TV payments can be initiated via API or dashboard, with dashboard payments requiring a 4-digit transaction PIN set via [Set Transaction Pin Endpoint](./transactions.markdown).

## Authentication
- **API Routes** (e.g., `/business/bills/tv/validate`, `/business/bills/tv/provider/items`, `/business/bills/tv/pay`):
  - `x-client-id` header (lowercase, required, string).
  - `Authorization` header: `Bearer <client_secret>` (required, string).
- **Dashboard Routes** (`/business/bills/tv/pay/dashboard`):
  - `Authorization` header: `Bearer <jwt_token>` (required, string).

## Prerequisites
- For `POST /business/bills/tv/pay/dashboard`, a 4-digit transaction PIN must be set using the [Set Transaction Pin Endpoint](./transactions.markdown).
- The source account must be a business float wallet.

## Endpoints

### Validate TV Data
- **Endpoint**: `POST /business/bills/tv/validate`
- **Description**: Validates TV smart card data and retrieves customer information.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`CableTVInputToBeValidatedDTO`):
    ```json
    {
      "tv_biller_id": "3",
      "smart_card_number": "1234567890123456"
    }
    ```
    - `tv_biller_id`: TV provider ID (required, string).
    - `smart_card_number`: Smart card number (required, string, numeric only).
    - `pkg_id`: Package ID (optional, string).
    - `amount`: Amount to validate (optional, string).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
        "success": true,
        "message": "TV data validated successfully",
        "data": {
            "customer_name": "Jane Smith",
            "customer_info": "Test TV Customer"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid smart card number or biller ID.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Smart card not found.

### Get TV Provider Items
- **Endpoint**: `GET /business/bills/tv/provider/items?biller_id=3`
- **Description**: Retrieves available packages for a TV provider.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Query Parameters**:
    - `biller_id`: TV provider ID (required, string).
- **Response**:
  - **Status**: 200 OK
  - **Body**:
    ```json
    {
        "success": true,
        "message": "TV provider items retrieved successfully",
        "data": {
            "pkg_id": {
                "items": [
                    {
                        "label": "DStv Compact - N19,000",
                        "id": "ng_dstv_compe36",
                        "amount": 19000
                    },
                    {
                        "label": "DStv Compact + Great Wall Africa - N22,800",
                        "id": "ng_dstv_compe36+ng_dstv_gwalle36",
                        "amount": 22800
                    },
                    {
                        "label": "DStv Compact + ExtraView - N25,000",
                        "id": "ng_dstv_compe36+ng_dstv_hdpvre36",
                        "amount": 25000
                    },
                    {
                        "label": "DStv Compact + MOBILE - N16,490",
                        "id": "ng_dstv_compe36+ng_dstv_mobmaxi",
                        "amount": 16490
                    },
                    {
                        "label": "DStv Compact + Movie Bundle - N22,500",
                        "id": "ng_dstv_compe36+ng_dstv_movies",
                        "amount": 22500
                    },
                    {
                        "label": "DStv Compact + Showmax - N20,750",
                        "id": "ng_dstv_compe36+ng_dstv_showcompe36",
                        "amount": 20750
                    },
                    {
                        "label": "DStv Compact Plus - N30,000",
                        "id": "ng_dstv_comple36",
                        "amount": 30000
                    },
                    {
                        "label": "DStv Compact Plus + Great Wall Africa - N33,800",
                        "id": "ng_dstv_comple36+ng_dstv_gwalle36",
                        "amount": 33800
                    },
                    {
                        "label": "DStv Compact Plus + ExtraView - N36,000",
                        "id": "ng_dstv_comple36+ng_dstv_hdpvre36",
                        "amount": 36000
                    },
                    {
                        "label": "DStv Compact Plus + MOBILE - N25,790",
                        "id": "ng_dstv_comple36+ng_dstv_mobmaxi",
                        "amount": 25790
                    },
                    {
                        "label": "DStv Compact Plus + Movie Bundle - N33,500",
                        "id": "ng_dstv_comple36+ng_dstv_movies",
                        "amount": 33500
                    },
                    {
                        "label": "DStv FTA Plus - N1,600",
                        "id": "ng_dstv_ftaple36",
                        "amount": 1600
                    },
                    {
                        "label": "DStv FTA Plus + Great Wall Africa - N4,725",
                        "id": "ng_dstv_ftaple36+ng_dstv_gwalle36",
                        "amount": 4725
                    },
                    {
                        "label": "DStv FTA Plus + ExtraView - N6,600",
                        "id": "ng_dstv_ftaple36+ng_dstv_hdpvre36",
                        "amount": 6600
                    },
                    {
                        "label": "DStv FTA Plus + MOBILE - N2,390",
                        "id": "ng_dstv_ftaple36+ng_dstv_mobmaxi",
                        "amount": 2390
                    },
                    {
                        "label": "DStv FTA Plus + Movie Bundle - N4,100",
                        "id": "ng_dstv_ftaple36+ng_dstv_movies",
                        "amount": 4100
                    },
                    {
                        "label": " Great Wall Africa - N3,800",
                        "id": "ng_dstv_gwalle36",
                        "amount": 3800
                    },
                    {
                        "label": "DStv HDPVR Access Service - N6,000",
                        "id": "ng_dstv_hdpvre36",
                        "amount": 6000
                    },
                    {
                        "label": "DSTV MOBILE - N790",
                        "id": "ng_dstv_mobmaxi",
                        "amount": 790
                    },
                    {
                        "label": "DStv Movie Bundle - N3,500",
                        "id": "ng_dstv_movies",
                        "amount": 3500
                    },
                    {
                        "label": "DStv Padi  - N4,400",
                        "id": "ng_dstv_nltese36",
                        "amount": 4400
                    },
                    {
                        "label": "DStv Padi + Great Wall Africa Bouquet - N8,200",
                        "id": "ng_dstv_nltese36+ng_dstv_gwalle36",
                        "amount": 8200
                    },
                    {
                        "label": "DStv Padi + ExtraView - N10,400",
                        "id": "ng_dstv_nltese36+ng_dstv_hdpvre36",
                        "amount": 10400
                    },
                    {
                        "label": "DStv Padi + MOBILE - N4,390",
                        "id": "ng_dstv_nltese36+ng_dstv_mobmaxi",
                        "amount": 4390
                    },
                    {
                        "label": "DStv Padi + Movie Bundle - N7,900",
                        "id": "ng_dstv_nltese36+ng_dstv_movies",
                        "amount": 7900
                    },
                    {
                        "label": "DStv Yanga  - N6,000",
                        "id": "ng_dstv_nnj1e36",
                        "amount": 6000
                    },
                    {
                        "label": "DStv Yanga + Great Wall Africa - N9,800",
                        "id": "ng_dstv_nnj1e36+ng_dstv_gwalle36",
                        "amount": 9800
                    },
                    {
                        "label": "DStv Yanga + ExtraView - N12,000",
                        "id": "ng_dstv_nnj1e36+ng_dstv_hdpvre36",
                        "amount": 12000
                    },
                    {
                        "label": "DStv Yanga + MOBILE - N5,890",
                        "id": "ng_dstv_nnj1e36+ng_dstv_mobmaxi",
                        "amount": 5890
                    },
                    {
                        "label": "DStv Yanga + Movie Bundle - N9,500",
                        "id": "ng_dstv_nnj1e36+ng_dstv_movies",
                        "amount": 9500
                    },
                    {
                        "label": "DStv Confam - N11,000",
                        "id": "ng_dstv_nnj2e36",
                        "amount": 11000
                    },
                    {
                        "label": "DStv Confam + Great Wall Africa - N14,800",
                        "id": "ng_dstv_nnj2e36+ng_dstv_gwalle36",
                        "amount": 14800
                    },
                    {
                        "label": "DStv Confam + ExtraView - N17,000",
                        "id": "ng_dstv_nnj2e36+ng_dstv_hdpvre36",
                        "amount": 17000
                    },
                    {
                        "label": "DStv Confam + MOBILE - N10,090",
                        "id": "ng_dstv_nnj2e36+ng_dstv_mobmaxi",
                        "amount": 10090
                    },
                    {
                        "label": "DStv Confam + Movie Bundle - N14,500",
                        "id": "ng_dstv_nnj2e36+ng_dstv_movies",
                        "amount": 14500
                    },
                    {
                        "label": "DStv Premium East Africa and Indian - N16,530",
                        "id": "ng_dstv_preasie36",
                        "amount": 16530
                    },
                    {
                        "label": "DStv Premium East Africa and Indian + Great Wall Africa - N19,655",
                        "id": "ng_dstv_preasie36+ng_dstv_gwalle36",
                        "amount": 19655
                    },
                    {
                        "label": "DStv Premium East Africa and Indian + ExtraView - N21,530",
                        "id": "ng_dstv_preasie36+ng_dstv_hdpvre36",
                        "amount": 21530
                    },
                    {
                        "label": "DStv Premium East Africa and Indian + MOBILE - N17,320",
                        "id": "ng_dstv_preasie36+ng_dstv_mobmaxi",
                        "amount": 17320
                    },
                    {
                        "label": "DStv Premium East Africa and Indian + Movie Bundle - N19,030",
                        "id": "ng_dstv_preasie36+ng_dstv_movies",
                        "amount": 19030
                    },
                    {
                        "label": "DStv Premium W/Afr + Asian - N50,500",
                        "id": "ng_dstv_prwasie36",
                        "amount": 50500
                    },
                    {
                        "label": "DStv Premium W/Afr + Asian + Great Wall Africa - N54,300",
                        "id": "ng_dstv_prwasie36+ng_dstv_gwalle36",
                        "amount": 54300
                    },
                    {
                        "label": "DStv Premium W/Afr + Asian + ExtraView - N56,500",
                        "id": "ng_dstv_prwasie36+ng_dstv_hdpvre36",
                        "amount": 56500
                    },
                    {
                        "label": "DStv Premium W/Afr + Asian + MOBILE - N42,790",
                        "id": "ng_dstv_prwasie36+ng_dstv_mobmaxi",
                        "amount": 42790
                    },
                    {
                        "label": "DStv Premium W/Afr + Asian + Movie Bundle - N54,000",
                        "id": "ng_dstv_prwasie36+ng_dstv_movies",
                        "amount": 54000
                    },
                    {
                        "label": "DStv Premium - N44,500",
                        "id": "ng_dstv_prwe36",
                        "amount": 44500
                    },
                    {
                        "label": "DStv Premium + Great Wall Africa - N48,300",
                        "id": "ng_dstv_prwe36+ng_dstv_gwalle36",
                        "amount": 48300
                    },
                    {
                        "label": "DStv Premium + ExtraView - N50,500",
                        "id": "ng_dstv_prwe36+ng_dstv_hdpvre36",
                        "amount": 50500
                    },
                    {
                        "label": "DStv Premium + MOBILE - N37,790",
                        "id": "ng_dstv_prwe36+ng_dstv_mobmaxi",
                        "amount": 37790
                    },
                    {
                        "label": "DStv Premium + Movie Bundle - N48,000",
                        "id": "ng_dstv_prwe36+ng_dstv_movies",
                        "amount": 48000
                    },
                    {
                        "label": "DStv PVR Access - N4,000",
                        "id": "ng_dstv_pvre36",
                        "amount": 4000
                    },
                    {
                        "label": "DStv PVR Access + Great Wall Africa - N7,125",
                        "id": "ng_dstv_pvre36+ng_dstv_gwalle36",
                        "amount": 7125
                    },
                    {
                        "label": "DStv PVR Access + ExtraView - N9,000",
                        "id": "ng_dstv_pvre36+ng_dstv_hdpvre36",
                        "amount": 9000
                    },
                    {
                        "label": "DStv PVR Access + MOBILE - N4,790",
                        "id": "ng_dstv_pvre36+ng_dstv_mobmaxi",
                        "amount": 4790
                    },
                    {
                        "label": "DStv PVR Access + Movie Bundle - N6,500",
                        "id": "ng_dstv_pvre36+ng_dstv_movies",
                        "amount": 6500
                    },
                    {
                        "label": "DStv Compact + Showmax - N20,750",
                        "id": "ng_dstv_showcompe36",
                        "amount": 20750
                    },
                    {
                        "label": "DStv CompactPlus + Showmax - N31,750",
                        "id": "ng_dstv_showcomple36",
                        "amount": 31750
                    },
                    {
                        "label": "GWALLE36 + SHOWMAX - N7,300",
                        "id": "ng_dstv_showgwalle36",
                        "amount": 7300
                    },
                    {
                        "label": "DStv Padi + Showmax - N7,900",
                        "id": "ng_dstv_shownltese36",
                        "amount": 7900
                    },
                    {
                        "label": "DStv Yanga + Showmax - N7,750",
                        "id": "ng_dstv_shownnj1e36",
                        "amount": 7750
                    },
                    {
                        "label": "DStv Confam + Showmax - N12,750",
                        "id": "ng_dstv_shownnj2e36",
                        "amount": 12750
                    },
                    {
                        "label": "PRWASIE36 + SHOWMAX - N50,500",
                        "id": "ng_dstv_showprwasie36",
                        "amount": 50500
                    },
                    {
                        "label": "DStv Premium + Showmax - N44,500",
                        "id": "ng_dstv_showprwe36",
                        "amount": 44500
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

### Pay TV Subscription (API)
- **Endpoint**: `POST /business/bills/tv/pay`
- **Description**: Processes TV subscription payment via API.
- **Authentication**: API Client (`x-client-id`, `Bearer <client_secret>`)
- **Request Parameters**:
  - **Headers**:
    - `x-client-id`: Client ID (required, string).
    - `Authorization`: `Bearer <client_secret>` (required, string).
  - **Body** (`BusinessTvPaymentDTO`):
    ```json
    {
      "tv_biller_id": "3",
      "smart_card_number": "1234567890123456",
      "package_id": "PKG001",
      "amount": 15000,
      "phone_number": "08012345678",
      "customer_name": "John Doe",
      "source_account_number": "1234567890",
      "description": "DStv subscription for July"
    }
    ```
    - `tv_biller_id`: TV provider ID (required, string).
    - `smart_card_number`: Smart card number (required, string, numeric only).
    - `package_id`: Package ID (required, string).
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
        "message": "TV payment successful",
        "data": {
            "transaction_id": "TXN-01K1Y36GYVF5ZDTQ8B0MCT85V3",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": "429225.60",
            "balance_after": 414225.6,
            "transaction_reference": "TXN-01K1Y36GYVF5ZDTQ8B0MCT85V3",
            "description": "PICKSON TV payment for 1234567890123456",
            "amount": "-15000.00",
            "channel": "bank_transfer",
            "payment_provider": "Safe_Haven",
            "mode": "electronic",
            "charge": "0.00",
            "provider_charge": "0.00",
            "meta": {
                "data": {
                    "source": "api",
                    "package_id": "PKG001",
                    "business_id": "65e6e6d18d41d876944861da",
                    "smart_card_number": "1234567890123456",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T20:46:33.226Z",
            "updated_at": "2025-08-05T20:46:33.226Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid payload, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid client credentials.
  - 404 Not Found: Business or wallet not found.

### Pay TV Subscription (Dashboard)
- **Endpoint**: `POST /business/bills/tv/pay/dashboard`
- **Description**: Processes TV subscription payment via the dashboard. Requires a 4-digit transaction PIN.
- **Authentication**: JWT (`Bearer <jwt_token>`), BusinessParentWalletGuard
- **Request Parameters**:
  - **Headers**:
    - `Authorization`: `Bearer <jwt_token>` (required, string).
  - **Body** (`BusinessTvPaymentDashboardDTO`):
    ```json
    {
      "tv_biller_id": "3",
      "smart_card_number": "1234567890123456",
      "package_id": "PKG001",
      "amount": 15000,
      "phone_number": "08012345678",
      "customer_name": "John Doe",
      "source_account_number": "1234567890",
      "description": "DStv subscription for July",
      "wallet_pin": "1234"
    }
    ```
    - `tv_biller_id`: TV provider ID (required, string).
    - `smart_card_number`: Smart card number (required, string, numeric only).
    - `package_id`: Package ID (required, string).
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
        "message": "TV payment successful",
        "data": {
            "transaction_id": "TXN-01K1Y3DMGERRSFMF0SF07AE28C",
            "transaction_type": "DEBIT",
            "transaction_category": "bill payment",
            "transaction_status": "successful",
            "balance_before": "429225.60",
            "balance_after": 414225.6,
            "transaction_reference": "TXN-01K1Y3DMGERRSFMF0SF07AE28C",
            "description": "PICKSON TV payment for 1234567890123456",
            "amount": "-15000.00",
            "channel": "bank_transfer",
            "payment_provider": "Safe_Haven",
            "mode": "electronic",
            "charge": "0.00",
            "provider_charge": "0.00",
            "meta": {
                "data": {
                    "source": "dashboard",
                    "package_id": "PKG001",
                    "business_id": "65e6e6d18d41d876944861da",
                    "smart_card_number": "1234567890123456",
                    "collection_account_number": "9016997676"
                }
            },
            "beneficiary_id": null,
            "created_at": "2025-08-05T20:50:26.187Z",
            "updated_at": "2025-08-05T20:50:26.187Z",
            "_v": 1,
            "currency": "NGN"
        }
    }
    ```
- **Errors**:
  - 400 Bad Request: Invalid PIN, insufficient funds, or non-float wallet.
  - 401 Unauthorized: Invalid JWT token or missing PIN.
  - 404 Not Found: Business or wallet not found. 
