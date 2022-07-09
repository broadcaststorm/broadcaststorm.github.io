---
layout: post
title:  "Creating and Assigning Roles in Intersight"
categories: cisco intersight rbac
---

Cisco's Intersight service provides flexible means to manage all your devices with a plethora of policies from a single web interface or via a robust REST API framework. Recent updates permit partitioning of an account and the associated resources according to use case, functional domain, etc. all while centralizing important business relevant elements (licensing, accounts).

## What the Business Needs

Cisco Intersight provides a great deal of capabilities and services as part of its Cloud Operations Platform [^1]. As different aspects of the business would benefit from its services, the business would naturally want to put the proper governance around access to the variety of available services.

Logically, as we consider how to access control resources, there are a few different entities that we have to bring together whose combinations would result in governance we require:

- Identities, managed by an identity provider
- Devices (targets), grouped in resource groups
- Organizations, providing the collections for policies
- System Roles, granting privileges to manage specific policies and devices

Intuitively, given these elements, we define a specific user-defined role as a unique combination of the above. The business simply has to define the appropriate groupings of roles, devices, and policies that can then be assigned to a set of users and/or groups.

## Building RBAC and Compartmentalization

These tasks also align to the workflow you'd follow within the GUI as well. First, we'll illustrate those tasks within the GUI and then we'll provide a couple of RBAC examples.

Different portions of this process could be accomplished separately by different Intersight system roles. For this exercise, we'll use an Account Administrator so that we can perform all the needed tasks. Account management is performed in the "Settings" area, located by the gear icon at the top of the website.

- Create Resource Groups, adding any existing devices (targets)

![Create Resource Groups](/images/intersight-rbac/create-resource-groups.png)

- Add Devices (Targets), assigning them to relevant resource groups
- Create Organizations, claiming relevant resource group(s)
- Create Role (Privilege), grouping system roles with an organization
- Add Role to User

## Employment Disclaimer

[^1] This blog post is my own words, written on my own time, and for my own self-interest/gratification. Nothing in this post has been approved by or in any way represents official statements from my employer. The entire content could be completely wrong and you should test its veracity with the referenced materials in a development environment. The usual "no warranties implied, explicity or implicity" warnings apply. You should also refer to my [Conflict of Interest](/conflict-of-interests/) disclaimer post as well.
