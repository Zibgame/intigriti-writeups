# IDOR – Cross-Session Cart Manipulation (Marionnaud)

## Summary

An Insecure Direct Object Reference (IDOR) vulnerability allows an attacker to manipulate another user's cart by reusing a valid `cartId` across different sessions. This leads to unauthorized cart modification without authentication.

## Endpoint

POST /api/v2/mfr/users/anonymous/carts/{cartId}/entries

## Vulnerability Type

Broken Access Control – IDOR

## Description

The application does not validate ownership of the `cartId`. By modifying this parameter, an attacker can perform actions on another user's cart.

## Steps to Reproduce

1. Open two browser sessions:

   * Session A (normal)
   * Session B (incognito)

2. In both sessions:

   * Add a product to initialize a cart
   * Remove the product (cart stays valid)

3. In Session A:

   * Intercept request to add item
   * Extract `cartId`

4. In Session B:

   * Add a product and intercept request
   * Replace `cartId` with Session A cartId
   * Modify quantity
   * Send request

5. Observe:

   * Session B unchanged
   * Session A cart modified

## Impact

* Unauthorized addition of items
* Unauthorized removal of items
* Modification of quantities
* Disruption of checkout flow
* Loss of integrity and business logic abuse

## Proof of Concept

* poc.mp4 (full exploitation)
* requests/ (intercepted and modified HTTP requests)
* screenshots/ (visual validation)

## Root Cause

Missing authorization check on `cartId` parameter.

## Recommended Fix

* Bind `cartId` to session or user context
* Validate ownership on every request
* Reject unauthorized cart access
* Rotate or regenerate cart identifiers

## Notes

* Works without authentication (anonymous users)
* Requires valid cartId (can be leaked or guessed depending on implementation)
* Full cart control once ID is known

## Severity

Critical (CVSS 9.3)

## Author

zibgame
