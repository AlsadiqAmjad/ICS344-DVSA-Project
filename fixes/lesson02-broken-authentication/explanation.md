# Lesson 02 - Broken Authentication

## Main component

DVSA-ORDER-MANAGER

## Root cause

JWT payload trusted without signature verification.

## Fix summary

Verify Cognito JWT signature and required claims before trusting identity.

## Verification

See the matching screenshots in `../../screenshots/lesson02/` and the commands in `../../commands/lesson02_commands.md`.
