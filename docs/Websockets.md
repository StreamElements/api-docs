## Connecting via Websocket using OAuth2

After gaining the AccessToken make sure to pass it in the const in the JS bellow.

Each event processed via the websockets can be seen
documented [here](https://dev.streamelements.com/docs/widgets/6707a030af0b9-custom-widget-events)

### JSON Schema

```json json_schema
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "StreamElements websocket widget schema",
  "type": "object",
  "properties": {
    "_id": {
      "type": "string",
      "pattern": "^[a-f0-9]{24}$",
      "example": "1234567890abcdef12345678",
      "description": "StreamElements hexadecimal Event ID"
    },
    "channel": {
      "type": "string",
      "pattern": "^[a-f0-9]{24}$",
      "example": "1234567890abcdef12345678",
      "description": "StreamElements hexadecimal Channel ID"
    },
    "type": {
      "enum": [
        "cheer",
        "follow",
        "host",
        "raid",
        "subscriber",
        "tip"
      ],
      "description": "Event type"
    },
    "provider": {
      "enum": [
        "twitch",
        "youtube",
        "facebook"
      ],
      "description": "Event provider"
    },
    "flagged": {
      "type": "boolean"
    },
    "data": {
      "type": "object",
      "properties": {
        "tipId": {
          "type": "string",
          "pattern": "^[a-f0-9]{24}$",
          "example": "1234567890abcdef12345678",
          "description": "StreamElements hexadecimal tip ID"
        },
        "username": {
          "type": "string",
          "description": "Twitch username",
          "pattern": "^[a-z][a-z0-9_]{3,15}$"
        },
        "providerId": {
          "type": "string",
          "format": "number",
          "description": "Twitch channel ID"
        },
        "displayName": {
          "type": "string",
          "description": "Twitch username case sensitive plus regional characters if provided"
        },
        "amount": {
          "type": "integer",
          "description": "Amount of currency in tip, months in subs, hosting/raiding viewers, bits in cheer"
        },
        "streak": {
          "type": "integer",
          "description": "Current months streak (subs only)"
        },
        "tier": {
          "enum": [
            "1000",
            "2000",
            "3000",
            "prime"
          ],
          "description": "Subscriber tier (subs only)"
        },
        "currency": {
          "type": "string",
          "pattern": "[A-Z]{3}",
          "description": "Currency symbol (tips)"
        },
        "message": {
          "type": "string",
          "description": "User provided message"
        },
        "quantity": {
          "type": "integer"
        },
        "items": {
          "type": "array",
          "items": {},
          "description": "Redeemed items"
        },
        "avatar": {
          "type": "string",
          "format": "uri",
          "description": "URI of user avatar"
        }
      },
      "required": [
        "username",
        "displayName",
        "amount",
        "message",
        "items",
        "avatar"
      ]
    },
    "createdAt": {
      "type": "string",
      "format": "date-time",
      "description": "Event creation date and time"
    },
    "updatedAt": {
      "type": "string",
      "format": "date-time",
      "description": "Event update date and time"
    }
  },
  "required": [
    "_id",
    "channel",
    "type",
    "provider",
    "flagged",
    "data",
    "createdAt",
    "updatedAt"
  ]
}
```

### Code snippet for connecting to Websocket with OAuth2:

Make sure that after you obtain your Access Token, you use it in the "accessToken" constant in the JS part

HTML:

```html

<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.2.0/socket.io.js"
        integrity="sha256-yr4fRk/GU1ehYJPAs8P4JlTgu0Hdsp4ZKrx8bDEDC3I=" crossorigin="anonymous"></script>
```

JS:
<!--
title: "Websocket connection example"
lineNumbers: true
-->

```js
// AccessToken is grabbed from OAuth2 authentication of the account.
const accessToken = "";
// JWT is available here: https://streamelements.com/dashboard/account/channels
const jwt = "";
const socket = io('https://realtime.streamelements.com', {
    transports: ['websocket']
});
// Socket connected
socket.on('connect', onConnect);
// Socket got disconnected
socket.on('disconnect', onDisconnect);
// Socket is authenticated
socket.on('authenticated', onAuthenticated);
socket.on('unauthorized', console.error);
socket.on('event:test', (data) => {
    console.log(data);
    // Structure as on https://github.com/StreamElements/widgets/blob/master/CustomCode.md#on-event
});
socket.on('event', (data) => {
    console.log(data);
    // Structure as on https://github.com/StreamElements/widgets/blob/master/CustomCode.md#on-event
});
socket.on('event:update', (data) => {
    console.log(data);
    // Structure as on https://github.com/StreamElements/widgets/blob/master/CustomCode.md#on-session-update
});
socket.on('event:reset', (data) => {
    console.log(data);
    // Structure as on https://github.com/StreamElements/widgets/blob/master/CustomCode.md#on-session-update
});


function onConnect() {
    console.log('Successfully connected to the websocket');
    socket.emit('authenticate', {method: 'oauth2', token: accessToken});
    //socket.emit('authenticate', {method: 'jwt', token: jwt});
}

function onDisconnect() {
    console.log('Disconnected from websocket');
    // Reconnect
}

function onAuthenticated(data) {
    const {
        channelId
    } = data;
    console.log(`Successfully connected to channel ${channelId}`);
}
```
