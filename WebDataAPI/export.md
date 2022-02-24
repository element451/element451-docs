# [IE] Web Data Export API

### Intro
This API allows you to export data from Element451 system.

## Export
### Parameters
- "template" => "mixed"  
The template can be passed inline in the request body itself
or if there's an existing template on the system a guid can be passed.

- "users"
List of users to export.

- "options"
Configurable options for the export task.

- - "column_key" (default: field) can also take the value of "slug"
and it will affect the resulting json object keys for each row.

- - "unwind.root" Any entity you want to use for unwinding user rows.

```
"output_settings" : {
    "column_key" : "field",
    "unwind" : {
        "root" : "user-applications-root"
    }
}, 
```

### Request example
```
Request:
POST https://{{client}}.{{api}}/v2/users/export

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body parameters:
{
    "item": {
        "options" : {
            "column_key" : "field",
            "unwind" : {
                "root" : "user-applications-root"
            }
        },
        "template": {
            "columns": [
                {
                    "field": "Email",
                    "mode": "slug",
                    "slug": "user-email-address",
                    "formula": "",
                    "transformations": [],
                    "scope": [],
                    "validations": [],
                    "type": "string",
                    "range": [
                        "unique"
                    ],
                    "empty": "remove"
                },
                {
                    "field": "First Name",
                    "mode": "slug",
                    "slug": "user-first-name",
                    "formula": "",
                    "transformations": [],
                    "scope": [],
                    "validations": [],
                    "type": "string",
                    "range": [],
                    "empty": "remove"
                },
                {
                    "field": "Last Name",
                    "mode": "slug",
                    "slug": "user-last-name",
                    "formula": "",
                    "transformations": [],
                    "scope": [],
                    "validations": [],
                    "type": "string",
                    "range": [],
                    "empty": "remove"
                }
            ]
        },
        "users": [
            "576af6b42e402e8d4c8b4569",
            "5e1cc41c0396032a6e3a06c8"
        ]
    }
}

Expected Response:
Status 200
{
    "data": [
        {
            "id": "621561e2f7fa620d22575e33",
            "Email": "john.smith@example.com",
            "First Name": "John",
            "Last Name": "Smith"
        },
        {
            "id": "621561e6f7fa620d22575e35",
            "Email": "jane.jenkins@example.com",
            "First Name": "Jane",
            "Last Name": "Jenkins"
        }
    ]
}
```

