---
title: "DSC Module Automated Documentation"
date: 2020-07-26T00:00:00+02:00
draft: false
author: johlju
---

This article describes the features of the automated documentation module
[DscResource.DocGenerator](https://github.com/dsccommunity/DscResource.DocGenerator).

_This article assumes the repository is using the pattern from the [Sampler](https://github.com/gaelcolas/Sampler)_
_project._

## Background

In early days when the DSC resource modules only had a few resources all
the documentation was gathered in the README.md, together with the change
log, contribution information etcetera. Some modules quickly grew out of
that due to the number of resources (e.g. [SharePointDsc](https://github.com/dsccommunity/SharePointDsc),
[SqlServerDsc](https://github.com/dsccommunity/SqlServerDsc), and
[ActiveDirectoryDsc](https://github.com/dsccommunity/ActiveDirectoryDsc)).

### Contributor experience

Historically the documentation for a DSC module was updated through pull
requests sent in by contributors. For example for the DSC module SqlServerDsc
a contributor had to:

- update the resource documentation in the README.md file (resource
  description, parameter names, parameter types, parameter descriptions,
  and resource requirements)
- update the parameter descriptions in the schema MOF file.
- update example files in the `Examples` resource folder
- update the comment-based help (including parameter descriptions)

The parameter descriptions would need to be updated in three locations,
often with the same change.

### User experience

The discoverability was not high as a user would have to read both the
README.md and browse to specific examples, located at another location in
the repository, to get to know the module and the resources.

### Earlier automated documentation

There have been a degree of automated documentation to enhance the contributor
and user experience. A few DSC modules opted-in to use an early iteration of
automated documentation which leverage the GitHub repository wiki. Most notable
is the DSC module [SharePointDsc](https://github.com/dsccommunity/SharePointDsc)
that was the first to use this. But there was manual steps to keep the documentation
updated which could only be done by a maintainer.

This early iteration went under the the name "auto-documentation".

## The new automated user documentation

When the DSC Community moved to a new CI/CD pipeline there was a need for
the maintainer, contributor, and user experience to be even better for those
DSC modules that still had not opt-in to auto-documentation. The DSC modules
that had opted-in to auto-documentation needed to be... well
automated.

The first goal was to make the user documentation fully automated
in the CI/CD pipeline. We have now gotten there with the module
[DscResource.DocGenerator](https://github.com/dsccommunity/DscResource.DocGenerator),
and we still call it auto-documentation (or auto-doc for short).

The features of automated documentation:

- Can generate conceptual help for MOF-based DSC resources.
- Can generate markdown documentation for MOF-based DSC resources.
  - Can also generate documentation for the embedded instances.
- Can have folder containing markdown files that can be published, e.g.
  `WikiSource`.
- Can publish markdown files to the GitHub repository Wiki.
- Can be used for both regular modules and modules containing DSC resources.
- Can be used with [cmdlets](https://github.com/dsccommunity/DscResource.DocGenerator#cmdlets),
  or use [tasks](https://github.com/dsccommunity/DscResource.DocGenerator#tasks)
  together with [Invoke-Build](https://github.com/nightroman/Invoke-Build)
  (the tasks are wrappers that uses the cmdlets).

>The tasks is preferably used in conjunction with the [Sampler](https://github.com/gaelcolas/Sampler)
>project.

### Maintainer experience

Since the user documentation is now fully automated a maintainer no longer
have to intervene. When a pull request is merged and a preview release or
a full release is published the documentation is now automatically built
and published to the GitHub repository wiki.

### Contributor experience

A contributor should still update the documentation through a pull request. There
are three aspects of the user documentation:

Type of documentation | Location in repository
--- | ---
Resource specific information like general information about the resource, requirements, known issues, etcetera. | `/source/DSCResources/ResourceName/README.md`
Resource parameter names, types, embedded CIM instances, and descriptions. | `/source/DSCResources/ResourceName/ResourceName.schema.mof`
Resource examples files. | `/source/Examples/Resources/ResourceName/*.ps1`

>A contributor still need to update comment-based help in the resource code
>as appropriate, but the comment-based help is not used for user documentation.
>The comment-based help is used for design documentation for other contributors.
>Comment-based help should be used to describe how parameters are used in
>the code and design choices for the various functions.

### User experience

The user now only have to browse to one location, the GitHub repository Wiki,
to have all the user documentation in one location.

For each resource there is a wiki page that contain the resource specific
information, parameters and their type and description, and all the resource
examples. An example of a wiki page can be seen here for the
[resource SqlDatabaseObjectPermission](https://github.com/dsccommunity/SqlServerDsc/wiki/SqlDatabaseObjectPermission).

The user can also use `Get-Help` to get the same information when a DSC
module is installed, e.g. `Get-Help SqlDatabaseObjectPermission`.

## Implement automated user documentation

This will explain how to add automated documentation for a DSC module.
It is assumed that the module is already using the build pipeline from
the [Sampler](https://github.com/gaelcolas/Sampler) project.

>There is documentation how to convert a repository to the [Sampler](https://github.com/gaelcolas/Sampler)
>project in the article [Steps to convert a module for continuous delivery](https://dsccommunity.org/blog/convert-a-module-for-continuous-delivery/).
>Not all steps in that article might be needed for your project.

### Prerequisites

There are some prerequisites that need to be in place to fully use
automated documentation.

- Only MOF-based DSC resources are supported as of now (pull requests
  welcome to support class-based resources).
- The DSC resource must have descriptions in the MOF schema file.
- Each DSC resource need to have a resource specific `README.md`.
- (optional) Each DSC resource should have at least one example.

#### Descriptions in MOF schema

The descriptions in the MOF schema is used to generate the parameter documentation
for both conceptual help documentation and markdown documentation.
Preferably the descriptions in the MOF schema should be descriptive to clearly
describe the parameter for the user. The descriptions can also contain markdown
code. There are no known hard limit to the length of the description.

>The SqlServerDsc module have implemented a first iteration of a guideline
>for the markdown code used in the parameter descriptions, see
>[Documentation with Markdown](https://github.com/dsccommunity/SqlServerDsc/blob/master/CONTRIBUTING.md#documentation-with-markdown).

An example below for the DSC resource `SqlDatabaseObjectPermission` in the module
SqlServerDsc.

```plaintext
[ClassVersion("1.0.0.0"), FriendlyName("SqlDatabaseObjectPermission")]
class DSC_SqlDatabaseObjectPermission : OMI_BaseResource
{
    [Key, Description("Specifies the name of the _SQL Server_ instance to be configured.")] String InstanceName;
    [Key, Description("Specifies the name of the database user, user-defined database role, or database application role that will have the permission.")] String Name;
    [Required, EmbeddedInstance("DSC_DatabaseObjectPermission"), Description("Specifies the permissions for the database object and the principal. The permissions is an array of embedded instances of the `DSC_DatabaseObjectPermission` CIM class.")] String Permission[];
    [Write, Description("Specifies the host name of the _SQL Server_ to be configured. Default value is `$env:COMPUTERNAME`.")] String ServerName;
    [Write, Description("Specifies that permissions that has parameter **Ensure** set to `'Present'` (the default value for permissions) should always be enforced even if that encompasses cascading revocations. An example if the desired state is `'Grant'` but the current state is `'GrantWithGrant'`. If parameter **Force** is set to `$true` the _With Grant_ permission is revoked, if set to `$false` an exception is thrown since the desired state could not be set. Default is to throw an exception.")] Boolean Force;
};

[ClassVersion("1.0.0")]
class DSC_DatabaseObjectPermission
{
    [Key, Description("Specifies the state of the permission."), ValueMap{"Grant","Deny","GrantWithGrant"}, Values{"Grant","Deny","GrantWithGrant"}] String State;
    [Required, Description("Specifies a set of permissions. Valid permission names can be found in the article [ObjectPermissionSet Class properties](https://docs.microsoft.com/en-us/dotnet/api/microsoft.sqlserver.management.smo.objectpermissionset#properties).")] String Permission[];
    [Write, Description("Specifies the desired state of the permission. When set to `'Present'` the permissions will be added. When set to `'Absent'` the permissions will be removed. Default value is `'Present'`."), ValueMap{"Present","Absent"}, Values{"Present","Absent"}] String Ensure;
};
```

#### DSC resource specific `README.md`

## Future

- Gather all wikis in one location to get a better overview over all
  DSC modules user documentation.
