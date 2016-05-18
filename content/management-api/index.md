---
date: 2016-05-19T18:56:13-04:00
title: Management API
---

At the core of MicroMDM is a RESTful HTTP API which allows integrations with other services and automation of certain tasks.
The current API groups resources under `/management/v1/` and supports the following endpoints:

## Devices
The devices endpoints allows an administrator to view and update devices and interact with the DEP service.

### List all devices
`GET /management/v1/devices`

### Show device information
`GET /management/v1/devices/{uuid}`

### Assign workflow to device
`PATCH /management/v1/devices/{uuid}`  

JSON request body:   
```json
{
    "workflow_uuid": "bd5191c1-6bc1-49e5-b9fc-b33accc0656e",
}
```
### Send push notification
`POST /management/v1/devices/{udid}/push`  
{{< note title="Note" >}}
Make sure that you are using the device **UDID** and not UUID here. These can look very similar.
{{< /note >}}

### Fetch DEP devices
`POST /management/v1/devices/fetch`

## Profiles
The Profiles endpoint allows managing configuration profiles in the MDM

### Add a new profile
`POST /management/v1/profiles`

JSON request body: 
```json
{
    "payload_identifier": "com.apple.loginwindow",
    "payload_data": "<base64 encoded mobileconfig profile"
}
```

### List all configuration profiles
`GET /management/v1/profiles`

### Show a configuration profile
`GET /management/v1/profiles/{uuid}`

### Delete a configuration profile
`DELETE /management/v1/profiles/{uuid}`

## Applications

## Workflows
Workflows allow an administrator to assign a group of configuration profiles and applications to a device.

### Add a new workflow
`POST /management/v1/workflows`

JSON request body: 
```json
{
    "name": "example_workflow",
    "profiles": [{
        "payload_identifier": "com.apple.loginwindow",
        "profile_uuid": "41647409-ec23-4177-8d5b-307363cd55fd"
    }],
    "applications": [{
        "name": "munkitools-latest",
        "manifest-url": "http://mdm.example.com/pkgs/..."
    }],
    "included_workflows": [{
        "workflow_uuid": "a3566878-b5f1-4d3d-a31e-030010180cc1"
    }]
}
```

## SDK

Go | Python | Other
