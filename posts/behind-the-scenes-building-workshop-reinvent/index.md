---
title: "Behind the scenes: Building a workshop for AWS re:Invent"
description: "Ever wondered what goes into the design and delivery of content at re:Invent?  This post explores the process of creating a 300-level workshop for delivery at AWS re:Invent 2023"
tags:
    - cloud
    - networking

authorGithubAlias: mhjwork
authorName: Matt Johnson
date: 2023-11-08
---

|ToC|
|---|

# Introduction


# Selection

Whilst I don't intend to go into much detail on the selection process, it's worth calling out that the process for determining re:Invent content (sessions, workshops, chalk talks, etc) starts more than 6 months before the event itself; each track (such as networking, security, databases, etc) will have a core team of experts that will review content ideas from across AWS and beyond.  As you can imagine, there are **lots** of ideas submitted, and so this process will often involve consolidating ideas into the number of allocation sessions.

If you're lucky enough to be selected, this is when the hard work really begins!

# Developing the theme

The workshop Laura and I are developing for re:Invent 2023 (NET301 - Approaches to layered security for Amazon VPC) is an evolution of the one we ran at last year's event (NET308 - Best practices for securing Amazon VPC).  The concept of the workshop is to allow participants to go beyond the "basics" of VPC security controls (such as security groups, NACLs, Flow Logs) and have a chance to get hands-on with some services they might not have used before.  Services we focus on include Route 53 query logging, DNS Firewall, AWS Network Firewall, Traffic Mirroring, AWS WAF, and Network Access Analyzer.  This year we also added support for demonstrating IPv6, and the ability to create your own custom firewall capability using Gateway Load Balancer.

# Workshop design

One of the key outcomes we want from the workshop is for people to "learn by doing"; it is important to us that participants are able to try and solve things on their own initiative, without simply following detailed step-by-step of which buttons to click for the 2 hours.  To this end, we came up with a design approach based loosely on the Perl slogan "There's More than One Way to Do it".

First, the workshop is broken up into a number of separate tracks, allowing you to "choose your own adventure".  These tracks focus on DNS Security, Network Analysis, AWS Network Firewall, 3rd Party Network Firewall, and AWS WAF.  These tracks can be done in any order, and don't have any depdenencies between them.  This is important - we don't want people getting stuck on one part of the workshop and then being unable to complete any other parts.

Within each track is one or two labs; these do have to be followed in the correct order, since the outcome of one lab will typically be needed to support the next lab.  For example, in the AWS Network Firewall track, the first lab focusses on the routing changes needed to integrate AWS Network Firewall into your VPC, which is needed to start the second lab, which looks at the various firewall rules you can use to filter or alert on specific traffic types.

In order to make the labs as interactive as possible, we allow participants two different approaches; the first is to state the desired outcome in the workshop guide, provide a starting point, and offer some suggestions as to what services to use.  Participants are free to explore and learn by experimentation, but with the ability to look at hints if they need to.  Alternatively, if attendees feel more confortable with a step-by-step walkthrough, we provide that as well.  The approaches can be mixed; you might start by experimenting, get stuck, and then drop into the walkthough guide to help with a specific task.

So, with those concepts in mind, it's on to the building!

# Building the workshop

## AWS Workshop Studio

AWS re:invent workshops are deployed using a capability called AWS Workshop Studio.  This allows us to provision 100s of pre-configured AWS accounts based on a workshop template, and then provide time-boxed access to those accounts for workshop attendees.  This is a **far** better experience than the approach I had to use at my first re:Invent workshop I ran back in 2017, when participants had to bring their own accounts, deploy resources before starting the labs, troubleshoot resource limits, etc - kudos to the AWS Workshop Studio team!

At its basic, the workshop environment consists of an AWS account provisioned on behalf of the attendee, with some pre-deployed resources that are there at the start of the workshop.  Under the hood, this is done using one or more AWS CloudFormation templates, together with an associated IAM policy.  The CloudFormation template is deployed prior to the workshop starting, and then when the participants log into their accounts, they assume a role with the appropriate permissions needed to complete the workshop.

What all of these means is that, as workshop authors, we need to create the CloudFormation template that is pre-deployed into the Workshop Studio accounts, together with the documentation & guidance needed for attendees to complete the tasks.

## The CloudFormation Template

A significant part of the effort of developing a workshop is focussed on the development of the CloudFormation template(s) that deploy the resources that the participants will use.  There are lots of ways of authoring CloudFormation templates - if you are a developer, I would highly recommend tools such as the AWS Cloud Development Kit (CDK).  For Laura and myself, we decided to write native CloudFormation (as YAML), because we were more experienced with this approach, and it gave us a bit more control over resource naming (useful when you need users to find specific resources).

Whilst most of the template is pretty straightfoward (if long - around 7,500 lines of code), there are a fewspecific implementation details that are worth calling out; the first is around conditional resources, the second custom resources, and the third CloudWatch Dashboards.


### Conditional resources


### Custom resources


### CloudWatch Dashboards

To encourage participants to explore, we make extensive use of AWS CloudWatch Dashboards (one for each Lab).  These provide two valuable capabilities; they can provide context-sensitive information not available in the static content guide, and also provide an up-to-date status on the completion of the lab tasks.

#### Contextual information

In many of the Labs, the participant will be asked to configure or change a pre-deployed resource (for example, to configure a route to a VPC endpoint).  Since the details of some of these resources aren't known until the workshop is deployed, it means that the workshop guide cannot be specific about names, etc.  However, since we build the Cloudwatch Dashboards as part of the same CloudFormation template, we can embed logical references to those resources within the dashboard definitions, and allow CloudFormation to resolve them for us when the stack is deployed.  This means that the Dashboard is able to display the details of the actual resources, unique to each participant. 

This sounds more complicated than it is, and so I've provided an example snippet below:

SNIPPET and SCREENSHOT HERE


### Cloudwatch Dashboards - Custom Widgets

If you haven't come across Custom Widgets before, I highly recommend checking them out - they are incredibly useful for so many things!  In our case, we use a custom widget (which under the hood is an AWS Lambda function that generates JSON, HTML or Markdown output that is displayed in the dashboard) to show progress against the tasks needed to complete the lab.

For example, in the Traffic Mirroring lab, participants need to complete a number of tasks, such as configuring a Mirror Target, defining a Mirror Filter, and establishing a Mirror Session.  The Lambda function checks (every 10 seconds) if those tasks have been completed (by making API calls to the relevant services), and if they have been, updates the widget output to show that a task has been completed.

An example of this is shown below:

SCREENSHOT HERE



For 

, with some substantial modifications. to include IPv6, and also to demonstrate 

## Build process

## Review process

## Security

## Delivery

## Wrap-up

