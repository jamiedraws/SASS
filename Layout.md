# Layout

A layout aims to address a large array of elements in a large space. It shares similar priviledges with the *component* and it shares the similar idea that it can stand alone as an entity. However; what makes a layout different from a component is that the *layout* can provide *slots* where *components* could be introduced into the user-interface.

## Blueprint

The blueprint of a layout begins with the base mixin. From there, modifier mixins can be used to modify the base without compromising the original base rules.


## Base Mixin

Using the *block* and *element* solution, per BEM guidelines, the base layout represents the complete entity of the user-interface layout. In this example, we are creating a *content* layout that contains a certain look and follows a specific HTML structure.

```html
<section class="content">
    <div class="content__group">
        <header class="content__header">
            <h1 class="content__title">Section Title</h1>
        </header>

        <figure class="content__figure card-picture-caption">
            <div class="card-picture-caption__picture">
                <img src="images/picture.jpg" alt="A view of the sunset on a beach" width="300" height="200">
            </div>
            <figcaption class="card-picture-caption__caption">This was taken in the summer of 2019</figcaption>
            <button>Zoom Picture</button>
        </figure>

        <footer class="content__footer">
            Footer Info
        </footer>
    </div>

```

```scss
$select: ".content" !default;

@mixin base {
    #{$select} {
        &__header {
            flex-basis: 100%;
        }

        &__title {
            text-align: center;
        }

        &__group {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;

            > * {
                margin: 1rem;
            }
        }

        &__figure {
            flex-basis: 30em;
        }

        &__footer {
            flex-basis: 100%;
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