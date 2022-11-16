# Model Data Binding

The framework also offers you the capability to bind incoming FORM/URL/REMOTE/XML/JSON/Structure data into your model objects by convention. This is done via [WireBox's object population](https://wirebox.ortusbooks.com/advanced-topics/wirebox-object-populator) capabilities. The easiest approach is to use our `populateModel()` function which will populate the object from many incoming sources:

* request collection **RC**
* Structure
* json
* xml
* query

This will try to match incoming variable names to setters or properties in your domain objects and then populate them for you. It can even do ORM entities with ALL of their respective relationships. Here is a snapshot of the method:

```javascript
/**
* Populate a model object from the request Collection or a passed in memento structure
* @model The name of the model to get and populate or the acutal model object. If you already have an instance of a model, then use the populateBean() method
* @scope Use scope injection instead of setters population. Ex: scope=variables.instance.
* @trustedSetter If set to true, the setter method will be called even if it does not exist in the object
* @include A list of keys to include in the population
* @exclude A list of keys to exclude in the population
* @ignoreEmpty Ignore empty values on populations, great for ORM population
* @nullEmptyInclude A list of keys to NULL when empty
* @nullEmptyExclude A list of keys to NOT NULL when empty
* @composeRelationships Automatically attempt to compose relationships from memento
* @memento A structure to populate the model, if not passed it defaults to the request collection
* @jsonstring If you pass a json string, we will populate your model with it
* @xml If you pass an xml string, we will populate your model with it
* @qry If you pass a query, we will populate your model with it
* @rowNumber The row of the qry parameter to populate your model with
*/
function populateModel(
	required model,
	scope="",
	boolean trustedSetter=false,
	include="",
	exclude="",
	boolean ignoreEmpty=false,
	nullEmptyInclude="",
	nullEmptyExclude="",
	boolean composeRelationships=false,
	struct memento=getRequestCollection(),
	string jsonstring,
	string xml,
	query qry
){
```

Let's do a quick example:

**Person.cfc**

```javascript
component accessors="true"{

    property name="name";
    property name="email";

    function init(){
        setName('');
        setEmail('');
    }
}
```

**editor.cfm**&#x20;

```javascript
<cfoutput>
<h1>Funky Person Form</h1>
#html.startForm(action='person.save')#

    #html.textfield(label="Your Name:",name="name",wrapper="div")#
    #html.textfield(label="Your Email:",name="email",wrapper="div")#

    #html.submitButton(value="Save")#

#html.endForm()#
</cfoutput>
```

**Event Handler -> person.cfc**

```javascript
component{

    function editor(event,rc,prc){
        event.setView("person/editor");        
    }

    function save(event,rc,prc){

        var person = populateModel( "Person" );

        writeDump( person );abort;
    }

}
```

In the dump you will see that the `name` and `email` properties have been bound.
