---
layout: post
title: Designing a Professional REST API Example using C# ASP.NET Core
summary: "Learn about essential components for a REST API"
image: /assets/images/20171017/header.jpg
twitter_image: /assets/images/20171017/twitter-image.jpg
facebook_link_image: /assets/images/20171017/facebook-link-image.jpg
date: 2017-10-17 04:30:13.000000000 -05:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
featured: 
  visible: true
categories:
- API
- Azure
- Microsoft
tags:
- C#
author:
  #login: aaronralls74
  #email: arkeytekllc@gmail.com
  #display_name: aaronralls74
  #first_name: ''
  #last_name: ''
excerpt: Design a professional REST API and Azure environments that will be ready
  for a production environment.
permalink: '/:year/:month/:day/:title'
---
<p>In this blog series, I will explain how to design a professional REST API that will be ready for a production environment.  My goal is to provide you with the framework for creating and managing quality API solutions quickly and effortlessly. This will allow you to focus your time on solving actual business problems. I will get you there by meeting two objectives. First, there will be an API solution with all the features needed. Second, the framework will cover the ongoing management after the initial production release.</p>
<h2>Objective: API Solution</h2>
<p>Over the past years, I have worked on many web API projects using different languages and technologies. Since the release of ASP.NET Core, I have come across many great posts on a specific feature, such as logging or authentication. I realized that there is no one project template that ties together all these great examples. The Microsoft examples are great and simplistic, yet they aren't production ready. As I read those articles, I felt like I was recreating the wheel. I know others have created API's with many of these production level features. This is a huge pet peeve of mine, where IT departments and the industry as a whole don't apply their knowledge and skills to our own workflows. IT teams need to start seeing themselves as manufacturing teams and seek to automate everything they do. To achieve an end-to-end automated process I will start with a Visual Studio solution that is production ready. I will not be covering how to design and implement a <a href="https://en.wikipedia.org/wiki/Multitier_architecture" target="_blank" rel="noopener">multilayered</a> or <a href="https://en.wikipedia.org/wiki/Domain-driven_design" target="_blank" rel="noopener">domain-driven design</a> (DDD) architecture. I will focus on the API solution that would consume the domain logic, regardless of how you decide to architect it.</p>
<h2>Objective: API Management Framework</h2>
<p><span class="hardreadability">Too often developers and product teams stop managing the API once they release to production</span>. To compound this problem, most of the examples don't cover how to design a robust system. <span class="hardreadability">As a developer and software architect, you need to know about the network and the various devices and the roles they play in the solution</span>. I will cover a series of guidelines to create an API management framework. <span class="hardreadability"> The end result will be an automated setup of the API solution and environments required to manage the complete lifecycle</span>. The management framework and templates will use <a href="https://azure.microsoft.com/en-us/">Microsoft Azure</a> cloud computing services.</p>
<h2>Future REST API Article Topics</h2>
<p>Here is a preview of some of the topics I will cover in this series.</p>
<ul>
<li>Configuration Data</li>
<li>Logging</li>
<li>Batch/Transactional Client Identification</li>
<li>Versioning</li>
<li>Consistent Data Formats</li>
<li>Security</li>
<li>Testing</li>
<li>Client-Side Integration</li>
<li>Documentation</li>
<li>Project Template NuGet package</li>
<li>DevOps</li>
<li>Manage Technical Debt</li>
<li>Optimizations</li>
</ul>
