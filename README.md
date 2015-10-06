angular-style-guide
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

Not recommended: 
    angular.module('core')
      .service('sample', sample);
Lowercase names don't impart the importance/singularity (for your singletons!) of a name that looks like a proper noun

Not recommended:
    angular.module('core')
      .service('SampleService', SampleService);
Don't be overly verbose. Services get injected into controllers all the time, whereas controllers stand alone. Don't complicate those injections with unneeded characters.

Recomended: 
    angular.module('core')
      .service('Sample', Sample);
This looks important, singular, and to the point.

All files in the module should be in the same folder in the filesystem and be named similarly:
  --sample/
    --sample.directive.js
    --sample.controller.js
    --sample.index.html
    --sample.module.js
    --sample.service.js

### Controllers ###
Controllers should have corresponding directives, and the name of the controller file should match the name of the directive file. 'whatever.directive.js' should have its controller in 'whatever.controller.js'. The name of the actual controller should match the filename. The actual controller in 'whatever.controller.js' should be 'whateverController'.

Controllers *do* have Controller in the name. ControllerAs syntax should replace Controller with View in the name:
  sampleController as sampleView

The 'whateverView' convention is preferred over 'whateverCtrl' or 'whateverController' for controllerAs because this is the variable name that will appear in the HTML template, which is the view in MVC. The controller itself can have more functionality than what is exposed in the view, and the 'whateverView' name makes it clear that everything saved on the controller object directly will be available in the view/template.

At the top of the controller, save a reference to 'this' as whateverView:
  var whateverView = this;

This keeps a reference to the controller (the proper value of 'this') available at all times (including event handlers) and keeps your variable names consistent. A reference to whateverView.property in the template/view will directly correspond to whateverView.property in the controller.

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

The name of a directive file should match the directive. A directive named 'sampleThingList' should be in a file called 'sampleThingList.directive.js'

### Templates/Views ###
The main HTML file for a module should always be whatever.index.html. Index is synonymous with root/main file, and the 'whatever' namespace indicates that it is just the main template for that piece of the app. Additional sub-components can have other names. For example, in the 'sample' module, the template for 'sampleThingList' directive should probably be 'sample.thingList.html'

### Variables and Functions ###
Keep the names as short and descriptive as possible. A coder, understanding the context of where the variable or function appears, should be able to easily guess what a function does or what purpose a variable serves and what data it is likely to contain.
