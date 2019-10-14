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

I see this being the new Terraform, Polumi etc

## Specification

### User Experience

```PowerShell
# Command to generate new DSC MOF file
Build-DscConfiguration

# Command to execute DSC on a machine
Invoke-DscConfiguration
```

### The basic Set
This RFC proposes a way of using PowerShell to perform the task of the LCM:

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
        Invoke-DscResource @InvokeParameters -Method Set
    }
}
```

This is a very trivial implementation that just demonstrates how we could implement the set
functionality of the LCM within PowerShell.

### Dependency model

I recommend we keep the near enough same data model for resources i.e. `resourceid`, `modulename`,
`moduleversion` and `dependson`. I would make a seperate `properties` object with all the resource
properties just to seperate the instance data from the meta data.

On initial run, all the resources without any dependencies would get added to a "deployment" queue
which will be looped over until empty. As resources that are successfully deployed, the dependent
resources will be discovered and dynamically added to the queue. Any remaining resources will be
considered to have failed due to missing dependencies.

### Alternate Proposals and Considerations

The RFC is open to comment

#### Open Questions

* Should we bother supporting Windows Powershell?
* Which file formats should be support? MOF, Json, Yaml
