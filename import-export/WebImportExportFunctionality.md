# [IE] Web Import/Export API

### Intro
This API allows you to import/export data to the Element451 system.

### Preparations
In order for Element451 to recognize and transform your data, you must provide a template. Templates are created in "Data" section of the Element451 dashboard.

## Import
### Request example (using template demo1.template.2729)
```
Request:
POST https://{{client}}.{{api}}/v2/users/import

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body:
{
    "item": {
        "source": "custom",
        "template": "demo1.template.2729",
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
            "created":2,
            "updated":0
        },
        "error": null,
        "result": {
            "created":["601d53cd71dfb37bd02fa3a8","601d53cd71dfb37bd02fa3a9"]
        }
    }
}
```
### Request example with named keys
```
Request:
POST https://{{client}}.{{api}}/v2/users/import

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body:
{
    "item": {
        "source": "custom",
        "template": "demo1.template.2729",
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
```
## Export
### Request example for export
```
Request:
GET https://{{client}}.{{api}}/v2/users/export?template=demo1.template.2729&users[]=602659091eb7a67a33231e4c&users[]=6026590a1eb7a67a33231e4d

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Query parameters:
template: ID of Element451 data template
users: array of Element451 user IDs

Request headers:
Feature: Feature token for that client

Expected Response:
Status 200
{
    "data": [
        {
            "Email address": "john.smith@example.com",
            "First name": "John",
            "Last name": "Smith"
        },
        {
            "Email address": "jane.smith@example.com",
            "First name": "Jane",
            "Last name": "Smith"
        }
    ]
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
