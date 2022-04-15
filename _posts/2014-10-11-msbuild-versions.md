---
layout: post
title: "MSBuild compability"
excerpt: "After some random error, I found msbuild has two similar versions"
tags: [build, automation, msbuild]
comments: true
---

#### When automating some C# deployment on Windows, be aware that there is two MSBuild instances available.


After some random error, I found msbuild has two similar versions.
The continuous integration server was pointing to:

{% highlight text %}
	C:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe
{% endhighlight %}

This was firing a build that was impossible to reproduce in my computer, not even on the build server... until I realized the path was strange. 

It end up that the msbuild that the Developer Command for VS2013 uses this msbuild: 

{% highlight text %}
C:\Program Files (x86)\MSBuild\12.0\Bin\MSBuild.exe
{% endhighlight %}


This version supports some extra features like /T:Package.