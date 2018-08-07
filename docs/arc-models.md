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
  "projects": "Array<String>. Contains a list of project associated with this request. This property is only used in Advanced REST client application."
  "name": "Request name if request.type is saved. This property is used in ARC project"
  "type": "Can be either saved or history. This property is used in ARC project",
  "description": "Request name if request.type is saved. This property is used in ARC project",
  "created": "Number, timestamp when the request was created",
  "updated": "Number, timestamp when the request was updated",
  "driveId": "ID of Google Drive file created when saving this request object",
  "config": "Object. Additional request configuration options: - timeout (number), - followRedirects (boolean)",
  "requestActions": "Array<Object>, List of actions to perform before making a request",
  "responseActions": "Array<Object>, List of actions to perform after response is ready",
  "nextRequest": "String, an ID or a request that must be called after this request finishes"
}
```

## ArcProject

A project of requests. Contains project metadata and information about requests.

```json
{
  "_id": "Optional, string, PouchDB datastore ID",
  "_rev": "Optional, string, PouchDB datastore review",
  "name": "Project name"
  "requests": "Array<String>. Ordered list of requests in the project",
  "order": "Number, project order on any listing",
  "created": "Number, timestamp when the project was created",
  "updated": "Number, timestamp when the project was updated"
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

