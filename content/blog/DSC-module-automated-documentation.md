---
title: "DSC Module Automated Documentation"
date: 2020-07-26T00:00:00+02:00
draft: false
author: johlju
---

This article describes the features of the auto-documentation module
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
and we still call it auto-documentation (or auto-doc).

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
examples. An example of a wiki page can be seen here for the [resource SqlProtocol](https://github.com/dsccommunity/SqlServerDsc/wiki/SqlProtocol).

The user can also use `Get-Help` to get the same information, e.g.
`Get-Help SqlProtocol`.

## Implement automated user documentation

The 

## Future

- Gather all wikis in one location to get a better overview over all 
  DSC modules user documentation.
