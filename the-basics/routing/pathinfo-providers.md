# Pathinfo Providers

By default, the URL mapping processor will detect routes by looking at the `CGI.PATH_INFO` variable, but you can override this and provide your own function. This feature can be useful to set flags for each request based on a URL and then clean or parse the URL to a more generic form to allow for simple route declarations. Uses may include internationalization (i18n) and supporting multiple experiences based on devices such as Desktop, Tablet, Mobile and TV.&#x20;

To modify the URI used by the Routing Services before route detection occurs simply follow the convention of adding a function called `pathInfoProvider()` to your application Router (`config/Router.cfc`).

The `pathInfoProvider()` function is responsible for returning the string used to match a route.

```javascript
// Example PathInfoProvider for detecting a mobile request
function PathInfoProvider( event ){
  var rc = event.getCollection();
  var prc = event.getCollection(private=true);

  local.URI = CGI.PATH_INFO;

  if (reFindNoCase('^/m',local.URI) == 0)
  {
    // Does not look like this could be a mobile request...
    return local.URI;
  }

  // Mobile Request? Let's find out.

  // If the URI is "/m" it is easy to determine that this is a
  // request for the Mobile Homepage.
  if (len(local.URI) == 2)
  {
    prc.mobile = true;
    // Simply return "/" since they want the mobile homepage
    return "/";
  }

  // Only continue with our mobile evaluation if we have a slash after
  // our "/m". Without a slash following the /m the route is something
  // else like coldbox.org/makes/cool/stuff
  if (REFindNoCase('^/m/',local.URI) == 1)
  {
    // Looks like we are mobile!
    prc.mobile = true;

    // Remove our "/m/" determination and continue
    // processing for languages...
    local.URI = REReplaceNoCase(local.URI,'^/m/','/');
  }

  // The URI starts with an "m" but does not look like
  // a mobile request. So, simply return the URI for normal
  // route detection...
  return local.URI;
}
```

{% hint style="info" %}
The[ Rewrite rules section](requirements/rewrite-rules.md#htaccess) has another useful example for a pathInfo provider
{% endhint %}
