# [IE] Web Data Import API

### Intro
This API allows you to import data to the Element451 system.

## Import
### Parameters
- "template" => "mixed"  
The template can be passed inline in the request body itself
or if there's an existing template on the system a guid can be passed.

- "items" => "array
The rows to be imported into the system.

### Request example
```
Request:
POST https://{{client}}.{{api}}/v2/users/import

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body parameters:
{
    "item": {
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
        "items": [
            {
                "0": "john.smith@example.com",
                "1": "John",
                "2": "Smith"
            },
            {
                "0": "jane.jenkins@example.com",
                "1": "Jane",
                "2": "Jenkins"
            }
        ]
    }
}

Expected Response:
Status 201
{
    "data": {
        "stats": {
            "created": 0,
            "updated": 2
        },
        "error": null,
        "warnings": [],
        "result": {
            "updated": [
                "621561e2f7fa620d22575e33",
                "621561e6f7fa620d22575e35"
            ]
        }
    }
}
```

