# [IE] Web Data Export API

### Intro
This API allows you to export data from Element451 system.

## Export
### Parameters
- "template" => "mixed"  
The template can be passed inline in the request body itself
or if there's an existing template on the system a guid can be passed.

  - "columns" list of properties to be exported for each row.
  
    - "field" (required)   
    Friendly name of the field to be exported.
    - "mode" (required)   
    .
    - "slug"   
    Slug of the referenced Mapping.
    - "transformations"   
    Any transformation to be applied on the value.
    - "validations"   
    Any valudation to be applied to the value.
    - "empty"   
    Action to do when the value is empty.

- "users"
List of users to export.
Limited to 50 users per request.

- "options"
Configurable options for the export task.

  - "column_key" (default: field) can also take the value of "slug"
and it will affect the resulting json object keys for each row.
When using "slug" given none slug columns will not have a key.

  - "unwind.root" Any entity you want to use for unwinding user rows.
If you are exporting repeaters using root mappings, don't use this option.

### Request example (basic, using slugs as keys)
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
            "column_key" : "slug"
        },
        "template": {
            "columns": [
                {
                    "field": "Email",
                    "mode": "slug",
                    "slug": "user-email-address"
                },
                {
                    "field": "First Name",
                    "mode": "slug",
                    "slug": "user-first-name"
                },
                {
                    "field": "Last Name",
                    "mode": "slug",
                    "slug": "user-last-name"
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
            "user-elementid": "621561e2f7fa620d22575e33",
            "user-email-address": "john.smith@example.com",
            "user-first-name": "John",
            "user-last-name": "Smith"
        },
        {
            "user-elementid": "621561e6f7fa620d22575e35",
            "user-email-address": "jane.jenkins@example.com",
            "user-first-name": "Jane",
            "user-last-name": "Jenkins"
        }
    ]
}
```

### Request example with unwinding applications
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
                    "slug": "user-email-address"
                },
                {
                    "field": "First Name",
                    "mode": "slug",
                    "slug": "user-first-name"
                },
                {
                    "field": "Last Name",
                    "mode": "slug",
                    "slug": "user-last-name"
                },
                {
                    "field": "App Registration Id",
                    "mode": "slug",
                    "slug": "user-applications-registration-id",
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
            "Last Name": "Smith",
            "App Registration Id" : "AA11"
        },
        {
            "id": "621561e2f7fa620d22575e33",
            "Email": "john.smith@example.com",
            "First Name": "John",
            "Last Name": "Smith",
            "App Registration Id" : "BB11"
        },
        {
            "id": "621561e6f7fa620d22575e35",
            "Email": "jane.jenkins@example.com",
            "First Name": "Jane",
            "Last Name": "Jenkins",
            "App Registration Id" : "ZZ99"
        }
    ]
}
```

### Request example with root mappings
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
        "template": {
            "columns": [
                {
                    "field": "Email",
                    "mode": "slug",
                    "slug": "user-email-address"
                },
                {
                    "field": "First Name",
                    "mode": "slug",
                    "slug": "user-first-name"
                },
                {
                    "field": "Last Name",
                    "mode": "slug",
                    "slug": "user-last-name"
                },
                {
                    "field" : "Applications", 
                    "mode" : "slug", 
                    "slug" : "user-applications-root", 
                    "columns" : [
                        {
                            "field": "Registration",
                            "mode": "slug",
                            "slug": "user-applications-registration-id"
                        },
                        {
                            "field": "Term",
                            "mode": "slug",
                            "slug": "user-applications-term"
                        },
                        {
                            "field": "Progress",
                            "mode": "slug",
                            "slug": "user-applications-numeric-progress"
                        }
                    ]
                },
                {
                    "field" : "Evaluations", 
                    "mode" : "slug", 
                    "slug" : "user-evaluations-root", 
                    "columns" : [
                        {
                            "field": "Type",
                            "mode": "slug",
                            "slug": "user-evaluations-type"
                        },
                        {
                            "field": "English",
                            "mode": "slug",
                            "slug": "user-evaluations-english-score"
                        },
                        {
                            "field": "Off Adv Math",
                            "mode": "slug",
                            "slug": "user-evaluations-math-score"
                        }
                    ]
                },
                {
                    "field" : "Events", 
                    "mode" : "slug", 
                    "slug" : "user-events-root", 
                    "columns" : [
                        {
                            "field": "Signup",
                            "mode": "slug",
                            "slug": "user-events-signup_guid"
                        },
                        {
                            "field": "Guid",
                            "mode": "slug",
                            "slug": "user-events-guid"
                        },
                        {
                            "field": "Attended",
                            "mode": "slug",
                            "slug": "user-events-status-attended"
                        }
                    ]
                },
                {
                    "field" : "Milestones", 
                    "mode" : "slug", 
                    "slug" : "user-milestones-root",
                    "columns" : [
                        {
                            "field": "Type",
                            "mode": "slug",
                            "slug": "user-milestones-type"
                        },
                        {
                            "field": "Name",
                            "mode": "slug",
                            "slug": "user-milestones-name"
                        },
                        {
                            "field": "Major",
                            "mode": "slug",
                            "slug": "user-milestones-term",
                            "transformations" : [
                                {
                                    "params" : {
                                        "to" : "name", 
                                        "enabled" : true, 
                                        "failOnUnmatch" : null, 
                                        "from" : "guid", 
                                        "data_source" : "data_source.terms", 
                                        "dimensions" : {
                                            "code" : "Code", 
                                            "guid" : "Guid", 
                                            "name" : "Name"
                                        }, 
                                        "taxonomy" : null, 
                                        "root_level" : false
                                    }, 
                                    "type" : "formAutomapExport"
                                }, 
                                {
                                    "type" : "uppercase", 
                                    "params" : [

                                    ]
                                }
                            ]
                        }
                    ]
                },
                {
                    "field" : "Notes", 
                    "mode" : "slug", 
                    "slug" : "user-notes-root",
                    "columns" : [
                        {
                            "field": "Type",
                            "mode": "slug",
                            "slug": "user-notes-note-type"
                        },
                        {
                            "field": "Name",
                            "mode": "slug",
                            "slug": "user-notes-note-body"
                        },
                        {
                            "field": "Date",
                            "mode": "slug",
                            "slug": "user-notes-note-created"
                        }
                    ]
                },
                {
                    "field" : "Schools", 
                    "mode" : "slug", 
                    "slug" : "user-education-schools-root",
                    "columns" : [
                        {
                            "field": "School Type",
                            "mode": "slug",
                            "slug": "user-education-schools-type"
                        },
                        {
                            "field": "School Name",
                            "mode": "slug",
                            "slug": "user-education-schools-name"
                        }
                    ]
                },
                {
                    "field" : "Sources", 
                    "mode" : "slug", 
                    "slug" : "user-sources-root",
                    "columns" : [
                        {
                            "field": "Type",
                            "mode": "slug",
                            "slug": "user-sources-type"
                        },
                        {
                            "field": "Name",
                            "mode": "slug",
                            "slug": "user-sources-name"
                        }
                    ]
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
            "user-elementid": "621561e2f7fa620d22575e33",
            "user-email-address": "john.smith@example.com",
            "user-first-name": "John",
            "user-last-name": "Smith",
            "user-applications-root": [
                {
                    "user-applications-registration-id": "04D1625E",
                    "user-applications-term": "tu.terms.112",
                    "user-applications-numeric-progress": 100
                }
            ],
            "user-evaluations-root": [
                {
                    "user-evaluations-type": "AP",
                    "user-evaluations-english-score": "",
                    "user-official-tests-math-score": "",
                    "user-unofficial-tests-math-score": ""
                },
                {
                    "user-evaluations-type": "PSAT",
                    "user-evaluations-english-score": 40,
                    "user-official-tests-math-score": "",
                    "user-unofficial-tests-math-score": 10
                },
                {
                    "user-evaluations-type": "AP_S",
                    "user-evaluations-english-score": "",
                    "user-official-tests-math-score": "",
                    "user-unofficial-tests-math-score": ""
                }
            ],
            "user-events-root": [
                {
                    "user-events-signup_guid": "vbmwpReY",
                    "user-events-guid": "tu.events.4337",
                    "user-events-status-attended": false
                }
            ],
            "user-milestones-root": [
                {
                    "user-milestones-type": "APPLICATION_START",
                    "user-milestones-name": "Spark App",
                    "user-milestones-term": "SPRING 2021"
                },
                {
                    "user-milestones-type": "APPLICATION_COMPLETE",
                    "user-milestones-name": "Spark App",
                    "user-milestones-term": "SPRING 2021"
                },
                {
                    "user-milestones-type": "APPLICATION_SUBMIT",
                    "user-milestones-name": "Spark App",
                    "user-milestones-term": "MINI FALL 2020"
                },
                {
                    "user-milestones-type": "CREATED",
                    "user-milestones-name": "Record Created",
                    "user-milestones-term": ""
                }
            ],
            "user-notes-root": [
                {
                    "user-notes-note-type": "tu.taxonomy.1062",
                    "user-notes-note-body": "<p>We need to call this student next monday.</p>",
                    "user-notes-note-created": "2022-02-28T15:49:56+00:00"
                }
            ],
            "user-education-schools-root": [
                {
                    "user-education-schools-type": "highschool",
                    "user-education-schools-name": "Navasota High School"
                },
                {
                    "user-education-schools-type": "college",
                    "user-education-schools-name": "Element451 College"
                }
            ],
            "user-sources-root": null
        },
        {
            "user-elementid": "621561e6f7fa620d22575e35",
            "user-email-address": "jane.jenkins@example.com",
            "user-first-name": "Jane",
            "user-last-name": "Jenkins",
            "user-applications-root": [
                {
                    "user-applications-registration-id": "7KSHWBAH",
                    "user-applications-term": "tu.terms.71",
                    "user-applications-numeric-progress": 100
                },
                {
                    "user-applications-registration-id": "MJCZYDUR",
                    "user-applications-term": "tu.terms.350391",
                    "user-applications-numeric-progress": 95.8
                }
            ],
            "user-evaluations-root": [
                {
                    "user-evaluations-type": "SAT",
                    "user-evaluations-english-score": "",
                    "user-official-tests-math-score": "",
                    "user-unofficial-tests-math-score": ""
                }
            ],
            "user-events-root": null,
            "user-milestones-root": [
                {
                    "user-milestones-type": "CREATED",
                    "user-milestones-name": "Record Created",
                    "user-milestones-term": ""
                },
                {
                    "user-milestones-type": "APPLICATION_START",
                    "user-milestones-name": "Spark App",
                    "user-milestones-term": "TU.TERMS.71"
                },
                {
                    "user-milestones-type": "APPLICATION_COMPLETE",
                    "user-milestones-name": "Spark App",
                    "user-milestones-term": "TU.TERMS.71"
                },
                {
                    "user-milestones-type": "APPLICATION_SUBMIT",
                    "user-milestones-name": "Spark App",
                    "user-milestones-term": "TU.TERMS.71"
                }
            ],
            "user-notes-root": null,
            "user-education-schools-root": [
                {
                    "user-education-schools-type": "highschool",
                    "user-education-schools-name": "Eva Y Patria Custodio Franqui"
                },
                {
                    "user-education-schools-type": "college",
                    "user-education-schools-name": "Marian University"
                }
            ],
            "user-sources-root": null
        }
    ]
}
```

