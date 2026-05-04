# Lesson 10 - Unhandled Exceptions

## Main component

DVSA-ORDER-GET

## Root cause

Missing orderId triggers KeyError and leaks stack trace.

## Fix summary

Validate required fields with event.get() and return controlled errors.

## Verification

See the matching screenshots in `../../screenshots/lesson10/` and the commands in `../../commands/lesson10_commands.md`.
