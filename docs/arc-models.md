# ARC models

List of data object used in Advanced Rest Client and API components.

## ArcRequest

The request object produced by the `request-editor` or `api-request-editor`

```json
{
  "_id": "Optional, string, PouchDB datastore ID",
  "_rev": "Optional, string, PouchDB datastore review",
  "type": "String, type of the request. Default to `history` but can be also `saved`",
  "url": "String, The URL of the endpoint.  Note, the app does not prevent invalid URL to be used",
  "method": "String, a HTTP method string",
  "headers": "String, List of headers to send. Note, the app does not prevent invalid headers to be send",
  "payload": "String|File|Blob|ArrayBuffer, optional, data to send",
  "queryModel": "Array, optional, List of query parameters model objects",
  "pathModel": "Array, optional, List of URI parameters model objects. This property is only available for `api-request-editor`"
}
```

## ArcWorkspace

An object representing requests workspace. Keeps information about current state of the request panels, environment and request selection.



```json
{
  "selected": "Number, selected request in the workspace",
  "environment": "String, the name of last used environment",
  "requests": "Array, List of `ArcRequest` objects"
}
```
