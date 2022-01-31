# Config

The *config* module provides specific configuration details that can be applied to either an *abstract*, a *utility*, a *component* or a *layout* module. It's objective is to ensure a consistent design system is in place.

## Anatomy

The anatomy of a config begins with the `$list` map variable and the `get` function. From there, optional variables and functions may be provided to further enhance the configuration.

```scss
@use "sass:map";

$list: (
    property: "value"
);

@function get($key) {
    @return map.get($list, $key);
}
```

## Priviledges

### Access Roles

| Properties | Values |
| ----------- | ----------- |
| Access to configs | Yes |
| Access to utilities | No |
| Access to abstracts | No |
| Access to components | No |
| Access to layouts | No |
| Access to interfaces | No |

## Getter Function

Using a getter function, we can access certain information that can be applied to an entire project. For example, let's consider creating a *colors* config module.

`_colors.scss`
```scss
@use "sass:map";

$list: (
    base: #333333,
    primary: red,
    secondary: blue
);

@function get($key) {
    @return map.get($list, $key);
}
```

## Applications

### Example Using an Abstract

In this example, we can provide config details over to an abstract that we can create for foreground & background color patterns.

`_color-pattern.scss`
```scss
// config
@use "../config/colors";

@mixin color-map($background-color: black, $text-color: white) {
    background: $background-color;
    color: $text-color;
}

@mixin primary-dark {
    @include color-map(colors.get(primary-dark), colors.get(primary-light));
}

@mixin primary-light {
    @include color-map(colors.get(primary-light), colors.get(primary-dark));
}
```

### Example Using a Utility

In this example, we can provide config details over to a utility that we can create to provide a set of generic color utility classes.

`_color.scss`
```scss
$select: ".color" !default;

// config
@use "../config/colors";

@mixin base {
    #{$select} {
        background-color: colors.get(base);
        color: currentColor;

        &--primary-dark {
            background-color: colors.get(primary-dark);
            color: colors.get(primary-light);
        }

        &--primary-light {
            background-color: colors.get(primary-light);
            color: colors.get(primary-dark);
        }
    }
}
```

### Example Using a Component

For our *card-picture-text* component, we can use the *colors* config to assign particular font and background colors to our component.

`_card-picture-text.scss`
```scss
$select: ".card-picture-text" !default;

// config
@use "../config/colors";

@mixin base {
    #{$select} {
        color: colors.get(primary-dark);

        &__picture {
            background-color: colors.get(primary-dark);
        }

        &__text {
            background-color: colors.get(primary-light);
        }
    }
}
```

### Example Using a Layout

In our *content* layout, we can also use the *colors* config to assign the *primary-dark* color to the header font and the *base* color to the text body.

`_content.scss`
```scss
$select: ".content" !default;

// config
@use "../config/colors";

@mixin base {
    #{$select} {
        &__header {
            color: colors.get(primary-dark);
        }

        &__text {
            color: colors.get(base);
        }
    }
}
```
