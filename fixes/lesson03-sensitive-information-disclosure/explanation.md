# Lesson 03 - Sensitive Information Disclosure

## Main component

DVSA-ORDER-MANAGER / DVSA-ADMIN-GET-RECEIPT

## Root cause

Injected code invokes internal admin receipt function and obtains signed S3 URL.

## Fix summary

Remove code execution path by using safe JSON parsing; keep admin functions unreachable from user input.

## Verification

See the matching screenshots in `../../screenshots/lesson03/` and the commands in `../../commands/lesson03_commands.md`.
