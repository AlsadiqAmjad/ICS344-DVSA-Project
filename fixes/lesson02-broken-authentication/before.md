# Before Fix

The backend decoded JWT payloads and trusted `username` without verifying the signature.
