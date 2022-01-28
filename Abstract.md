# Abstract

While a layout aims to address large areas of complex content and a component aims to address smaller areas of specific content, an abstract aims to address solutions and patterns that are often found in both a component and a layout.

Unlike components and layouts, an abstract is not a standalone entity. Instead, an abstract is designed to create composition for either a component or a layout.

## Anatomy

The anatomy of an abstract begins with the `role` mixin. Replace `role` with the name that represents what the abstract code is aiming to accomplish.

```scss
@mixin role {
    @content;
}

```

## Priviledges

### Access Roles

| Properties | Values |
| ----------- | ----------- |
| Access to configs | No |
| Access to utilities | No |
| Access to abstracts | No |
| Access to components | No |
| Access to layouts | No |
| Access to interfaces | No |


## Implementations

In this example, we can create a *space* abstract that can provide composition tools for providing space between elements. We create a tool named `between-flow-elements` that takes an optional argument to specify the gap space that will apply between flow elements.

`_space.scss`
```scss
@mixin between-flow-elements($gap: 1rem) {
    > * + * {
        margin-top: #{$gap};
    }

    @content;
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
@use "../abstracts/space";

@mixin base {
    #{$select} {
        &__group {
            @include group.contain;
        }

        &__copy {
            @include space.between-flow-elements;
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