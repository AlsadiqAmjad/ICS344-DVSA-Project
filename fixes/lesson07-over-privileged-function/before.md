# Before Fix

The `DVSA-SEND-RECEIPT-EMAIL` execution role had broad S3, DynamoDB, and SES permissions. Temporary Lambda role credentials could scan `DVSA-ORDERS-DB`.
