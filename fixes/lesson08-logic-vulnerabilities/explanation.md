# Lesson 08 - Logic Vulnerabilities

## Main component

DVSA-ORDER-UPDATE

## Root cause

Billing and update requests can be processed out of order.

## Fix summary

Reject updates after billing starts using orderStatus validation.

## Verification

See the matching screenshots in `../../screenshots/lesson08/` and the commands in `../../commands/lesson08_commands.md`.
