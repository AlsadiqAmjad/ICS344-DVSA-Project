# After Fix

IAM policies were restricted to least privilege:

- S3 scoped to the DVSA receipts bucket.
- DynamoDB reduced to required table/actions.
- `dynamodb:Scan`, write, and delete paths removed where unnecessary.
- `AmazonSESFullAccess` replaced with send-only SES permissions.
