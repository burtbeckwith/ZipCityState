Zip-City-State Plugin
=====================
Provides utilities to populate the City and State from a given 5 digit zip code. Due to licensing concerns no postal
code date will be packaged with this plugin. The original intent of this plugin was to allow users to easily implement
their own zip code database and lookup.  The design is such that you can easily utilize an external service if you would
prefer.

Dependencies
============
This plugin requires jQuery (no strict requirement on version)

Installation
============
Add the following to the BuildConfig.groovy file of your Grails project:

```java
plugins {
        compile(":zip-city-state:1.0") {
            excludes 'jquery'
        }
    }
```
The above assumes that you installed jQuery already in your project.  If you have not you can remove the excludes line.

Implementation Instructions
===========================
Maintain your own database
--------------------------
1. Create a domain class with the zip code info (city,state,zip).
    A simple example can be found in the project source [ZipCode](/grails-app/domain/zipcitystate/ZipCode.groovy).
2. Provide a service which implements the [CityStateLookup](/src/groovy/zipcitystate/CityStateLookup.groovy) interface.<a name="step2"></a>
    An example using the above domain class is provided in the project source [ZipCodeService](/grails-app/services/zipcitystate/ZipCodeService.groovy)
    and copied here:

    ```java
    class ZipCodeService implements CityStateLookup {
        @Override
        ZipCityState lookupByZip(String code) {
            def zipCode = ZipCode.findByCode(code)
            zipCode ? [code:zipCode.code,city:zipCode.city,state:zipCode.state] as ZipCityState : null
        }
    }
    ```

3. Tell the plugin the name of the service bean you created in step 2

    ```java
    zipcitystate.cityStateLookupService.beanName = 'zipCodeService'
    ```
4. Create your gsp
a. Place the tag to include the necessary js on your page (will use the resources plugin if installed in your application)

    ```html
    <zipCode:resources/>
    ```
b. Bind the javascript function and event to your zip code field. An example usage is provided below:

    ```javascript
    $("#zipField").ziplookup()
    .on('zipChange',function(event, state, city, zip) {
       //populate the city and state
       $("#cityField").val(city);
       $("#stateField").val(state);
       //automatically move the cursor to the field that comes next
       $("#phoneField").focus();
    })
    .on('zipNotFound',function(event,zip) {
        //maybe display something or just do nothing
        $("zipError").html('Zip not found!');
    });
    ```
5. Test it out!

Use an external service
------------------------
Start at [step 2](#step2) above

Sample Application
==================
Good news! This plugin is also a fully functional sample application.  Simply download the source and fire it up.
