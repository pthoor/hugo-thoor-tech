---
title: Partner Center API - How to
date: '2022-01-07T21:00:00+02:00'
images: ["cover.jpg"]
draft: true
tags: ["Partner Center"]
description: ""
---

Install Azure AD Application:
ddd


PowerShell script to get the Trainings:
ddd




POST https://api.partnercenter.microsoft.com/insights/v1/mpn/ScheduledQueries

```json
{
  "Name": "CustomersRevenueQuery",
  "Description": "Query to get top 10 customers by revenue for last month",
  "Query": "SELECT CustomerName, Product, BilledRevenueUSD FROM CustomersAndTenants ORDER BY BilledRevenueUSD LIMIT 10 TIMESPAN LAST_MONTH"
}
```

RESPONSE
```json
{
    "value": [
        {
            "queryId": "37427a58-46a4-4151-a26c-994ad11bb32f",
            "name": "CustomersRevenueQuery",
            "description": "Query to get top 10 customers by revenue for last month",
            "query": "SELECT CustomerName, Product, BilledRevenueUSD FROM CustomersAndTenants ORDER BY BilledRevenueUSD LIMIT 10 TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "pierre.thoor@teliasonerasverige.onmicrosoft.com",
            "createdTime": "2022-01-08T08:24:25Z"
        }
    ],
    "nextLink": null,
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200,
    "dataRedacted": false
}
```



POST https://api.partnercenter.microsoft.com/insights/v1/mpn/ScheduledReport

```json
{
  "ReportName": "Trainings",
  "Description": "Top trainings",
  "QueryId": "20f5da57-3c2a-481b-b6a0-ec34d6db14e2",
  "StartTime": "2022-01-08T18:41:00Z",
  "ExecuteNow": true,
  "QueryStartTime": null,
  "QueryEndTime": null,
  "RecurrenceInterval": 24,
  "RecurrenceCount": 100,
  "Format": "CSV"
}
```

RESPONSE
```json
{
    "value": [
        {
            "reportId": "18615d8d-eb6b-493a-b259-793e8b5023e6",
            "reportName": "Trainings",
            "description": "Top trainings",
            "queryId": "20f5da57-3c2a-481b-b6a0-ec34d6db14e2",
            "query": "SELECT PGAMpnId,TrainingActivityId,TrainingTitle,TrainingType,IndividualFirstName,IndividualLastName,Email,CorpEmail,TrainingCompletionDate,ExpirationDate,ActivationStatus,Month,IcMCP,MCPID,MPNId,PartnerName,PartnerCityLocation,PartnerCountryLocation FROM TrainingCompletions TIMESPAN LAST_6_MONTHS",
            "user": "pierre.thoor@teliasonerasverige.onmicrosoft.com",
            "createdTime": "2022-01-08T08:51:26Z",
            "modifiedTime": null,
            "executeNow": true,
            "queryStartTime": null,
            "queryEndTime": null,
            "startTime": "2022-01-08T08:51:26Z",
            "reportStatus": "Active",
            "recurrenceInterval": -1,
            "recurrenceCount": 1,
            "callbackUrl": null,
            "callbackMethod": null,
            "format": "csv"
        }
    ],
    "nextLink": null,
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200,
    "dataRedacted": false
}
```

GET https://api.partnercenter.microsoft.com/insights/v1/mpn/ScheduledReport/execution/18615d8d-eb6b-493a-b259-793e8b5023e6

RESPONSE

```json
{
    "value": [
        {
            "executionId": "08fd9cd0-47a6-42fd-abff-d1457526c899",
            "reportId": "18615d8d-eb6b-493a-b259-793e8b5023e6",
            "recurrenceInterval": -1,
            "recurrenceCount": 1,
            "callbackUrl": null,
            "callbackMethod": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportLocation": null,
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "nextLink": null,
    "totalCount": 1,
    "message": null,
    "statusCode": 200,
    "dataRedacted": false
}
```