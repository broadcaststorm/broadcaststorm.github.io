---
layout: post
title:  "Managing RBAC Programatically with Cisco Intersight"
categories: cisco intersight rbac models automation
---



## What Intersight Models

The relationship of the above defined elements are depicted below:

![Intersight Privilege Data Model](/images/intersight_rbac_data_model.png)

As shown above, the combination of Organizations, Devices, and Roles form the entity "Resource Roles", which in the Intersight GUI are shown as "Roles" of type "User Defined". Users or Groups can then be assigned to these roles as they have need, via the above Permission model.

Fortunately, as I've laid them out, the natural business requirements align to the data models within the Intersight service, mapping directly to the REST API we need to leverage.

## Employment Disclaimer

[^1] This blog post is my own words, written on my own time, and for my own self-interest/gratification. Nothing in this post has been approved by or in any way represents official statements from my employer. The entire content could be completely wrong and you should test its veracity with the referenced materials in a development environment. The usual "no warranties implied, explicity or implicity" warnings apply. You should also refer to my [Conflict of Interest](/conflict-of-interests/) disclaimer post as well.
