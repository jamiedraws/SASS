# Sass Style Guide

## Goals
This style guide focuses on the *Mixin Module* design pattern and adopts the *B.E.M.* design methodology.

### Mixin Module
This is a design pattern that operates primarily around the Sass mixin syntax and the module system. In Sass, [a mixin allows you to define styles that can be re-used throughout your stylesheet](https://sass-lang.com/documentation/at-rules/mixin). This enables developers to include a mixin either in the same Sass file or in another Sass file.

Combining this *At-Rule* with the Sass module system, a Sass partial can contain either a single or multiple mixin definitions and then be used in either another Sass partial or in a Sass file.

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
The concept is to think of mixins as independent scopes that have the priviledge of accessing other members inside the module. Using the *B.E.M.* methodology, we can design our module to mold around *B.E.M.*. This creates structure for our module and enables separation of our code through the mixin. 

The *base mixin* represents the *Block* from *B.E.M.* and it's objective is to serve as a standalone entity that is meaningful on it's own. The *Element* from *B.E.M.* can be defined inside the base in order to provide further structure to the module.

When it comes to the *Modifier* from *B.E.M.*, instead of defining modifiers in the base mixin, we can delegate the modifier to it's own mixin. This creates the separation from the *Block* and *Modifier* and enables us to cherry pick how we want to customize our module in application. 

`_interface-A.scss`
```scss
// private module directives with ability to alias the namespace
@use "../modules/module-C" as module;

// public mixin members
@mixin main {
    // access public members from module
    @include module.base;
    @include module.modifier;

    // access public memebers from the inherited module
    @include module.parent.base;
}

```
