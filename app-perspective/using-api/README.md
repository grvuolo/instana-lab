# Create App Perspective with API

This guide show the steps to interact with Application Perspective resources through Instana API 

## Get API Token

Get a token with "canConfigureApplications" permission

https://www.ibm.com/docs/en/instana-observability/current?topic=apis-web-rest-api#unit-specific-api-tokens


## Get All App Perspectives

```
curl --request GET 'https://<instana-url>/api/application-monitoring/settings/application' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool
```

    {
        "id": "m4YB9K1UQ-eugBlSmLkvkw",
        "label": "Banking App",
        "matchSpecification": null,
        "tagFilterExpression": {
            "type": "EXPRESSION",
            "logicalOperator": "OR",
            "elements": [
                {
                    "type": "TAG_FILTER",
                    "name": "service.name",
                    "stringValue": "PHP",
                    "numberValue": null,
                    "booleanValue": null,
                    "key": null,
                    "value": "PHP",
                    "operator": "EQUALS",
                    "entity": "DESTINATION"
                },
                {
                    "type": "TAG_FILTER",
                    "name": "service.name",
                    "stringValue": "Unspecified",
                    "numberValue": null,
                    "booleanValue": null,
                    "key": null,
                    "value": "Unspecified",
                    "operator": "EQUALS",
                    "entity": "DESTINATION"
                },
                {
                    "type": "EXPRESSION",
                    "logicalOperator": "AND",
                    "elements": [
                        {
                            "type": "TAG_FILTER",
                            "name": "endpoint.name",
                            "stringValue": "GET /simple-lamp",
                            "numberValue": null,
                            "booleanValue": null,
                            "key": null,
                            "value": "GET /simple-lamp",
                            "operator": "EQUALS",
                            "entity": "DESTINATION"
                        },
                        {
                            "type": "TAG_FILTER",
                            "name": "host.zone",
                            "stringValue": "teclab",
                            "numberValue": null,
                            "booleanValue": null,
                            "key": null,
                            "value": "teclab",
                            "operator": "EQUALS",
                            "entity": "DESTINATION"
                        }
                    ]
                }
            ]
        },
        "scope": "INCLUDE_ALL_DOWNSTREAM",
        "boundaryScope": "ALL",
        "accessRules": [
            {
                "accessType": "READ_WRITE",
                "relationType": "GLOBAL",
                "relatedId": null
            }
        ]
    }


## Delete App Perspective



```
curl --request DELETE 'https://<instana-url>/api/application-monitoring/settings/application/m4YB9K1UQ-eugBlSmLkvkw' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool


  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
Expecting value: line 1 column 1 (char 0)
```

## Create App Perspective

vi spgi-pot-app.json

```
{
 "label": "SPGI PoT",
    "matchSpecification": null,
    "tagFilterExpression": {
        "type": "EXPRESSION",
        "logicalOperator": "OR",
        "elements": [
            {
                "type": "TAG_FILTER",
                "name": "service.name",
                "stringValue": "PHP",
                "numberValue": null,
                "booleanValue": null,
                "key": null,
                "value": "PHP",
                "operator": "EQUALS",
                "entity": "DESTINATION"
            },
            {
                "type": "TAG_FILTER",
                "name": "service.name",
                "stringValue": "Unspecified",
                "numberValue": null,
                "booleanValue": null,
                "key": null,
                "value": "Unspecified",
                "operator": "EQUALS",
                "entity": "DESTINATION"
            },
            {
                "type": "EXPRESSION",
                "logicalOperator": "AND",
                "elements": [
                    {
                        "type": "TAG_FILTER",
                        "name": "endpoint.name",
                        "stringValue": "GET /simple-lamp",
                        "numberValue": null,
                        "booleanValue": null,
                        "key": null,
                        "value": "GET /simple-lamp",
                        "operator": "EQUALS",
                        "entity": "DESTINATION"
                    },
                    {
                        "type": "TAG_FILTER",
                        "name": "host.zone",
                        "stringValue": "teclab",
                        "numberValue": null,
                        "booleanValue": null,
                        "key": null,
                        "value": "teclab",
                        "operator": "EQUALS",
                        "entity": "DESTINATION"
                    }
                ]
            }
        ]
    },
    "scope": "INCLUDE_ALL_DOWNSTREAM",
    "boundaryScope": "ALL",
    "accessRules": [
        {
            "accessType": "READ_WRITE",
            "relationType": "GLOBAL",
            "relatedId": null
        }
    ]
}
```

```
curl -X POST -H "Content-Type: application/json" -d @/<path-to>/spgi-pot-app.json https://<instana-url>/api/application-monitoring/settings/application/ --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  3240  100  1085  100  2155   1101   2187 --:--:-- --:--:-- --:--:--  3289
{
    "id": "S_GrRgEITQqZlFlDO8jcsg",
    "label": "SPGI PoT",
    "matchSpecification": null,
.
.
.
```
