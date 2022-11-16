# Layout Helpers

Just like views, layouts can also have helpers on a per-layout, per-layout-folder or per-application basis. If the framework detects the helper, it will inject it into the rendering layout so you can use methods, properties or whatever. All you need to do is follow a set of conventions. Let's say we have a layout in the following location:

```javascript
/layouts
  /general
    +main.cfm
```

Then we can create the following templates

* `mainHelper.cfm` : Helper for the `main.cfm` layout.
* `generalHelper.cfm` : Helper for any layout in the `general` folder.

```javascript
/layouts
  /general
    +main.cfm
    +mainHelper.cfm
    +generalHelper.cfm
```

That's it. Just append Helper to the layout or folder name and there you go, the framework will use it as a helper for that layout specifically.

> **Caution** Please note that layout helpers will be inheritenly available to any view rendered inside of the layout.

## Application wide helpers

You can also use the `coldbox.viewsHelper` directive to tell the framework what helper file to use for ALL layouts rendered:

```
coldbox.viewsHelper = "includes/helpers/viewsHelper.cfm;
```
