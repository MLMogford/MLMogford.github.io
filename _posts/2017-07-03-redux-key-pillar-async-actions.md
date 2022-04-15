---
layout: post
title: "Redux pillars to keep in mind - Async actions"
excerpt: "Things to keep in mind while developing redux application"
tags: [redux, foundation, keystone, cornerstone, pillar]
comments: true
---

## Async Action Creators

Middleware lets you inject custom logic that interprets every action object before it is dispatched. Async actions are the most common use case for middleware.

You could create a generic handler for all the api calls for instance. That triggers the REQUEST, SUCCESS and ERROR events. However it depends on the model and the complexity linked to the API result.

> **Punchline:** If you want to clean up data before sending to the store you may end up having specific implementation for each API call.