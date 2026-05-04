# Lesson 04 - Insecure Cloud Configuration

## Main component

S3 receipts bucket / DVSA-SEND-RECEIPT-EMAIL

## Root cause

External upload into trusted S3 receipt path triggers backend processing.

## Fix summary

Enable S3 Block Public Access; validate .raw keys; remove unsafe shell usage.

## Verification

See the matching screenshots in `../../screenshots/lesson04/` and the commands in `../../commands/lesson04_commands.md`.
