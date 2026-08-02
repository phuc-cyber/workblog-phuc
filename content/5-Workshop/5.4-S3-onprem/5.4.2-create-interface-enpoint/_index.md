---
title: "User Reserves a Slot and Receives a QR"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

# User Reserves a Slot and Receives a QR

## 1. Sign in as User

Open `http://localhost:3000` and sign in with a `USER` account. When Cognito is enabled, a new user confirms the email with an OTP; in local-auth mode, the backend authenticates the PostgreSQL account.

![Smart Parking sign-in screen](/images/5-Workshop/01-login.png)

*Figure 5.4.2-1: The Smart Parking sign-in screen provides role-based access for Users and Administrators.*

## 2. Select time and slot

1. Open the **Reservation** tab.
2. Select a parking lot and arrival time.
3. Choose the parking duration.
4. Reload the map so the system calculates availability for the exact time range.
5. Select an `AVAILABLE` slot.

The backend checks overlapping reservations before accepting the request. Two users cannot reserve the same slot for an overlapping period.

![User parking-slot selection screen](/images/5-Workshop/02-user-booking.png)

*Figure 5.4.2-2: A User selects the parking lot, arrival time, and an available slot from the visual map.*

## 3. Create the booking

After confirmation:

- The booking is created as `PENDING`.
- The slot becomes `RESERVED`.
- A random QR token is associated with the booking.
- A simulated hold is recorded, with a default value of `20,000 VND`.
- The QR does not contain or bind a plate number yet.

```text
User + Slot + Time range
          ↓
PENDING booking + simulated hold
          ↓
Opaque QR token
```

## 4. Validate the result

- The QR appears in the active booking list.
- History displays the time, slot, and simulated payment state.
- RDS contains new `bookings`, `qr_codes`, and `fee_summaries` records.
- S3 does not require an image yet because the vehicle has not reached the gate.

{{% notice info %}}
The same QR is used for check-in and check-out. Its lifecycle and state are held on the server; the application does not trust business data declared by the client.
{{% /notice %}}
