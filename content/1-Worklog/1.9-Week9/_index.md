---
title: "Worklog Week 9"
date: 2026-07-01
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

# Week 9: Parking-Space Analysis with Lambda and Rekognition

**Period:** 06/07/2026 - 12/07/2026

## Objectives

- Determine whether a parking space contains a vehicle from a camera image.
- Move AI processing out of FastAPI into AWS Lambda.
- Store analysis results and operational logs.

## Activities

| Date | Work completed |
| --- | --- |
| 06/07/2026 | Defined the input, output, and confidence threshold for image analysis. |
| 07/07/2026 | Built a Lambda that reads S3 images and invokes Amazon Rekognition. |
| 08/07/2026 | Normalized results into occupied or empty states. |
| 09/07/2026 | Connected AI results with backend parking-space state. |
| 10/07/2026 | Tested multiple images and reviewed execution failures in CloudWatch Logs. |

## Outcomes

- Lambda analyzes camera images without increasing FastAPI workload.
- Rekognition output is converted into a simple operational state.
- CloudWatch captures sufficient data for error and duration analysis.

## Product relation

AI supports parking-space monitoring only; an Admin still reads and enters plates at the gate.
