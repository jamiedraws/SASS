# Interface

While layouts, components and abstracts provide implementation details, interfaces provide abstractions that can represent either portions or enterities of a user-interface.



## Blueprint

A blueprint of an interface begins with the relationship to the webpage. Deciding the relationship can then outline that blueprint through either a single mixin or multiple mixins.


## Interface Mixin

In this example, we are creating a *page* interface. This interface is responsible for providing abstractions for each of the page types that make up the whole website. `@content` directives are provided in order to indent our interface applications from the main stylesheet.

```scss
@mixin home {
    @content;
}

@mixin contact {
    @content;
}

@mixin checkout {
    @content;
}
```


## Inheriting Interface Mixins

In some situations, it is necessary to apply common abstractions across multiple interfaces. One way to accomplish this is to establish a *base* mixin and let it be responsible for providing the common abstraction rules.


```scss
// layouts
@use "../layouts/document";
@use "../layouts/home";
@use "../layouts/contact";
@use "../layouts/checkout";

@mixin base {
    @include document.base;

    @content;
}

@mixin home {
    @include home.base;
    
    @content;
}

@mixin contact {
    @include contact.base;

    @content;
}

@mixin checkout {
    @include checkout.base;

    @content;
}
```

In the stylesheet, this will allow us to apply the interface mixins in an understandable way.

```scss
// interfaces
@use "interfaces/page";

@include page.base {
    @include page.home;
    @include page.contact;
    @include page.checkout;
}
```

This stylesheet tells us that we are applying the base styles first, then we are applying the home-page styles, then the contact styles and finally the checkout styles.

If we're aiming to deliver multiple stylesheets to provide better caching strategies and or managing larger-scale web applications, we can simply re-assign the mixins to their respective stylesheets.

`app.scss`
```scss
// interfaces
@use "interfaces/page";

@include page.base;
```

`index.scss`
```scss
// interfaces
@use "interfaces/page";

@include page.home;
```

`contact.scss`
```scss
// interfaces
@use "interfaces/page";

@include page.contact;
```

`checkout.scss`
```scss
// interfaces
@use "interfaces/page";

@include page.checkout;
```