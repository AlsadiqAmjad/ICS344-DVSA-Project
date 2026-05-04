# Lesson 07 - Over-Privileged Function

> This lesson demonstrates IAM blast radius in a controlled lab. Never commit real AWS credentials, access keys, secret keys, or session tokens.

## Temporary Lambda debug line used in the controlled test

```python
def lambda_handler(event, context):
    print(dict(os.environ))
    # normal function logic continues...
```

## Trigger the receipt Lambda with a dummy S3 object

```bash
touch ~/empty
aws s3 cp ~/empty \
  s3://<dvsa-receipts-bucket>/2020/20/20/trigger.raw
```

## CloudWatch evidence location

```text
/aws/lambda/DVSA-SEND-RECEIPT-EMAIL
```

Look for the temporary environment dictionary. Redact these values before any screenshot or commit:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```

## Assume the Lambda identity locally for the impact test

```bash
export AWS_ACCESS_KEY_ID="<redacted-lambda-access-key>"
export AWS_SECRET_ACCESS_KEY="<redacted-lambda-secret-key>"
export AWS_SESSION_TOKEN="<redacted-lambda-session-token>"
```

## Pre-fix impact proof

```bash
aws dynamodb scan --table-name DVSA-ORDERS-DB --region us-east-1
```

Expected pre-fix evidence: order records are returned.

## IAM Policy Simulator checks

Test S3 actions:

```text
s3:GetObject
s3:PutObject
s3:DeleteObject
```

Test DynamoDB actions:

```text
dynamodb:GetItem
dynamodb:PutItem
dynamodb:DeleteItem
dynamodb:Scan
```

## CloudTrail quick trail

```text
Trail name: dvsa-policygen-trail
Region: us-east-1
Management events: On
Data events: Off
```

## Least privilege policy changes

S3 scope should be reduced to the receipts bucket only:

```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:PutObject"],
  "Resource": "arn:aws:s3:::<dvsa-receipts-bucket>/*"
}
```

DynamoDB should remove `Scan`, `PutItem`, and `DeleteItem` when not needed:

```json
{
  "Effect": "Allow",
  "Action": ["dynamodb:GetItem", "dynamodb:Query"],
  "Resource": [
    "arn:aws:dynamodb:us-east-1:<account-id>:table/DVSA-ORDERS-DB",
    "arn:aws:dynamodb:us-east-1:<account-id>:table/DVSA-ORDERS-DB/index/*"
  ]
}
```

SES should be send-only:

```json
{
  "Effect": "Allow",
  "Action": ["ses:SendEmail", "ses:SendRawEmail"],
  "Resource": "*"
}
```

## Post-fix verification

```bash
aws dynamodb scan --table-name DVSA-ORDERS-DB --region us-east-1
```

Expected result:

```text
AccessDeniedException for dynamodb:Scan
```
