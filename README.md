# softlayer-node
[Softlayer API](http://sldn.softlayer.com/reference/softlayerapi) wrapper for node. The module provides a simple fluent interface to construct API calls.

## Installation
The most simple way is to install the npm package:
```
npm install softlayer-node --save
```

## Usage
First, require the module and create an instance of the client:
```javascript
var SoftLayer = require('softlayer-node');
var client = new SoftLayer();
```
Every request to the SoftLayer REST API must be authenticated. Internally the wrapper uses basic authentication using the API User and API key. You find these values on the Softlayer portal. To provide credentials to the wrapper, you have two options
- by node environment variables SOFTLAYER_API_USER and SOFTLAYER_API_KEY.
- by passing user and key to the constructor of the API client
- by using the auth() method.
*Please note:* Setting credentials in the constructor overwrites the values coming from the environment variables, and the auth() method overwrites both environment variables and the values set in the constructor.

#### Fluent interface
The module exposes the following chainable methods:
##### path()
With this method the URL is constructed. You can pass an array of path elements, or simply string arguments to the method.
```javascript
client.path('PathElement1','PathElement1').path(['PathElement3', 'PathElement4']);
```
*Please note:* Dont't add the 'SoftLayer_' prefix to the path elements, as only the first element has to have this prefix and the client takes care of this.
For exmaple: Softlayer_Billing_Invoice becomes just Billing_Invoice.
##### id()
This method is an alias for the path() method. Use it to add an id of a SoftLayer object to the path.
```javascript
client.id(1234);
```
##### SoftLayer service types
Although you can build your requests with the aforementioned methods, the wrapper also provides methods for all SoftLayer services. These methods work the same way as the the path() method.
For example:
```javascript
client.billingInvoice().id(12345);
```
will create the following request:
```
/rest/v3/SoftLayer_Billing_Invoice/12345
```
As you can see, the method name starts with a lower case and the underscores are removed. In the resulting URL, the correct SoftLayer service name is concstructed. See [here](http://sldn.softlayer.com/reference/services/) for a complete list of available services.

##### auth()
This method sets the credentials
```javascript
client.auth(apiUser, apiKey);
```

##### mask()
Using this method you can add an object mask to the request. You can pass an array of mask elements, or simply string arguments to the method.
```javascript
client.mask('maskElement1','maskElement2').mask(['maskElement3', 'maskElement4']);
```
##### resultLimit()
To use pagination, you can add offset and limit to the request.
```javascript
client.resultLimit(offset, limit);
```
##### headers()
By default, the API client returns uses application/json. You can use this method to set different request headers.
```javascript
client.headers({
    'Content-Type': 'application/json'
});
```
##### get()
With get() the request is submitted. This method returns a Promise. If you prefer node callbacks, you can pass a callback to the method.
```javascript
//Using Promises:
client.get()
    .then(function(result) {
        //result contains the result of the request
    }),function(reason) {
        //the Promise has been rejected. reason contains the error.
    });
//Using node callback:
client.get(function(err,res) {
    
});
```
##### post(), put(), delete()
These methods are not yet implemented.
##### Chaining it all together
```javascript
var SoftLayer = require('softlayer-node');
var client = new SoftLayer();
client
  .auth('youruser', 'yourkey')
  .path('Hardware_Server', 1234, 'NetworkComponents', '5678')
  .mask('datacenter','operatingSystem.passwords')
  .get()
  .then(function(result) {
    // :-)
  }, function(error) {
    // :-(
  });
```

## Tests
To run the tests open a command line and tpye
```
npm test
```
or
```
gulp test
```
You will need to have gulp and mocha installed to run the tests. **Important:** The tests make real http requests (only GET) to an account, which has to be configured in a file called credentials.json in the test folder.
This file must have the following content:
``` json
{
  "accountId": "[account id]",
  "apiUser": "[api user]",
  "apiKey": "[api key]"
}
```
Don't put these informations in a source code repository ;-)


## Contributing
1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request :D
