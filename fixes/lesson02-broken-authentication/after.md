# After Fix

The backend fetches Cognito JWKS keys, verifies JWT signatures and claims, then uses the verified identity only after validation succeeds.
