# IAM Least Privilege - Lesson 07

Function role:

```text
DVSA-SEND-RECEIPT-EMAIL execution role
```

Fix approach:

- Scope S3 permissions to the DVSA receipts bucket only.
- Reduce DynamoDB permissions to required actions and resources.
- Remove `dynamodb:Scan` when not needed.
- Replace `AmazonSESFullAccess` with send-only SES permissions.

Example SES send-only actions:

```json
{
  "Effect": "Allow",
  "Action": [
    "ses:SendEmail",
    "ses:SendRawEmail"
  ],
  "Resource": "*"
}
```

Expected verification:

```bash
aws dynamodb scan --table-name DVSA-ORDERS-DB --region us-east-1
```

Expected result after fix: `AccessDeniedException`.
