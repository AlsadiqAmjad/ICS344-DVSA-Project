# Lesson 05 - Broken Access Control

## Main component

DVSA-ORDER-MANAGER / DVSA-ADMIN-UPDATE-ORDERS

## Root cause

User-controlled request reaches admin order update function.

## Fix summary

Remove unsafe deserialization and restrict public-to-admin invocation path.

## Verification

See the matching screenshots in `../../screenshots/lesson05/` and the commands in `../../commands/lesson05_commands.md`.
