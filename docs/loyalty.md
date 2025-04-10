---
layout: page
title: Loyalty Integration
permalink: /loyalty/
---

## Business Flows

### Tracking Purchases

Merchants send transaction data (e-commerce & POS) to the Loyalty backend for points calculation.  This updates the customer's Frasers Plus app balance.

![Tracking Purchases Flow](assets/images/loyalty/b5e53686f4f8b75f1659f5696be01598.jpeg)

### Reward Redemption

#### E-commerce

1.  Customer enters Reward ID & PIN.
2.  Merchant Backend requests authorization from Loyalty API.
3.  Payment authorized; funds reserved.
4.  Merchant Backend initiates capture/void/refund calls (see [API Integration](#api-integration)).

![E-commerce Redemption Flow](assets/images/loyalty/fa4a94bd2eca5dbdb10e13c897e52d96.jpg)

#### Physical Stores (POS)

1.  Customer scans Reward ID.
2.  Merchant Backend/POS requests authorization & capture from Loyalty API.
3.  Payment authorized & captured.
4.  Merchant Backend/POS initiates refund calls (see [API Integration](#api-integration)).

![POS Redemption Flow](assets/images/loyalty/33e5da879cb90b4a4172839490a3667b.png)

## API Integration

### Transaction API

*   **Purpose:**  Send transaction data (Order Completed, Order Refunded) for points calculation.
*   **Contact:** FGFS for field mapping & data sending method (batch preferred).

#### Example: Tracking Plan

| Property Name           | Description                                  | Status   | Type   |
| :---------------------- | :------------------------------------------- | :------- | :----- |
| affiliation             | Affiliate partner (e.g., 'rakuten')          | Optional | string |
| billing\_address\_1    | Billing Address Line 1                       | Optional | string |
| billing\_country       | Billing Address Country                      | Optional | string |
| billing\_postcode      | Billing Address Postcode                     | Optional | string |
| currency                | Customer's Currency (e.g., 'GBP')            | Required | string |
| discount                | Total Order Discount (local currency)        | Required | number |
| discount\_gbp           | Total Order Discount (GBP)                   | Required | number |
| email                   | User's Verified Email                        | Required | string |
| fascia                  | Fascia Short Code (from FGFS)               | Required | string |
| loyalty\_id             | Frasers Plus Account ID                      | Required | string |
| order\_id                | Customer-Facing Order ID                     | Required | string |
| payment\_methods        | Payment Methods Used                         | Required | array  |
| products                | Array of Products in Cart                     | Required | array  |
| products[].name        | Product Name                                 | Required | string |
| products[].price       | Product Unit Price (local currency, incl tax) | Required | number |
| products[].quantity    | Product Quantity                             | Required | number |
| products[].sku         | Product SKU (11 digits)                      | Required | string |
| subtotal                | Products Subtotal (local currency, excl tax) | Required | number |
| tax                     | Total Order Tax (local currency)             | Required | number |
| total                   | Total Order Value (local currency, incl tax) | Required | number |

### Analytics Integration with Segment

Segment is used to track customer interactions and loyalty events across our platform. Here's how to implement tracking:

#### Available Libraries
- **Web/JavaScript**: Use `analytics.js` library
  ```javascript
  analytics.track('Order Completed', {
    loyalty_id: 'user123',
    points: 100,
    order_id: 'ORDER123'
  });
  ```

- **Server-Side**: Libraries for Node.js, Python, Ruby, Java, PHP, and .NET

#### Key Events to Track
- `Order Completed`
- `Order Refunded`


For a list of supporting libraries, refer to [Segment Libraries](https://segment.com/docs/connections/sources/catalog/).
For detailed implementation (Javascript Example), refer to [Segment's documentation](https://segment.com/docs/connections/sources/catalog/libraries/website/javascript/).

### Reward Redemption API

##### POST /redeem (AUTHORIZATION)

*   **Purpose:** Authorize Reward payment.
*   **URL:** `POST <https://brand.external.frasers.plus/redeem/v1/authorize>`

**Request:**
```json
{
  "loyaltyId": "A1B2C3D4E5",
  "pin": "1111",
  "fascia": "FLAN",
  "storeId": "1234",
  "orderId": "FLAN40000012345678",
  "currency": "GBP",
  "redemptionAmount": 1.23
}
```

**Response:**
```json
{
  "status": "SUCCESS",
  "transactionId": "TXN123456789",
  "authorizedAmount": 1.23,
  "remainingBalance": 8.77,
  "timestamp": "2025-03-24T13:27:07Z"
}
```

##### POST /redeem (CAPTURE) 

*   **Purpose:** Capture authorized Reward payment (goods ready to ship).
*   **URL:** `POST <https://brand.external.frasers.plus/redeem/v1/capture>`

**Request:**
```json
{
  "token": "3ZDKCbGXK3FGtscnX",
  "amount": 1.23
}
```

**Response:**
```json
{
  "status": "SUCCESS",
  "transactionId": "TXN123456789",
  "capturedAmount": 1.23,
  "remainingBalance": 8.77,
  "timestamp": "2025-03-24T13:27:07Z"
}
```

##### POST /redeem (VOID) 

*   **Purpose:** Cancel authorized Reward payment (e.g., product unavailable).
*   **URL:** `POST <https://brand.external.frasers.plus/redeem/v1/void>`

**Request:**
```json
{
  "token": "3ZDKCbGXK3FGtscnX"
}
```

**Response:**
```json
{
  "status": "SUCCESS",
  "transactionId": "TXN123456789",
  "voidedAmount": 1.23,
  "remainingBalance": 10.00,
  "timestamp": "2025-03-24T13:27:07Z"
}
```

##### POST /redeem (REFUND) 

*   **Purpose:** Refund Reward payment (customer returned goods).
*   **URL:** `POST <https://brand.external.frasers.plus/redeem/v1/refund>`

**Request:**
```json
{
  "token": "3ZDKCbGXK3FGtscnX",
  "amount": 1.23
}
```

**Response:**
```json
{
  "status": "SUCCESS",
  "transactionId": "TXN123456789",
  "refundedAmount": 1.23,
  "remainingBalance": 9.00,
  "timestamp": "2025-03-24T13:27:07Z"
}
```

#### Physical Stores (POS) 

##### POST /redeem (AUTHORIZATION_CAPTURE) 

*   **Purpose:** Authorize & Capture Reward payment simultaneously.
*   **URL:** `POST <https://brand.external.frasers.plus/redeem/v1/authCapture>`

**Request:**
```json
{
  "loyaltyId": "A1B2C3D4E5",
  "pin": "1111",
  "fascia": "FLAN",
  "storeId": "1234",
  "orderId": "FLAN40000012345678",
  "currency": "GBP",
  "redemptionAmount": 1.23
}
```

**Response:**
```json
{
  "status": "SUCCESS",
  "transactionId": "TXN123456789",
  "authorizedAmount": 1.23,
  "capturedAmount": 1.23,
  "remainingBalance": 8.77,
  "timestamp": "2025-03-24T13:27:07Z"
}