# Lesson 01 - Event Injection

## Main component

DVSA-ORDER-MANAGER

## Root cause

Unsafe node-serialize deserialization leads to RCE.

## Fix summary

Replace node-serialize with JSON.parse and safe header handling.

## Verification

See the matching screenshots in `../../screenshots/lesson01/` and the commands in `../../commands/lesson01_commands.md`.
