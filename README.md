
## Description

PM2 module to automatically rotate logs of processes managed by PM2 and upload to azure before deleting old logs.

## Install

`pm2 install pm2-logrotate-azure-upload`

## Configure

```javascript
pm2 set pm2-logrotate-azure-upload:azureStorageConnectionString <AZURE_STORAGE_CONNECTION_STRING>
pm2 set pm2-logrotate-azure-upload:azureStorageContainer <AZURE_STORAGE_CONTAINER_NAME>
```

OR 

create pm2-logrotate-azure-upload-config.js file in PM2 home folder which by default is ~/.pm2 .
set config values as given in example below:

```javascript
module.exports = {
    "max_size": "10M",
    "retain": "10",
    "compress": false,
    "dateFormat": "YYYY-MM-DD_HH-mm-ss",
    "workerInterval": "30",
    "rotateInterval": "0 0 * * * *",
    "rotateModule": true,
    "serverIp": null,
    "azureStorageConnectionString": "<AZURE_STORAGE_CONNECTION_STRING>",
    "azureStorageContainer": "<AZURE_STORAGE_CONTAINER_NAME>",
    "azureFilePathFormat": ""
}
```

### Config Property Description
#### AZURE upload properties
All of the following properties needs to defined for azure upload to work.
- `azureStorageConnectionString` (Defaults to `null`): This is Connection string of your azure storage account.
- `azureStorageContainer` (Defaults to `null`): This is container name in azure storage account where logs will be stored.

#### PM2 log rotate properties
- `max_size` (Defaults to `10MB`): When a file size becomes higher than this value it will rotate it (its possible that the worker check the file after it actually pass the limit) . You can specify the unit at then end: `10G`, `10M`, `10K`
- `retain` (Defaults to `all`): This number is the number of rotated logs that are keep at any one time, it means that if you have retain = 7 you will have at most 7 rotated logs and your current one.
- `compress` (Defaults to `false`): Enable compression via gzip for all rotated logs
- `dateFormat` (Defaults to `YYYY-MM-DD_HH-mm-ss`) : Format of the data used the name the file of log
- `rotateModule` (Defaults to `true`) : Rotate the log of pm2's module like other apps
- `workerInterval` (Defaults to `30` in secs) : You can control at which interval the worker is checking the log's size (minimum is `1`)
- `rotateInterval` (Defaults to `0 0 * * *` everyday at midnight): This cron is used to a force rotate when executed.
We are using [node-schedule](https://github.com/node-schedule/node-schedule) to schedule cron, so all valid cron for [node-schedule](https://github.com/node-schedule/node-schedule) is valid cron for this option. Cron style :
```
*    *    *    *    *    *
┬    ┬    ┬    ┬    ┬    ┬
│    │    │    │    │    |
│    │    │    │    │    └ day of week (0 - 7) (0 or 7 is Sun)
│    │    │    │    └───── month (1 - 12)
│    │    │    └────────── day of month (1 - 31)
│    │    └─────────────── hour (0 - 23)
│    └──────────────────── minute (0 - 59)
└───────────────────────── second (0 - 59, OPTIONAL)
```
