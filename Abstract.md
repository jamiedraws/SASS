# Abstract

An abstract is an entity that performs a single task for a component or a layout.

## Blueprint

A blueprint of a copy abstract can demonstrate it's ability to apply margin space to every element that follows right after another element.


## Base Mixin

A base mixin can define a utility class that can perform the main task.

```scss
$select: ".copy" !default;

@mixin base {
    #{$select} {
        @include utility();

        @content;
    }
}
```


## Utility Mixin

A utility mixin can provide direct access to the functionality that can be accessible from a base mixin.


```scss
@mixin utility {
    > * + * {

    }

    @content;
}
```

## Applications

A config and an abstract can all be applied to an abstract.