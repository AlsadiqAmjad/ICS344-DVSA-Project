# Before Fix

The receipts S3 bucket accepted direct uploads with public ACLs, and uploaded `.raw` files could trigger backend Lambda processing.
