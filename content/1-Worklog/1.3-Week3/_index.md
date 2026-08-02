---
title: "Worklog Week 3"
date: 2026-07-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

# Week 3: Authentication and User/Admin Authorization

**Period:** 25/05/2026 - 31/05/2026

## Objectives

- Complete registration, sign-in, and account-verification flows.
- Separate access rights for Users and Admins.
- Support local authentication and prepare Cognito integration.

## Activities

| Date | Work completed |
| --- | --- |
| 25/05/2026 | Designed account and role data. |
| 26/05/2026 | Implemented sign-in and session-state handling. |
| 27/05/2026 | Applied role checks to User and Admin APIs. |
| 28/05/2026 | Prepared a Cognito User Pool with `USER` and `ADMIN` groups. |
| 29/05/2026 | Tested valid sign-in, incorrect passwords, and unauthorized access. |

## Outcomes

- Accounts are routed to the correct workspace according to role.
- APIs reject requests without the required authorization.
- The application works with local authentication and is ready for Cognito mode.

## Product relation

The sign-in screen and separate User/Admin menus directly reflect this week's work.
