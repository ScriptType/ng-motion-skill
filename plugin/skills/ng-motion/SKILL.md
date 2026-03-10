---
name: ng-motion
description: |
  Build Angular animations with the ng-motion library — an Angular wrapper around motion-dom providing Framer Motion-style declarative animations, spring physics, gestures, presence/exit animations, layout transitions, scroll-linked effects, drag-to-reorder, and imperative animation sequences. Use this skill whenever the user is working in the ng-motion codebase, wants to add animations to Angular components using ng-motion, asks about ng-motion APIs (directives, hooks, types), or references Framer Motion patterns in an Angular context. Also trigger when the user mentions ngmMotion, NgmPresenceDirective, useMotionValue, useSpring, useTransform, layout animations, whileHover, whileTap, or exit animations in Angular code.
---

# ng-motion — Angular Animation Library

ng-motion wraps `motion-dom` (the framework-agnostic engine from the Framer Motion ecosystem) in Angular directives, signals, and DI. It provides declarative animations, spring physics, gestures, presence/exit animations, layout transitions, scroll-linked effects, drag-to-reorder, and imperative animation control.

**Target**: Angular 21, standalone components, browser rendering.
**Package manager**: bun
**Build**: `cd workspace && npx ng build ng-motion`

## Architecture Overview

ng-motion delegates animation math and WAAPI rendering to `motion-dom` (^12.34.5) and `motion-utils` (^12.29.2). The Angular layer provides:
- Directives for declarative animation binding
- Injection-context hooks (Angular equivalents of React hooks)
- Angular DI for global configuration
- Angular outputs for motion event callbacks

## Core Directive: `NgmMotionDirective`

The center of the library. Add `ngmMotion` to any element, then bind animation state through directive inputs.

### Import Pattern
```ts
import { Component, signal } from '@angular/core';
import { NgmMotionDirective, type Variants, type Transition, type TargetAndTransition } from '@scripttype/ng-motion';

@Component({
  standalone: true,
  imports: [NgmMotionDirective],
  template: `
    <div
      ngmMotion
      [initial]="{ opacity: 0, y: 24 }"
      [animate]="{ opacity: 1, y: 0 }"
      [transition]="{ type: 'spring', stiffness: 280, damping: 24 }"
    >
      Animated content
    </div>
  `,
})
```

### Directive Inputs

| Input | Type | Purpose |
|-------|------|---------|
| `initial` | `Target \| VariantLabels \| boolean` | Starting state before first animation |
| `animate` | `AnimationDefinition` | Target state after mount or state change |
| `transition` | `Transition` | Timing, easing, springs, repeats |
| `variants` | `Variants` | Named animation states |
| `style` | `MotionStyle` | Inline style object, supports `MotionValue`s |
| `exit` | `TargetAndTransition \| VariantLabels` | Exit state — works with `@if`/`@for` automatically; also compatible with `*ngmPresence` |
| `whileHover` | `TargetAndTransition \| VariantLabels` | Hover gesture state |
| `whileTap` | `TargetAndTransition \| VariantLabels` | Press/tap gesture state |
| `whileFocus` | `TargetAndTransition \| VariantLabels` | Focus gesture state |
| `whileInView` | `TargetAndTransition \| VariantLabels` | In-viewport state |
| `whileDrag` | `TargetAndTransition \| VariantLabels` | Drag gesture state |
| `viewport` | `ViewportOptions` | IntersectionObserver config (`once`, `amount`, `margin`, `root`) |
| `drag` | `boolean \| 'x' \| 'y'` | Enable drag |
| `dragConstraints` | `Constraints` | `{ left, right, top, bottom }` bounds |
| `dragElastic` | `number` | Rubber-band elasticity (0-1) |
| `dragMomentum` | `boolean` | Inertial scrolling after release |
| `dragSnapToOrigin` | `boolean` | Spring back to start position |
| `dragTransition` | `InertiaOptions` | Post-drag transition |
| `dragDirectionLock` | `boolean` | Lock to dominant axis |
| `dragListener` | `boolean` | Enable/disable drag listening |
| `dragPropagation` | `boolean` | Allow drag event bubbling |
| `layout` | `boolean \| 'position' \| 'size' \| 'preserve-aspect'` | Enable layout animation |
| `layoutId` | `string` | Shared layout animation identifier |
| `layoutDependency` | `unknown` | Force layout re-measurement on change |
| `layoutScroll` | `boolean` | Account for scroll containers |
| `layoutRoot` | `boolean` | Projection root |
| `viewTransitionName` | `string` | CSS `view-transition-name` for route transitions |
| `globalTapTarget` | `boolean` | Listen for tap on window instead of element |
| `dragX` | `MotionValue<number>` | External MotionValue for drag x position |
| `dragY` | `MotionValue<number>` | External MotionValue for drag y position |

### Directive Outputs

| Output | When |
|--------|------|
| `animationStart` | Animation begins |
| `animationComplete` | Animation ends |
| `update` | Frame update during animation |
| `hoverStart`, `hoverEnd` | Hover lifecycle |
| `tap`, `tapStart`, `tapCancel` | Tap lifecycle |
| `viewportEnter`, `viewportLeave` | In-view lifecycle |
| `dragStart`, `dragMove`, `dragEnd` | Drag lifecycle |
| `directionLock` | Drag axis locked |
| `layoutAnimationStart`, `layoutAnimationComplete` | Layout animation lifecycle |

## Reactivity Model

Three reactive tools for different jobs:

1. **Angular `signal()` / `computed()`** — application state and declarative animation state
2. **`MotionValue`** — high-frequency animated values (drag position, springs, transforms, scroll progress)
3. **Angular outputs** — when Angular state needs to react to a motion event

**Key rule**: Directive inputs accept plain values. Angular reactivity comes from the template binding:
```ts
// Bind the current value of the signal, not the signal itself
[variants]="panelVariants()"
[transition]="panelTransition()"
[layoutDependency]="expanded()"
[animate]="open() ? 'open' : 'closed'"
```

## Variants and Staggered Lists

Variants enable named animation states. For staggered lists, each child should have its own `[animate]` binding driven by the signal, with manual delay via the loop index:

```ts
readonly itemVariants: Variants = {
  visible: { opacity: 1, y: 0 },
  hidden: { opacity: 0, y: 16 },
};
```

```html
@for (item of items; track item; let i = $index) {
  <div ngmMotion
    [animate]="visible() ? 'visible' : 'hidden'"
    [variants]="itemVariants"
    [transition]="{ type: 'spring', stiffness: 300, damping: 24, delay: i * 0.08 }"
  >
    {{ item }}
  </div>
}
```

For stagger effects, use `delay: i * 0.08` in the transition. Each child gets its own `[animate]` binding driven by the same signal.

## Exit Animations

`[exit]` works with `@if`, `@for`, and `@switch` automatically — no wrapper needed. When Angular removes an element with `[exit]`, the directive creates a clone and animates it out.

### @if + [exit] (default for show/hide)
```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    @if (visible()) {
      <article ngmMotion
        [initial]="{ opacity: 0, scale: 0.92 }"
        [animate]="{ opacity: 1, scale: 1 }"
        [exit]="{ opacity: 0, scale: 0.92 }"
        [transition]="{ type: 'spring', stiffness: 280, damping: 24 }"
      >Content</article>
    }
  `,
})
```

### @for + [exit] (default for lists)
```html
@for (item of items(); track item.id; let last = $last) {
  <div ngmMotion
    [initial]="{ opacity: 0, x: -16, height: 0, marginBottom: 0 }"
    [animate]="{ opacity: 1, x: 0, height: 44, marginBottom: last ? 0 : 8 }"
    [exit]="{ opacity: 0, x: 16, height: 0, marginBottom: 0 }"
    [transition]="{ type: 'spring', stiffness: 300, damping: 25 }"
    class="overflow-hidden"
  >{{ item.label }}</div>
}
```
Remove is just: `this.items.update(list => list.filter(i => i.id !== id));`

### Two Exit Modes

The properties in `[exit]` determine the clone strategy:

- **Visual exit** (no `height` in exit): Clone uses `position: fixed`. Animates `opacity`/`transform` on the **GPU compositor** — zero layout cost. Best for modals, panels, tooltips.
- **Collapsing exit** (`height` in exit): Clone stays in document flow with `overflow: hidden`. Surrounding items collapse smoothly. Causes layout reflow per frame — the expected cost for smooth list collapse. Best for list items, accordions.

**Performance tip**: Prefer `opacity` and `transform` in exits — they run on the GPU compositor and are essentially free. Only add `height` when surrounding items need to collapse smoothly.

### Fast-toggle behavior

If a user toggles show/hide while an exit is mid-flight, the library cancels the exit clone and reverses from the snapshot — producing a smooth reversal animation.

### *ngmPresence (advanced — opt-in)

Use `*ngmPresence` only when you need:
- `usePresence()` / `useIsPresent()` hooks for custom exit logic
- Coordinated multi-child exit sequencing
- The element to stay mounted during exit (canvas, WebGL animations)

```ts
import { NgmMotionDirective, NgmPresenceDirective } from '@scripttype/ng-motion';

@Component({
  imports: [NgmMotionDirective, NgmPresenceDirective],
  template: `
    <article *ngmPresence="visible()" ngmMotion
      [initial]="{ opacity: 0, scale: 0.92 }"
      [animate]="{ opacity: 1, scale: 1 }"
      [exit]="{ opacity: 0, scale: 0.92 }"
      [transition]="{ type: 'spring', stiffness: 280, damping: 24 }"
    >Content</article>
  `,
})
```

### Presence Hooks and Signals
- `useIsPresent()` — returns `Signal<boolean>` indicating enter/exit state
- `usePresence()` — returns `[Signal<boolean>, () => void]` for manual exit control
- `presenceChange` — readonly `Signal<boolean>` on `NgmMotionDirective` (replaces the old Observable)
- `usePresenceList(items, { getId })` — returns `{ visibleIds, visibleById, gapAfter }` signals for list layout metadata during exit animations

## Motion Values and Hooks

All hooks must run in an Angular injection context (field initializer, constructor, `runInInjectionContext()`).

### Core Hooks

| Hook | Returns | Purpose |
|------|---------|---------|
| `useMotionValue(initial)` | `MotionValue<T>` | Mutable animated value |
| `useSpring(source, opts?)` | `MotionValue<number>` | Spring-following value |
| `useTransform(...)` | `MotionValue<T>` | Derived value (range map or function) |
| `useVelocity(value)` | `MotionValue<number>` | Velocity tracker |
| `useTime()` | `MotionValue<number>` | Current animation-frame time |
| `useMotionTemplate` | `MotionValue<string>` | CSS string builder (tagged template) |
| `useMotionValueEvent(val, evt, cb)` | void | Subscribe with auto-cleanup |
| `useAnimationFrame(callback)` | void | Per-frame callback |
| `useCycle(...items)` | `[Signal, cycleFn]` | Rotate through values |
| `useReducedMotion()` | `Signal<boolean>` | OS accessibility preference |
| `useWillChange()` | `MotionValue<string>` | Manual will-change control |
| `useInView(target, opts?)` | `Signal<boolean>` | Viewport visibility as signal |
| `useScroll(opts?)` | `ScrollMotionValues` | Scroll position/progress |
| `useDragControls()` | `DragControls` | Programmatic drag start via `controls.start(event)` |

### Binding MotionValues to Styles
```ts
readonly x: MotionValue<number> = useMotionValue(0);
readonly opacity: MotionValue<number> = useTransform(this.x, [-200, 0, 200], [0.2, 1, 0.2]);
readonly motionStyle: MotionStyle = { x: this.x, opacity: this.opacity };
// Template: <div ngmMotion [style]="motionStyle">
```

### useTransform Variants
```ts
// Range mapping
useTransform(source, [0, 100], [0, 1]);
// Single-input function
useTransform(source, (v) => v * 2);
// Multi-input combine
useTransform([a, b], ([valA, valB]) => valA + valB);
```

### useMotionTemplate
```ts
readonly hue: MotionValue<number> = useMotionValue(200);
readonly background: MotionValue<string> = useMotionTemplate`hsl(${this.hue}, 70%, 50%)`;
```

## Scroll-Linked Animation

```ts
import { useScroll, useTransform, useSpring } from '@scripttype/ng-motion';

readonly scroll = useScroll(); // tracks document by default
readonly smoothProgress = useSpring(this.scroll.scrollYProgress, { stiffness: 200, damping: 30 });
readonly heroY = useTransform(this.scroll.scrollYProgress, [0, 1], [0, -180]);
```

`useScroll()` accepts `container` and `target` as `ElementRef<HTMLElement>` or `HTMLElement`, plus `offset` for scroll trigger points.

Returned values: `scrollX`, `scrollY`, `scrollXProgress`, `scrollYProgress`.

## Layout Animation

Enable with `[layout]="true"` on elements whose position/size changes due to DOM layout changes:

```html
<div ngmMotion [layout]="true" [layoutDependency]="expanded()" [transition]="spring">
```

### Shared Layout with layoutId
```html
@for (tab of tabs; track tab.id) {
  <button (click)="active.set(tab.id)">
    @if (active() === tab.id) {
      <div ngmMotion [layoutId]="'indicator'" [transition]="spring" class="indicator"></div>
    }
    {{ tab.label }}
  </button>
}
```

### Layout Group
Wrap with `ngmLayoutGroup` for shared layout across DOM branches:
```html
<div ngmLayoutGroup>
  <aside>...</aside>
  <main>...</main>
</div>
```

## Imperative Animation

### `animate()`
```ts
import { animate } from '@scripttype/ng-motion';

// MotionValue
animate(opacity, 1, { duration: 0.3 });
// DOM element/selector
animate('.card', { opacity: [0, 1], y: [24, 0] }, { duration: 0.5 });
// Sequence
animate([
  ['.badge', { scale: [1, 1.2, 1] }, { duration: 0.3 }],
  ['.label', { opacity: [0, 1] }, { at: '<', duration: 0.2 }],
]);
```

### `useAnimate()` — Scoped Animation
```ts
const [scope, animateInScope] = useAnimate<HTMLElement>();
// After render, set scope.current to the DOM element
// Then: await animateInScope(scope.current, { scale: 1.2 }, { duration: 0.2 }).finished;
```

## Global Configuration

```ts
import { provideMotionConfig } from '@scripttype/ng-motion';

export const appConfig: ApplicationConfig = {
  providers: [provideMotionConfig({ transition: { duration: 0.4 }, reducedMotion: 'user' })],
};
```

## Feature Loading

```ts
import { provideMotionFeatures, ngmAnimationFeatures, ngmAllFeatures } from '@scripttype/ng-motion';

export const appConfig: ApplicationConfig = {
  providers: [provideMotionFeatures(ngmAnimationFeatures)],
};
```

## Route Transitions

`withMotionTransitions()` wraps Angular Router's `withViewTransitions()` with animation presets:

```ts
import { withMotionTransitions } from '@scripttype/ng-motion';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes, withMotionTransitions()),           // default: fade
    provideRouter(routes, withMotionTransitions('slide-left')), // preset
    provideRouter(routes, withMotionTransitions({ duration: 300 })), // custom config
  ],
};
```

Presets: `'fade'`, `'slide-left'`, `'slide-right'`, `'slide-up'`, `'slide-down'`, `'scale'`.

Combine with `viewTransitionName` input on elements to control which parts participate:
```html
<div ngmMotion [viewTransitionName]="'hero-image'">Participates in route transition</div>
```

## Transition Types

```ts
// Spring (default for most animations)
{ type: 'spring', stiffness: 280, damping: 24 }
// Tween/duration
{ duration: 0.5, ease: 'easeOut' }
// With delay
{ delay: 0.2, duration: 0.4 }
// Keyframes with repeat
{ duration: 2, ease: 'easeInOut', repeat: Infinity, repeatDelay: 0.5 }
// Per-property transitions
{ opacity: { duration: 0.3 }, x: { type: 'spring', stiffness: 300 } }
```

## Reorder (Advanced/Pre-1.0)

```ts
import { NgmReorderGroupDirective, NgmReorderItemDirective } from '@scripttype/ng-motion';

// Template:
<div [ngmReorderGroup]="items()" [axis]="'y'" (reorder)="items.set($event)">
  @for (item of items(); track item.id) {
    <div [ngmReorderItem]="item" ngmMotion [layout]="true" [drag]="'y'" [dragSnapToOrigin]="true">
      {{ item.label }}
    </div>
  }
</div>
```

## Common Patterns

For detailed patterns with full examples, read `references/patterns.md`.

## Performance Rules

- Do NOT mirror every MotionValue change into Angular signals — keep hot paths in MotionValue land
- Use `[style]` with MotionValues for frame-driven visual state
- Only bridge to signals when Angular template needs to display the value
- For high-frequency readouts (FPS, clock), write directly to DOM via `nativeElement.textContent`

### Infinite Animations — CPU/GPU Pitfalls

Infinite WAAPI animations (`repeat: Infinity`) are the #1 cause of high CPU/RAM in ng-motion apps. Unlike CSS keyframes, the browser cannot throttle WAAPI animations when elements are off-screen — they burn CPU continuously.

**Use `[whileInView]` instead of `[animate]` for infinite decorative animations.** This pauses them when scrolled out of the viewport:

```html
<!-- BAD: burns CPU even when off-screen -->
<div ngmMotion
  [animate]="{ y: [-20, 20, -20] }"
  [transition]="{ duration: 12, repeat: Infinity, ease: 'easeInOut' }">
</div>

<!-- GOOD: pauses when scrolled away -->
<div ngmMotion
  [initial]="{ y: 0 }"
  [whileInView]="{ y: [-20, 20, -20] }"
  [transition]="{ duration: 12, repeat: Infinity, ease: 'easeInOut' }">
</div>
```

**Never-unmounted components are especially dangerous.** A `position: fixed` background component with infinite animations runs on every page, even when hidden behind opaque content. Gate heavy animated layers behind route checks or `@if` blocks so they only render when visible.

**`will-change: transform` is unnecessary** — WAAPI automatically promotes elements to GPU compositor layers when animating `transform`/`opacity`. The CSS spec mandates that animated properties behave as if included in `will-change`. Adding it manually is redundant and wastes GPU memory.

**`setTimeout` in components needs cleanup.** Track timer IDs and clear them via `DestroyRef.onDestroy()` to prevent orphaned timers writing to destroyed signals after navigation:

```ts
private readonly timers: ReturnType<typeof setTimeout>[] = [];
constructor() {
  inject(DestroyRef).onDestroy(() => this.timers.forEach(clearTimeout));
}
doSomething(): void {
  this.timers.push(setTimeout(() => this.signal.set(value), 500));
}
```

**Quick checklist for animation-heavy pages:**
1. Count infinite `repeat: Infinity` animations — each one is a continuous CPU cost
2. Convert decorative infinite animations to `[whileInView]`
3. Gate always-mounted animated components (backgrounds, overlays) by route
4. Remove manual `will-change` CSS — let the animation engine handle it
5. Clean up all `setTimeout`/`setInterval` calls on component destroy

## Testing Notes

- Signal inputs need AOT: `@analogjs/vite-plugin-angular` in vitest.config.ts
- TestBed init via shared `src/lib/test-env.ts` (import in each spec)
- Host component pattern for directive tests
- Dynamic updates: host uses `signal()`, then `detectChanges()` + `TestBed.flushEffects()`
- `PointerEventInit` doesn't include `pageX`/`pageY` — use `clientX`/`clientY` only
