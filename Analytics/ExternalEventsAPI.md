# [Analytics] External Events API

### Intro
This API allows you to register external user events into Element451.

## Register
Each event sent will be validated and processed in isolation, if some events
fail validation or processing those will not affect good events which will
be registered.

### Available Event types:
- EmailSend
- EmailOpen
- EmailUnsubscribe
- EmailBounce
- EmailComplaint
- EmailDelivered

### Parameters
- "events"
The list of events you want to register into Element.

Example:

```
"events" : [...]
```    

### Request example
```
Request:
POST https://{{client}}.{{api}}/v2/analytics/events

URL parameters:
{{client}}: Assigned client subdomain.
{{api}}: URL of the Element451 API. "api.451.io" for Production API.

Request headers:
Feature: Feature token for that client
Content-Type: 'application/json'

Request body parameters:
{
    "events" : [
        {
            "type": "EmailDelivered",
            "timestamp": 618395780,
            "source": "MailTronV2",
            "notes": "delivery report from sendgrid",
            "user_id": "5702ec092e402e851a8b4567"
        },
        {
            "type": "...EmailOpen",
            "timestamp": 618935780,
            "source": "MailTron",
            "notes": "Open from Safari",
            "user_id": "5702ec092e402e851a8b4567",
            "context": "A context"
        },
        {
            "type": "EmailSend",
            "timestamp": 618355780,
            "source": "MailTron",
            "notes": "Something about this interesting email event",
            "user_id": "5702ec092e402e851a8b4567",
            "campaign": {
                "campaign_guid": "tu.camp_oscar.589",
                "variant_id": "VariantA",
                "title": "The Campaign"
            },
            "email": {
                "email_guid": "tu.email_oscar.589",
                "email_name": "Welcome here!",
                "subject": "Hello Friend!",
                "template": "tu.template_oscar.589",
                "from": "info@element451.io",
                "from_name": "Element",
                "from_domain": "element451.io",
                "reply_to": "reply@element451.io",
                "to": "oscar@element451.com",
                "to_name": "Oscar",
                "evaluated_subject": "Subject B"
            },
            "notification": {
                "notification_guid": "tu.notif_oscar.589",
                "notification_name": "The Notification",
                "source": "A source",
                "type": "A type",
                "target": "A target"
            },
            "status": {
                "bounceCat": "Bounce",
                "jobId": "",
                "timeQueued": "",
                "queue": "",
                "srcMta": ""
            },
            "context": "A context"
        }
    ]
}

Expected Response:
Status 202
{
    "data": [
        {
            "index": 0,
            "status": "accepted"
        },
        {
            "index": 1,
            "status": "rejected",
            "errors": {
                "type": [
                    "Invalid Event Type!"
                ]
            }
        },
        {
            "index": 2,
            "status": "rejected",
            "errors": {
                "user_id": [
                    "The user id field is required."
                ]
            }
        },
    ]
}