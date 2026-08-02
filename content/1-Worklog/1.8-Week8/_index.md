---
title: "Worklog Week 8"
date: 2026-07-01
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

# Week 8: Amazon S3 Integration for Gate and Camera Images

**Period:** 29/06/2026 - 05/07/2026

## Objectives

- Store vehicle and parking-space images in Amazon S3.
- Avoid transferring large files through the backend.
- Control object access with presigned URLs.

## Activities

| Date | Work completed |
| --- | --- |
| 29/06/2026 | Designed object-key conventions for entry, exit, and space-camera images. |
| 30/06/2026 | Created the bucket and configured required CORS and access rules. |
| 01/07/2026 | Built an API for issuing upload presigned URLs. |
| 02/07/2026 | Stored image metadata and linked it to parking sessions. |
| 03/07/2026 | Tested expired URLs, invalid formats, and unauthorized access. |

## Outcomes

- Images upload directly to S3 through time-limited URLs.
- The backend manages only image metadata and business relationships.
- The storage layout clearly separates gate and space-camera images.

## Product relation

Gate images and parking-space analysis images both rely on the Week 8 integration.
