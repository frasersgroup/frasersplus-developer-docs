---
layout: page
title: Payment Integration
permalink: /pay/
---

## Overview

This guide provides technical instructions for integrating the **Frasers Plus** payment system into your e-commerce or in-store (POS) platform. It covers business flows, API integration, security considerations, and testing guidelines.

## Business Flows

To integrate with **Frasers Plus**, merchants need to understand two primary transaction flows:

- **E-commerce Payment**
- **In-store (POS) Payment**

### E-commerce Payment Flow

In an e-commerce environment, customers can choose **Frasers Plus** as a payment option. The process varies based on customer account status:

1. **Fast Track**: The customer has an existing **Frasers Plus** account and receives a push notification for payment approval. Meanwhile, their browser is redirected to the **Frasers Plus HPP** (Hosted Payment Page).
2. **Partial Match**: The customer’s information does not fully match **Frasers Plus** records. They are redirected to the HPP for verification via SMS OTP before receiving a push notification for approval.
3. **New Customer**: The customer does not have a **Frasers Plus** account. They are redirected to the HPP for onboarding. After completing registration, they receive a push notification for approval.

![E-commerce Payment Flow](assets/images/pay/e-commerce-payment.png)

### In-store Payment (POS) Flow

For in-store purchases, customers pay at the counter using the following methods:

1. **Existing Customer**: The customer logs into the **Frasers Plus** app and presents a QR code for payment at the POS.
2. **New Customer**: The store provides a QR code that links to the **Frasers Plus Landing Page** for onboarding. The customer completes the process, downloads the app, and uses a QR code for payment.

![In-store POS Payment Flow](assets/images/pay/in-store-pos.png)

## API Integrations

To enable payments through **Frasers Plus**, merchants must integrate with the following APIs:

- **E-commerce Payment API**: Initiates and processes payments via the HPP.
- **POS Payment API**: Handles in-store payments via QR code scanning.

### E-commerce Payment API

#### **POST /payment (AUTHORIZATION)**
This API request starts the payment process, returning a **Unique Payment Token (UPT)** and a redirect URL for the **Frasers Plus HPP**.

**Endpoint:**
```
POST https://{environment-url}/v1/payment
```
**Request Example:**
```json
{
  "paymentId": "09900022030372702871537096",
  "type": "AUTHORIZATION",
  "customer": {
    "id": "eff1ee72-88e6-11ec-a8a3-0242ac120002",
    "firstName": "Joe",
    "lastName": "Doe",
    "email": "joe.doe@email.com"
  },
  "merchant": {
    "id": "f41201ea-88e6-11ec-a8a3-0242ac120002",
    "redirectUrl": "https://example.com"
  },
  "paymentAmount": {
    "amount": 500.89,
    "currency": "GBP"
  }
}
```

### Webhooks

If configured, **Frasers Plus** sends webhook notifications to the merchant’s backend.

**Webhook Example:**
```json
{
  "uniquePaymentReference": "f41201ea-00e6-11ec-a8a3-987654321098",
  "type": "AUTHORIZATION",
  "status": "APPROVED",
  "paymentAmount": {
    "amount": 100.0,
    "currency": "GBP"
  }
}
```

### **PUT /payment (CAPTURE, REVERSAL, CREDIT)**

- **CAPTURE**: Finalizes an approved payment after confirming goods are available.
- **REVERSAL**: Cancels a payment before capture.
- **CREDIT**: Issues a refund to the customer after capture.

**Endpoint:**
```
PUT https://{environment-url}/v1/payment/{uniquePaymentReference}
```
**Request Example:**
```json
{
  "type": "CAPTURE",
  "paymentAmount": {
    "amount": 500.89,
    "currency": "GBP"
  }
}
```

## Security Considerations

| **API**   | **WAF Perimeter Security** | **API Inner Security**   |
| --------- | -------------------------- | ------------------------ |
| ECommerce | IP Whitelisting            | **API Key** / Fascia     |
| POS       | IP Whitelisting            | **API Key**              |

### Webhook Security

- Webhooks must include a shared key for authentication.
- IP addresses for webhook origins:
  - **Staging**: 3.121.132.125, 52.215.99.246
  - **Production**: 3.8.74.182, 3.11.167.29

## Testing & Deployment

### Development/Unit Testing

- Use sandbox APIs in **Swagger**.

### Staging

- Obtain staging API credentials.
- Access the **Frasers Plus** TestFlight app.
- Ensure firewall rules allow API access.

### Production Deployment

- Secure production API credentials.
- Finalize firewall configurations.
- Confirm the registered merchant name in production.

---

For complete technical details, refer to the [Swagger API Documentation](https://app.swaggerhub.com/apis/tymit/tymit-merchants-ecommerce-api/1.0.0).

