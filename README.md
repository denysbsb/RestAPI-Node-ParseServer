# RestParse
Parse REST API Client for Node.js

install 
```
npm install @denysfontenele/restparse
```
```js
var restparse = require('restparse');

var config = {
  serverUrl: 'http://localhost:1337',
  applicationId: 'myAppId',
  masterKey: 'myMasterKey', 
  mountPath: '/parse'
};

var restparse = new restparse(config);
```

### Callbacks

All callbacks should follow this format: `function(error, response, body, success) { ... }`. This is because restparse is based on [Request](https://github.com/mikeal/request)

 Read more about the Response format [here](https://parse.com/docs/rest#general-responses).

 * __body__: On successful requests, this will be an object or array depending on the method called.

 * __success__: A convenience parameter, this is a boolean indicating if the request was a success.

### Users

#### createUser (data, callback)

This will pass to `body` whatever you passed in `data` plus the returned `createdAt` and `sessionToken` fields.

```js
var userInfo = {
  // required
  username: 'maricris',
  password: 'whew',

  name: 'Maricris',
  gender: 'female',
  nickname: 'Kit'
};

restparse.createUser(userInfo, function(err, res, body, success) {
  console.log('user created with session token = ', body.sessionToken);
  console.log('object id = ', body.objectId);
});
```
#### getUser (objectId, params, callback)

Gets a user info based on the `objectId` (user id). The `params` is currently unused but is there for a
future use. You can pass in the callback function as the second parameter.

```js
restparse.getUser('<object-id>', function(err, res, body, success) {
  console.log('user info = ', body);
});
```

#### loginUser (username, password, callback)

Log in a user. This will give you a user's `sessionToken` that you can use in `updateUser` and `deleteUser`, and other API calls that may need a `sessionToken`.

```js
restparse.loginUser('username', 'my secret password', function(err, res, body, success) {
  console.log('user logged in with session token = ', body.sessionToken);
});
```

#### getCurrentUser (callback)

Get active user based on the current`sessionToken`. Use `loginUser` or `createUser` to obtain the session token. You can also use this function to validate a `sessionToken`.

```js
restparse.sessionToken = 'le session token';
restparse.getCurrentUser(function(err, res, body, success) {
  if (success)
    console.log('Session token is valid for user ', body.username);
});
```

#### updateUser (objectId, data, callback)

Updates a user object (if that wasn't obvious). This requires a sessionToken received from `loginUser` or `createUser`. If successful, body will contain the `updatedAt` value.

```js
restparse.sessionToken = 'le session token';
restparse.updateUser('<object-id>', {name: 'new name'}, function(err, res, body, success) {
  console.log('updated at = ', body.updatedAt);
});
```

#### deleteUser (objectId, data, callback)

Deletes a user. Like `updateUser()`, this needs a `sessionToken`.

```js
restparse.sessionToken = '<user-seassion-token>';
restparse.deleteUser('<object-id>', function(err, res, body, success) {
  if (success)
    console.log('deleted!');
  else
    console.log('failed!');
});
```

#### getUsers (params, callback)

Returns an array of users. The `params` parameter can be an object containing the query options as described [here](https://parse.com/docs/rest#queries-basic). Note that unlike the Parse API Doc, you do not have to pass in strings for the parameter values. This is all taken care of for you.

If you do not want to pass in some query parameters, you can set the callback as the first parameter.

The `body` in the callback is an array of the returned objects.

```js
// get all users (no parameters)
restparse.getUsers(function(err, res, body, success) {
  console.log('all users = ', body);
});

// query with parameters
var params = {
  where: { gender: "female" },
  order: '-name'
};
restparse.getUsers(params, function(err, res, body, success) {
  console.log('female users = ', body);
});
```


