# S3 Block Public Access - Lesson 04

Target resource:

```text
dvsa-receipts-bucket-646278323630-us-east-1
```

Required security setting:

```text
Block all public access: enabled
BlockPublicAcls: enabled
```

Expected verification:

```bash
aws s3 cp ~/empty 's3://<receipts-bucket>/2020/20/20/TEST.raw' --acl public-read
```

Expected result: `AccessDenied`.
