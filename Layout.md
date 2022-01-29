# Layout

A layout aims to address a large array of elements in a large space. It shares the same anatomy and the same priviledges as the *component* and it shares the similar idea that it can stand alone as an entity. However; what makes a layout different from a component is that the *layout* can provide *slots* where *components* could be introduced into the user-interface.

## Anatomy

The anatomy of a layout begins with the `$select` variable and `base` mixin. From there, optional modifier mixins can be used to modify the base without compromising the original base rules.

```scss
$select: ".class-name" !default;

@mixin base {
    #{$select} {
        @content;
    }
}
```

## Priviledges

### Access Roles

| Properties | Values |
| ----------- | ----------- |
| Access to configs | Yes |
| Access to utilities | Yes |
| Access to abstracts | Yes |
| Access to components | No |
| Access to layouts - *different namespace* | No |
| Access to layouts - *same namespace* | Yes
| Access to interfaces | No |

## Base Mixin

Using the *block* and *element* solution, per BEM guidelines, the base layout represents the complete entity of the user-interface layout. In this example, we are creating a *content* layout that contains a certain look and follows a specific HTML structure.

```html
<section class="content">
    <div class="content__group">
        <header class="content__header">
            <h1 class="content__title">Section Title</h1>
        </header>

        <div class="content__figure">
            <figure class="card-picture-caption">
                <div class="card-picture-caption__picture">
                    <img src="images/picture.jpg" alt="A view of the sunset on a beach" width="300" height="200">
                </div>
                <figcaption class="card-picture-caption__caption">This was taken in the summer of 2019</figcaption>
                <button>Zoom Picture</button>
            </figure>
        </div>

        <footer class="content__footer content__text">
            <p>This is a place where a paragraph can go</p>
            <p>This is a place where a paragraph can go</p>
        </footer>
    </div>
</section>
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
            font-size: 3em;
        }

        &__group {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;

            > * {
                margin: 1rem;
            }
        }

        &__text {
            > * + * {
                margin-top: 1rem;
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

Using the *modifier* solution, per BEM guidelines, we can extend our base layout to create a variant solution. This may be ideal when there is an opportunity to apply similar patterns and leverage the cascade to override certain style declarations.

In our *content* example, we can introduce a variant to alter a layout without having to redefine the base rules once again. Instead of redefining base rules, we can leverage the existing classes and the cascade to modify the base in the same namespace.

This creates a new scope in our layout where any modifications will not compromise the original base rules.

```html
<section class="content content--figure-text">
    <div class="content__group">
        <div class="content__figure">
            <figure class="card-picture-caption">
                <div class="card-picture-caption__picture">
                    <img src="images/picture.jpg" alt="A view of the sunset on a beach" width="300" height="200">
                </div>
                <figcaption class="card-picture-caption__caption">This was taken in the summer of 2019</figcaption>
                <button>Zoom Picture</button>
            </figure>
        </div>

        <div class="content__text">
            <h2 class="content__title">Section Title</h2>

            <p>This is a place where a paragraph can go</p>
            <p>This is a place where a paragraph can go</p>
        </div>
    </div>
</section>
```

```scss
@mixin figure-text {
    #{$select} {
        &--figure-text {
            #{$select} {
                &__text {
                    flex-basis: 30em;
                }

                &__title {
                    font-size: 2em;
                }
            }

            @content;
        }
    }
}
```

## Implementations

When defining a new layout, other lower-level modules such as *abstracts* may be implemented in order to provide reusable code that can contribute towards the new layout.

It's ill advised to introduce another layout because it violates the layout's single responsibility. It's important to remember that each layout is it's own entity and is only responsible for its own members.

Instead of introducing another layout, consider extracting the code from that layout into a new abstract and implementing that abstract into both layouts.

```scss
$select: ".content" !default;

// config 
@use "../config/colors";

// abstracts
@use "../abstracts/group";
@use "../abstracts/copy";

@mixin base {
    #{$select} {
        &__header {
            flex-basis: 100%;
        }

        &__title {
            text-align: center;
            font-size: 3em;
        }

        &__group {
            // notice how we're substituting the flexbox rules for the group abstract's contain mixin which outputs the flexbox styles along with initial flex rules for it's immediate children and margin gap.
            @include group.contain;
        }

        &__text {
            @include copy.separate;
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

## Cascading & Inheritance

The one exception to introducing another layout is when we want to introduce a layout with the same namespace. We might want to do this because we have a need to modify the rules of a layout without compromising the origin layout. This leads to the concept of a cascade layout.

### Using the Origin Layout to Inherit and Cascade Existing Members

On one hand, cascading is ideal when you need to modify the base rules on the same mixin namespace as the origin layout. Use the origin layout and bind the cascade `$select` variable to the origin `$select` variable so that they represent the same namespace.

From there, the idea is to overwrite the implementation details of the base mixin to further enhance the functionality of the cascade layout. In this example, we can inherit the implementation details of the origin layout's base mixin and then build new implementation details on to it.

```scss
$select: ".content" !default;

// layouts
@use "Shared/css/template/base/layout/content" with ($select: $select);

@mixin base {
    // notice that we inherit the base mixin within the new cascade base mixin. This allows us to cascade on the origin base.
    @include content.base;

    #{$select} {
        &__figure {
            flex-basis: auto;
        }
    }
}
```
### Forwarding the Origin Layout to Inherit all Origin Members and Cascade Members
On the other hand, inheritance is ideal when you only need to modify the base rules using a modifier mixin. This approach is no different than if you were to define a base mixin on the same partial file. When the layout is applied to an interface, all of the inherited members can be accessed along with the new *sidebar* member.

```scss
$select: ".content" !default;

// layouts
@forward "Shared/css/template/base/layouts/content" with ($select: $select);

// sidebar is a new mixin member that isn't defined in the origin layout
@mixin sidebar {
    #{$select}--sidebar {
        #{$select} {
            &__group {
                flex-direction: column;
            }
        }

        @content;
    }
}
```

## Applications

### Example Using an Interface

Using a *page* interface, we can use the *content* layout and include the mixins that we need. For the *base* page, we need to use the base and sidebar styles and for the *index* page, we only need to use the *alternate* styles.

```scss
// layouts
@use "../layouts/content";

@mixin base {
    @include content.base;
    @include content.sidebar;
}

@mixin index {
    @include content.alternate;
}
```

## Violations

### Example of Multiple Namespaces

In this example, we are violating the *content* layout by introducing a different class name. This can lead to poor management of layouts and higher complexities as well. 


```scss
$select: ".content" !default;

@mixin base {
    #{$select} {
        &__header {

        }

        &__footer {

        }

        // .content should be not responsible to deal with .list
        .list {

        }
    }
}
```
Instead of creating an invasive dependency, create a new member with a similar name like `.list`. This will further define the layout while simplyfing the specificity value.

```scss
@mixin base {
    #{$select} {
        &__header {

        }

        &__footer {

        }

        // instead of .list, create a new member under the same namespace. This will liberate .content from becoming more complex and reduce the specificity value.
        &__list {

        }
    }
}
```

### Example 