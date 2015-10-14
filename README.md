Angular Style Guide
===================

An opinionated angular style guide for use in RAN projects like Curate/Guggenheim

This style guide is to be considered an extension of the good work of John Papa. Some things we expand upon, some things we reinforce for emphasis, some things we add, others we change. If it's not covered here, it's covered by [John Papa's Angular Style Guide](https://github.com/johnpapa/angular-styleguide).

Naming Coventions
-----------------

### Modules ###
Module names should be straightforward and to the point. It can be whatever you want as long as it is straightforward and descriptive. There are a few notable exceptions:
- app: this is always the main module for your app
- core: this is always the module that contains things that are used in multiple places in the app
- blueprint: this is the module that contains modules that are reused in or are from Project Blueprint

Module hierarchy should not be present in the naming scheme. No modules named 'app.core.thing', just name it 'thing' and have it be a dependency of 'core' which is a dependency of 'app'. This makes it easier to reorganize modules and keeps services/directives/controllers in the module from needing the parent module prefixes to keep naming consistent. The modules should always _still work_ outside of the parent module, so it makes sense to not include the parent module in the name.

### Services and Factories ###
Services and Factories should be capital cased and should *not* contain the word 'service' or 'factory'. They should simply describe what exactly it is they do in as few words as possible, ideally just one word.


    //Not recommended

    angular.module('core')
      .service('sample', sample);
Lowercase names don't impart the importance/singularity (for your singletons!) of a name that looks like a proper noun


    //Not recommended

    angular.module('core')
      .service('SampleService', SampleService);
Don't be overly verbose. Services get injected into controllers all the time, whereas controllers stand alone. Don't complicate those injections with unneeded characters.

    //Recommended

    angular.module('core')
      .service('Sample', Sample);
This looks important, singular, and to the point.

All files in the module should be in the same folder in the filesystem and contain the module namespace:

    --sample/
        --sample.directive.js
        --sample.controller.js
        --sample.index.html
        --sample.module.js
        --sample.service.js

### Controllers ###
Each controller should have a corresponding directive, and the name of the controller file should match the name of the directive file. 'whatever.directive.js' should have its controller in 'whatever.controller.js'. The name of the actual controller should match the filename. The actual controller in 'whatever.controller.js' should be 'whateverController'.

Controllers *do* have Controller in the name. Use controllerAs syntax and replace Controller with View in the controllerAs:

    SampleController as sampleView

The 'whateverView' convention is preferred over 'whateverCtrl' or 'whateverController' for controllerAs because this is the variable name that will appear in the HTML template, which is the view in MVC. The controller itself can have more functionality than what is exposed in the view, and the 'whateverView' name makes it clear that everything saved on the controller object directly will be available in the view/template.

At the top of the controller, save a reference to 'this' as whateverView:

    var whateverView = this;

This keeps a reference to the controller (the proper value of 'this') available at all times (including event handlers) and keeps your variable names consistent. A reference to whateverView.property in the template/view will directly correspond to whateverView.property in the controller.

Controller function names are CapitalCase ('SampleThingController'), names of instantiated controllers are camelCase ('sampleThingView'). We follow this convention because your defined controller function is a contstructor, a singular thing that will be used to make many instances of that controller.

    //not recommended
    angular
      .module('sample')
      .controller('sampleController', sampleController);
      
    function sampleController () {
        var sampleView = this;
    }
Linters will complain about the assignment of 'this' because a camelCased function doesn't register as a constructor.

    //recommended
    angular
      .module('sample')
      .controller('SampleController', SampleController);
      
    function SampleController () {
        var sampleView = this;
    }
SampleController registers as a constructor function

### Directives ###
As with all the other things, a directive should be as short and to the point as possible. One of the best reasons to use directives often is the ability to create highly semantic HTML that is easy to understand at a glance. Overly generic or overly verbose names undermine that benefit.

    Not recommended:

    <galleries-list-without-ability-to-add-new-one></galleries-list-without-ability-to-add-new-one>
This describes the directive in detail but it is crazy specific and verbose. Be more general and flexible.

    Not recommended:

    <list></list>
List of what?

    Recommended:

    <gallery-list></gallery-list>
Basic purpose easily discernable, generic enough to be flexible with addition of attributes

Directives need not have controllers. If there is a controller for the directive, it is named and not an inline function.

    //not recommended
    controller: function (Dependency) { },
this can and will get messy

    //recommended
    controller: DirectiveNameController,
    controllerAs: directiveNameView,

Directives will almost always have templates. Every module that templates out HTML should have a directive at the root of the module's namespace ('whatever.directive.js'). For other directives in the module, the name of a directive file should match the directive and contain the module's namespace. A directive named 'thingList' in the 'whatever' module should be in a file called 'whatever.thingList.directive.js'. If there's an associated controller, it should be called 'WhateverThingListController'.

### Templates/Views ###
The main HTML file for a module should always be 'whatever.index.html'. Index is synonymous with root/main file, and the 'whatever' namespace indicates that it is just the main template for that piece of the app. This will also be the template file for the root directive of that module. Additional sub-components can have other names. For example, in the 'sample' module, the template for a 'thingList' directive in the 'whatever' module should probably be 'whatever.thingList.html'.

### Variables and Functions ###
Keep the names as short and descriptive as possible. A coder, understanding the context of where the variable or function appears, should be able to easily guess what a function does or what purpose a variable serves and what data it is likely to contain.

In general, functions should be named. Callback functions *may* require more than what gets passed along with, say, an event. In that case, an inline function is required, but it's purpose should be to invoke a named function with the extra data required by that named function.

#### Private functions/variables ####

Private variables that won't change are constants. Name them as such (eg: CONTSTANT_NAME).

*Don't* use _functionName notation to denote a private function. A private function is just one that isn't bound to "this" and therefore isn't exposed outside of the parent function. Binding a function to "this" happens *before* any function implementation details, so all the public functions should be readily visible up at the top of an object.

If you need to unit test a private function and simply testing the public functions that use the private function is insufficient, expose it through a "utils" object attached to "this":

    //self.utils = {
        privateFunctionOne: privateFunctionOne,
        privateFunctionTwo: privateFunctionTwo
    }
Remember that unit tests only have value if they test things that might actually happen. If you can't use a combination of state and inputs for public functions to generate bad inputs to a private function, your program isn't going to be able to do so either.

Don't put private variables in utils.

Services, Factories and Providers
---------------------------------

These things are all really the same: singletons that can be injected into controllers. There will only ever be one instance of each defined service, factory or provider in your app.

**Factory:** return a simple object that can be configured in the function. That object is shared amongst all controllers that use it, but individual functions that are returned can be instantiated ('newed') differently for different controllers.

**Service:** same simple object, but newable and instantiated. Internally, attach public methods and properties to "this" and leave private things as variables. Simple and easy. When in doubt, use this one. It's really the most straightforward of the three options.

**Provider:** The basis for both factory and service, but more complex than either. Essentially a configurable service. this.$get is a function that will run in the config stage of Angular, so if you're making a service that needs to be configured before the app runs this is the one to use.

### Service Best Practices ###
Attach 'this' to a variable named 'self' at the top to preserve the proper context of this.

    angular
      .module('sample')
      .service('Sample', Sample);
    
    function Sample() {
      var self = this;

Services are especially useful for acting as the data source for controllers. They can store data in private variables that multiple instances of controllers can ask for (as opposed to creating the data themselves) and act as a go-between for the controllers/view logic and the backend app by containing all the ajax request methods.

Controllers should not be expected to know whether or not the data is coming from stuff stored in the Service or stuff stored in the database and fetched via ajax. Always return a promise when returning data that might be fetched from a server.

Keep data private as much as possble. Use getter and setters. Always try to keep variables like lookups or data lists referring to the same object in memory. This means manipulating the existing list or object instead of just assigning a new list or object. If controllers rely on the references returned in getters for view logic, orphaning that reference will cause issues with the view if the data changes.

Controllers
-----------

The sole purpose of Controllers in Angular is to take data and organize it for templating out in the HTML. Controllers should be as skinny as possible and restricted to view logic.

Ajax requests should be relegated to a Service and the Service function can be invoked by the Controller.

DOM manipulation should be relegated to the link function in a Directive and the Controller can trigger an event if necessary that the Directive is listening for. Directives and controllers share the same $scope, so when doing this, just $scope.$broadcast your event in the controller. No need to $emit up the scope tree or $broadcast from $rootScope down the scope tree.

In your templates, you might need to use a lookup stored in a service to provide feedback to the user. Don't attach the Service to the view, just save a reference to the lookup in the view.

    //not recommended:
    sampleView.Service = Service;
This gives the view access to ALL of a Service's functions and properties, not just the one specific thing we need. Overkill, and potentially dangerous.

    //recommended:
    sampleView.serviceLookup = Service.getLookup();
Assuming Service.getLookup() returns that lookup, you have a reference to that lookup that should be mutable by the Service as long as your reference isn't orphaned. 

When listening to events in the Controller, *don't* listen on $rootScope. That listener will persist as long as $rootScope does. Listen on $scope, as that will only last as long as the Controller.

    //not recommended
    $rootScope.$on('whateverEvent' functionName);
This will keep a the controller around well after you expect it to be destroyed, and potentially leave you with multiple instances of the controller and multiple event callbacks running when there should only be one of each.

    //recommended
    $scope.$on('whateverEvent', functionName);
This listener will be destroyed with the Controller's $scope.

If you need to send events to other Controllers, use $rootScope.$broadcast. Angular (as of 1.3) is smart enough to only send that event places that you're listening for it instead of everywhere.


Directives
----------
Use them. Any time you have some repeated HTML associated with a component, consider a directive.

Use directives instead of ng-includes. If it's worth separating out into a different file, it's worth creating a directive for.

There are a three functions you can add to a directive: compile, controller and link.

*complile:* This happens first, useful for transforming the template itself. Usually not necessary. Will require that you return a link function if you want a link function.

*controller:* never write this inline. Use a named controller in a separate file and use controllerAs.

*link:* there are pre-link and post-link functions which will run before and after link (respectively), but link is the most important. This is where you add DOM events and manipulate the DOM.
