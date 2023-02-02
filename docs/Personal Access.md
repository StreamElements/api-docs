## Personal Access - Using JWT Secert Token to access the API
If you intend to use the API for your own stream or to any single use case, you do not need to have OAuth access. What you can do, is use your JWT token instead.

## Obtaining your token

1. Login to your StreamElements account
2. Click on the account icon (top-right, where your channel icon is)
3. Click on the channel name to get to your account page
4. Under **Channels**, on your channel's row, enable **Show Secrets** toggle
5. Copy your "JWT" Token.

**CAUTION** do not show or send this token to anybody or include it in a front end code!!!

## Accessing the API with JWT Token

This is very straight forward. You can do it with any of the API methods, just use it as your Authorisation token:

```javascript
const options = {
  method: 'GET',
  headers: {'Content-Type': 'application/json', Authorization: 'Bearer JWT_TOKEN'}
};

fetch('https://api.streamelements.com/kappa/v2/activities/channel', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```
