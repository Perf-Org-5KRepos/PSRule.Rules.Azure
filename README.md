# PSRule for Azure

A suite of rules to validate Azure resources using PSRule.

![ci-badge]

Features of PSRule for Azure include:

- [Ready to go](docs/features.md#ready-to-go) - Leverage over 100 pre-built rules to validate Azure resources.
- [DevOps](docs/features.md#devops) - Validate resources pre or post-deployment.
- [Cross-platform](docs/features.md#cross-platform) - Run on MacOS, Linux and Windows.

## Disclaimer

This project is to be considered a **proof-of-concept** and **not a supported product**.

For issues with rules and documentation please check our GitHub [issues](https://github.com/Microsoft/PSRule.Rules.Azure/issues) page.
If you do not see your problem captured, please file a new issue and follow the provided template.

If you have any problems with the [PSRule][engine] engine, please check the project GitHub [issues](https://github.com/Microsoft/PSRule/issues) page instead.

## Getting the modules

This project requires the `PSRule` and `Az` PowerShell modules. For details on each see [install].

You can download and install these modules from the PowerShell Gallery.

Module             | Description | Downloads / instructions
------             | ----------- | ------------------------
PSRule.Rules.Azure | Validate Azure resources | [latest][module] / [instructions][install]

## Getting started

PSRule for Azure provides two methods for analyzing Azure resources:

- _Pre-flight_ - Before resources are deployed from Azure Resource Manager templates.
- _In-flight_ - After resource are deployed to an Azure subscription.

The following example shows basic _In-flight_ usage. For specific use cases see [scenarios](#scenarios).

For additional details see the [FAQ](docs/features.md#frequently-asked-questions-faq).

### Export resource data

To validate Azure resources running in a subscription, export the resource data with the `Export-AzRuleData` cmdlet.
The `Export-AzRuleData` cmdlet exports a resource graph for one or more subscriptions that can be used for analysis with the rules in this module.

By default, resources for the current subscription context are exported. See below for more options.

Before running this command you should connect to Azure by using the `Connect-AzAccount` cmdlet.

For example:

```powershell
# Authenticate to Azure, only required if not currently connected
Connect-AzAccount;

# Export resource data
Export-AzRuleData;
```

### Validate resources

To validate Azure resources use the extracted data with the `Invoke-PSRule` cmdlet.

For example:

```powershell
Invoke-PSRule -InputPath .\*.json -Module 'PSRule.Rules.Azure';
```

### Additional options

By default, resource data for the current subscription context will be exported to the current working directory as JSON.

To export resource data for specific subscriptions use:

- `-Subscription` - to specify subscriptions by id or name.
- `-Tenant` - to specify subscriptions within an Azure Active Directory Tenant by id.

For example:

```powershell
# Export data from two specific subscriptions
Export-AzRuleData -Subscription 'Contoso Production', 'Contoso Non-production'
```

To export specific resource data use:

- `-ResourceGroupName` - to filter resources by Resource Group.
- `-Tag` - to filter resources based on tag.

For example:

```powershell
# Export information from two resource groups within the current subscription context
Export-AzRuleData -ResourceGroupName 'rg-app1-web', 'rg-app1-db'
```

To export resource data for all subscription contexts use:

- `-All` - to export resource data for all subscription contexts.

For example:

```powershell
# Export data from all subscription contexts
Export-AzRuleData -All;
```

To filter results to only failed rules, use `Invoke-PSRule -Outcome Fail`.
Passed, failed and error results are shown by default.

For example:

```powershell
# Only show failed results
Invoke-PSRule -InputPath .\*.json -Module 'PSRule.Rules.Azure' -Outcome Fail;
```

The output of this example is:

```text
   TargetName: storage

RuleName                            Outcome    Recommendation
--------                            -------    --------------
Azure.Storage.UseReplication        Fail       Storage accounts not using GRS may be at risk
Azure.Storage.SecureTransferRequ... Fail       Storage accounts should only accept secure traffic
Azure.Storage.SoftDelete            Fail       Enable soft delete on Storage Accounts
```

A summary of results can be displayed by using `Invoke-PSRule -As Summary`.

For example:

```powershell
# Display as summary results
Invoke-PSRule -InputPath .\*.json -Module 'PSRule.Rules.Azure' -As Summary;
```

The output of this example is:

```text
RuleName                            Pass  Fail  Outcome
--------                            ----  ----  -------
Azure.ACR.MinSku                    0     1     Fail
Azure.AppService.PlanInstanceCount  0     1     Fail
Azure.AppService.UseHTTPS           0     2     Fail
Azure.Resource.UseTags              73    36    Fail
Azure.SQL.ThreatDetection           0     1     Fail
Azure.SQL.Auditing                  0     1     Fail
Azure.Storage.UseReplication        1     7     Fail
Azure.Storage.SecureTransferRequ... 2     6     Fail
Azure.Storage.SoftDelete            0     8     Fail
```

## Scenarios

For walk through examples of `PSRule.Rules.Azure` module usage see:

- [Validate Azure resources from templates with Azure Pipelines](docs/scenarios/azure-pipelines-ci/azure-pipelines-ci.md)
- [Validate Azure resources from templates with continuous integration (CI)](docs/scenarios/azure-template-ci/azure-template-ci.md)

## Rule reference

For a list of rules included in the `PSRule.Rules.Azure` module see:

- [Rules by category](docs/rules/en/module.md)
- [Rules by resource](docs/rules/en/resource.md)

## Baseline reference

The following baselines are included within `PSRule.Rules.Azure`.

- [Azure.Default](docs/baselines/en/Azure.Default.md) - Default baseline for Azure rules.
- [Azure.Preview](docs/baselines/en/Azure.Preview.md) - Includes Azure features in preview.
- [Azure.All](docs/baselines/en/Azure.All.md) - Includes all Azure rules.
- [Azure.GA_2020_06](docs/baselines/en/Azure.GA_2020_06.md) - Baseline for GA rules released June 2020 or prior.

## Language reference

PSRule for Azure extends PowerShell with the following cmdlets.

### Commands

The following commands exist in the `PSRule.Rules.Azure` module:

- [Export-AzRuleData](docs/commands/PSRule.Rules.Azure/en-US/Export-AzRuleData.md) - Export resource configuration data from Azure subscriptions.
- [Export-AzTemplateRuleData](docs/commands/PSRule.Rules.Azure/en-US/Export-AzTemplateRuleData.md) - Export resource configuration data from Azure templates.
- [Get-AzRuleTemplateLink](docs/commands/PSRule.Rules.Azure/en-US/Get-AzRuleTemplateLink.md) - Get a metadata link to a Azure template file.

### Concepts

The following conceptual topics exist in the `PSRule.Rules.Azure` module:

- [Azure metadata link](docs/concepts/PSRule.Rules.Azure/en-US/about_PSRule_Azure_Metadata_Link.md)
- [Configuration](docs/concepts/PSRule.Rules.Azure/en-US/about_PSRule_Azure_Configuration.md)
  - [Azure_AKSMinimumVersion](docs/concepts/PSRule.Rules.Azure/en-US/about_PSRule_Azure_Configuration.md#azure_aksminimumversion)
  - [Azure_AKSNodeMinimumMaxPods](docs/concepts/PSRule.Rules.Azure/en-US/about_PSRule_Azure_Configuration.md#azure_aksnodeminimummaxpods)
  - [Azure_AllowedRegions](docs/concepts/PSRule.Rules.Azure/en-US/about_PSRule_Azure_Configuration.md#azure_allowedregions)
  - [Azure_MinimumCertificateLifetime](docs/concepts/PSRule.Rules.Azure/en-US/about_PSRule_Azure_Configuration.md#azure_minimumcertificatelifetime)

## Changes and versioning

Modules in this repository will use the [semantic versioning](http://semver.org/) model to declare breaking changes from v1.0.0.
Prior to v1.0.0, breaking changes may be introduced in minor (0.x.0) version increments.
For a list of module changes please see the [change log](CHANGELOG.md).

> Pre-release module versions are created on major commits and can be installed from the PowerShell Gallery.
> Pre-release versions should be considered experimental.
> Modules and change log details for pre-releases will be removed as standard releases are made available.

## Contributing

This project welcomes contributions and suggestions.
If you are ready to contribute, please visit the [contribution guide](CONTRIBUTING.md).

## Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/)
or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Maintainers

- [Bernie White](https://github.com/BernieWhite)

## License

This project is [licensed under the MIT License](LICENSE).

[install]: docs/scenarios/install-instructions.md
[ci-badge]: https://dev.azure.com/bewhite/PSRule.Rules.Azure/_apis/build/status/PSRule.Rules.Azure-CI?branchName=master
[module]: https://www.powershellgallery.com/packages/PSRule.Rules.Azure
[engine]: https://github.com/Microsoft/PSRule
