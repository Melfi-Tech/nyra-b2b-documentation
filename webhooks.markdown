## Overview
Webhooks provide real-time notifications for specific events related to business wallets, such as transfers and funding. Businesses must subscribe to each event type to receive corresponding webhook payloads. Webhooks are dispatched to configured HTTPS URLs, signed with HMAC-SHA256 using the webhook's secret for verification.

## Authentication
- **Webhook Configuration Endpoints**: Require a valid JWT token in the `Authorization` header as `Bearer <token>` for logged-in users (see [Business Webhooks](./business-webhooks.md)).
- **Webhook Payload Verification**: Use the `X-Nyra-Signature` header to verify the payload with the webhook's secret.

## Webhook Events
The system supports three webhook event types, each requiring explicit subscription via the [Create Webhook Configuration](./webhooks%20config.markdown#CreateWebhookConfiguration) endpoint. Businesses must include the desired event types in the `subscribed_events` array to receive notifications.

### Supported Events
- **`managed_wallet.funded`**: Triggered when a managed wallet is credited (e.g., via bank transfer or other funding methods).
- **`managed_wallet.transfer`**: Triggered when a transfer is initiated from a managed wallet to another account.
- **`managed_wallet.temporary_account_funded`**: Triggered when a temporary virtual account linked to a business wallet is funded.

## Subscription Requirement
To receive webhooks for any of the above events, a business must:
1. Create a webhook configuration using `POST /business/:businessId/webhooks` (see [Business Webhooks](./webhooks%20config.markdown#CreateWebhookConfiguration)).
2. Specify the event types (e.g., `["managed_wallet.funded", "managed_wallet.transfer"]`) in the `subscribed_events` field.
3. Ensure the configured `url` is a secure HTTPS endpoint capable of handling POST requests.

If a business does not subscribe to an event, no webhook will be dispatched for that event.

## Webhook Payloads
Each webhook payload follows a standard structure, with the `event` field indicating the event type and the `data` field containing event-specific details. Below are example payloads for each event type.

### `managed_wallet.funded` Payload
Triggered when a managed wallet is credited.

```json
{
  "event": "managed_wallet.funded",
  "data": {
    "account_number": "1234567890",
    "amount": 5000,
    "currency": "NGN",
    "narration": "Wallet funding",
    "wallet_id": "wallet_12345",
    "timestamp": "2025-08-07T12:29:00.000Z",
    "reference": "txn_987654321",
    "sender_name": "John Doe",
    "sender_account_number": "0987654321",
    "sender_bank": "Access Bank",
    "business_id": "business_12345",
    "transaction_date": "2025-08-07T12:29:00.000Z",
    "credit_account_name": "Business Inc"
  }
}
```

### `managed_wallet.transfer` Payload
Triggered when a transfer is made from a managed wallet.

```json
{
  "event": "managed_wallet.transfer",
  "data": {
    "account_number": "1234567890",
    "amount": 1000,
    "currency": "NGN",
    "narration": "Payment for services",
    "wallet_id": "wallet_12345",
    "timestamp": "2025-08-07T12:29:00.000Z",
    "reference": "txn_123456789",
    "sender_name": "Business Inc",
    "sender_account_number": "1234567890",
    "sender_bank": "NYRA WALLET",
    "business_id": "business_12345",
    "transaction_date": "2025-08-07T12:29:00.000Z",
    "credit_account_name": "Jane Smith"
  }
}
```

### `managed_wallet.temporary_account_funded` Payload
Triggered when a temporary virtual account is funded.

```json
{
  "event": "managed_wallet.temporary_account_funded",
  "data": {
    "account_number": "9876543210",
    "amount_paid": 2000,
    "currency": "NGN",
    "status": "success",
    "narration": "Virtual account funding",
    "paid_at": "2025-08-07T12:29:00.000Z",
    "reference": "txn_456789123",
    "external_reference": "ext_ref_789",
    "sender_name": "Alice Brown",
    "sender_account_number": "5432109876",
    "sender_bank": "GTBank",
    "business_id": "business_12345",
    "transaction_date": "2025-08-07T12:29:00.000Z",
    "credit_account_name": "Business Inc",
    "meta": {
      "customer_name": "Alice Brown",
      "customer_email": "alice@brown.com"
    }
  }
}
```

## Retry Mechanism
If a webhook delivery fails (e.g., due to network issues or the recipient server returning a non-2xx status), the system attempts to redeliver the webhook up to **10 times** with the following retry intervals:

- **Attempts 1–5**: Exponential backoff starting at 30 seconds, doubling each attempt (30s, 60s, 120s, 240s, 480s), with a random jitter of up to 1 second.
- **Attempts 6–10**: Fixed intervals of 1 hour, 3 hours, 6 hours, 12 hours, and 24 hours, respectively.

The retry process stops if a 2xx response is received or after the 10th attempt fails. Each attempt includes the same `X-Nyra-Signature` for verification.

## Resend Webhook Endpoint
To manually resend a webhook for a specific transaction, use the `GET /business/1/resend-webhook/:reference` endpoint. This endpoint resends the webhook to all configured URLs for the business associated with the transaction reference. For details, see [Resend Webhook](./webhooks%20config.markdown#ResendWebhook).


## Best Practices
- **Verify Signatures**: Always verify the `X-Nyra-Signature` header using the webhook's secret to ensure payload authenticity.
- **Respond Quickly**: Return a 2xx status code within 10 seconds to avoid retries.
- **Idempotency**: Use the `reference` field to handle duplicate webhook deliveries.
- **Monitor Logs**: Check server logs for retry attempts if webhooks are not received.
- **Test Subscriptions**: Use the [List Webhook Configurations](./webhooks%20config.markdown#ListWebhookConfigurations) endpoint to confirm subscription to desired events.