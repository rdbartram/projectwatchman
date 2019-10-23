---
RFC: 0001
Author: Ryan Bartram
Status: Draft
Version: 1.0
Area: Engine
Comments Due: 14/11/2019
Plan to implement: <Yes | No>
---

# PowerShell Based LCM

The Local Configuration Manager (LCM) was a windows component made available with the
release of the WMF 4. It's the engine behind Desired State Configuration (DSC) and is used
predominently as a Windows based configuration management tool.

The current implementation is based on WMI/OMI and receives MOF files either from a Pull Server
or by use of the cmdlets `Start-DscConfiguration` or `Publish-DscConfiguration`.

The LCM can be configured to pull its configuration on a recurring basis and implements security
by verifying signatures of MOFs or modules before they are downloaded.

The LCM has 3 application modes `ApplyOnly`, `ApplyAndMonitor` and `ApplyAndAutoCorrect`. When
the configuration is applied, it is applied in a serial fashion i.e. one resource after another.
There is a dependency model defined within the configuration which enforces the order with which
resources are deployed.

## Motivation

As a user, I want to replace the existing WMI/OMI based LCM on Windows and Linux with cross
platform version that can perform all of the tasks of the existing LCM
plus a lot lot more. So I can utilise my existing DSC knowledge to manage infrastructure
all over my organisation. Onprem and in the cloud.

I see this being the new Terraform, Pulumi etc

## Scope

The scope of this RFC focuses on provided the same functionality as the current LCM whilst
improving it in two key ways. One by providing cross platform support and second by increasing
performance by means of parallelism.

## Specification

### User Experience

```PowerShell
# User generates MOF as per normal. Latter RFCs can deal with improvements around configuration
generation

# Command to execute DSC on a machine
Invoke-DscConfiguration
```

### The basic Set
This RFC proposes a way of using PowerShell to perform the task of the LCM:

The RFC aims to provide similar functionality to the existing LCM by means of providing
runtime credentials. As Linux/Mac doesn't currently support this, we would need additional
code to handle this. The other improvement, focuses on implementing parallelism up to a
configurable degree, default perhaps 5.

```PowerShell
param([Resources[]]$resources)

# Loop each resource and use the Invoke-DscResource cmdlet available in both Windows Powershell
# and PowerShell Core to start each resource.

foreach($resource in $resources) {
    $InvokeParameters = @{
        name = $resource.name
        module = $resource.module
        properties = $resource.properties
    }

    if ($false -eq (Invoke-DscResource @InvokeParameters -Method Test)) {
        if($IsWindows) {
            Invoke-DscResource @InvokeParameters -Method Set
        } else {
            # run old python scripts for linux using ps job for user context
            
            for new powershell resources on mac and linux, simply import root module and execute
            either inline or within a job.
        }
    }
}
```

This is a very trivial implementation that just demonstrates how we could implement the set
functionality of the LCM within PowerShell.

### Dependency model

I recommend we keep the near enough same data model for resources i.e. `resourceid`, `modulename`,
`moduleversion` and `dependson`. I would make a seperate `properties` object with all the resource
properties just to seperate the instance data from the meta data.

### Parallelism

On initial run, all the resources without any dependencies would get added to a "execution" queue
which will be looped over until empty. As resources are successfully executed, the subsequent
dependent resources would be dynamically added to the queue. Any remaining resources after the
execution has completed will be considered to have failed due to missing dependencies.

### Alternate Proposals and Considerations

The RFC is open to comment

#### Open Questions

* Should we bother supporting Windows Powershell?
    WinPS is out
* Which file formats should be support? MOF, Json, Yaml
    MOF has to stay to support legacy configuration. Json will be defacto standard but can be
    extended by the community
* What does Invoke-DscResource give us really? If it doesn't work on Linux and Mac, should we
just keep it for Windows and hope Microsoft implement it also for the other OSs?
