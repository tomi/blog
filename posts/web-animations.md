# How to animate elements on the web

Animation means changing an element's property or properties gradually from one state to another. For example, instead of an element jumping from point A to point B, doing it with an animation means changing it's position little by little so that it appears to be sliding from A to B.

Animations can make a site more appealing by adding some flair, or make it more usable by drawing attention to some detail or conveying progress. However, animations should be used properly or they can frustrate and distance users.

There are multiple technical ways to do to animations. These include CSS, JavaScript, SVGs, canvas and WebGL. In this article we'll focus on CSS animations.

## Animating with CSS

Out of all the possible ways to do animations on the web, CSS is the simplest one as you don't have to write any JS code. CSS animations tend to be also more performant, as the browser can employ techniques like hardware acceleration and frame-skipping to keep the animation smooth. The only prerequisite for CSS animations is that the element needs to exist in the DOM.

There are two ways to animate elements with CSS: transitions and animations.

## CSS transitions

CSS transitions allow you to control how fast an element's CSS property (or properties) changes from A to B. For example, changing an elements opacity from 0.0 to 1.0 happens instantaneously by default, but with a CSS transition it can be configured to happen with a duration of 500 ms, so that the element appears to "fade in".

Not all CSS properties can be transitioned. See [this page on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties) for a complete list.

A transition can be configured by defining the following sub-properties:

- [`transition-property`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-property): Defines the CSS properties to which the transition applies to.
- [`transition-duration`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-duration): Defines the duration over which the transition occurs.
- [`transition-timing-function`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function): Defines how the intermediatery values between the start and end value are calculated. See [the list of available functions](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function).
- [`transition-delay`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-delay): Defines the delay to wait before the transition begins.

These sub-properties can be defined individually or together with the [`transition`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition) property with the following syntax:

```css
transition: <property> <duration> <timing-function> <delay>;
```

### CSS transition example

```html
<div class="trigger">Hover me to show the box</div>

<div class="box">This element fades in and out</div>
```

```css
.box {
  background-color: red;
  color: black;
  padding: 1rem;
  display: inline-block;
  opacity: 0;
  transition: opacity 500ms cubic-bezier(0.4, 0, 0.2, 1);
}

.trigger:hover + .box {
  opacity: 1;
}
```

## CSS animations

CSS animations are a bit more powerful and therefore a bit more complex than CSS transitions. They allow you to define how an element transitions from start state to an end state, with possible intermediate states.

Not all CSS properties can be transitioned. See [this page on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties) for a complete list. This is the same list that is valid for CSS transitions.

CSS animations consist of two parts:

1. Using the [`animation`](https://developer.mozilla.org/en-US/docs/Web/CSS/animation) property to configure the timing and other details that define how the animation should progress.
2. Using the [`@keyframes`](https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes) at-rule to configure how the element appears in different stages of the animation.

## Defining the animation timing

The most important `animation` sub-properties are the following:

- [`animation-name`](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-name): Defines which `@keyframe` describes this animation's states.
- [`animation-duration`](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-duration): Defines the duration in which the animation completes one cycle.
- [`animation-timing-function`](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timing-function): Defines the timings how animation transitions between keyframes. See [the list of available functions](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function).

See [`animation`](https://developer.mozilla.org/en-US/docs/Web/CSS/animation) for full list of sub-properties.

## Defining the animation sequence

How the animation looks like is defined using one or more `@keyframes` [at-rules](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule). Each frame defines how the animated element is rendered at a given point in time. The point in time is defined using percentage, since it's duration is defined separately with the `animation-duration` sub-property. The beginning and end also have special aliases `from` (`0%`) and `to` (`100%`)

Formal syntax:

```css
@keyframes <keyframes-name> { <rule-list> }
```

### CSS animation example

```html
<div class="nudge" />
```

```css
.nudge {
  animation-name: nudge;
  animation-duration: 1s;
  animation-timing-function: ease;
  animation-duration: infinite;
}

@keyframes nudge {
  30% {
    transform: scale(1.2);
  }
  40%,
  60% {
    transform: rotate(-20deg) scale(1.2);
  }
  50% {
    transform: rotate(20deg) scale(1.2);
  }
  70% {
    transform: rotate(0deg) scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}
```
