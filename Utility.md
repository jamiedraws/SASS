# Utility

Like with a component or a layout, a utility is a standalone entity; however, what makes a utility different from a layout or a component is that a *utility* aims to solve common problems that a component or a layout isn't responsible for.

The utility shares the same priviledge as the *abstract* in that it's composed of an array of small solutions; however, the utility also contains a namespace so that it can be used to create a composition from the HTML markup.

## Anatomy

The anatomy of a utility begins with the `$select` variable and the `base` mixin. From there, optional modifier mixins can be used to modify the base without compromising the original base rules.

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
| Access to utilities *different namespace* | No |
| Access to utilities *same namespace* | Yes |
| Access to abstracts | Yes |
| Access to components | No |
| Access to layouts | No |
| Access to interfaces | No |

## Base Mixin

Using the *block* solution, per BEM guidelines, the base utility represents the complete entity of the tool. In this example, we are creating a *flow-break* utility that contains a certain look and follows a specific HTML structure.

```html
<div class="flow-break">
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p>
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p>
    <ul class="flow-break">
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
    </ul>
</div>
```

```scss
$select: ".flow-break" !default;

@mixin base {
    #{$select} {
        > * + * {
            margin-top: 1rem;
        }
    }
}
```

## Modifier Mixin

Using the *modifier* solution, per BEM guidelines, we can extend our base utility to create a variant solution. This may be ideal when there is an opportunity to apply similar patterns and leverage the cascade to override certain style declarations.

In our *flow-break* example, we can introduce a variant to highlight a certain element group without having to redefine the base rules once again. Instead of redefining base rules, we can leverage the existing classes and the cascade to modify the base in the same namespace.

```scss
$select: ".flow-break" !default;

@mixin base {
    #{$select} {
        > * + * {
            margin-top: 1rem;
        }
    }
}

@mixin double {
    #{$select}--double {
        > * + * {
            margin-top: 2rem;
        }
    }
}
```
## Implementations

When defining a new utility, other lower-level modules such as *abstracts* may be implemented in order to provide reusable code that can contribute towards the new utility.

It's ill advised to introduce another utility because it violates the utility's single responsibility. It's important to remember that each utility is it's own entity and is only responsible for itself.

Instead of introducing another utility, consider extracting the code from that utility into a new abstract and implementing that abstract into both utilities.

```scss
$select: ".flow-break" !default;
$space: 1rem !default;

// abstracts
@use "../abstracts/space";

@mixin base {
    #{$select} {
        @include space.between-flow-elements(#{$space});
    }
}

@mixin double {
    #{$select}--double {
        @include space.between-flow-elements(calc(#{$space} * 2));
    }
}
```
## Cascading & Inheritance

The one exception to introducing another utility is when we want to introduce a utility with the same namespace. We might want to do this because we have a need to modify the rules of a utility without compromising the origin utility. This leads to the concept of a cascade utility.

### Using the Origin Utility to Inherit and Cascade Existing Members

On one hand, cascading is ideal when you need to modify the base rules on the same mixin namespace as the origin utility. Use the origin utility and bind the cascade `$select` variable to the origin `$select` variable so that they represent the same namespace.

From there, the idea is to overwrite the implementation details of the base mixin to further enhance the functionality of the cascade utility. In this example, we can inherit the implementation details of the origin utility's base mixin and then build new implementation details on to it.

```scss
$select: ".flow-break" !default;
$gap: 2rem !default;

// components
@use "Shared/css/template/base/utils/flow-break" with ($select: $select, $gap: $gap);

@mixin base {
    // notice that we inherit the base mixin within the new cascade base mixin. This allows us to cascade on the origin base.
    @include flow-break.base;

    #{$select} {
        @content;
    }
}
```

### Forwarding the Origin Utility to Inherit all Origin Members and Cascade Members
On the other hand, inheritance is ideal when you only need to modify the base rules using a modifier mixin. This approach is no different than if you were to define a base mixin on the same partial file. When the utility is applied to an interface, all of the inherited members can be accessed along with the new *triple* member.

```scss
$select: ".flow-break" !default;
$gap: 1rem !default;

// utils
@forward "Shared/css/template/base/utils/flow-break" with ($select: $select, $gap: $gap);

// abstracts
@use "Shared/css/template/base/abstracts/space";

// triple is a new mixin member that isn't defined in the origin utility
@mixin triple {
    #{$select}--triple {
        #{$select} {
            @include space.between-flow-elements(calc(#{$select} * 3));
        }

        @content;
    }
}
```

## Applications

### Example Using an Interface

Using a *app* interface, we can use the *flow-break* component and include the mixins that we need. For this app, we can use all of the available members to provide an assortment of utility classes.
```scss
// utils
@use "../utils/flow-break";

@mixin shell {
    @include flow-break.base;
    @include flow-break.double;
    @include flow-break.triple;
}
```

## Violations

### Example of Multiple Namespaces

In this example, we are violating the *flow-break* utility by introducing a different class name. This can lead to poor management of utilities and higher complexities as well. 

```scss
$select: ".flow-break" !default;

@mixin base {
    #{$select} {
        // .flow-break should be not responsible to deal with .btn
        .btn {

        }
    }
}
```

### Example of Mutiple *Element* Classes

In this example, we are violating the *flow-break* utility by introducing *Element* classes into the implementation. 

```scss
$select: ".flow-break" !default;

@mixin base {
    #{$select} {
        // .flow-break should be not responsible to deal with *element* classes
        &__btn {
            
        }
    }
}
```
The problem is adding *element* classes signals that this module is behaving more like a component or a layout and less like a utility. Instead of introducing invasive *element* classes, decide if the role belongs to a component or a layout and use *abstracts* to create the composition.

```scss
$select: ".text-btn" !default;

// abstracts
@use "../abstracts/space";

@mixin base {
    #{$select} {
        @include space.between-flow-elements();

        &__btn {
            display: inline-block;
            padding: 0.5em 1.5em;

            text-align: center;
            background: red;
            color: white;
        }
    }
}
```