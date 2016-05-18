---
date: 2016-05-20T07:46:57-04:00
title: Commands API
---

The commands API manages a queue of MDM payloads for each device.

## Create Command
`POST /mdm/commands`

JSON request body:
```json
{
    "request_type": "InstallApplication",
    "udid" : "184012D9-753A-5DFC-8149-5C9AF257629F",
    "manifest_url" : "https://mdm.example.com/repo/manifests/munkitools-2.5.1.2637.plist",
    "management_flags" : 1
}
```

JSON response body:
```json
{
  "CommandUUID": "d15df5ef-4e02-47eb-b90e-f72b5d0dda04",
  "Command": {
    "RequestType": "InstallApplication",
    "manifest_url": "https://mdm.example.com/repo/manifests/munkitools-2.5.1.2637.plist",
    "management_flags": 1
  }
}
```

## Next Command
`GET mdm/commands/{device udid}/next`

The server will respond will reply back with a list of currently queued commands and the next command payload
which is a base64 encoded plist.
Each time next is called, the returned command is pushed to the back of the queue.

JSON response body:
```json

{
  "command_payload": "PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4KPCFET0NUWVBFIHBsaXN0IFBVQkxJQyAiLS8vQXBwbGUvL0RURCBQTElTVCAxLjAvL0VOIiAiaHR0cDovL3d3dy5hcHBsZS5jb20vRFREcy9Qcm9wZXJ0eUxpc3QtMS4wLmR0ZCI+CjxwbGlzdCB2ZXJzaW9uPSIxLjAiPjxkaWN0PjxrZXk+Q29tbWFuZDwva2V5PjxkaWN0PjxrZXk+TWFuYWdlbWVudEZsYWdzPC9rZXk+PGludGVnZXI+MTwvaW50ZWdlcj48a2V5Pk1hbmlmZXN0VVJMPC9rZXk+PHN0cmluZz5odHRwczovL21kbS5ncm9vYi5pby9yZXBvL21hbmlmZXN0cy9tdW5raXRvb2xzLTIuNS4xLjI2MzcucGxpc3Q8L3N0cmluZz48a2V5PlJlcXVlc3RUeXBlPC9rZXk+PHN0cmluZz5JbnN0YWxsQXBwbGljYXRpb248L3N0cmluZz48L2RpY3Q+PGtleT5Db21tYW5kVVVJRDwva2V5PjxzdHJpbmc+ZDE1ZGY1ZWYtNGUwMi00N2ViLWI5MGUtZjcyYjVkMGRkYTA0PC9zdHJpbmc+PC9kaWN0PjwvcGxpc3Q+Cg==",
  "total_payloads": 1
}

```

## Delete Command
`DELETE /mdm/commands/{device udid}/{command uuid}`

JSON response body
```
{
  "remaining_payloads": 1
}
```

