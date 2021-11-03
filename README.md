# Ecosystem World Tour
### by Josh W. Comeau

See also: [Demystifying styled-components](https://www.joshwcomeau.com/react/demystifying-styled-components/)

When it comes to writing CSS in a modern JS application, there are a lot of options!
In this course, we'll use styled-components. I chose this option for a few reasons:

- It encourages good habits, and solves whole classes of CSS problems for us, like scoping and specificity.
- It's a very popular tool! styled-components + React is likely the most common combo when it comes to styling in modern JS applications.
- I have a lot of experience, both using it and teaching it.

In my own career, I've used many different CSS preprocessors and frameworks, and I considered many options for this course. In this optional lesson, we'll look at some of the other solutions out there, and talk about their pros and cons.

An important bit of context: these are my opinions. They're not “hot takes” — I've worked with each of the tools mentioned in this list, and my opinions are based on real-world experience — but they also aren't objective or universal. Lots of very smart and experienced developers will likely disagree with me, that's just the way it goes.

## Vanilla CSS

### Pros:

- No tooling means less complexity, no runtime performance costs
- Modern CSS features like CSS Custom Properties? make certain tooling features (eg. Sass variables) redundant

### Cons:

- Global and unscoped. The CSS you write in one place will be applied everywhere. Using a naming convention like BEM can help, but it's high-friction and prone to failure at scale: naming collisions are still possible, and it requires enormous dedication and willpower to stay 100% consistent with it.
- Requires that you remember to add vendor prefixes for many CSS properties, to ensure optimal browser support.
- Can't easily share data between CSS and JS.
- When it comes to modern JS application development, it feels a bit like a square peg being squeezed into a round hole. CSS is built for documents, not apps.

## Sass / Less

As we saw in this module's introduction, [Sass](https://sass-lang.com/) is a preprocessor that compiles to vanilla CSS. It extends CSS with features like nesting, variables, and iteration.

It's similar in operation to TypeScript: all valid CSS is also valid Sass, and Sass compiles to CSS at build-time, just like TypeScript compiles to JavaScript.

```Sass
  $font-stack: Helvetica, sans-serif;
  $primary-color: #333;
  .wrapper {
    font-family: $font-stack;
    h1 {
      font-size: 2rem;
      color: $primary-color;
    }
  }
```
