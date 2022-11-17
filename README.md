# Sass Style Guide

This style guide focuses on the *Mixin Module* design pattern and adopts the *BEM* design method.

## BEM Mixin Module *(BEM Double-M)*
This is a design pattern that operates primarily around the Sass mixin syntax and the module system. In Sass, [a mixin allows you to define styles that can be re-used throughout your stylesheet](https://sass-lang.com/documentation/at-rules/mixin). This enables developers to include a mixin either in the same Sass file or in another Sass file.

Combining this *At-Rule* with the Sass module system, a Sass partial can contain either a single or multiple mixin definitions and then be used in either another Sass partial or in a Sass file.

### Implementing Details in Modules
Let's take an example of a *module* Sass partial to learn how to construct a *Mixin Module*. With the Sass module system, variables and mixins can be considered as *public access modifiers* where the members can be access both inside the module and outside the module.

`_module-C.scss`
```scss
// public variable members
$select: ".module" !default;

// private module directives with ability to alias the namespace
@use "../references/module-A" as private-module;

// inherit module's public members
@forward "../references/module-B" as parent;

// public mixin members
@mixin base {
    #{$select} {
        @content;
    }
}

// public mixin modifier members
@mixin modifier {
    #{$select}--modifier {
        #{$select} {
            
        }

        @content;
    }
}
```
The concept is to think of mixins as independent scopes that have the priviledge of accessing other members inside the module. Using the *BEM* methodology, we can design our module to mold around *BEM*. This creates structure for our module and enables separation of our code through the mixin. 

The *base mixin* represents the *Block* from *BEM* and it's objective is to serve as a standalone entity that is meaningful on it's own. The *Element* from *BEM* can be defined inside the base in order to provide further structure to the module.

When it comes to the *Modifier* from *BEM*, instead of defining modifiers in the base mixin, we can delegate the modifier to it's own mixin. This creates the separation from the *Block* and *Modifier* and enables us to cherry pick how we want to customize our module in application. 

### Applying Module Abstractions in Interfaces

In one Sass partial, we implemented the details for a *module-C* and just as we are able to separate implementation details between the *Block* and the *Modifier*, we can use another Sass partial to handle the application of the *module-C* in an abstract way. This introduces the role of an interface.

`_interface-A.scss`
```scss
// private module directives with ability to alias the namespace
@use "../modules/module-C" as module;

// public mixin members
@mixin base {
    // access public memebers from the inherited module
    @include module.parent.base;
}

@mixin page {
    // access public members from module
    @include module.base;
    @include module.modifier;
}
```
The interface's objective is to *interface* with other modules and apply the mixins that contribute towards a single user-interface or multiple user-interfaces. 

Interfaces are agnostic of implementation details and are focused on the abstract details. To further clarify, take the `module.base` for example; this interface is not aware of what the code inside the `module.base` mixin looks like or is supposed to do. Those details are reserved inside the module itself.

Separating implementation from abstraction allows us to read *interfaces* as a document of commands that create a user-interface. When we want an overview perspective of the entire assembly line of modules without the details, we can rely on an interface.

### Interfacing through Stylesheets

At the end of the road lies the actual Sass stylesheet and just like with the *interface*, the *stylesheet* has a similar privledge. A stylesheet can use any interface and include any of it's public members.

`style.scss`
```scss
// interfaces
@use "interfaces/app";
@use "interfaces/page";

@include app.critical {
    @include page.base;
    @include page.index;
}
```
Similarly with the interface, the stylesheet provides an overview perspective of commands; however, while an interface is aware of which *utilities*, *components*, and *layouts*, are included, the stylesheet is only aware of which *intefaces* are included. That is the difference between a stylesheet and an interface.