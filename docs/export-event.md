# Exporting data

Each ARC application should include `arc-data-export` component. The element processes data to export and redirects the even to appropriate component (dependeds on the `destination` value). This event creates ARC export object.

## `arc-data-export` event

This event is to export ARC related data from the datastore to external location.

The detail object contains:

**data** `Object`, required. Data to export.

Each key is the model name (eg saved, history, websocket, url-history, variables, auth, cookies, host-rules).
The value is either boolean `true` to indicate that the data should be read from the data store or an array with items to export. Both can be combined.
```javascript
const e = new CustomEvent('arc-data-export', {
  detail: {
    data: {
      requests: true,
      variables: [{variable: 'environment', value: 'qax', enabled: true}]
    }
  }
});
```
The declaration above would export all requests and a single variable.

**options** - Export options.

**options.provider** `String`, required - Name of the export provider. ARC supports `drive` and `file` providers at the moment.

**options.file** `String`, required - Export file name. Without a path. This will be defined by the user in save file dialog.

**options.skipImport** `Boolean`, optional - Adds flag to the export file to skip import table and storing the data to the data store when opening the file.

**options.kind** `String`, optional - Sets the `kind` property of export object.

**providerOptions** `Object`, optional. A list of options to be passed to export provider (currently File and Google Drive).
Schema depends on the provider (`options.provider`)

### Example

```javascript
const e = new CustomEvent('arc-data-export', {
  bubbles: true,
  cancelable: true,
  detail: {
    options: {
      file: 'my-export.json',
      provider: 'drive',
      skipImport: false
    },
    providerOptions: {
      parents: [{name: 'New folder'}, {name: 'Existing folder', id: 'google-drive-id'}]
    },
    data: {
      requests: true,
      variables: [{variable: 'environment', value: 'qax', enabled: true}]
    }
  }
});
document.body.dispatchEvent(e);
```


## `export-data` event

An event to export any data. It won't change incomming data (won't create export object) and will store data as they are.

### Event properties

The `details` object of the `export-data` event should have the following properties:

**data** `Array|Object|String`, required. Data to export.

**file** `String`, required. Name of the file to create. Note that it may only be a suggestion for the user in save dialog box.

**destination** `String`, required. A place where to save the data. Currently ARC supports `file` and `drive` where `drive` is Google Drive export.
Note, `file` in web environment (ARC web app) is the name of the file that will be downloaded. Electron / Chrome application uses this only as a suggestion for the user.

**providerOptions** `Object`, optional. A list of options to be passed to export provider (currently File and Google Drive).
Schema depends on the provider (`destination`)


### Example

```javascript
const e = new CustomEvent('export-data', {
  bubbles: true,
  cancelable: true,
  composed: true,
  detail: {
    file: 'arc-data-export.json',
    destination: 'file',
    data: {...}
  }
});
this.dispatchEvent(e);
```
