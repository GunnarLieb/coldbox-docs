# Parent Configuration

There are a few parent application settings when dealing with modules. In your `ColdBox.cfc` you can have a `modules` structure with some configuration settings.

```
modules = {
    // reload and unload modules in every request
    // DEPRECATED in coldbox 5
    autoReload = false,
    // An array or list of the module names that will load ONLY
    include = [],
    // An array or list of the module names that will be EXCLUDED
    exclude = ["paidModule1","paidModule2"]
};
```
