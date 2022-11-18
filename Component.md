# Components

A component aims to address specific content in a smaller area of space. It is an entity of it's own and is responsible for it's own members. What this means is that a component is neither responsible for any other namespaces, or classes, that are outside or inside that component.

## Anatomy

The anatomy of a componet begins with the `$select` variable and the `base` mixin. From there, optional modifier mixins can be used to modify the base without compromising the original base rules.

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
| Access to utilities | No |
| Access to abstracts | Yes |
| Access to components - *different namespace* | No |
| Access to components - *same namespace* | Yes |
| Access to layouts | No |
| Access to interfaces | No |

## Base Mixin

Using the *block* and *element* solution, per BEM guidelines, the base component represents the complete entity of the user-interface component. In this example, we are creating a *card-picture-caption* component that contains a certain look and follows a specific HTML structure.

```html
<figure class="card-picture-caption">
    <div class="card-picture-caption__picture">
        <img src="images/picture.jpg" alt="A view of the sunset on a beach" width="300" height="200">
    </div>
    <figcaption class="card-picture-caption__caption">This was taken in the summer of 2019</figcaption>
    <button>Zoom Picture</button>
</figure>
```

```scss
$select: ".card-picture-caption" !default;

@mixin base {
    #{$select} {
        border-radius: 0.5em;
        overflow: hidden;

        &__group {
            display: inline-flex;
            align-items: center;
            justify-content: space-around;

            > * {
                flex: 0 1 auto;
                margin: 1rem;
            }
        }

        &__picture {
            flex-basis: 20em;
        }

        &__caption {
            flex-basis: 25em;
            flex-grow: 1;
        }

        button {
            padding: 0.5em;
            border: 0.1em solid;

            color: white;
            background: black;
        }

        @content;
    }
}
```


## Modifier Mixin

Using the *modifier* solution, per BEM guidelines, we can extend our base component to create a variant solution. This may be ideal when there is an opportunity to apply similar patterns and leverage the cascade to override certain style declarations.

In our *card-picture-caption* example, we can introduce a variant to highlight a certain element group without having to redefine the base rules once again. Instead of redefining base rules, we can leverage the existing classes and the cascade to modify the base in the same namespace.

This creates a new scope in our component where any modifications will not compromise the original base rules.

```scss
@mixin highlight {
    #{$select} {
        &--highlight {
            max-width: 20em;

            #{$select} {
                &__group {
                    flex-direction: column;
                }

                &__picture {
                    flex-basis: auto;
                }

                &__caption {
                    flex-basis: auto;
                }
            }

            button {
                color: black;
                background: white;
            }
        }

        @content;
    }
}
```

## Implementations

When defining a new component, other lower-level modules such as *abstracts* may be implemented in order to provide reusable code that can contribute towards the new component.

It's ill advised to introduce another component because it violates the component's single responsibility. It's important to remember that each component is it's own entity and is only responsible for its own members.

Instead of introducing another component, consider extracting the code from that component into a new abstract and implementing that abstract into both components.

```scss
$select: ".card-picture-caption" !default;

// config 
@use "../config/colors";

// abstracts
@use "../abstracts/group";
@use "../abstracts/copy";

@mixin base {
    #{$select} {
        border-radius: 0.5em;
        overflow: hidden;

        &__group {
            // notice how we're substituting the flexbox rules for the group abstract's contain mixin which outputs the flexbox styles along with initial flex rules for it's immediate children and margin gap.
            @include group.contain;
        }

        &__picture {
            flex-basis: 20em;
        }

        &__caption {
            flex-basis: 25em;
            flex-grow: 1;

            @include copy.separate;
        }

        button {
            padding: 0.5em;
            border: 0.1em solid;

            color: colors.get(base-light);
            background: colors.get(primary);
        }

        @content;
    }
}

```
## Cascading & Inheritance

The one exception to introducing another component is when we want to introduce a component with the same namespace. We might want to do this because we have a need to modify the rules of a component without compromising the origin component. This leads to the concept of a cascade component.

### Using the Origin Component to Inherit and Cascade Existing Members

On one hand, cascading is ideal when you need to modify the base rules on the same mixin namespace as the origin component. Use the origin component and bind the cascade `$select` variable to the origin `$select` variable so that they represent the same namespace.

From there, the idea is to overwrite the implementation details of the base mixin to further enhance the functionality of the cascade component. In this example, we can inherit the implementation details of the origin component's base mixin and then build new implementation details on to it.

```scss
$select: ".card-picture-caption" !default;

// components
@use "Shared/css/template/base/components/card-picture-caption" with ($select: $select);

@mixin base {
    // notice that we inherit the base mixin within the new cascade base mixin. This allows us to cascade on the origin base.
    @include card-picture-caption.base;

    #{$select} {
        &__picture {
            flex-basis: auto;
        }
    }
}
```

### Forwarding the Origin Component to Inherit all Origin Members and Cascade Members
On the other hand, inheritance is ideal when you only need to modify the base rules using a modifier mixin. This approach is no different than if you were to define a base mixin on the same partial file. When the component is applied to an interface, all of the inherited members can be accessed along with the new *hero* member.

```scss
$select: ".card-picture-caption" !default;

// components
@forward "Shared/css/template/base/components/card-picture-caption" with ($select: $select);

// hero is a new mixin member that isn't defined in the origin component
@mixin hero {
    #{$select}--hero {
        #{$select} {
            &__picture {
                width: 100%;
            }
        }

        @content;
    }
}
```

## Applications

### Example Using an Interface

Using a *page* interface, we can use the *card-picture-caption* component and include the mixins that we need. For the index page, we only need to use the base styles and for the gallery page, we need both the base styles and the highlight styles.
```scss
// components
@use "../components/card-picture-caption";

@mixin index {
    @include card-picture-caption.base;
    @include card-picture-caption.hero;
}

@mixin gallery {
    @include card-picture-caption.base;
    @include card-picture-caption.highlight;
}
```

## Violations

### Example of Multiple Namespaces

In this example, we are violating the *card-picture-caption* component by introducing a different class name. This can lead to poor management of components and higher complexities as well. 


```scss
$select: ".card-picture-caption" !default;

@mixin base {
    #{$select} {
        &__picture {

        }

        &__caption {

        }

        // .card-picture-caption should be not responsible to deal with .btn
        .btn {

        }
    }
}
```
Instead of creating an invasive dependency, create a new member with a similar name like `.btn`. This will further define the component while simplyfing the specificity value.

```scss
@mixin base {
    #{$select} {
        &__picture {

        }

        &__caption {

        }

        // instead of .btn, create a new member under the same namespace. This will liberate .card-picture-caption from becoming more complex and reduce the specificity value.
        &__btn {

        }
    }
}
```