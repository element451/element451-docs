# [IE] Web Data Connector (WDC)

### Intro
The Web Data Connectors (WDC) allows you to access the results from a Data Export Task via Element451 REST API.
This API allows you to:
* Access your data on demand without having to export it to file first.
* Paginate over the data as needed without having to receive the full at once.
* Create your own scripts to read the full set in small chunks.

### Preparations
In order to access data through the WDC, first you need to create a Data Export Task and set its destination to “Web Data Connector”. Once you activate it the output of that task will be available via a dedicated API endpoint. 

A few simple steps to convert any data export task to a WDC enabled one:
1. Go to “Destination” section and make sure you add the “Web Data Connector” destination.
2. Go to “Run” section and make sure you put the task in “Ready” status.
3. That’s it.. Have fun accessing your data via the Web Data Connector

### Accessing your data

Once you have a WDC enabled task, you are ready to start using the it.

1. Opening a Web Data Connector
   * Creating a WDC gives you a “connector_id" which is required for next API requests.
   * Each connector has a TTL specified by “connector_ttl_min” after it, the Connector will not be accessible.
   * A side effect of opening a WDC is that any other open Connectors to the same task will be closed immediately and they will not be accessible anymore.
   
```
Request:
POST https://{{client}}.{{api}}/v2/importExport/tasks/{{TASK_GUID}}/wdc?feature={{feature}}

Parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.
{{feature}}: Authentication token provided to access the feature.
{{TASK_GUID}}: Guid of the task you want to access.

Expected Response:
Status 202
{
    "data": {
        "connector_id": "5f56546af4fb5468589979ba",
        "connector_ttl_min": 120
    }
}
```

2. Read the output
   * “connector_id” obtained after opening a WDC, is required to identify the opened connector.
   * “per_page” defines the number of users to process. Note: if you are exporting entities, you can receive more than 1 row per user, resulting in pages with more rows than the specified “per_page”.
   * “last_id” is used to access next pages in the result set, for first page it must be NULL or not specified, on each page received you will get under “meta.next_last_id” the value you need to specify to pull the next page.
   * The columns and schema returned matches the position of the template you provided on the task. You can return the header row or schema with the parameter “embed[]=schema”. It’s not recommended to request the schema on each request, since it causes extra processing to generate it. If you need it, request it only on first page, and save it for later use.
   * You will find the last page when there are no rows in page content or “next_last_id” value is empty in response.
   
```
Request:
GET https://{{client}}.{{api}}/v2/importExport/tasks/{{TASK_GUID}}/wdc?feature={{feature}}&connector_id={{CONNECTOR_ID}}&per_page={{PER_PAGE}}&last_id={{LAST_ID}}&embed[]={{EMBED}}

Parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.
{{feature}}: Authentication token provided to access the feature.
{{TASK_GUID}}: Guid of the task you want to access.
{{CONNECTOR_ID}}: The "connnector_id" value obtained on Step #1 when the Connector was opened.
{{PER_PAGE}}: How many users information you wanna retrieve per page.
{{LAST_ID}}: The value provided in "meta.next_last_id" response of the previous page.
{{EMBED}}: Optional, if you want schema returned in metadata. Use "schema".

Expected Response:
Status 200
{
  "data": [
    [
      "55e9ac4e21402e2c198b79ea",
      "jdoe@element451.com",
      "John",
      "Patrick",
      "Doe"
    ],
    [...]
  ],
  "meta": {
    "total_returned": N,
    "per_page": N,
    "next_last_id": "55e9ac4e2ea02e6c198b79ec",
    "schema": [
      {
        "index": 0,
        "label": "Element ID",
        "description": "user-elementid: Element ID",
        "dataType": "STRING"
      },
      {
        "index": 1,
        "label": "Email - Email address",
        "description": "user-email-address: Email address",
        "dataType": "STRING"
      },
      {
        "index": 2,
        "label": "First Name",
        "description": "user-first-name: First Name",
        "dataType": "STRING"
      },
      {
        "index": 3,
        "label": "Middle Name",
        "description": "user-middle-name: Middle Name",
        "dataType": "STRING"
      },
      {
        "index": 4,
        "label": "Last Name",
        "description": "user-last-name: Last Name",
        "dataType": "STRING"
      }
    ]
  }
}

Other Response:
Status 102
Segment is still processing, try again in a minute.
```

3. Closing the WDC
* When you are finished, close the connector. Closing the connector is optional however it is highly recommended. The data is used to accurately display the duration of the Export tasks on the results section of the task.

```
Request:
PUT https://{{client}}.{{api}}/v2/importExport/tasks/{{TASK_GUID}}/wdc?feature={{feature}}&connector_id={{CONNECTOR_ID}}&action=close

Parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.
{{feature}}: Authentication token provided to access the feature.
{{TASK_GUID}}: Guid of the task you want to access.
{{CONNECTOR_ID}}: The "connnector_id" value obtained on Step #1 when the Connector was opened.

Expected Response:
Status 200
{
   "data": []
}
```

## Sample Integrations
### Python
```python
#!/usr/bin/env python3

##################################################
## {Description}
##
## Sample code that shows how to consume the Element451
## Export Task Web Data Connectors.
##################################################
## {Requirements}
##
## Python >= 3.7 
## requests >= 2.24.0
##################################################
## {Parameters}
##
## "secure" : Tells which protocol to use HTTP vs HTTPS (default)
## "client" : The Client URL token.
## "api" : This is the Element451 API url.
## "task_guid" : The unique Export Task Guid.
## "feature" : The Feature token to Authenticate the requests.
## "per_page" : Number of rows you want to receive in response.
## "show_schema" : Prints the schema of the returned data. (Recommended to ask for it once)
##################################################

import requests
import time

# Integration variables.
secure = True
client = 'element'
api = 'api.451.io'
task_guid = 'element.tasks.451'
feature = '451451451451451'
per_page = 100
show_schema = True

# Prepare API url.
api_url = '{protocol}://{client}.{api}/v2/importExport/tasks/{task_guid}/wdc?feature={feature}&per_page={per_page}'.format(
    protocol= 'https' if secure else 'http', client=client, api=api, task_guid=task_guid, feature=feature, per_page=per_page
)

# [Step 1] Open the Connector.
resp = requests.post(api_url)
if resp.status_code != 202:
    # This means something went wrong.
    raise Exception('Error opening Connector: {}'.format(resp.content))

# Extract the connector id and inject into the API url.
connector_id = resp.json()['data']['connector_id']
api_url = '{api_url}&connector_id={connector_id}'.format(
    api_url=api_url, connector_id=connector_id
)
print('#################################################################')
print('The Connector is now Open with id: "{}"'.format(connector_id))
print('#################################################################')

# [Step 2] Read Connector Pages until the last one.
slowest_page_time = 0
total_records = 0
loop_count = 1
page_count = 1
paged_api_url = '{}&embed[]={}'.format(api_url, 'schema' if show_schema else '')
while True:
    resp = requests.get(paged_api_url)

    # Record the slowest page time.
    if resp.elapsed.total_seconds() > slowest_page_time:
        slowest_page_time = resp.elapsed.total_seconds();

    if resp.status_code == 102:
        # Segment is still processing, wait and try again soon.
        if loop_count > 5:
            # Segment is taken too long to be ready.
            raise Exception('Error getting first Connector page: {}'.format(resp.content))

        print('Segment is still processing, waiting and retrying in few seconds.')
        time.sleep(5)
        continue
    elif resp.status_code != 200:
        # This means something went wrong.
        raise Exception('Error getting Connector Page: {}'.format(resp.content))

    # Print schema if requested only on 1st page.
    if show_schema and page_count == 1:
        print('Printing Schema:'.format(page_count))
        print('=============================================')
        for row in resp.json()['meta']['schema']:
            print(row)
        print('=============================================')

    if resp.json()['data']: 
        print('Printing page #{} results:'.format(page_count))
        print('>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>')
        for row in resp.json()['data']:
            print(row)
            print('-------------------')
    
    page_count += 1
    total_records += resp.json()['meta']['total_returned']

    # Check if the last page has been reached.
    if not resp.json()['meta']['next_last_id']:
        break

    # Add the last page indicator to API url.
    paged_api_url = '{api_url}&last_id={last_id}'.format(
        api_url=api_url, last_id=resp.json()['meta']['next_last_id']
    )

    loop_count += 1

# [Step 3] Close the Connector since we finished reading the data.
resp = requests.put(api_url)
print('#################################################################')
print('Total pages read: {}'.format(page_count))
print('Requested per page: {}'.format(per_page))
print('Total rows read: {}'.format(total_records))
print('Slowest page: {} seconds'.format(page_count))
print('The Connector with id "{}" has been Closed.'.format(connector_id))
print('#################################################################')


```
