# Component

A component describes a single entity that can live within an layout and perform a single task.

## Blueprint

A blueprint of a card component can illustrate the type of card that contains a button element and then an element that represents a title, a picture and copy.


## Base Mixin

A base mixin can define common properties on a component that would be also be applicable to any modifiers.

```scss
$select: ".card" !default;

@mixin base {
    #{$select} {
        button {

        }

        &__title {

        }

        &__picture {

        }

        &__copy {

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
            button {

            }
        }

        #{$select} {
            &__title {

            }

            &__picture {

            }

            &__copy {

            }
        }

        @content;
    }
}
```

