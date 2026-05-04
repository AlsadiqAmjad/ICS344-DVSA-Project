# Lesson 07 - Over-Privileged Function

## Main component

DVSA-SEND-RECEIPT-EMAIL execution role

## Root cause

Function role can scan DynamoDB and has broad S3/SES permissions.

## Fix summary

Reduce IAM permissions to least privilege for S3, DynamoDB, and SES.

## Verification

See the matching screenshots in `../../screenshots/lesson07/` and the commands in `../../commands/lesson07_commands.md`.
