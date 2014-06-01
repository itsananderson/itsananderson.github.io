---
layout: post
title: "ASP.NET vNext Sounds Awesome"
date: 2014-05-12 19:32:26 -0700
comments: true
categories: ASP.NET
---

Microsoft made some anouncements about the [next generation of ASP.NET today](http://blogs.msdn.com/b/dotnet/archive/2014/05/12/the-next-generation-of-net-asp-net-vnext.aspx).

David Fowler published a post about [the reasoning behind some of the changes](http://davidfowl.com/asp-net-vnext/).
Scott Hanselman published a post with more details about [the features that are coming our way](http://www.hanselman.com/blog/IntroducingASPNETVNext.aspx).
There's a ton of stuff to be excited about here, but my favorite part is these two paragraphs from Scott's post (emphasis his).

> The project system is also changing - we're integrating packages.config, NuGet specifications (nuspec),
> and project files (csprojs) into a unified view of your project dependencies expressed in a project.json file.
> 
> NuGet packages and class libraries are treated the same.
> You get full intellisense in the project.json file and NuGet packages come down automatically and transparently.
> Even better, let's say NuGet package Foo.Bar has a bug but you've only got the NuGet package.
> You can make a folder called Foo.Bar in our local project and put the source via "git clone" in that folder.
> This is great for open source projects.
> **That local version overrides the NuGet, allowing you to easily patch bugs locally in libraries while you wait for a new release.**
> When a new fixed NuGet-distributed version shows up, update the version and delete the local source.
>
> [Introducing ASP.NET vNext - Scott Hanselman](http://www.hanselman.com/blog/IntroducingASPNETVNext.aspx)

Mind. Blown.

![Mind Blown](/images/post-images/mind-blown.gif)