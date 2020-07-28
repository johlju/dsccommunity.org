---
title: "DSC Module Automatic Documentation"
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

## The new automated documentation

When the DSC Community moved to a new CI/CD pipeline there was a need for
the maintainer, contributor, and user experience to be even better for those
DSC modules that still had not opt-in to auto-documentation. The DSC modules
that had opted-in to auto-documentation needed to be... well
automated.

So the first goal was to make the automated documentation fully automated
in the CI/CD pipeline. We have not gotten there with the module
DscResource.DocGenerator, and we still call it auto-documentation (or auto-doc).

### Maintainer experience

Since the auto-doc is now fully automated a maintainer no longer have to
intervene. When a pull request is merged and a release is published, the
documentation is also published to the

---
as seen in this example for the resource
[SPInstall](https://github.com/dsccommunity/SharePointDsc/wiki/SPInstall/63397afade10bd9794e5cb5378c20bcc8c0cefd2).
