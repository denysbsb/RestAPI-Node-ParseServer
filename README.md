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
