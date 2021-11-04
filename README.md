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
import { StyleSheet, css } from "aphrodite";
function App() {
  return <div className={css(styles.wrapper)}>Hello World!</div>;
}
const styles = StyleSheet.create({
  wrapper: {
    backgroundColor: "gray",
    minHeight: 200,
    "@media (min-width: 1025px)": {
      fontSize: "1.25rem",
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
import styles from "./App.module.css";
function App() {
  return <div className={styles.wrapper}>Hello World!</div>;
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

## Single-file components

Both Vue and Svelte offer a way to create scoped CSS in the same file as your component definition.

```html
// Hello.vue
<template>
  <p>Hello World</p>
</template>

<style scoped>
  p {
    background-color: gray;
    min-height: 200px;
  }
  @media (min-width: 1025px) {
    p {
      font-size: 1.25rem;
    }
  }
</style>
```

This solution reminds me a lot of CSS Modules, and the pros and cons are essentially the same: we can scope our CSS to our component and not have to worry about fallible naming methodologies, but it doesn't help us share data or state between JS and CSS. It feels a lot like writing vanilla CSS, for better or worse.

(Vue recently added support for [state-driven dynamic CSS](https://v3.vuejs.org/api/sfc-style.html#state-driven-dynamic-css), which uses CSS variables to pass data from JS to CSS, but I haven't personally tried it, so I can't vouch for its effectiveness.)

In my opinion, the biggest benefit compared to similar options is that your styles and markup are written in the same file. This is awesome, and requires way less jumping-around between files.

## styled-components

As we've already discussed, [styled-components](https://github.com/styled-components/styled-components) is a CSS-in-JS solution with a radical idea: every style is also a React component.

```jsx
import styled from "styled-components";

function App() {
  return <Wrapper>Hello World!</Wrapper>;
}
const Wrapper = styled.div`
  background-color: gray;
  min-height: 200px;

  @media (min-width: 1025px) {
    font-size: 1.25rem;
  }
`;
```

### Pros:

- Solves scoping and specificity in a magnificent way. This solution is tailor-made for component-driven architectures, and it fits like a glove.
- Feels like writing CSS (since you are just writing css!), with some of the quality-of-life improvements from Sass/Less rolled in.
- Offers good solutions for animations and global styles — you can write 100% of your CSS with this tool
- Extremely high developer satisfaction.
- Best-in-class performance: styled-components version 5 has gotten extremely performant, as we'll see in a future lesson. Plus it supports SSR?, which allows us to improve performance even more.
- The most popular CSS-in-JS solution out there, according to the # of Github projects that depend on it. Lots of active development, an engaged community, and plenty of resources.

### Cons:

- Requires a build system
- It's primarily a React tool (you can use styled-components with [Vue](https://github.com/styled-components/vue-styled-components) and possibly other frameworks, but those communities will be much smaller).
- Obfuscates the underlying markup tags, which can make it harder to get a sense of the HTML semantics at a glance.

## Emotion

[Emotion](https://emotion.sh/docs/introduction) emerged shortly after styled-components, featuring a very similar API, but with improved performance characteristics.

Over time, the performance difference has disappeared (both libraries are extremely fast), and Emotion has grown to be a bit broader in scope.

### Pros:

- Can be used without React (albeit with a different syntax)
- Slightly smaller bundle size compared with styled-components

### Cons:

- Not as popular or battle-tested as styled-components — according to Github in early 2021, ~100k projects depend on Emotion, while ~500k projects use styled-components.
- `emotion/styled` takes a different approach towards dynamic props which can be slightly less flexible.

Ultimately, Emotion is so similar to styled-components that it largely doesn't matter which you pick. They're both fantastic choices.

## Tailwind

[Tailwind](https://tailwindcss.com/) is a utility-first CSS framework.

"Utilities" in this context are short CSS classes that map to specific styles. Here's an example:

```html
<div class="relative z-10 max-w-screen-lg xl:max-w-screen-xl mx-auto">Hello World</div>
```

These classes apply the following CSS to the `div`:

```css
z-index: 10;
position: relative;
max-width: 1024px;
margin-left: auto;
margin-right: auto;
```

### Pros:

- Solves scoping and specificity
- Encourages good habits when it comes to following a design system — instead of specifying sizes in pixels, for example, they're specified in terms of size (lg, xl)
- Includes built-in design tokens (colors, sizes…), which can make it easier to come up with a nice design.
- Can be faster to write, once you get the hang of it
- Not React-specific
- Tailwind has exploded in popularity in 2020, and has an extremely passionate user base, and a ton of community resources.

### Cons:

- Relatively steep learning curve, compared to other tools
- Because not all CSS can be turned into utilities, you'll likely need a secondary system for writing more-traditional CSS
- Tailwind is the fastest-growing CSS solution right now, but its ecosystem is still quite a bit smaller than styled-components (according to Github dependent-project stats, at least)
- Adds a lot of "bulk" to your markup. In real-world scenarios, many many classes are required, and this can make it harder to read. For example:

```html
<div
  class="relative col-start-1 col-end-2 sm:col-start-2
  sm:col-end-3 lg:col-start-1 lg:col-span-full
  xl:col-start-2 xl:col-end-3 row-start-2 row-end-3
  xl:row-start-3 xl:row-end-4 self-center pr-8 sm:px-6
  md:px-8 pb-6 md:pb-8 lg:px-0 lg:pb-0 -mt-6 sm:-mt-10
  md:-mt-16 lg:-mt-32 xl:mt-0"
></div>
```

Tailwind tends to be very polarizing: developers love it or hate it.

For React developers, an interesting option has surfaced recently: [twin](https://github.com/ben-rogerson/twin.macro). With twin, you write Tailwind classes, but it gets transformed under-the-hood to use styled-components. This allows us to mix "raw" CSS in with our Tailwind classes, for cases when utilities don't exist. It also addresses some of the build complexities.

## In the end, it doesn't really matter

We're living in something of a "golden age" of CSS — we have so many awesome tools to pick from!

This course uses styled-components, but ultimately, it doesn't really matter which tool you use. The fundamentals are all the same. The skills you learn in this course will stick with you for your entire career, regardless which tool you use. In 5-10 years, we'll probably all be using some fancy CSS tool that doesn't even exist yet, and I feel confident the skills you learn in this course will still help you every day.

If I had to make one recommendation, it's that you pick an option that is scoped by default — using Vanilla CSS or Sass (without another tool like PostCSS) is still a viable path, but it adds a lot of mental overhead and unnecessary challenge. It takes all the fun out of writing CSS. Beyond that, though, they're all good choices. The tool matters less than how you use it.

As I mentioned in the last video, though, it's worth getting comfortable with styled-components even if you have no intention of switching to it as your primary tool. It'll help you in this course, and give you ideas you can take back to your tool of choice.

```
CSS-in-JS and haters

Some of the tools in this list are part of a category of tools known as “CSS-in JS”—styled-components, CSS modules, and Aphrodite are all examples of “CSS-in-JS” tools.

A tool is considered “CSS in JS” if the CSS is processed in some way by JavaScript at runtime before being applied.

The name “CSS-in-JS” has always bothered me—it's a misnomer! It makes it sound like we've come up with some pure-JavaScript way of doing styling. Nothing could be further from the truth: we're still writing 100% full-fat real-world CSS. JS is the delivery mechanism for our CSS, that's all.

Don't let anyone make you feel bad for choosing a CSS-in-JS tool. Most of the critics are curmudgeons who have never actually used these tools themselves.
```