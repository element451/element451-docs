# [IE] Web Document Import/Export API

### Intro
This API allows you to import/export documents to the Element451 system.

## Import
### Parameters
- "file" => File  
The file content to be uploaded.

- "match_key" => "[Matching Attrib Key](#matching-attribute-keys)"  
The attribute used to match the user with given value.

- "match_value" => "mixed"  
The value to match the user by the given attribute key.

- "folder" => "guid"  
Folder guid matching an existing documents folder.

- "tags[]" => "guid"  
Tag guid matching an existing documents tag.  
Can be repeater to add multiple tags at once.

- "context[application_guid]" => "guid"  
Value to be set on File context.

- "context[registration_id]" => "string"  
Value to be set on File context.

### Request example
```
Request:
POST https://{{client}}.{{api}}/v2/users/documents/import

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'multipart/form-data'

Request body parameters:
>
    "file" => File
    "match_key" => "user:email_address"
    "folder" => "tu.taxonomies.451"
    "tags[]" => "tu.taxonomies.452"
    "tags[]" => "tu.taxonomies.453"
    "context[application_guid]" => "tu.applications.451"
    "context[registration_id]" => "4mzsxbue"
<

Expected Response:
Status 201
{
    "data": [
        {
            "name": "pdf-test.pdf",
            "result": "uploaded",
            "file": {
                "guid": "tu.files.28969",
                "name": "pdf-test.pdf",
                "size": 20597,
                "extension": "pdf",
                "mime_type": "application/pdf",
                "created_at": "2021-12-13T18:38:14+00:00"
            }
        }
    ]
}
```

### Matching Attribute Keys

| Attribute | Key |
| ----------- | ----------- |
| Act Id | act:id |
| Campus Nexus | campus_nexus:id |
| Coalition Id | coalition:id |
| College Board Id | college_board:id |
| Common App Id | commonapp:id |
| Encoura | encoura:id |
| Ethos Id | ethos:id |
| Historic Id | historic:id |
| Parchment Id | parchment:id |
| Salesforce Id | salesforce:id|
| School Email | school-email |
| School Id | school:id |
| Search Id | search:id |
| Scoir | scoir:id |
| Spark Id | spark:id |
| User Email | user:email_address |
| User Id | user:id, element:id |


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
