# Technical Paper on HTML and CSS

**Topic:** Core HTML and CSS Concepts for Building Responsive Web Pages

## Introduction

HTML and CSS are the basic technologies used to build web pages. HTML provides the structure and content, while CSS controls the design, layout and appearance. Together, they help create websites that are clear, attractive and usable on different devices.

## 1. CSS Box Model

Every HTML element is treated as a rectangular box. The box model has four parts:

- **Content:** The actual text, image or other content.
- **Padding:** Space between the content and the border.
- **Border:** The line surrounding the padding and content.
- **Margin:** Space outside the border, between elements.

A useful rule is `box-sizing: border-box;`. It makes the declared width and height include the padding and border, which makes sizing easier.

```css
* {
  box-sizing: border-box;
}
```

## 2. Inline versus Block Elements

HTML elements have different display behaviours.

- **Block elements** start on a new line and usually take the full available width. Examples: `<div>`, `<p>`, `<h1>`, `<section>` and `<header>`.
- **Inline elements** stay in the same line and take only the space they need. Examples: `<span>`, `<a>`, `<strong>` and `<em>`.

The CSS `display` property can change this behaviour. `inline-block` allows an element to stay in line while also accepting width and height.

## 3. CSS Positioning: Relative and Absolute

The `position` property controls where an element appears.

- **Relative:** The element stays in the normal page flow and can be moved from its original position using `top`, `right`, `bottom` or `left`.
- **Absolute:** The element is removed from the normal flow and positioned relative to its nearest positioned ancestor. If there is no such ancestor, it uses the initial containing block.

Example:

```css
.parent {
  position: relative;
}

.child {
  position: absolute;
  top: 10px;
  right: 10px;
}
```

Other useful values are `fixed`, which positions an element relative to the viewport, and `sticky`, which behaves normally until it reaches a defined scroll position.

## 4. Common CSS Structural Classes

Structural classes help arrange the main parts of a webpage. Their names are not built into CSS; developers create them as needed.

Common examples include:

- `.container` – keeps content within a suitable width and aligns it.
- `.header` – styles the top section of a page.
- `.navbar` – styles navigation links.
- `.main` – styles the main content area.
- `.sidebar` – creates a side content area.
- `.section` – separates content into sections.
- `.footer` – styles the bottom section.
- `.card` – groups related content inside a box.

Using meaningful class names makes HTML and CSS easier to understand and maintain.

## 5. Common CSS Styling Classes and Properties

Styling classes control the appearance of elements. Some commonly used names are:

- `.text-center` – aligns text to the centre.
- `.text-muted` – gives text a softer colour.
- `.bg-primary` – applies a primary background colour.
- `.btn` – styles a button.
- `.rounded` – adds rounded corners.
- `.shadow` – adds a shadow.
- `.hidden` – hides an element, commonly with `display: none`.

These are naming conventions, not built-in CSS classes. Developers must define their styles.

Important CSS properties include `color`, `background-color`, `font-size`, `font-family`, `font-weight`, `text-align`, `border`, `border-radius`, `padding`, `margin` and `box-shadow`.

## 6. CSS Specificity

Specificity decides which CSS rule is applied when multiple rules target the same element. In general, the priority is:

1. Inline styles
2. ID selectors
3. Class, attribute and pseudo-class selectors
4. Element and pseudo-element selectors

For example, `#title` has higher specificity than `.title`, and `.title` has higher specificity than `h1`.

If two rules have the same specificity, the rule written later usually wins. The `!important` declaration can override normal declarations, but it should be avoided unless necessary because it makes CSS harder to manage.

## 7. CSS Responsive Queries

Responsive design makes a website adjust to different screen sizes, such as mobile phones, tablets and desktops.

Media queries apply styles only when a condition is met.

```css
.container {
  width: 90%;
  margin: auto;
}

@media (max-width: 600px) {
  .container {
    width: 100%;
  }
}
```

A mobile-first approach is useful: write the basic styles for smaller screens first, then add media queries for larger screens. Flexible widths, responsive images and relative units such as `%`, `rem` and `vw` also help.

## 8. Flexbox and CSS Grid

Flexbox and Grid are modern CSS layout systems.

**Flexbox** is mainly used for arranging items in one direction — a row or a column. It is useful for navigation bars, button groups and aligning items.

```css
.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
}
```

Important Flexbox properties include `flex-direction`, `justify-content`, `align-items`, `flex-wrap` and `gap`.

**CSS Grid** is used for layouts with rows and columns. It is useful for image galleries, dashboards and page layouts.

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
}
```

Important Grid properties include `grid-template-columns`, `grid-template-rows`, `gap`, `grid-column` and `grid-row`.

In simple terms, Flexbox is best for one-dimensional layouts, while Grid is best for two-dimensional layouts. They can also be used together.

## 9. Common HTML Header Meta Tags

The `<head>` section contains information about the webpage that is not normally displayed as page content.

Common tags include:

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="A short description of the webpage">
  <title>My Webpage</title>
  <link rel="stylesheet" href="style.css">
</head>
```

- `charset` sets the character encoding, usually UTF-8.
- `viewport` helps the page display correctly on mobile screens.
- `description` provides a short summary of the page, which may be used by search engines.
- `title` sets the page title shown in the browser tab.
- `link` connects external resources such as CSS files.

## 10. Additional Topic: Semantic HTML and Accessibility

Semantic HTML means using elements according to their meaning. For example, use `<header>` for the page header, `<nav>` for navigation, `<main>` for the main content, `<article>` for independent content and `<footer>` for the footer.

Semantic elements make a page easier to understand for browsers, developers and assistive technologies. For better accessibility, use meaningful headings, labels for form fields, alternative text for useful images, keyboard-friendly controls and sufficient colour contrast.

## 11. Additional Topic: CSS Variables and Reusable Styles

CSS variables allow us to store values and reuse them throughout a stylesheet. They are useful for keeping colours, spacing and other design values consistent.

```css
:root {
  --primary-color: #2563eb;
  --main-spacing: 16px;
}

button {
  background-color: var(--primary-color);
  padding: var(--main-spacing);
}
```

If a shared value needs to change, we can update it in one place instead of editing many rules. Reusable classes and variables make stylesheets easier to maintain.

## Conclusion

HTML gives a webpage its structure, and CSS controls how that structure looks and behaves. Understanding the box model, display types, positioning, specificity, responsive design, Flexbox and Grid is important for building well-organised websites. Semantic HTML and accessibility make websites easier for everyone to use. By practising these concepts, we can create pages that work well on different devices and are easier to maintain.

## References

1. MDN Web Docs – [CSS Box Model](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Box_model)
2. MDN Web Docs – [The Box Model, Block and Inline Elements](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics/Box_model)
3. MDN Web Docs – [CSS Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/position)
4. MDN Web Docs – [CSS Specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)
5. MDN Web Docs – [CSS Media Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Media_queries)
6. MDN Web Docs – [Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout)
7. MDN Web Docs – [CSS Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
8. MDN Web Docs – [HTML `<meta>` Element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta)
9. MDN Web Docs – [HTML Semantics](https://developer.mozilla.org/en-US/docs/Glossary/Semantics#semantics_in_html)
10. MDN Web Docs – [CSS Custom Properties (Variables)](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Cascading_variables/Using_custom_properties)
