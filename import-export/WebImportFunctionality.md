# [IE] Web Import functionality

### Intro
Web import data route allows you to import data to the Element451 system.

### Preparations
In order for this api to be used we need to have a template created. Template can be created in data section of Element451 dashboard.
This is the example of template used for import request example bellow:
```
{
   "type":"Data_import_task",
   "name":"[IE] Insert from Route demo",
   "columns":[
      {
         "field":"Email - Email address",
         "mode":"slug",
         "slug":"user-email-address",
         "formula":"",
         "transformations":[],
         "scope":[],
         "validations":[],
         "type":"string",
         "range":[
            "unique"
         ]
      },
      {
         "field":"First Name",
         "mode":"slug",
         "slug":"user-first-name",
         "formula":"",
         "transformations":[],
         "scope":[],
         "validations":[],
         "type":"string",
         "range":[]
      },
      {
         "field":"Last Name",
         "mode":"slug",
         "slug":"user-last-name",
         "formula":"",
         "transformations":[],
         "scope":[],
         "validations":[],
         "type":"string",
         "range":[]
      }
   ],
   "_subdom":"demo1",
   "guid":"demo1.template.2729",
   "updated_at": "",
   "created_at": "",
   "output_settings": {
      "import_mode":"upsert"
   }
}
```
### Request example (for template demo1.template.2729)
```
Request:
POST https://{{client}}.{{api}}/v2/importExport/templates/{{templateGuid}}

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.
{{templateGuid}}: Guid of the template you want to import to be based on.

Request headers:
HTTP_Feature: Feature token for that client
HTTP_Authorization: Basic http authorization

Request body:
{
    "item": {
        "source": "custom",
        "items": [
            {
                "0": "john.smith@example.com",
                "1": "John",
                "2": "Smith"
            },
            {
                "0": "jane.jane@example.com",
                "1": "Jane",
                "2": "Jenkins"
            },
        ]
    }
}

Expected Response:
Status 200
{
    "httpCode":200,
    "responseCode":2002,
    "userMessage":"The resource specified in the request was created.",
    "data": {
        "stats": {
            "created":2,
            "updated":0
        },
        "result": {
            "created":["601d53cd71dfb37bd02fa3a8","601d53cd71dfb37bd02fa3a9"]
        }
    }
}
```
### Request example with named keys
```
Request:
POST https://{{client}}.{{api}}/v2/importExport/templates/{{templateGuid}}

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.
{{templateGuid}}: Guid of the template you want to import to be based on.

Request headers:
HTTP_Feature: Feature token for that client
HTTP_Authorization: Basic http authorization

Request body:
{
    "item": {
        "source": "custom",
        "items": [
            {
                "user-email-address": "john.smith@example.com",
                "user-first-name": "John",
                "user-last-name": "Smith"
            },
            {
                "user-last-name": "Jenkins",
                "user-emai-address": "jane.jane@example.com",
                "user-first-name": "Jane"
            },
        ]
    }
}

Expected Response:
Status 200
{
    "httpCode":200,
    "responseCode":2002,
    "userMessage":"The resource specified in the request was created.",
    "data": {
        "stats": {
            "created":2,
            "updated":0
        },
        "result": {
            "created":["601d53cd71dfb37bd02fa3a8","601d53cd71dfb37bd02fa3a9"]
        }
    }
}
```