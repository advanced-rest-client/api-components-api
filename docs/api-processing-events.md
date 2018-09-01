# API data processing events

The [exchange-search-panel](https://github.com/advanced-rest-client/exchange-search-panel) component dispatches `process-exchange-asset-data` but do not handles any other event related to API processing.
The application handles this event and parses it using [AMF](https://a.ml/) parser.

## List of related events

-   process-exchange-asset-data
-   api-process-file
-   api-select-entrypoint
-   api-data-ready
-   process-error


### process-exchange-asset-data

Fired when the user requested to process the item. The `detail` object of the event contains item response from Exchange API.

-   `{String}` groupId
-   `{String}` assetId
-   `{String}` version
-   `{String}` versionGroup
-   `{String}` productAPIVersion
-   `{Boolean}` isPublic
-   `{String}` name
-   `{String}` type
-   `{String}` status
-   `{String}` assetLink
-   `{String}` createdAt
-   `{String}` runtimeVersion
-   `{Number}` rating
-   `{Number}` numberOfRates
-   `{String}` id
-   `{String}` icon
-   `{String}` modifiedAt
-   `{Object}` organization
-   `{Object}` createdBy
-   `{Array}` tags
-   `{Array}` files

### process-api-file

Dispatched when user selected a file to process as an API project.

The `detail` object has to contain a `file` property with the file (or blob).
The component / module that handles this event has to cancel the event and set a `result` property on the detail object. This property is a Promise resolved to API data model.

**Example**

```javascript
const e = new CustomEvent('api-process-file', {
  bubbles: true,
  cancelable: true,
  detail: {
    file: blob // blob is a file selected by the user
  }
});
document.body.dispatchEvent(e);
if (e.defaultPrevented) {
  e.detail.result
  .then((model) => console.log(model))
  .catch((cause) => console.log(cause));
}
```

### api-select-entrypoint

The event is dispatched when API processor is unable to determine API main file. This can happen when the root of the project contains multiple raml / yaml / json files (for RAML or OAS) and more than one file matches a criteria of an API spec.
In this case the processor dispatches `api-select-entrypoint` custom event with `candidates` array on the detail object.
The event must be handled by the application and canceled. If not canceled it causes the process to fail.

The `candidates` array contains a list of file names to suggest to the user.
The handler sets `result` property which is a promise resolved to selected by the user file. If the selection is empty then the processor assumes the user cancelled the selection and quietly cancels the process. 

Suggested UI component: `advanced-rest-client/api-entrypoint-selector`

**Example**

```javascript
window.addEventListener('api-select-entrypoint', (e) => {
  if (e.defaultPrevented) {
    return;
  }
  const {candidates} = e.detail;
  e.detail.result = new Promise((resolve) => {
    // candidates is an array of strings, do something with it.
    // Always resolve the promise so the processor can clean up the data
  });
});
```

### api-data-ready

Only called when the process started with `process-exchange-asset-data` event as the exchange panel do not handles responses for its event.
Informs the application thet the API data has been processed.

The detail object contains `api` property which is AMF json-ld model.


**Example**

```javascript
window.addEventListener('api-data-ready', (e) => {
  const {api} = e.detail;
  console.log(api); // prints AMF data model
});
```

### process-error

Only called when the process started with `process-exchange-asset-data` event. This is ARC's generat purpose error reporting event.
It has `message` property on the detail object with error details that can be presented to the user and source set to `amf-service`.

