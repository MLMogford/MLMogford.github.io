---
layout: post
title: "Angular Modules"
excerpt: "Overview angular modules"
tags: [angular, javascript, modules]
comments: true
---

After using angular basics, it become important to understand better how
exactly the modules are loaded and how to take advantage of this when planning
to port the application to the new Angular2.0 or event moving angular 1.x to
ES2015 modules.

As the
  [documentation](https://docs.angularjs.org/guide/module)
describes modules are a way to isolate behavior and application concerns.

## Blocks
Angular modules are divided in two straight forward blocks:

 * **Configuration** blocks: Provider (not instances)
 * **Run** blocks: instances (not Providers)

## Creation / Retrieval
> Important notion to avoid headaches!

```javascript
angular.module('fooModule', [])
```

is completely different from

```javascript
angular.module('fooModule')
```

First one will create a module (and overwrite if the case), the last will retrieve
 the previously defined module (or throw error if not defined.)

---

Following this idea, the next post will be about the service [$injector](https://docs.angularjs.org/api/auto/service/$injector)
