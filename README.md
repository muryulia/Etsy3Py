# Etsy3Py

Client for Etsy API v3 (Python).

## Installation

The package is currently available from GitHub. You can install `etsy3py` using pip:

```bash
pip install git+https://github.com/muryulia/Etsy3Py.git
```

## Requirements

Python 3.6 or higher.

## Etsy API

Etsy API v3 requests require:
- `Authorization: Bearer <access_token>` (for scoped endpoints)
- `x-api-key: <client_id>:<shared_secret>` (new required format, https://github.com/etsy/open-api/discussions/1529)

## Usage

To use the `EtsyApi` class, you will need to obtain an access token from the Etsy API.

```python
from etsy3py.v3 import EtsyApi

# Replace these with your values from the Etsy Developer Console
access_token = "YOUR_ACCESS_TOKEN"
client_id = "YOUR_CLIENT_ID"
shared_secret = "YOUR_SHARED_SECRET"

etsy_api = EtsyApi(
    access_token=access_token,
    client_id=client_id,
    shared_secret=shared_secret
)

listing_id = 12345
response = etsy_api.get_listing(listing_id)
print(response.status_code)
print(response.json())
```

## Quick test: Get my user info (`get_me`)

A simple smoke test to verify that authentication and headers are set correctly.

```python
from etsy3py.v3 import EtsyApi

# Replace these with your values from the Etsy Developer Console
access_token = "YOUR_ACCESS_TOKEN"
client_id = "YOUR_CLIENT_ID"
shared_secret = "YOUR_SHARED_SECRET"

etsy_api = EtsyApi(
    access_token=access_token,
    client_id=client_id,
    shared_secret=shared_secret
)

response = etsy_api.get_me()
print(response.status_code)
print(response.json())
```

## Authentication

The `EtsyApi` class uses OAuth 2.0 access tokens. You need to obtain an access token before making requests.  
See Etsy documentation for details: https://developers.etsy.com/documentation

## Authentication step-by-step (OAuth)

`EtsyOAuthClient` is a Python class that provides an OAuth2 authentication client for Etsy (PKCE flow).

### Usage

```python
from etsy3py.oauth import EtsyOAuthClient

# Replace these with your values from the Etsy Developer Console
client_id = "your_client_id"
client_secret = "your_client_secret"
redirect_uri = "your_redirect_uri"
scope = ["your_scope_1", "your_scope_2"]

client = EtsyOAuthClient(
    client_id=client_id,
    client_secret=client_secret,
    redirect_uri=redirect_uri,
    scope=scope,
)

authorization_url, state = client.authorization_url()
print("Open this URL in a browser:", authorization_url)

# Redirect the user to the authorization URL to grant access.
# After the user grants access, you will receive an authorization code:
authorization_code = "the_authorization_code"

token = client.fetch_token(authorization_code)
access_token = token["access_token"]
refresh_token = token.get("refresh_token")

print("access_token:", access_token)
print("refresh_token:", refresh_token)
```

You can now use the access token to make requests to the Etsy API:

```python
from etsy3py.v3 import EtsyApi

etsy_api = EtsyApi(
    access_token=access_token,
    client_id=client_id,
    shared_secret=client_secret,  # shared_secret = client_secret from the Developer Console
)
```

## Refresh token

The `refresh_token` method of the `EtsyOAuthClient` class requests a new access token from the authorization server using a refresh token.

### Parameters

- `refresh_token` (required): The refresh token used to obtain a new access token.

### Return Value

The `refresh_token` method returns a dictionary containing the new access token and any additional data returned by the authorization server.

### Usage

```python
from etsy3py.oauth import EtsyOAuthClient

# Replace these with your own values from the Etsy Developer Console
client_id = "your_client_id"
client_secret = "your_client_secret"

# Create an instance of the EtsyOAuthClient
client = EtsyOAuthClient(client_id=client_id, client_secret=client_secret)

# If the access token expires, you can use the refresh token to obtain a new one
refresh_token = "YOUR_REFRESH_TOKEN"
new_token = client.refresh_token(refresh_token)

new_access_token = new_token["access_token"]
print("new_access_token:", new_access_token)
```

## Rate Limiting

The Etsy API has a rate limiting policy that limits the number of requests that can be made in a given time period.
If you receive HTTP 429 responses, retry with backoff.

## Changelog

### Unreleased
- TBD

### 0.2.0
- Updated Etsy API authentication header format: `x-api-key` now uses `<client_id>:<shared_secret>`.

### 0.1.0
- Initial release: OAuth (PKCE) helper and basic Etsy API v3 client.

## License

MIT
