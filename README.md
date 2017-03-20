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
#### requestPasswordReset (email, callback)

Just provide an email and this function will send the user an email to reset their password

```js
restparse.requestPasswordReset('email@mail.com', function(err, res, body, success) {
  if (success) {
    console.log('Successfully Sent Password Reset');
  } else {
    console.log('Error: ', err, body);
  }
});
```

### Objects

Object methods are similar to the User methods except that Object methods require you to specify the Parse class name. Class names are generally passed as the first parameter.

#### createObject (className, data, callback)

Creates an object and passes to `body` whatever you passed in `data` plus the returned `createdAt` field.

```js
var dog = {
  name: 'Prince',
  breed: 'Pomeranian'
};
var className = 'Dogs';

restparse.createObject(className, dog, function(err, res, body, success) {
  console.log('object created = ', body);
  console.log('object id = ', body.objectId);
});
```

#### createObjects (className, data, callback)

Creates objects and passes to `body` whatever you passed in `data` plus the returned `createdAt` field for each object.

```js
var dogs = [
  {
    name: 'Prince',
    breed: 'Pomeranian'
  },
  {
    name: 'Queen',
    breed: 'Dandie Dinmont Terrier'
  }
]
var className = 'Dog';

restparse.createObjects(className, dogs, function(err, res, body, success) {
  console.log('objects created = ', body);
});
```

#### getObject (className, objectId, params, callback)

Gets an object based on the `objectId`.

The `params` parameter can be an object containing the query options as described [here](https://parse.com/docs/rest#queries-basic). Note that unlike the Parse API Doc, you do not have to pass in strings for the parameter values. This is all taken care of for you.

If you do not want to pass in some query parameters, you can set the callback as the second parameter.

```js
// query with parameters
var params = {
  where: { breed: "Chow Chow" },
  order: '-name'
};
restparse.getObject('<class-name>', '<object-id>', params, function(err, res, body, success) {
  console.log('found object = ', body);
});
```

#### updateObject (className, objectId, data, callback)

Updates an object. If successful, body will contain the `updatedAt` value.

```js
restparse.updateObject('<class-name>', '<object-id>',
  {name: 'new object name'},
  function(err, res, body, success) {

  console.log('object updated at = ', body.updatedAt);
});
```

#### updateObjects (className, updates, callback)

Updates objects of specified className.

Returns an array of objects with `updatedAt` value.

```js
/*
An update contains an object-id and data fields to update
{
  objectId: '<object-id>',
  data: '<data-object>'
}
*/

var updates = [
  { objectId: 'Q1BfhrqB30', data: { breed: 'Chow Chow' } },
  { objectId: 'VS5JzNyp2g', data: { breed: 'Maltese' } },
  { objectId: 'XeTBhTW4ig', data: { breed: 'Dalmatian' } },
  { objectId: 'lyDBcnsTc3', data: { breed: 'Pomeranian' } }
];

var className = 'Dog';

restparse.updateObjects(className, updates, function(err, res, body, success) {
  for (var i = 0; i < body.length; i++) {
    object = body[i];
    console.log('objects updated = at ', object.updatedAt);
  }
});
```

#### deleteObject (className, objectId, callback)

Deletes an object.

```js
restparse.deleteObject('<class-name>', '<object-id>', function(err, res, body, success) {
  if (success)
    console.log('deleted!');
  else
    console.log('failed!');
});
```

#### getObjects (className, params, callback)

Returns an array of objects in the class name. The `params` parameter can be an object containing the query options as described [here](https://parse.com/docs/rest#queries-basic). Note that unlike the Parse API Doc, you do not have to pass in strings for the parameter values. This is all taken care of for you.

If you do not want to pass in some query parameters, you can set the callback as the first parameter.

The `body` in the callback is an array of the returned objects.

```js
// get all objects (no parameters)
restparse.getObjects('Dogs', function(err, res, body, success) {
  console.log('all dogs = ', body);
});

// query with parameters
var params = {
  where: { breed: "Chow Chow" },
  order: '-name'
};
restparse.getObjects('Dogs', params, function(err, res, body, success) {
  console.log('Chow chow dogs = ', body);
});
```

##### Using `count` with `getObjects`.

You are allowed to pass in the `count` parameter when using `getObjects`. If you do, the value of `body` will be an object with the properties `results` and `count`. The `results` property contains the objects resulting from the query. See more about counting [here](https://parse.com/docs/rest#queries-counting). If you only need `count`, you may also use the helper method `countObjects`. Using `getObjects` for counting has the advantage of querying for a _limited_ list of objects and getting the total possible objects that can be queried at the same time.

```js
// Get 10 objects, but also return the total number of objects
var params = {
  where: { breed: "Chow Chow" },
  limit: 10,
  count: true
};
restparse.getObjects('Dogs', params, function(err, res, body, success) {
  console.log('The first 10 Chow chow dogs = ', body.results);
  console.log('Total number of Chow chow dogs = ', body.count);
});
```

#### countObjects (className, params, callback)

Same as getObjects but returns a count in the `body.count` parameter without returning any objects.

```js
// count all objects (no parameters)
restparse.countObjects('Dogs', function(err, res, body, success) {
  console.log('number of dogs = ', body.count);
});

// query with parameters
var params = {
  where: { breed: "Chow Chow" },
  order: '-name'
};
restparse.getObjects('Dogs', params, function(err, res, body, success) {
  console.log('Number of Chow chow dogs = ', body.count);
});
```

### Roles

Role methods are similar to the Object methods except that they don't require you to use a classname.

#### createRole (data, callback)

Creates a role and passes to `body` whatever you passed in `data` plus the returned `createdAt` field. You can only create a role if you provide the `restparse.masterKey` property.

```js
var data = {
  name: 'Administrator',
  ACL: {
      "*": {
        "read": true
      }
    },
  roles: {
      "__op": "AddRelation",
      "objects": [
        {
          "__type": "Pointer",
          "className": "_Role",
          "objectId": <role-id>
        }
      ]
    },
  users: {
      "__op": "AddRelation",
      "objects": [
        {
          "__type": "Pointer",
          "className": "_User",
          "objectId": <user-id>
        }
      ]
    }
};

restparse.createRole(data, function(err, res, body, success) {
  console.log('role created = ', body);
  console.log('object id = ', body.objectId);
});
```

#### getRole (objectId, params, callback)

Gets a role based on the `objectId`. The `params` is currently unused but is there for a future use. You can pass in the callback function as the second parameter.

```js
restparse.getRole('<object-id>', function(err, res, body, success) {
  console.log('found role = ', body);
});
```

#### updateRole (objectId, data, callback)

Updates a role. If successful, body will contain the `updatedAt` value. You can only update a role if you provide the `restparse.masterKey` property or the `restparse.sessionToken` property and if that user has write access to the role.

```js
var data = {
  users: {
      "__op": "RemoveRelation",
      "objects": [
        {
          "__type": "Pointer",
          "className": "_User",
          "objectId": <user-id>
        }
      ]
    }
};

restparse.updateRole('<role-object-id>', data, function(err, res, body, success) {
  console.log('role updated at = ', body.updatedAt);
});
```

#### deleteRole (objectId, callback)

Deletes a role. The REST API does not return anything in the body so it's best to check for `success` if the operation was successful. You can only delete a role if you provide the `restparse.masterKey` property or the `restparse.sessionToken` property and if that user has write access to the role.

```js
restparse.deleteRole('<object-id>', function(err, res, body, success) {
  if (success)
    console.log('deleted!');
  else
    console.log('failed!');
});
```



