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

### Request example (With inline template)
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

### Request example (With saved template and named keys)
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
        "template": "element.templates.451",
        "items": [
             {
                "user-email-address": "john.smith@example.com",
                "user-first-name": "John",
                "user-last-name": "Smith"
            },
            {
                "user-last-name": "Jenkins",
                "user-email-address": "jane.jenkins@example.com",
                "user-first-name": "Jane"
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

## Sample Integrations
### Python
```python
import requests
import json

# Integration variables.
client = 'tu'
api = 'local.my451.com:8080'
feature = '451451451451451'
template = 'demo1.template.2729'

# Prepare API url.
api_url = '{protocol}://{client}.{api}/v2/users/import'.format(
    protocol= 'http', client=client, api=api
)

request_body = {
    'item': {
        'source': 'custom',
        'template': template,
        'items': [
            {
                'user-email-address': 'john.smith@example.com',
                'user-first-name': 'John',
                'user-former-last-name': 'Smith'
            },
            {
                'user-email-address': 'jane.jenkins@example.com',
                'user-first-name': 'Jane',
                'user-former-last-name': 'Jenkins'
            }
        ]
    }
}
request_headers = {
    'Feature': feature,
    'Content-Type': 'application/json'
}
resp = requests.post(api_url, data=json.dumps(request_body), headers=request_headers)

if resp.status_code != 200:
    # This means something went wrong.
    raise Exception('Error while importing: {}'.format(resp.content))

created_users_count = resp.json()['data']['stats']['created']
updated_users_count = resp.json()['data']['stats']['updated']
print('Number of created users: {}'.format(created_users_count))
print('Number of updated users: {}'.format(updated_users_count))
```

