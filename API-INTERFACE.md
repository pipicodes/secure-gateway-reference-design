# API Interface (Example JSON)

This section provides vendor-neutral, simplified request/response examples for merchant integration.  
Responses return stable statuses and error codes for consistent merchant handling.

---

## A) Purchase
**POST** `/v1/transactions/purchase`

### Request
```json
{
  "merchantId": "m_123",
  "externalOrderId": "ORD-77812",
  "amount": 1000.00,
  "currency": "PHP",
  "paymentToken": "tok_abc123",
  "customer": {
    "email": "user@email.com",
    "ipAddress": "1.2.3.4"
  }
}
## Response (Approved)
{
  "transactionId": "tx_90001",
  "status": "APPROVED",
  "bankReferenceId": "br_555",
  "authCode": "A12345",
  "feeAmount": 10.00,
  "currency": "PHP"
}
## Response (Declined)
{
  "transactionId": "tx_90001",
  "status": "DECLINED",
  "error": {
    "code": "BANK_DECLINED",
    "message": "Payment declined"
  }
}
--
## B) Cancellation
**POST** `/v1/transactions/{transactionId}/cancel`

## Request
```json
{
  "reason": "Customer canceled prior to shipping"
}
## Response
```json
{
  "transactionId": "tx_90001",
  "status": "CANCELED",
  "bankReferenceId": "void_123"
}
--
## C) Risk Validation (standalone)
**POST** `/v1/risk/validate`

## Request
```json
{
  "merchantId": "m_123",
  "customer": {
    "email": "user@email.com",
    "ipAddress": "1.2.3.4"
  },
  "amount": 1000.00,
  "currency": "PHP"
}

## Response
```json
{
  "risk": {
    "decision": "REVIEW",
    "score": 66,
    "reasons": ["HIGH_AMOUNT", "NEW_DEVICE"]
  }
}

##Notes
	•	Use stable error codes for integrations; localize only the message text.
	•	Use idempotency keys on Purchase to avoid duplicate processing during retries/timeouts.
	•	Avoid logging sensitive payload fields; prefer tokenized payment references.
