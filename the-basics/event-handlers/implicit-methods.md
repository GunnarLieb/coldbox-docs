# Implicit Methods

Every event handler controller has some **implicit** methods that if you create them, they come alive. Just like the implicit methods in `Application.cfc`

## onMissingAction()

With this convention you can create virtual events that do not even need to be created or exist in a handler. Every time an event requests an action from an event handler and that action does **not** exist in the handler, the framework will check if an `onMissingAction()` method has been declared. If it has, it will execute it. This is very similar to ColdFusion's `onMissingMethod()` but on an event-driven framework.

```javascript
function onMissingAction( event, rc, prc, missingAction, eventArguments ){

}
```

This event has an extra argument: **missingAction** which is the missing action that was requested. You can then do any kind of logic against this missing action and decide to do internal processing, error handling or anything you like. The power of this convention method is extraordinary, you have tons of possibilities as you can create virtual events on specific event handlers.

## onError()

This is a localized error handler for your event handler. If any type of **runtime** error occurs in an event handler and this method exists, then the framework will call your method so you can process the error first. If the method does not exist, then normal error procedures ensue.

{% hint style="info" %}
Please note that compile time errors will not fire this method, only runtime exceptions.
{% endhint %}

```javascript
// On Error
function onError( event, rc, prc, faultAction, exception, eventArguments ){
    // prepare a data packet
    var data = {
        error = true,
        messages = exception.message & exception.detail,
        data = ""
    }

    // log via the log variable already prepared by ColdBox
    log.error("Exception when executing #arguments.faultAction# #data.messages#", exception);    

    // render out a json packet according to specs status codes and messages
    event.renderData(data=data,type="json",statusCode=500,statusMessage="Error ocurred");

}
```

## onInvalidHTTPMethod()

This method will be called for you if a request is trying to execute an action in your handler without the proper approved HTTP Verb. It will then be your job to determine what to do next:

```javascript
function onInvalidHTTPMethod( faultAction, event, rc, prc ){
    return "Go away!";
}
```
