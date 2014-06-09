---
layout: post
title: "Pass Dynamic Flags to NuGet With PowerShell Splatting"
date: 2014-02-07 10:24:32 -0700
comments: true
categories: Powershell NuGet
---

While working on a PowerShell script for packaging multiple NuGet packages, I discovered a peculiarity in calling NuGet with PowerShell splatting. I wanted to do something like the following:

```powershell
Function Package-Project(
    [string]$folder,
    [switch]$build,
    [switch]$symbols
) {
    pushd $folder
    $nugetArgs = @{Properties="Configuration=$configuration"}
    if($build) { $nugetArgs.Build=$True }
    if($symbols) { $nugetArgs.Symbols=$True }
    nuget pack @nugetArgs
    popd
}

Package-Project ProjectA
Package-Project ProjectB
```

Even though I've "Splatted" $nugetArgs into my NuGet call, the way NuGet handles arguments causes it to choke on the
HashSet of arguments, and throw an error like the following:

```text
nuget : Unknown option: '-Build:True'
At line:1 char:38
+ nuget pack $project $nugetArgs
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (Unknown option: '-Build:True':String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
```

Digging into the [source code](http://nuget.codeplex.com/SourceControl/latest#src/CommandLine/CommandLineParser.cs),
I found that they have a pretty customized argument parser, which seems to make passing a HashSet pretty much impossible.
(If I'm wrong, leave a comment!)

Fortunately, there's a *fairly* reasonable workaround: Pass an array of arguments rather than a HashSet:

```powershell
Function Package-Project(
    [string]$folder,
    [switch]$build,
    [switch]$symbols
) {
    pushd $folder
    $nugetArgs = "-Properties", "Configuration=$configuration"
    if($build) { $nugetArgs += "-Build" }
    if($symbols) { $nugetArgs += "-Symbols" }
    nuget pack @nugetArgs
    popd
}

Package-Project ProjectA
Package-Project ProjectB
```

So there you have it. Use a list of parameter names and values rather than a HashSet when dynamically invoking NuGet.
This is actually somewhat intuitive from a classic CMD standpoint, but not so much from a PowerShell perspective.