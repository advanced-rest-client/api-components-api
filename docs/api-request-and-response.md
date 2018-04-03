# API components request and response events

API components uses common `api-request`, `api-response` and `abort-api-request` custom events.

`api-request` and `abort-api-request` are dispatched by `api-request-editor`.
The first one informs application or included API transport library about intention
of sendig a request or to cancel it.

Common property for all 3 events is `id`. It is generated each time when the event
is dispatched. It is UUID v4 that identifies specific request instance.
It can be used to compare request when handing the events. In case of multi request panel
environments this is the only way to compare requests.

## api-request

When propagating through the DOM the request properties may change comparing to original
request object dispatched on `api-request-editor`. For example `api-request-panel`
may add additional headers defined in configuration options.

The event properties are as follows:

- url `String` - Endpoint's full and final url. Note that if the API definition in AMF model does not specify base uri then the `url` can be relative url to the resource. Transport library may try to deal with it.
- method `String` - HTTP method.
- headers `String|undefined` - valid HTTP headers string
- payload `String|FormData|File|Blob|undefined` - Request body
- id `String` - A unique request identifier. This ID has to be reported back by `api-response` event.
- auth `Object|undefined` - Authorization settings received from the authorization panel. In case of basic, oauth 1 and oauth 2 authorizations, the token is applied to the headers or query parameters so no action is required. NTLM however hast to be performed on the socket so transport library has to read this information.
- authType `String|undefined` - Name of the authorization methods. One of `advanced-rest-client/auth-methods`.
- queryModel `Array<Object>` - Query parameters data view model.
- pathModel `Array<Object>` - URI parameters data view model
- headersModel `Array<Object>` - Headers data view model


## api-response

Event dispatched by the transport library when the response is ready.

API components has `advanced-rest-client/xhr-simple-request` element to
handle `api-request` and `abort-api-request` events and to use XHR to make the connection.
Finally it dispatches valid `api-response` event.

Note that it may not always work if the API does not allows CORS.

Properties:

- id `String` - Request ID used with `api-request` event
- request `Object` - Original request object from `api-request` event
- loadingTime `Number` - High precise timing used by the performance API
- isError `Boolean` - Indicates if the request is errored
- error `Error|undefined` - Error object
- response `Object` - The response data:
  - status `Number` - Response status code
  - statusText `String` - Response status text. Can be empty string.
  - payload `String|Document|ArrayBuffer|Blob|undefined` - Response body
  - headers `String|undefined` - Response headers

### Advanced response properties

When using own transport libraries or server side transport you may have
access to more information about the request and response like redirects
and timings. The response status view can render additional UI for this
data.

To enable this feature, set `isXhr` to false and any of the following
properties:

- sentHttpMessage `String` - Raw HTTP message sent to server
- redirects `Array<Object>` - A list of redirect information. Each object has the following properties:
  - status (`Number`) - Response status code
  - statusText (`String`) - Response status text. Can be empty string.
  - headers (`String|undefined`) - Response headers
  - payload (`String|Document|ArrayBuffer|Blob|undefined`) - Response body
- redirectsTiming `Array<Object>` - List of HAR 1.2 timing objects for each redirected request. The order must corresponds with order in `redirects` array.
- timing `Object` - HAR 1.2 timings object

#### Example

```javascript
const e = new CustomEvent('api-response', {
  bubbles: true,
  detail: {
    id: 'abc',
    request: {
      url: 'https://domain.com/'
      method: 'GET',
      headers: 'accept: text/plain'
    },
    response: {
      status: 200,
      statusText: 'OK',
      payload: 'Hello world',
      headers: 'content-type: text/plain'
    },
    loadingTime: 124.12345678,
    isError: false,
    // everything below is optional
    isXhr: false,
    sentHttpMessage: 'GET / HTTP/1.1\nHost: domain.com\naccept: text/plain\n\n\n'
    redirects: [{
      status: 301,
      statusText: 'Not here',
      payload: 'Go to https://other.domain.com',
      headers: 'content-type: text/plain\nlocation: https://other.domain.com\ncontent-length: 30'
    }],
    timing: {
      blocked: 12.0547856,
      dns: 0.12,
      connect: 112.21458762,
      send: 4.4748989,
      wait: 15.8436988,
      receive: 65.125412256,
      ssl: 10
    },
    redirectsTiming: [{
      blocked: 12.0547856,
      dns: 0.12,
      connect: 112.21458762,
      send: 4.4748989,
      wait: 15.8436988,
      receive: 65.125412256,
      ssl: 10
    }]
  }
});
document.dispatchEvent(e);
```

## abort-api-request

Dispatched by `api-request-editor` when the user decided to cancel the request.

Note, `abort-api-request` triggers an error response in the `xhr-simple-request`.
Other transport libraries should also trigger errored response event to reset components
state.

Properties:

- url `String` The request URL. Can be empty string. Also, it may be different URL that the one used to send the request if the user changed it in between. Use the `id` property to compare requests.
- id `String`  Generated UUID of the request with `api-request` event.
