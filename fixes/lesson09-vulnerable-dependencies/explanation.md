# Lesson 09 - Vulnerable Dependencies

## Main component

DVSA-ORDER-MANAGER dependencies

## Root cause

Known vulnerable packages, especially node-serialize, are reachable from public input.

## Fix summary

Remove node-serialize from request path; upgrade/replace vulnerable dependencies.

## Verification

See the matching screenshots in `../../screenshots/lesson09/` and the commands in `../../commands/lesson09_commands.md`.
