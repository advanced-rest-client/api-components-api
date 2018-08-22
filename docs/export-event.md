# Exporting data

Each ARC application should include `arc-data-export` component. The element processes data to export and redirects the even to appropriate component (dependeds on the `destination` value).

The export system is based on `export-data` custom event.

## `export-data` event

The event is handled by `arc-data-export` component. It transforms the data if required and dispatches an event depending on value of the `destination` property.
Each ARC application should include elements that handle destination events based on the platform.

### Event properties

The `details` object of the `export-data` event should have the following properties:

**file** `String` - Name of the file to create. Note that it may only be a suggestion for the user in save dialog box.

**destination** `String` A place where to save the data. Currently ARC supports `file` and `drive` where `drive` is Google Drive export.
Note, `file` in web environment (ARC web app) is the name of the file that will be downloaded. Electron / Chrome application uses this only as a suggestion for the user.

**data** `Array|Object|String` Data to export.

**type** `String` - The data type to export. Most of the data are transparently passed to the export function where it is stringified by JSON object. There are however special types handled by `arc-export` component that transform data to an appropiate structure. This are ARC database export files that has set structure.

`type` can have the following values:
-   `default` or when missing - Pass the data to target component
-   `project` - Expects `project` and `requests` properties on `data` property. Exports saved project data
-   `request` - Expects `data` to be an array of requests to export.
-   `arc-export` - An event dispatched by settings panel to export selected by used data. It creates ARC full export object with the data found in the `data` object. The valiue of each property on the `data` object can be either a boolean indicating to dump current database or an array of objects to export (without getting the data from the datastore)


### Example

```javascript
const e = new CustomEvent('export-data', {
  bubbles: true,
  cancelable: true,
  composed: true,
  detail: {
    file: 'arc-data-export.json',
    destination: 'file',
    type: 'arc-export',
    data: {
      requests: true,
      projects: true,
      'history-url': [{...}]
    }
  }
});
this.dispatchEvent(e);
```
