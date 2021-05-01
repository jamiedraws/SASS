# Layout

A layout describes a section of the user-interface that serves a single purpose.

## Blueprint

A blueprint of a header can illustrate what a header with a logo, a title and a button could look like.


## Base Mixin

A base mixin can define common properties on a component that would be also be applicable to any modifiers.

```scss
$select: ".header" !default;

@mixin base {
    #{$select} {
        img {

        }

        &__logos {

        }

        &__title {

        }

        &__button {

        }

        @content;
    }
}
```


## Modifier Mixin

A modifier mixin can be stacked on top of a base mixin to extend the component with distinct properties.


```scss
@mixin modifier {
    #{$select} {
        &--modifier {
            img {

            }
        }

        #{$select} {
            &__logos {

            }

            &__title {

            }

            &__button {

            }
        }

        @content;
    }
}
```

## Applications

An abstract, config, component and a layout can all be applied to a layout.

```scss
@use "../abstracts/copy";
@use "../abstracts/contain";
@use "../abstracts/group";
@use "../components/button";
@use "../config/colors";

@mixin base {
    #{$select} {
        @include colors.get(primary);

        img {
            @include contain.ratio(1/1);
        }

        &__logos {
            @include group.contain();
        }

        &__title {
            @include copy.separate();
        }

        &__button {
            @include button.base();
        }
    }

    @content;
}
```