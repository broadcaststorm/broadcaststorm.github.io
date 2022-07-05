---
layout: post
title:  "Managing RBAC Programatically with Cisco Intersight"
categories: github-pages jekyll fedora vagrant
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

## What Intersight Models

The relationship of the above defined elements are depicted below:

![Intersight Privilege Data Model](/images/intersight_rbac_data_model.png)

As shown above, the combination of Organizations, Devices, and Roles form the entity "Resource Roles", which in the Intersight GUI are shown as "Roles" of type "User Defined". Users or Groups can then be assigned to these roles as they have need, via the above Permission model.

Fortunately, as I've laid them out, the natural business requirements align to the data models within the Intersight service, mapping directly to the REST API we need to leverage. These tasks also align to the workflow you'd follow within the GUI as well:

- Create Resource Groups, adding existing targets
- Create Targets (Devices), assigning to relevant resource groups
- Create Organizations, claiming relevant resource groups
- Create Role


## Employment Disclaimer

[^1] This blog post is my own words, written on my own time, and for my own self-interest/gratification. Nothing in this post has been approved by or in any way represents official statements from my employer. The entire content could be completely wrong and you should test its veracity with the referenced materials in a development environment. The usual "no warranties implied, explicity or implicity" warnings apply. You should also refer to my [Conflict of Interest](/conflict-of-interests/) disclaimer post as well.

