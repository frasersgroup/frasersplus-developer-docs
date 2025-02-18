---
layout: home
title: Home
permalink: /
---

<div style="display:none;">
  Frasers plus developer documentation
</div>

# Frasers Plus - Partnerships Integration Handbook

| Contents                                                              |
| --------------------------------------------------------------------- |
| [Document Version Control](#document-version-control)                 |
| [Introduction](#introduction)                                         |
| [Stakeholders Clients and Systems](#stakeholders-clients-and-systems) |
| [Payment Integration](#payment-integration)                           |
| [Business Flows](#business-flows)                                     |
| [E-commerce Payment](#e-commerce-payment)                             |
| [In-store Payment (POS)](#in-store-payment-pos)                       |
| [API Integrations](#api-integrations)                                 |
| [E-commerce Payment API](#e-commerce-payment-api)                     |
| [POS Payment API](#pos-payment-api)                                   |
| [Mock API & “Magic Values”](#mock-api--magic-values)                  |
| [Full Sequence diagrams](#full-sequence-diagrams)                     |
| [E-comm flows](#e-comm-flows)                                         |
| [POS Flow](#pos-flow)                                                 |
| [Merchant Registration/Onboarding](#merchant-registrationonboarding)  |
| [Onboard Merchant Fascia](#onboard-merchant-fascia)                   |
| [Security summary](#security-summary)                                 |
| [Webhook security](#webhook-security)                                 |
| [Authentication Type](#authentication-type)                           |
| [How to Test an Integration](#how-to-test-an-integration)             |
| [Development/Unit Testing](#developmentunit-testing)                  |
| [Staging](#staging)                                                   |
| [How to Promote to Production](#how-to-promote-to-production)         |
| [Loyalty Integration](#loyalty-integration)                           |
| [Business Flows](#business-flows-1)                                   |
| [Tracking Purchases](#tracking-purchases)                             |
| [Reward Redemption](#reward-redemption)                               |
| [API Integration](#api-integration)                                   |
| [Reward Redemption API](#reward-redemption-api)                       |
| [Appendices](#appendices)                                             |
| [Glossary of Terms](#glossary-of-terms)                               |

# Document Version Control

| Version Number | Description                                                      | Issued by                  | Date Issued |
| -------------- | ---------------------------------------------------------------- | -------------------------- | ----------- |
| V0.9           | Document includes required detail for Payment Integration.       | Lisa Hay                   | 04/03/2024  |
| V1.0           | Document reviewed for 1st release                                | Ian Stacey & Chris Balchin | 08/03/2024  |
| V1.1           | Document reviewed and updated, “magic values” in Mock API added. | Ian Stacey                 | 26/04/2024  |
| V1.2           | Loyalty Transaction payload updated including descriptions       | Lisa Hay                   | 04/06/2024  |
| V1.3           | Updated to include references to webhooks for Pay.               | Ian Stacey                 | 05/11/2024  |
| V1.3.1         | Update links to reference DI                                     | Khole Jones                | 18/12/2024  |

# Introduction

In this document you will find guides and API reference documentation to integrate your business with _Frasers Plus_ product.

## Stakeholders Clients and Systems

- **Customer**: end customers who buy products from the merchant e-commerce or stores.

- **Merchant e-commerce Web**: merchant website (e-commerce) where customers are adding products to the basket to later pay with Frasers Plus system. Technically this is the front end of the merchant e-commerce.

- **Merchant Backend**: this is the backend of the merchant. It interacts with the Merchant e-commerce and Tymit Backend in order to process the different requests, i.e. payments.

- **Frasers Plus App (built by Tymit)**: this is the app built by Tymit and customised for Frasers. Customers interact with this app in order to manage purchases, instalments, loyalty points, pay with QR code in stores, approve purchases, etc.

- **Frasers Plus HPP** **(built by Tymit)**: this is the _Hosted Payment Page_ built by Tymit and customised for Frasers. It is a web site that manages all customer interactions to verify identity, onBoard new customers and wating for the customer to approve the purchase order.

- **Tymit Backend**: this is the Tymit system which manages all the on-boardings, payment methods (like QR), purchases, loyalty, etc. It interacts with Merchant Backend, **Frasers Plus App** and **Frasers Plus HPP**.

![](assets/images/pay/Picture1.png)

_A_ [_Glossary of Terms_](#glossary-of-terms) _is available at the end of this document._

# Appendices

## Glossary of Terms

- Fascia: group of stores.

- E-commerce: stores or fascia webpage. customer can pay in the e-commerce with the **Frasers Plus** option.

- POS: physical shop. Customers can pay here with the **Frasers Plus** App QR code.

- Warehouse: physical store which confirms the send of goods with Tymit. It confirms the payment captures.

- Unique Payment Token (aka UPT): token generated by Tymit per customer to pay in e-commerce or POS. It expires every X seconds.

- Basket: in e-commerce, group of items to be paid as a unique purchase. In Tymit, every unique payment token is assigned to a basket.

- Payment QR code: QR code that contains a unique payment token. Used to pay in POS.

- Landing page QR code: QR code that contains a link with the **Frasers Plus** Web landing page to start the on-boarding.

- Payment: process by which a customer pays for a purchase and obtains it. Normally is composed by 2 parts (autorization and capture).

- Authorization (e-commerce): first step of a payment process, this means the purchase is successfully registered in **Frasers Plus** system and the customer will see the purchase in the next bill. But the merchant warehouse didn’t confirm yet the shipment.

- Capture (e-commerce): second part of a payment, the merchant warehouse confirm the good can be sent and it is communicated to Tymit. If the capture is not correctly processed, it cancels the previous authorization.

- Authorization+Capture (POS): in physical stores, the payment has only 1 step, because the customer authorizes the purchase and obtain the goods at the same time.
