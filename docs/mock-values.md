---
layout: page
title: Mock
permalink: /mock-values/
---

To facilitate development and unit testing, the following “magic values” can be used to influence a specific response when using the mock API as specified in the Swagger.

Authentication  
Header name: x-api-key  
Value: test-api-token

```
POST /v1/payment

- "paymentId": "09900022030372702871537096" -\> 200 OK

- "paymentId": "09900022030372702871537097" -\> 500 Internal Server Error

- "paymentId": "09900022030372702871537098" -\> 400 Bad Request - Payment already exists

- missing paymentId -\> 400 Bad Request

GET /v1/payment/{uniquePaymentReference}

- uniquePaymentReference=7232fb1e-2140-4848-bf13-8045a974a8d0 -\> 200 OK (AUTHORIZATION - PENDING)

- uniquePaymentReference=c7680b93-f014-4ccb-9338-9aef4f4d4eed -\> 200 OK (AUTHORIZATION - APPROVED)

- uniquePaymentReference=e0ecc8b5-9b34-4f63-a6b1-c25cf921e9b8 -\> 200 OK (AUTHORIZATION - DECLINED)

- uniquePaymentReference=819784f6-09e3-4d44-b901-a920f242aa86 -\> 200 OK (CAPTURE - COMPLETED)

- uniquePaymentReference=bf01f62f-e434-469d-8b5f-1e7174bb1256 -\> 200 OK (REVERSAL - CANCELLED)

- uniquePaymentReference=4a4de685-7142-4fd6-a88c-dd41d3b93604 -\> 200 OK (CREDIT - COMPLETED)

- empty uniquePaymentReference -\> 404 Not Found

PUT /v1/payment/{uniquePaymentReference}

- uniquePaymentReference=7232fb1e-2140-4848-bf13-8045a974a8d0 -\> 200 OK (UPDATE to CAPTURE)

- uniquePaymentReference=f625e66f-b8ff-46a9-a493-eb33806f3e51 -\> 200 OK (UPDATE to REVERSAL)

- uniquePaymentReference=605a7057-8285-45a5-abf1-4d104a6b7547 -\> 200 OK (UPDATE to CREDIT)

- uniquePaymentReference=ac04b35a-f851-45f7-bc14-e47750207a0c -\> 404 Not Found

- uniquePaymentReference=605a7057-8285-45a5-abf1-4d104a6b7547 -\> 400 BAD REQUEST with "type": "AUTHORIZATION" (already created authorization)
```
