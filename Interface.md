# Interface

An interface describes a user-interface that serves a single purpose through the mixin directive. 

## Blueprint

A blueprint of a page interface illustrates what a user-interface could look like for a home page, a contact page and a checkout page.


## Interface Mixin

An interface mixin can provide a collection of rules that create a user-interface.

```scss
@mixin home-page {
    @content;
}

@mixin contact-page {
    @content;
}

@mixin checkout-page {
    @content;
}
```


## Base Mixin

A base mixin can provide common rules that can be applicable to other interface mixins.


```scss
@mixin global {
    @content;
}

@mixin home-page {
    @include global();
    
    @content;
}

@mixin contact-page {
    @include global();

    @content;
}

@mixin checkout-page {
    @include global();

    @content;
}
```

## Applications

An abstract, config, component, layout and an interface can all be applied to an interface.

```scss
@use "../abstracts/copy";
@use "../abstracts/group";

@use "../components/button";
@use "../components/list";

@use "../layouts/hero";

@use "../interfaces/header";
@use "../interfaces/form";

@use "../config/queries";

@mixin global {
    @media all and (min-width: queries.get(desktop)) {
        @include header.header-navbar();
    }

    @media all and (max-width: queries.get(desktop)) {
        @include header.header-navdrawer();
    }

    @include button.base();
    @include list.bullet();

    @include copy.base();

    @content;
}

@mixin home-page {
    @include global {
        @include hero.image-text();

        @content;
    }
}

@mixin contact-page {
    @include global {
        @include form.contact();

        @content;
    }
}

@mixin checkout-page {
    @include global {
        @include form.checkout();
        @include group.base();

        @content;
    }
}
```