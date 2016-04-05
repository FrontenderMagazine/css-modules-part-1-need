<article id="post-239976" class="instapaper_body h-entry e-content">
I’ve been intrigued by CSS Modules lately. If you haven't heard of them, this
post is for you. We'll be looking at the project and it's goals and aims. If you
're intrigued, stay tuned, as the next post will be about how to get started 
using the idea. If you're looking to looking to implement or level up your usage,
part 3 will be about using them in a React environment.

#### Article Series

**Part 1: What are CSS Modules and why do we need them? *(You are here!)***  
Part 2: How to get started with CSS Modules *(Coming soon!)*  
Part 3: React + CSS Modules = 😍 *(Coming soon!)* 

### What are CSS Modules?

According to the [repo][1], CSS modules are:

> CSS files in which all class names and animation names are scoped locally by
> default.
>

So CSS Modules is not an *official spec* or an *implementation in the browser*
but rather a process in a build step (with the help of[Webpack][2] or 
[Browserify][3]) that changes class names and selectors to be scoped (i.e.
kinda like namespaced
). 

What does this look like and why do it? We'll get to that in a sec. First,
remember how HTML and CSS normally work. A class is applied in HTML:

    <h1 class="title">An example heading</h1>

And that class is styled in CSS:

    .title {
      background-color: red;
    }

As long as that CSS is applied to the HTML document, the background of that 
`<h1>` would be red. We don't need to process the CSS or the HTML. The
browser understands both those file formats.

CSS Modules takes a different approach. Instead of writing plain HTML, we need
to write all of our markup in a JavaScript file, like`index.js`. Here’s an
example of how that might work (we’ll take a look at a more realistic example 
later
):

    import styles from "./styles.css";
    
    element.innerHTML = 
      `<h1 class="${styles.title}">
         An example heading
       </h1>`;

During our build step, the compiler would search through that `styles.css` file
that we’ve imported, then look through the JavaScript we’ve written and make the
`.title` class accessible via `styles.title`. Our build step would then process
both these things into new, separate HTML and CSS files, with a new string of 
characters replacing both the HTML class and the CSS selector class.

Our generated HTML might look like this:

    <h1 class="_styles__title_309571057">
      An example heading
    </h1>

Our generated CSS might look like this:

    ._styles__title_309571057 {
      background-color: red;
    }<figure id="post-240197" class="align-none media-240197">

![][4]</figure>
The class attribute and selector `.title` is entirely gone, replaced by this
entirely new string; Our original CSS is not being served to the browser at all.

As [Hugo Giraduel said][5] in his tutorial on the subject: 

> [the classes] are dynamically generated, unique, and mapped to the correct
> styles.
>

This is what is meant by styles being scoped. They are scoped to particular
templates. If we have a`buttons.css` file we would import it only into a 
`buttons.js` template and a `.btn` class within would be inaccessible to some
other template (e.g.`forms.js`), unless we imported it specifically there too
.

Why would we want to mess with the CSS and HTML to do this? Why on earth would
we want to work this way?

### Why should we use CSS Modules?

With CSS Modules, it’s a guarantee that all the styles for a single component
:

1.  Live in one place
2.  Only apply to that component and nothing else

Plus, any component can have a true dependency, like:

    import buttons from "./buttons.css";
    import padding from "./padding.css";
    
    element.innerHTML = `<div class="${buttons.red} ${padding.large}">`;

**This approach is designed to fix the problem of the *global scope* in CSS.**

Have you ever been tempted by a lack of time or resources to simply write CSS
as quickly as possible, without considering what else you might affect?

Have you ever slapped some random bits and junk at the bottom of a stylesheet,
intending to get around to organizing it but never do?

Have you ever run across styles that you weren't entirely sure what they did or
if they were even being used?

Have you ever wondered if you could get rid of some styles without breaking
something? Wondered if the styles stood on their own or depended on other things?
Or overwrote styles elsewhere?

These are questions that can lead to big headaches, bloated project deadlines
and sad, wistful looks out of the window.

With CSS Modules, and the concept of **local scope by default**, this problem
is avoided. You're always forced to think about the consequences as you write 
styles.

For instance, if you use `random-gross-class` in HTML without applying it as a
CSS modules-style class, the style will not be applied, as the CSS selector will
be transformed to`._style_random-gross-class_0038089`.

### The \`composes\` keyword

Let’s say we have a module called `type.css` for our text styles. In that file
we might have the following:

    .serif-font {
      font-family: Georgia, serif;
    }
    
    .display {
      composes: serif-font;
      font-size: 30px;
      line-height: 35px;
    }

Wwe would declare one of those classes in our template like so:

    import type from "./type.css";
    
    element.innerHTML = 
      `<h1 class="${type.display}">
        This is a heading
      </h1>`;

This would result in markup like: 

    <h1 class="_type__display_0980340 _type__serif_404840">
      Heading title
    </h1>

Both classes have been bound to the element by the use of the `composes`
keyword, thus avoiding[some of the problems of similar solutions][6], like Sass
’ `@extend`. 

We can even compose from a specific class in a separate CSS file:

    .element {
      composes: dark-red from "./colors.css";
      font-size: 30px;
      line-height: 1.2;
    }

### BEM not required

We don’t need to use [BEM][7] when we’re making a CSS module. This is for two
reasons:

1.  **Easy parsing** - Code like `type.display` is just as legible for
    developers as the BEM-y
   `.font-size__serif--large`. Likely even easier to mentally parse when the
    BEM selectors get long.
   
2.  **Local scope** - Say we have a class like `.big` in one module where it
    changes the
   `font-size`. In another we use the *exact* same class `.big` that increases
   `padding` and `font-size` in a different amount. It simply doesn't matter!
    They won't conflict, because the styles are scoped very deliberately. Even if a 
    module imports both stylesheets, then it has a custom name which our build 
    process makes specifically for that class.
   
    
    In other words, **specificity issues disappear with CSS Modules.**

Cool, huh? 

These are only some of the benefits of writing CSS Modules. 

If you’re interested in learning more, [Glen Madden has written extensively][8]

The next article in this series will look at how to get a project up and
running with Webpack and CSS Modules. We’ll be using the latest ES2015 features 
to do this and looking at some example code to guide us through the process.

### Further reading

#### Article Series

**Part 1: What are CSS Modules and why do we need them? *(You are here!)***  
Part 2: How to get started with CSS Modules *(Coming soon!)*  
Part 3: React + CSS Modules = 😍 *(Coming soon!)* </article>

 [1]: https://github.com/css-modules/css-modules
 [2]: https://webpack.github.io/
 [3]: http://browserify.org/
 [4]: img/devtools.png%201200w
 [5]: http://www.sitepoint.com/understanding-css-modules-methodology/
 [6]: http://www.sitepoint.com/avoid-sass-extend/
 [7]: https://css-tricks.com/bem-101/
 [8]: http://glenmaddern.com/articles/css-modules