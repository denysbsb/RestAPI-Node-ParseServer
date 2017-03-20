# RestParse
Parse REST API Client for Node.js

install 
```
npm install @denysfontenele/restparse
```
```js
var restparse = require('restparse');

// instantiate
var config = {
  serverUrl: 'http://localhost:1337',
  applicationId: 'myAppId',
  masterKey: 'myMasterKey', // optional
  mountPath: '/parse' // optional ("/parse" by default)
};

var restparse = new restparse(config);
```
