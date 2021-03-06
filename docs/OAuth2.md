# OAuth2

OAuth2 enables developers to build applications that utilize data from the StreamElements API.

*Apply for OAuth2 credentials [here.](https://strms.net/oauth2_request)*
*Code samples are available [here.](https://github.com/StreamElements/authentication-samples)*

URL | Description
---|---
https://api.streamelements.com/oauth2/authorize | Authorization endpoint
https://api.streamelements.com/oauth2/token | Token endpoint
https://api.streamelements.com/oauth2/token/revoke | Token Revocation endpoint

## Authorization Code Grant

Description:

Parameter|Type|Required|Description
---|---|---|---
**client_id**| string | true | Your registered client ID
**redirect_uri**|URI|true|Your registered redirect URI.
**response_type**|string|true|this must be **code**
**scope**|string|false|Space-separated list of scopes
**state**|string|false|A unique token generated by your application. This value is echoed back in the response

Authorization URL Example

```text
https://api.streamelements.com/oauth2/authorize?client_id=9d5422b8ff529d420&redirect_uri=https%3A%2F%2Foauth.it-really.rocks%2Fcallback&response_type=code&scope=channel%3Aread
```

When someone navigates to this URL, they will be authorized for the requested scopes. Once accepted they will be
redirected to your *redirect_uri*, which will contain a code and state if specified.

Redirect URL Example

```text
https://oauth.it-really.rocks?code=hjqkweSNDSDQ2___12&state=ejlkqwj2u31u31i1
```

The code is now exchanged for a user access token by making a POST request to the token URL with the following payload.

Parameter|Description
---|---
client_id|Your registered client ID
client_secret|Your registered client Secret
grant_type|This must be *authorization_code*
code|The code from the OAuth callback.
redirect_uri|Your registered redirect URI.

Access token response

```json
{
  "access_token": "5a30c9621f638600014af608123",
  "token_type": "Bearer",
  "expires_in": 604800,
  "refresh_token": "ed7a57c4cfbb540e5a30c9621f6386000",
  "scope": ""
}
```

## OAuth2 Scopes

More scopes will be added in the future.

Scope|Description
---|---
tips:read|    Ability to read your tips
tips:write|    Ability to create/modify/delete tips
activities:read|    Ability to read your activities
activities:write|    Ability to create activities
loyalty:read|    Ability to read loyalty settings and the leaderboard
loyalty:write|    Ability to update loyalty settings and update the leaderboard
overlays:read|    Ability to read overlays
overlays:write|    Ability to update and create overlays
store:read|    Ability to read redemptions and items
store:write|    Ability to create new store items and complete redemptions
bot:read|    Ability to read timers, commands, spam filters and modules
bot:write|    Ability to create and update timers, commands, spam filters and modules
session:read|    Ability to read your session values
contest:read|    Ability to read contests
contest:write|    Ability to create and update contests
giveaway:read|    Ability to read giveaways
giveaway:write|    Ability to create and update giveaways

## Validating Requests

<!--
type: tab
title: Sample request
-->

```text
curl -H "Authorization: OAuth <access token>" https://api.streamelements.com/oauth2/validate
```

<!--
type: tab
title: Sample response
-->

```json
{
  "channel_id": "5a30c9621f638600014af608",
  "client_id": "ed7a57c4cfbb540e",
  "expires_in": 2468006,
  "scopes": []
}
```
<!-- type: tab-end -->

