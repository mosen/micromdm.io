---
date: 2016-04-14T00:45:28-04:00
title: MicroMDM 
type: index
---
MicroMDM is a flexible Mobile Device Management server for OS X administrators.   
MicroMDM integrates with [DEP](http://www.apple.com/business/dep/) and can provision a device with configuration profiles, user accounts and settings.  
Unlike most commercial MDM products, MicroMDM allows an administrator to choose which management tools to deploy during DEP Enrollment.

## Features
* Support for `InstallApplication` and `InstallProfile` commands
* DEP Enrollment
* [Workflows](workflows/)
* [RESTful API](management-api/) and SDK
* single binary - can be deployed on linux, osx
* CLI tools for admins
* supports APNS http2 API for push notifications
* Web UI *(planned)*


## Workflows
A workflow allows an administrator to group initial command payloads, a list of configuration profiles and a list of applications together. During DEP enrollment a device will check to see if it has a workflow assigned and execute it.


{{< note title="Comming soon" >}}
Workflows are almost finished but there are a few details left to implement.
{{< /note >}}
### Nested workflows 
A workflow can include other workflows.

### Default workflow 
If the device has no assigned workflow, it will also check to find a workflow named `site_default`. The `site_default` workflow will be applied to any device which does not have a currently assigned one.
