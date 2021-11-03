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
  $font-stack: Helvetica, sans-serif
  $primary-color: #333
  .wrapper {
    font-family: $font-stack
    h1 {
      font-size: 2rem
      color: $primary-color
    }
  }
```

I grouped [Less](http://lesscss.org/) into this category because it's quite similar. I've worked with both extensively, and the main difference I remember is that they use different syntax for variables: `@var: 10` vs `$var: 10`.

### Pros:

- Includes powerful tools like for-loops, mixins, and nesting
- Has really high developer satisfaction compared with vanilla CSS.

### Cons:

- Requires a build step (and it's pretty slow in development compared with hot-reloading)
- Because it compiles to CSS, it remains global by nature, and isn't scoped to specific components. It inherits a lot of "cons" from vanilla CSS, such as not automatically vendor-prefixing.
- Everything happens at build time, so it can't react to things in real-time. As we'll learn later on, Sass variables are nowhere near as powerful as CSS variables for this reason.
- Requires native dependencies that can fail or get out-of-date. I can't access my old Sass projects without spending hours dealing with native dependency issues. I can't just `npm install` — it's a real pain.
- CSS has added a bunch of new features over the years, and there have been naming clashes. CSS has a native `min` function now, and it's way better than Sass' `min` function, but the keyword is already reserved. There are workarounds, but it's added friction.
- It's becoming less and less popular in the JS scene. While popularity isn't everything, a fading solution will have less community resources, less support, and more potential for trouble.

## Aphrodite

[Aphrodite](https://github.com/Khan/aphrodite) was one of the first popular CSS-in-JS libraries. It was created and maintained by [Khan Academy](https://www.khanacademy.org/), an organization I used to work for!

Aphrodite brings a React-Native-like styling solution to the web. Styles are written in JS, and used by your React components:

```jsx
import { StyleSheet, css } from 'aphrodite';
function App() {
  return (
    <div className={css(styles.wrapper)}>
      Hello World!
    </div>
  );
}
const styles = StyleSheet.create({
  wrapper: {
    backgroundColor: 'gray',
    minHeight: 200,
    '@media (min-width: 1025px)': {
      fontSize: '1.25rem',
    },
  },
});
```

### Pros:

- Scoping and specification issues entirely disappear. This is the real game-changer, and the reason so many React applications use one CSS-in-JS solution or another.
- Allowed you to use JS within your CSS! This makes it easy to share state (eg. design tokens). It also allows for complex iteration and generation without the need to learn a new DSL (as you would with Sass/Less).
- Encourages good habits — no support for nesting, or other things that can land you in hot water.
- Automatically adds vendor prefixes, so you don't have to worry about it.
- Similar API to React Native makes it easy to move between projects, or possibly port components between them (though the CSS alternative on React Native is a bit different, so this isn't trivial).
- Having styles and JSX in the same file is awesome. No need to keep tabbing between files.

### Cons:

- Requires a build step
- High-friction. It's a lot of messing around, and it's so easy to forget the `css()` function call.
- Has a cost in terms of bundle size and runtime execution time, though it's relatively small.
- Styles must be written in JS. Property names are camelCased, values must be quoted. This can lead to surprising and frustrating differences, like needing to write `content: '""'` for pseudoelements (discussed later in this course).
- Writing global styles is a bit funky (requires [using an extension](https://github.com/Khan/aphrodite/issues/139#issuecomment-264033545) with some unintuitive syntax).
- It's not a very active project — no new releases in 2020. Possibly as a result, the project has been losing the mindshare game.

Aphrodite is a great tool: it gets the important parts right and solves the biggest problems we face. My biggest complaint is that it's a bit more high-friction than some of the other alternatives: I have less fun building with Aphrodite compared to styled-components. Though this is subjective.

## CSS Modules

[CSS Modules](https://github.com/css-modules/css-modules) is a tool that allows you to write vanilla CSS, and import it into a JS file.

“CSS Modules” is a name for a very specific third-party project, not a built-in CSS feature for modules.

```css
/* App.module.css */
.wrapper {
  background-color: gray;
  min-height: 200px;
}
@media (min-width: 1025px) {
  .wrapper {
    font-size: 1.25rem;
  }
}
```

```js
// App.js
import styles from './App.module.css';
function App() {
  return (
    <div className={styles.wrapper}>
      Hello World!
    </div>
  );
}
```

### Pros:

- Solves scoping and specificity! The `.wrapper` class you write will be globally-unique, even if a different CSS file has a `.wrapper` as well. If you were to `console.log` in the JS file, you'd see something like `.wrapper-abc123`.
- Feels like writing straight-up CSS, which can be nice and familiar
- Offers a `composes` feature, to extend existing CSS classes

### Cons:

- Doesn't really offer any modern convenience features, like autoprefixing
- Hard to share data between CSS and JS

CSS Modules are often mixed with [PostCSS](https://postcss.org/), a plugin ecosystem for extending CSS in beneficial ways (autoprefixing, transpiling, etc). This can smooth out the experience a bit, but in my opinion, it still doesn't feel near as nice in a modern JS context as some of the other options.
