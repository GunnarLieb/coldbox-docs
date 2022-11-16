# Using Flash RAM

There are several ways to interact with the ColdBox Flash RAM:

* Using the `flash` scope object (Best Practice)
* Using the `persistVariables()` method from the super type and ColdBox Controller
* Using the `persistence` arguments in the `relocate()` method from the super type and ColdBox Controller.

All of these methods interact with the Flash RAM object but the last two methods not only place variables in the temporary storage bin but actually serialize the data into the Flash RAM storage immediately. The first approach queues up the variables for serialization and at the end of a request it serializes the variables into the correct storage scope, thus saving precious serialization time. In the next section we will learn what all of this means.

## Flash Scope Object

The `flash` scope object is our best practice approach as it clearly demarcates the code that the developer is using the flash scope for persistence. Any flash scope must inherit from our `AbstractFlashScope` and has access to several key methods that we will cover in this section. However, let's start with how the flash scope stores data:

1. The flash persistence methods are called for saving data, the data is stored in an internal temporary request bin and awaiting serialization and persistence either through relocation or termination of the request.
2. If the flash methods are called with immediate save arguments, then the data is immediately serialized and stored in the implementation's persistent storage.
3. If the flash's `saveFlash()` method is called then the data is immediately serialized and stored in the implementation's persistent storage.
4. If the application relocates via relocate() or a request finalizes then if there is data in the request bin, it will be serialized and stored in the implementation's storage.

> **Caution** By default the Flash RAM queues up serializations for better performance, but you can alter the behavior programmatically or via the configuration file.
>
> **Info** If you use the `persistVariables()` method or any of the persistence arguments on the `relocate()` method, those variables will be saved and persisted immediately.

To review the Flash Scope methods, please [go to the API](http://apidocs.ortussolutions.com/coldbox/current) and look for the correct implementation or the `AbstractFlashScope`.

> **Info** Please note that the majority of a Flash scope methods return itself so you can concatenate method calls. Below are the main methods that you can use to interact with the Flash RAM object:

### clear()

Clears the temporary storage bin

```javascript
flash.clear();
```

### clearFlash()

Clears the persistence flash storage implementation

```javascript
flash.clearFlash();
```

### discard()

```javascript
any discard([string keys=''])
```

Discards all or some keys from flash storage. You can use this method to implement flows.

```javascript
// discard all flash variables
flash.discard();

// discard some keys
flash.discard('userID,userKey,cardID');
```

### exists()

```javascript
boolean exists(string name)
```

Checks if a key exists in the flash storage

```javascript
if( flash.exists('notice') ){
 // do something
}
```

### get()

```javascript
any get(string name, [any default])
```

Get a value from flash storage and optionally pass a default value if it does not exist.

```javascript
// Get a flash key that you know exists
cardID = flash.get("cardID");

// Render some flash content
<div class="notice">#flash.get("notice","")#</div>
```

### getKeys()

Get a list of all the objects in the temp flash scope.

```javascript
Flash Keys: #flash.getKeys()#
```

### getFlash()

Get a reference to the permanent storage implementation of the flash scope.

```javascript
<cfdump var="#flash.getFlash()#">
```

### getScope()

Get the flash temp request storage used throughout a request until flashed at the end of a request.

```javascript
<cfdump var="#flash.getScope()#">
```

### isEmpty()

Check if the flash scope is empty or not.

```javascript
<cfif !flash.isEmpty()>
</cfif>
```

### keep()

```javascript
any keep([string keys=''])
```

Keep flash temp variable(s) alive for another relocation. Usually called from interceptors or event handlers to create conversations and flows of data from event to event.

```javascript
function step2(event){
    // keep variables for step 2 wizard
    flash.keep('userID,fname,lname');

    // keep al variables
    flash.keep();
}
```

### persistRC()

```javascript
any persistRC([string include=''], [string exclude=''], [boolean saveNow='false'])
```

Persist variable(s) from the ColdBox request collection into flash scope. Persist the entire request collection or limit the variables to be persisted by providing the keys in a list. "Include" will only try to persist request collection variables with keys in the list. "Exclude" will try to persist the entire request collection except for variables with keys in the list.

```javascript
// persist some variables that can be reinflated into the RC upon relocation
flash.persistRC(include="name,email,address");
relocate('wizard.step2');

// persist all RC variables using exclusions that can be reinflated into the RC upon relocation
flash.persistRC(exclude="ouser");
relocate('wizard.step2');

// persist some variables that can be reinflated into the RC upon relocation and serialize immediately
flash.persistRC(include="email,addressData",savenow=true);
```

### put()

```javascript
any put(string name, any value, [boolean saveNow='false'], [boolean keep='true'], [boolean inflateToRC=FROMConfig], [boolean inflateToPRC=FROMConfig], [boolean autoPurge=FROMConfig)
```

The main method to place data into the flash scope. Optional arguments control whether to save the flash immediately, inflate to RC or PRC on the next request, and if the data should be auto-purged. You can also use the configuration settings to have a consistent flash experience, but you can most certainly override the defaults. By default all variables placed in flash RAM are automatically purged in the next request once they are inflated UNLESS you use the keep() methods in order to persist them longer or create flows. However, you can also use the autoPurge argument and set it to false so you can control when the variables will be removed from flash RAM. Basically a glorified ColdFusion scope that you can use.

```javascript
// persist some variables that can be reinflated into the RC upon relocation
flash.put(name="userData",value=userData);
relocate('wizard.step2');

// put and serialize immediately.
flash.put(name="userData",value=userData,saveNow=true);

// put and mark them to be reinflated into the PRC only
flash.put(name="userData",value=userData,inflateToRC=false,inflateToPRC=true);


// put and disable autoPurge, I will remove it when I want to!
flash.put(name="userData",value=userWizard,autoPurge=false);
```

### putAll()

```javascript
any putAll(struct map, [boolean saveNow='false'], [boolean keep='true'], [boolean inflateToRC='[runtime expression]'], [boolean inflateToPRC='[runtime expression]'], [boolean autoPurge='[runtime expression]'])
```

Pass an entire structure of name-value pairs into the flash scope (similar to the put() method).

```javascript
var map = {
    addressData = rc.address,
    userID = securityService.getUserID(),
    loggedIn = now()
};

// put all the variables in flash scope as single items
flash.putAll(map);

// Use them later
if( flash.get("loggedIn") ){

}
```

### remove()

```javascript
any remove(string name, [boolean saveNow='false'])
```

Remove an object from the temporary flash scope so it will not be included when the flash scope is serialized. To remove a key from the flash scope and make sure your changes are effective in the persistence storage immediately, use the saveNow argument.

```javascript
// mark object for removal
flash.remove('notice');

// mark object and remove immediately from flash storage
flash.remove('notice',true);
```

### removeFlash()

Remove the entire flash storage. We recommend using the clearing methods instead.

### saveFlash()

Save the flash storage immediately. This process looks at the temporary request flash scope, serializes it if needed, and persists to the correct flash storage on demand.

> **Info** We would advice to not overuse this method as some storage scopes might have delays and serializations

```javascript
flash.saveFlash();
```

### size()

Get the number of the items in flash scope.

```javascript
You have #flash.size()# items in your cart!
```

### More Examples

```javascript
// handler code:
function saveForm(event){
    // save post
    flash.put("notice","Saved the form baby!");
    // relocate to another event
    relocate('user.show');
}
function show(event){
    // Nothing to do with flash, inflating by flash object automatically
    event.setView('user.show');
}

// User/show.cfm template using if statements
<cfif flash.exists("notice")>
    <div class="notice">#flash.get("notice")#</div>
</cfif>

// User/show.cfm using defaults
<div class="notice">#flash.get(name="notice",defaultValue="")</div>
```
