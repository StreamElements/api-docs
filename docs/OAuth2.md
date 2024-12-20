# OAuth2

OAuth2 enables developers to build applications that utilize data from the StreamElements API.

*Apply for OAuth2 credentials [here.](https://strms.net/oauth2_request)*
*Code samples are available [here.](https://github.com/StreamElements/authentication-samples)*

| URL                                                | Description               |
|----------------------------------------------------|---------------------------|
| https://api.streamelements.com/oauth2/authorize    | Authorization endpoint    |
| https://api.streamelements.com/oauth2/token        | Token / refresh endpoint  |
| https://api.streamelements.com/oauth2/revoke       | Token Revocation endpoint |

## Authorization Code Grant

Description:

Once you receive the ```client_id``` and ```client_secret``` you will be able to make requests on behalf of the user who authorizes your application. Create a URL with the following parameters:

| Parameter         | Type   | Required | Description                                                                             |
|-------------------|--------|----------|-----------------------------------------------------------------------------------------|
| **client_id**     | string | true     | Your registered client ID                                                               |
| **redirect_uri**  | URI    | true     | Your registered redirect URI.                                                           |
| **response_type** | string | true     | this must be **code**                                                                   |
| **scope**         | string | false    | Space-separated list of scopes                                                          |
| **state**         | string | false    | A unique token generated by your application. This value is echoed back in the response |

Authorization URL Example

```text
https://api.streamelements.com/oauth2/authorize?client_id=9d5422b8ff529d420&redirect_uri=https%3A%2F%2Foauth.it-really.rocks%2Fcallback&response_type=code&scope=channel%3Aread
```

![image](https://user-images.githubusercontent.com/75918726/226506102-731911c3-9fdf-4f24-86fe-de22dfc3da38.png)

When someone navigates to this URL, they will be authorized for the requested scopes. Once accepted they will be
redirected to your *redirect_uri*, which will contain a code and state if specified.

*In case the user does not authorize the application, they will be send to your *redirect_uri* with ```error=true```.

Redirect URL Example

```text
https://oauth.it-really.rocks?code=hjqkweSNDSDQ2___12&state=ejlkqwj2u31u31i1
```

The code is now exchanged for a user access token by making a POST request with `x-www-form-urlencoded` parameters to the token URL with the following payload.

| Parameter     | Description                       |
|---------------|-----------------------------------|
| client_id     | Your registered client ID         |
| client_secret | Your registered client Secret     |
| grant_type    | This must be *authorization_code* |
| code          | The code from the OAuth callback. |
| redirect_uri  | Your registered redirect URI.     |

<!--
type: tab
title: Request using curl
-->

```text
curl -X POST "https://api.streamelements.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "grant_type=authorization_code" \
-d "client_id=9d5422b8ff529d420" \
-d "client_secret=1234567890abcdef" \
-d "code=a1b2c3d4e5f6g7h8i9j0k" \
-d "redirect_uri=https://oauth.it-really.rocks"
```
<!--
type: tab
title: Access token response
-->

```json
{
  "access_token": "5a30c9621f638600014af608123",
  "token_type": "Bearer",
  "expires_in": 604800,
  "refresh_token": "ed7a57c4cfbb540e5a30c9621f6386000",
  "scope": "channel:read"
}
```
<!-- type: tab-end -->

You need to store that information on your server to be able to refresh the access token once it expires. Refresh tokens are also updated sometimes, so make sure you update the refresh token information once you get a new access token.

## Example request

oAuth requests should use `oAuth <access_token>` as authorizatin header.

<!--
type: tab
title: Example request
-->

```text
curl -X GET "https://api.streamelements.com/kappa/v2/channels/me" \
-H "Accept: application/json" \
-H "Authorization: oAuth 5a30c9621f638600014af608123" 
```

<!--
type: tab
title: Example response
-->
```json
{
  "profile": {
    "title": "example's profile",
    "headerImage": "https://cdn.streamelements.com/uploads/cc9f302f-44e6-4527-9db5-f461678ec643.png"
  },
  "_id": "0123456789abcdefghijkl",
  "provider": "twitch",
  "broadcasterType": "affiliate",
  "suspended": false,
  "providerId": "28057703",
  "email": "example@example.com",
  "avatar": "https://static-cdn.jtvnw.net/jtv_user_pictures/01234567-0123-abcd-efgh-9876543210ab-profile_image-300x300.png",
  "type": "streamer",
  "apiToken": "abcdefghijklmnopqrstuvwxyz",
  "username": "example",
  "alias": "example",
  "displayName": "Example",
  "[...]"
}
```
<!-- type: tab-end -->

## Refreshing access token

Once your token expires, you need to request a new access token, which can be done with a POST request with `x-www-form-urlencoded` parameters to the token/refresh URL with the following payload.

| Parameter     | Description                       |
|---------------|-----------------------------------|
| client_id     | Your registered client ID         |
| client_secret | Your registered client Secret     |
| grant_type    | This must be *refresh_token*      |
| refresh_token | Your current refresh token.       |

<!--
type: tab
title: Request using curl
-->

```text
curl -X POST "https://api.streamelements.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "grant_type=refresh_token" \
-d "client_id=9d5422b8ff529d420" \
-d "client_secret=1234567890abcdef" \
-d "refresh_token=ed7a57c4cfbb540e5a30c9621f6386000"
```

<!--
type: tab
title: Refresh token response
-->

```json
{
  "access_token":"6200af6081f635a304986123c01",
  "expires_in":2592000,
  "refresh_token":"8600fbb1ed7a50540e57ca30c94c3f662",
  "scope":"",
  "token_type":"Bearer"
}
```
<!-- type: tab-end -->

## OAuth2 Scopes

More scopes will be added in the future.

| Scope            | Description                                                             |
|------------------|-------------------------------------------------------------------------|
| channel:read     | Ability to read the channel information (default)                       |
| tips:read        | Ability to read your tips                                               |
| tips:write       | Ability to create/modify/delete tips                                    |
| activities:read  | Ability to read your activities                                         |
| activities:write | Ability to create activities                                            |
| loyalty:read     | Ability to read loyalty settings and the leaderboard                    |
| loyalty:write    | Ability to update loyalty settings and update the leaderboard           |
| overlays:read    | Ability to read overlays                                                |
| overlays:write   | Ability to update and create overlays                                   |
| bot:read         | Ability to read timers, commands, spam filters and modules              |
| bot:write        | Ability to create and update timers, commands, spam filters and modules |

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

## Revoking tokens

If you want to revoke a token, send a POST request to the revoke URL with the following parameters in the URL. 

| Parameter         | Type   | Required | Description                                                                             |
|-------------------|--------|----------|-----------------------------------------------------------------------------------------|
| **client_id**     | string | true     | Your registered client ID                                                               |
| **token**         | string | true     | Access token to be revoked                                                              |

*No body is sent if the revokation is succeeded. Header returns with status code 200 (OK)

```text
curl -X POST "https://api.streamelements.com/oauth2/revoke?client_id=9d5422b8ff529d420&token=<access token>"
```
