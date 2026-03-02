## Purchase Flow (XX Gateway)

```mermaid
sequenceDiagram
autonumber
actor Merchant as Online Merchant Shop
participant GW as XX Gateway API
participant Risk as External Risk/Fraud Service
participant BC as Bank Connector (Adapter)
participant Bank as Bank API

Merchant->>GW: POST /transactions/purchase
GW->>GW: Validate auth + schema + Idempotency-Key
GW->>Risk: Risk Validate
Risk-->>GW: decision + score + reasons

alt Risk REJECT
  GW-->>Merchant: DECLINED (RISK_REJECTED)
else Risk APPROVE
  GW->>BC: Map to bank format
  BC->>Bank: Authorize
  Bank-->>BC: APPROVED/DECLINED
  BC-->>GW: Normalized response
  GW-->>Merchant: Final status
end
