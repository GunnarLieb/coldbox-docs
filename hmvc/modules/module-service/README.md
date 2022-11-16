# Module Service

The beauty of ColdBox Modules is that you have an internal module service that you can tap in order to dynamically interact with the ColdBox Modules. This service is available by talking to the main ColdBox controller and calling its `getModuleService()` method or via dependency injection.

```javascript
// get module service from handlers, plugins, layouts, interceptors or views.
ms = controller.getModuleService();

// You can also inject it via our autowire DSL
property name="moduleService" inject="coldbox:moduleService";
```
