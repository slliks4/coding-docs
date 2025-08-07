# 🔐 Fixing Auth Data Inconsistency (Technical Documentation)

## 📌 Purpose

This document outlines a **comprehensive fix** for the data inconsistency between third-party users, payment-based users, and app-authenticated users. It is intended for **backend developers**, **project managers**, and **product leads**.

## 🚨 Problem Overview

Currently, the platform has:

* Users created via **email/password** or **third-party login (Google, Facebook, Apple)**
* **Subscribers** who signed up via the mobile app (or external system) **without a user account in the database**, identified only by email (via payment gateway)

This has created multiple inconsistency issues:

* Users exist in the payment system but **not in our DB**
* Some users have **password = null**, but are valid accounts
* No standardized flow for identifying third-party vs. migrated vs. standard users

---

## ✅ Goals

* Fix the inconsistency between `user`, `auth`, and `subscription`
* Unify login logic for all types of users
* Minimize frontend complexity
* Make onboarding clean for future users

---

## 📊 User Types Summary

| User Type       | In DB? | Password? | Third-Party? | Subscriber? |
| --------------- | ------ | --------- | ------------ | ----------- |
| Email/Password  | ✅      | ✅         | ❌            | Optional    |
| Google/Facebook | ✅      | ❌         | ✅            | Optional    |
| Payment-Only    | ❌      | ❌         | ❌            | ✅           |

---

## 🧩 Solution Architecture

### 1. 🔄 Lookup Flow on Email Input

* **Step 1:** User enters email
* **Backend checks:**

  * [ ] Exists in DB?
  * [ ] If yes, what is `authSource`?
  * [ ] If not in DB, does email exist in Payment System?

### 2. 📦 Create Stub Account for Payment-Only Users

If user exists **in payment service** but **not in DB**:

* Create new user with:

  ```json
  {
    "email": "from_payment_service",
    "password": null,
    "authSource": "magic",
    "createdVia": "payment"
    "userProfile": {
        "isSubscriber": true,
        "isProfileCompleted": false,
    }
  }
  ```
* Trigger **magic link email** to complete login

### 3. 🧠 Magic Link Usage

Magic link should be sent for all users that:

* Exist in DB with `password: null`
* Have no third-party `authSource`
* OR are created during this flow

### 4. 👤 Complete Profile

After magic link login:

* If `isProfileCompleted: false` → Redirect to onboarding
* Else → Proceed to subscription check

### 5. 💳 Subscription Paywall

After profile completion:

* If `isSubscriber: false` → Redirect to paywall
* Else → Proceed to dashboard

---

## 🔄 API Flow & Contract

### Endpoint 1: `POST /auth/lookup`

#### Request

```json
{
  "email": "user@example.com"
}
```

#### Response (Possible Outcomes)

```json
// A. Existing password-based user
{
  "status": "hasPassword",
  "message": "User has password. Prompt for password."
}

// B. Google/Apple/Facebook user
{
  "status": "thirdParty",
  "provider": "google",
  "message": "Login via Google."
}

// C. Existing DB user with no password or provider
{
  "status": "magic",
  "message": "User uses magic link."
}

// D. Payment subscriber, not in DB
{
  "status": "paymentOnly",
  "message": "Migrated subscriber. Magic link sent."
}

// E. Completely new user
{
  "status": "newUser",
  "message": "New registration."
}
```

---

### Endpoint 2: `POST /auth/register-or-magic`

Used when user enters password after lookup shows `newUser` or `magic`

#### Request

```json
{
  "email": "user@example.com",
  "password": "hunter2",
  "otp": "optional"
}
```

#### Logic:

* If new user: send OTP and wait for verification
* If existing user with `password: null`: complete magic login and generate JWT

---

### Endpoint 3: `GET /auth/me`

Returns full user context for client route guards

#### Response

```json
{
  "email": "user@example.com",
  "isAuthenticated": true,
  "isSubscriber": true,
  "isProfileCompleted": false,
  "roles": ["student"],
  "permissions": ["view_dashboard"]
}
```

---

## 🎯 Frontend Strategy

1. **Unify Login/Register Flow**

   * No more split "Login" and "Register"
   * All users enter **Email First**
   * Backend determines flow from there

2. **Steps Implementation**

   ```tsx
   Step 1: Enter Email
   Step 2: Password (if needed)
   Step 3: OTP Verification (if new)
   Step 4: Magic Link Handling
   Step 5: Complete Profile
   Step 6: Paywall
   ```

3. **Settings Page** → Allow users who came from magic link to set a permanent password

---

## 🧠 Benefits

* ✅ Handles all user types (legacy + new)
* ✅ Makes backend logic clean (single source of truth)
* ✅ Onboards payment-only users without friction
* ✅ Encourages password setting for long-term users
* ✅ Reduces frontend edge-case handling

---

## 🚧 Implementation Notes

* Require **token auto-generation** when creating stub accounts (magic link)
* Ensure **`isSubscriber` and `isProfileCompleted`** are updated atomically on `/me`
* Add **unit tests** for each login state (magic, password, third-party, payment-only)

---

## ✅ Migration Plan

* Step 1: Sync emails from payment service to database
* Step 2: Implement `lookup`, `register-or-magic`, and `me` endpoints
* Step 3: Update frontend flows to use new step-based form
* Step 4: Monitor metrics: login success, bounce, magic link usage

---

## 🧾 Appendix

* `authSource` ENUM: \['password', 'google', 'facebook', 'apple', 'magic']
* New DB Flag: `createdVia: 'payment' | 'form' | 'admin'`
* Security: All magic link tokens expire in 10 mins

---
