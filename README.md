# ICS344 DVSA Vulnerability Discovery and Remediation

**Student:** Amjad Alsadiq  
**ID:** 202269000  
**Course:** ICS344 - Information Security  
**Section:** 03  
**AWS Region:** us-east-1  
**DVSA Website:** http://dvsa-website-kfupm-646278323630-us-east-1.s3-website.us-east-1.amazonaws.com/

This repository contains the organized deliverables for the DVSA vulnerability discovery and remediation project. It is designed so that a beginner can follow the structure, understand each lesson, review the evidence, and reproduce the controlled demonstrations inside their own DVSA lab deployment.

> **Academic / safety note:** DVSA is intentionally vulnerable and must only be tested in a dedicated non-production AWS account that you own or are authorized to use. Do not use these notes, commands, or payloads against third-party systems.

## What is included

- `report/` - final project report and presentation slides.
- `screenshots/` - evidence screenshots organized by lesson.
- `commands/` - reproduction and verification command notes for each lesson, with placeholders for tokens and IDs.
- `fixes/` - before/after fix notes, code snippets, and configuration-change summaries.

## Lessons covered

| Lesson | Vulnerability | Main affected component | Fix summary |
|---|---|---|---|
| 01 | Event Injection | DVSA-ORDER-MANAGER | Replace node-serialize with JSON.parse and safe header handling |
| 02 | Broken Authentication | DVSA-ORDER-MANAGER | Verify Cognito JWT signature and required claims before trusting identity |
| 03 | Sensitive Information Disclosure | DVSA-ORDER-MANAGER / DVSA-ADMIN-GET-RECEIPT | Remove code execution path by using safe JSON parsing; keep admin functions unreachable from user input |
| 04 | Insecure Cloud Configuration | S3 receipts bucket / DVSA-SEND-RECEIPT-EMAIL | Enable S3 Block Public Access; validate .raw keys; remove unsafe shell usage |
| 05 | Broken Access Control | DVSA-ORDER-MANAGER / DVSA-ADMIN-UPDATE-ORDERS | Remove unsafe deserialization and restrict public-to-admin invocation path |
| 06 | Denial of Service (DoS) | API Gateway Stage / billing workflow | Enable API Gateway stage throttling: Rate 5, Burst 10 |
| 07 | Over-Privileged Function | DVSA-SEND-RECEIPT-EMAIL execution role | Reduce IAM permissions to least privilege for S3, DynamoDB, and SES |
| 08 | Logic Vulnerabilities | DVSA-ORDER-UPDATE | Reject updates after billing starts using orderStatus validation |
| 09 | Vulnerable Dependencies | DVSA-ORDER-MANAGER dependencies | Remove node-serialize from request path; upgrade/replace vulnerable dependencies |
| 10 | Unhandled Exceptions | DVSA-ORDER-GET | Validate required fields with event.get() and return controlled errors |

## Quick start for graders / reviewers

1. Open `report/Project_Report.docx` for the full investigation.
2. Open `report/ICS344_DVSA_Project_Presentation.pptx` for the short presentation summary.
3. Review `commands/lessonXX_commands.md` to see how each lesson was reproduced and verified.
4. Review `fixes/lessonXX-*/` for the remediation notes.
5. Review `screenshots/lessonXX/` for evidence images.

# Security and Responsible Use

This repository documents an academic DVSA lab completed for ICS344. The contents are intended only for authorized testing in a controlled DVSA deployment.

It does NOT contain:

- Real AWS access keys, secret keys, or session tokens
- Real JWTs
- Unblurred screenshots containing credentials
- Private production URLs or resources

All reproduction commands should use placeholders and must be executed only against a DVSA instance that you own or are explicitly authorized to test.