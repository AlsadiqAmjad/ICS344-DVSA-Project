# Lesson 06 - Denial of Service (DoS)

## Main component

API Gateway Stage / billing workflow

## Root cause

Repeated billing requests reach backend without practical throttling.

## Fix summary

Enable API Gateway stage throttling: Rate 5, Burst 10.

## Verification

See the matching screenshots in `../../screenshots/lesson06/` and the commands in `../../commands/lesson06_commands.md`.
