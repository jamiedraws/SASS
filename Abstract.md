# Abstract

While a layout aims to address large areas of generic content and a component aims to address smaller areas of specific content, an abstract aims to address solutions and patterns that are often found in both a component and a layout.

Unlike a traditional utility, an abstract *can* assume the role of a utility using its own namespace; but moreso, an abstract can assume the role of an *element* under the namespace of a component or a layout.

## Blueprint

A blueprint of a *copy* abstract can demonstrate it's ability to apply margin space to every element that follows right after another element.


## Base Mixin

A base mixin can define a utility class that can perform the main task.

```scss
$select: ".copy" !default;

@mixin base {
    #{$select} {
        @include separate();

        @content;
    }
}
```


## Utility Mixin

A utility mixin can provide direct access to the functionality that can be accessible from a base mixin.


```scss
@mixin separate($gap: 1rem) {
    > * + * {
        margin-top: #{$gap};
    }

    @content;
}
```

## Implementations

When defining a new abstract, other abstracts *may* be implemented in order to provide reusable code to complement the new abstract. 

It's important to remember that an abstract can assume the role of a utility and contribute to a higher-level module, such as a component or a layout. It's role is not to assume the role of a component or a layout.  

Therefore, if the implementation details begin to build and become an entity, consider using a component or a layout instead of an abstract. There might even be an opportunity to extract patterns into abstracts in this case.

```scss
$select: ".abstract-solution" !default;

// config
@use "../config/fonts";

// abstracts
@use "abstract-helper";

@mixin base {
    @include abstract-helper.base;

    #{$select} {
        @content;
    }
}
```


## Applications

### Example Using a Component

In this example, we can delegate multiple abstract solutions to help us build a new *card* component where we might want to group elements and separate bodies of text.

A decision to introduce abstracts into a component may be necessary when you see an opportunity to reuse a code solution in order to simplify the components readability.
```scss
$select: ".card" !default;

// abstracts
@use "../abstracts/group";
@use "../abstracts/copy";

@mixin base {
    #{$select} {
        &__group {
            @include group.contain;
        }

        &__copy {
            @include copy.separate;
        }
    }
}
```

### Example Using a Layout

An abstract can also be implemented into a new *page* layout. While the code structure looks identical to the card component at first, this also allows the page layout to tackle more generic and larger areas of content while the component can focus in on a more specific and smaller area of content.

```scss
$select: ".page" !default;

// abstracts
@use "../abstracts/group";
@use "../abstracts/copy";

@mixin base {
    #{$select} {
        &__group {
            @include group.contain;
        }

        &__copy {
            @include copy.separate;
        }
    }
}


```

### Example Using an Interface

An interface may be ideal to define an abstract when the abstract might be required to operate in it's own namespace. This gives the abstract the same privilege as the component and the layout while they can fulfill their roles on different scopes.
```scss
// abstracts
@use "abstracts/icon-text";
@use "abstracts/logo-text";

@mixin index {
    @include icon-text.base;
}

@mixin all-pages {
    @include logo-text.base;
}

```