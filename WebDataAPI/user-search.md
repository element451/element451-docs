# [Users] Search

### Intro
This API allows you to search and pull users from Element451.

## Search
### Parameters
- "segment" => (required) "mixed"  
The segment can be passed as an inline raw segment in the request body itself
or if there's an existing segment on the system a guid can be passed.

- "project" => (optional) "array" default:[_id]
The fields you want to list for each user, there is a very limited amount
of fields you can project: [_id, first_name, last_name, email]
In case you need more fields use the WebDataAPI export functionality.

- "per_page" => (optional) "integer" default:5000
How many users information you want to retrieve for a single request.
Cannot be more than the default value.

- "last_id" => (optional) "string" default:null
Is used to access next pages in the result set, for first page it must be NULL or not specified, on each page received you will get under “meta.next_last_id” the value you need to specify to pull the next page.
You will find the last page when there are no rows in page content or “meta.next_last_id” value is empty in response.

### Request example (with existing segment)
```
Request:
POST https://{{client}}.{{api}}/v2/users/export/segment

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body parameters:
{
    "item": {
        "segment": "tu.segments.902",
        "project" : ["first_name", "email"],
        "per_page": 10,
        "last_id" : null
    }
}

Expected Response:
Status 200
{
    "data": [
        {
            "_id": "621561e2f7fa620d22575e33",
            "first_name": "John",
            "email": "john.smith@example.com"
        },
        {
            "_id": "621561e6f7fa620d22575e35",
            "first_name": "Jane",
            "email": "jane.jenkins@example.com"
        }
    ],
    "meta": {
        "next_last_id": "621561e6f7fa620d22575e35"
    }
}
```

### Request example (with inline segment)
```
Request:
POST https://{{client}}.{{api}}/v2/users/export/segment

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body parameters:
{
    "item": {
        "segment": {
            "users": {
                "filters": {
                    "type": "filter",
                    "target": "<mapping:user-email-address>",
                    "operator": "$match",
                    "value": "/element451/i"
                }
            }
        },
        "per_page": 500
    }
}

Expected Response:
Status 200
{
    "data": [
        {
            "_id": "621561e2f7fa620d22575e33",
            "email": "john.smith@example.com"
        },
        {
            "_id": "621561e6f7fa620d22575e35",
            "email": "jane.jenkins@example.com"
        },
        ...
        {
            "_id": "1e2f6f7565656fa620d561e6",
            "email": "jane.jenkins@example.com"
        }
    ],
    "meta": {
        "next_last_id": "1e2f6f7565656fa620d561e6"
    }
}
```


