# [IE] Web Document Export API

### Intro
This API allows you to export documents from Element451 system.

## Export
### Parameters
- "notifications"
In case you require a notification when the export finishes, you can setup an email or a webhook notification.

Example:

```
"notification_webhook" : "https://...",
"notification_email" : "oaloria@element451.com",
```      

- "sources"
Tells which files you are exporting.

Example:

```
"sources" : [
    {
        "provider" : "Application", 
        "path" : "/tu.applications.451/__preview", 
        "name" : "[user:id]_applicationpreviewpdf", 
        "source_name" : "Application", 
        "have_cover" : false, 
        "filter_model" : null, 
        "filters" : [

        ], 
        "transformations" : [

        ]
    }
], 
```

- "destination"
The existing connector guid and path where the files will be exported.

```
"destination" : {
        "connector_guid" : "tu.connector.451", 
        "path" : "fire/test2"
    }, 
```

- "output_settings"
Details about file format and names of the exported files.

```
"output_settings" : {
    "combine_pdf" : false, 
    "combine_pdf_filename" : "", 
    "output_one_folder_per_user" : false, 
    "output_one_folder_per_user_filename" : "", 
    "zip_everything" : false, 
    "zip_everything_filename" : ""
}, 
```

### Request example
```
Request:
POST https://{{client}}.{{api}}/v2/users/documents/export

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body parameters:
{
    "notification_webhook" : "https://...",
    "notification_email" : "oaloria@element451.com",
    "destination" : {
        "connector_guid" : "tu.connector.451", 
        "path" : "fire/testFolder"
    }, 
    "sources" : [
        {
            "provider" : "Application", 
            "path" : "/tu.applications.451/__preview", 
            "name" : "[user:id]_applicationpreviewpdf", 
            "source_name" : "Application", 
            "have_cover" : false, 
            "filter_model" : null, 
            "filters" : [

            ], 
            "transformations" : [

            ]
        }
    ], 
    "output_settings" : {
        "combine_pdf" : false, 
        "combine_pdf_filename" : "", 
        "output_one_folder_per_user" : false, 
        "output_one_folder_per_user_filename" : "", 
        "zip_everything" : false, 
        "zip_everything_filename" : ""
    }
}

Expected Response:
Status 202
{
    "guid" : "tu.ie_task.451"
}

Webhook notification:
POST <url>
{
    'guid' : 'tu.ie_task.451',
    'name' : 'Export Documents via web',
    'started_at' => '2020-01-28T16:22:37-00:00',
    'finished_at' => '2020-01-28T16:23:37-00:00',
    'run_duration' => '1m 0s',
    'status' => 'done',
    'stats' => {
        "count" : 98, 
        "created" : 95, 
        "updated" : 0, 
        "unchanged" : 0, 
        "failed" : 4, 
        "skipped" : 0, 
        "exported" : 94,
    },
    'error' => ,
    'warnings' => [
        "Cannot export 0/5d4de713039603788989050b9_applicationpreviewpdf. Reason: File missing on server: tu/private/files/hd1pHFUwski09MglhbPi/tu.files.23536.pdf", 
    ],
    'info' => []
}
```

