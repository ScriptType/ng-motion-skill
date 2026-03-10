# ng-motion Common Patterns

Patterns extracted from the ng-motion example app and documentation.

## Table of Contents
1. [Fade In on Mount](#fade-in-on-mount)
2. [Spring Physics with Presets](#spring-physics-with-presets)
3. [Keyframe Animation with Repeat](#keyframe-animation-with-repeat)
4. [Signal-Driven State Animation](#signal-driven-state-animation)
5. [Variant Orchestration with Stagger](#variant-orchestration-with-stagger)
6. [Gesture: Hover Effects](#gesture-hover-effects)
7. [Gesture: Tap with Counter](#gesture-tap-with-counter)
8. [Gesture: Focus Ring](#gesture-focus-ring)
9. [Gesture: Free Drag with Snap Back](#gesture-free-drag-with-snap-back)
10. [Gesture: Constrained Drag with Position Tracking](#gesture-constrained-drag-with-position-tracking)
11. [Gesture: Axis-Locked Drag](#gesture-axis-locked-drag)
12. [Gesture: Velocity Tracking](#gesture-velocity-tracking)
13. [Presence: Toggle Visibility](#presence-toggle-visibility)
14. [Presence: Animated List Removal](#presence-animated-list-removal)
15. [Layout: Shared Layout (Tab Indicator)](#layout-shared-layout-tab-indicator)
16. [Layout: Accordion/Expand](#layout-accordionexpand)
17. [Scroll: Progress Bar](#scroll-progress-bar)
18. [Scroll: Parallax](#scroll-parallax)
19. [Scroll: In-View Reveal](#scroll-in-view-reveal)
20. [Values: useSpring Smoothing](#values-usespring-smoothing)
21. [Values: useTransform Range Mapping](#values-usetransform-range-mapping)
22. [Values: useMotionTemplate](#values-usemotiontemplate)
23. [Values: useCycle](#values-usecycle)
24. [Values: useReducedMotion](#values-usereducedmotion)
25. [Imperative: Stagger Sequence](#imperative-stagger-sequence)
26. [Imperative: useAnimate Scope](#imperative-useanimate-scope)
27. [Reorder: Drag-to-Reorder List](#reorder-drag-to-reorder-list)

---

## Fade In on Mount

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    <div
      ngmMotion
      [initial]="{ opacity: 0, y: 30 }"
      [animate]="{ opacity: 1, y: 0 }"
      [transition]="{ duration: 0.6, ease: 'easeOut' }"
    >
      Hello, motion
    </div>
  `,
})
```

## Spring Physics with Presets

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    <div
      ngmMotion
      [animate]="{
        scale: activeSpring().stiffness > 400 ? 1.3 : 1.15,
        rotate: 15,
        borderRadius: '12px'
      }"
      [transition]="{
        type: 'spring',
        stiffness: activeSpring().stiffness,
        damping: activeSpring().damping
      }"
      [whileHover]="{ scale: 1.4, rotate: 0 }"
      [whileTap]="{ scale: 0.9 }"
    ></div>
  `,
})
export class SpringDemo {
  activeSpring = signal({ stiffness: 600, damping: 10 });
}
```

## Keyframe Animation with Repeat

```ts
template: `
  <div
    ngmMotion
    [animate]="{ x: [0, 50, -50, 0] }"
    [transition]="{
      duration: 2,
      ease: 'easeInOut',
      repeat: Infinity,
      repeatDelay: 0.5
    }"
  ></div>
`
// Note: use `inf = Infinity` field for templates that can't use Infinity directly
```

## Signal-Driven State Animation

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    <div
      ngmMotion
      [animate]="{
        scale: activeState().scale ?? 1,
        rotate: activeState().rotate ?? 0,
        x: activeState().x ?? 0
      }"
      [transition]="{ type: 'spring', stiffness: 300, damping: 22 }"
      [whileHover]="{ scale: (activeState().scale ?? 1) + 0.1 }"
    ></div>
  `,
})
export class SignalDrivenDemo {
  activeState = signal({ scale: 1, rotate: 0, x: 0 });
}
```

## Variant Orchestration with Stagger

Each child gets its own `[animate]` binding with `delay: i * 0.08` for stagger:

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    @for (i of items; track i; let idx = $index) {
      <div ngmMotion
        [animate]="shown() ? 'visible' : 'hidden'"
        [variants]="childVariants"
        [transition]="{ type: 'spring', stiffness: 300, damping: 24, delay: idx * 0.08 }"
      ></div>
    }
  `,
})
export class StaggerDemo {
  shown = signal(true);
  items = [0, 1, 2, 3, 4];

  readonly childVariants: Variants = {
    hidden: { opacity: 0, y: 20 },
    visible: { opacity: 1, y: 0 },
  };
}
```

## Gesture: Hover Effects

```ts
template: `
  <div
    ngmMotion
    [whileHover]="{ scale: 1.05, y: -6, boxShadow: '0 20px 40px rgba(6,182,212,0.15)' }"
    [transition]="{ type: 'spring', stiffness: 400, damping: 25 }"
    (hoverStart)="label.set('Hovering')"
    (hoverEnd)="label.set('Not hovering')"
  ></div>
`
```

## Gesture: Tap with Counter

```ts
template: `
  <div
    ngmMotion
    [whileHover]="{ scale: 1.04 }"
    [whileTap]="{ scale: 0.92, borderRadius: '20px' }"
    [transition]="{ type: 'spring', stiffness: 500, damping: 30 }"
    (tap)="tapCount.set(tapCount() + 1)"
  >
    Press me
  </div>
`
```

## Gesture: Focus Ring

```ts
template: `
  <input
    ngmMotion
    [whileFocus]="{ scale: 1.03, boxShadow: '0 0 0 2px rgba(6,182,212,0.5)' }"
    [transition]="{ type: 'spring', stiffness: 400, damping: 25 }"
    type="text"
  />
`
```

## Gesture: Free Drag with Snap Back

```ts
template: `
  <div
    ngmMotion
    [drag]="true"
    [dragSnapToOrigin]="true"
    [whileDrag]="{ scale: 1.1, boxShadow: '0 16px 48px rgba(6,182,212,0.2)' }"
    [transition]="{ type: 'spring', stiffness: 300, damping: 20 }"
    (dragStart)="dragging.set(true)"
    (dragEnd)="dragging.set(false)"
    class="touch-action-none"
  >
    Drag
  </div>
`
```

## Gesture: Constrained Drag with Position Tracking

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    <div
      ngmMotion
      [drag]="true"
      [dragConstraints]="{ left: -120, right: 120, top: -60, bottom: 60 }"
      [dragElastic]="0.15"
      [dragMomentum]="false"
      [whileDrag]="{ scale: 1.06 }"
      [style]="{ x: constrainedX, y: constrainedY }"
      class="touch-action-none"
    ></div>
    <p>x: {{ xVal() }} / y: {{ yVal() }}</p>
  `,
})
export class ConstrainedDragDemo {
  readonly constrainedX = useMotionValue(0);
  readonly constrainedY = useMotionValue(0);
  xVal = signal(0);
  yVal = signal(0);

  constructor() {
    this.constrainedX.on('change', (v: number) => this.xVal.set(Math.round(v)));
    this.constrainedY.on('change', (v: number) => this.yVal.set(Math.round(v)));
  }
}
```

## Gesture: Axis-Locked Drag

```ts
template: `
  <!-- Horizontal only -->
  <div
    ngmMotion
    drag="x"
    [dragConstraints]="{ left: -130, right: 130, top: 0, bottom: 0 }"
    [dragElastic]="0.08"
    [dragMomentum]="false"
    [style]="{ x: axisX }"
    class="touch-action-none"
  ></div>

  <!-- Vertical only -->
  <div
    ngmMotion
    drag="y"
    [dragConstraints]="{ left: 0, right: 0, top: -55, bottom: 55 }"
    [style]="{ y: axisY }"
    class="touch-action-none"
  ></div>
`
```

## Gesture: Velocity Tracking

```ts
export class VelocityDemo {
  readonly velocityX = useMotionValue(0);
  readonly velocityMv = useVelocity(this.velocityX);
  readonly absVelocity = useTransform(this.velocityMv, (v: number) => Math.min(Math.abs(v), 2000));
  readonly barWidth = useTransform(this.absVelocity, [0, 2000], ['0%', '100%']);
  readonly hue = useTransform(this.absVelocity, [0, 1000, 2000], [190, 270, 35]);
  readonly handleBg = useMotionTemplate`hsl(${this.hue}, 70%, 50%)`;
}
```

## Presence: Toggle Visibility

Uses `@if` + `[exit]` — no wrapper directive needed:

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    <button (click)="visible.set(!visible())">Toggle</button>
    @if (visible()) {
      <article
        ngmMotion
        [initial]="{ opacity: 0, scale: 0.92, y: 12 }"
        [animate]="{ opacity: 1, scale: 1, y: 0 }"
        [exit]="{ opacity: 0, scale: 0.92, y: -12 }"
        [transition]="{ type: 'spring', stiffness: 280, damping: 24 }"
      >
        Content
      </article>
    }
  `,
})
export class PresenceDemo {
  visible = signal(true);
}
```

## Presence: Animated List Removal

Uses `@for` + `[exit]` — no wrapper directive needed. Include `height` and `marginBottom` in exit for smooth collapse:

```ts
@Component({
  imports: [NgmMotionDirective],
  template: `
    @for (item of items(); track item.id; let last = $last) {
      <div
        ngmMotion
        [initial]="{ opacity: 0, x: -16, height: 0, marginBottom: 0 }"
        [animate]="{ opacity: 1, x: 0, height: 44, marginBottom: last ? 0 : 8 }"
        [exit]="{ opacity: 0, x: 16, height: 0, marginBottom: 0 }"
        [transition]="{ type: 'spring', stiffness: 300, damping: 25 }"
        class="overflow-hidden"
      >
        {{ item.label }}
        <button (click)="remove(item.id)">Remove</button>
      </div>
    }
  `,
})
export class AnimatedListDemo {
  items = signal([
    { id: 'a', label: 'First' },
    { id: 'b', label: 'Second' },
    { id: 'c', label: 'Third' },
  ]);

  remove(id: string): void {
    this.items.update((list) => list.filter((item) => item.id !== id));
  }
}
```

## Layout: Shared Layout (Tab Indicator)

```ts
template: `
  @for (tab of tabs; track tab.id) {
    <button (click)="active.set(tab.id)">
      @if (active() === tab.id) {
        <div
          ngmMotion
          [layoutId]="'active-indicator'"
          [transition]="{ type: 'spring', stiffness: 500, damping: 36 }"
          class="indicator"
        ></div>
      }
      {{ tab.label }}
    </button>
  }
`
```

## Layout: Accordion/Expand

```ts
template: `
  <section
    ngmMotion
    [layout]="true"
    [layoutDependency]="expanded()"
    [transition]="{ type: 'spring', stiffness: 360, damping: 30 }"
  >
    <h2 (click)="expanded.set(!expanded())">Toggle</h2>
    @if (expanded()) {
      <div>Expanded content</div>
    }
  </section>
`
```

## Scroll: Progress Bar

```ts
export class ScrollProgressDemo {
  private readonly scroll = useScroll();
  readonly smoothProgress = useSpring(this.scroll.scrollYProgress, { stiffness: 200, damping: 30 });
  readonly progressStyle: MotionStyle = { scaleX: this.smoothProgress, transformOrigin: '0% 50%' };
}
// Template: <div ngmMotion [style]="progressStyle" class="progress-bar"></div>
```

## Scroll: Parallax

```ts
export class ParallaxDemo {
  private readonly scroll = useScroll();
  readonly heroY = useTransform(this.scroll.scrollYProgress, [0, 1], [0, -180]);
  readonly heroStyle: MotionStyle = { y: this.heroY };
}
// Template: <section ngmMotion [style]="heroStyle">Parallax content</section>
```

## Scroll: In-View Reveal

```ts
template: `
  <section
    ngmMotion
    [initial]="{ opacity: 0, y: 32 }"
    [whileInView]="{ opacity: 1, y: 0 }"
    [viewport]="{ once: true, amount: 0.4 }"
  >
    Reveal me once
  </section>
`
```

## Values: useSpring Smoothing

```ts
export class SpringSmoothing {
  private readonly rawMV = useMotionValue(100);
  private readonly springMV = useSpring(this.rawMV, { stiffness: 300, damping: 25 });
  springValue = signal(100);

  constructor() {
    this.springMV.on('change', (v: number) => this.springValue.set(v));
  }

  onSlider(event: Event): void {
    const val = +(event.target as HTMLInputElement).value;
    this.rawMV.set(val);
  }
}
```

## Values: useTransform Range Mapping

```ts
export class TransformRangeDemo {
  readonly dragX = useMotionValue(0);
  readonly dragOpacity = useTransform(this.dragX, [-150, 0, 150], [0.2, 0.6, 1]);
  readonly dragColor = useTransform(this.dragX, [-150, 0, 150], ['#06b6d4', '#7c3aed', '#f59e0b']);
}
```

## Values: useMotionTemplate

```ts
export class MotionTemplateDemo {
  readonly hueMV = useMotionValue(200);
  readonly hslString = useMotionTemplate`hsl(${this.hueMV}, 80%, 60%)`;
}
```

Note: In some cases the tagged template literal form may need manual construction:
```ts
const strings = Object.assign(['hsl(', ', 80%, 60%)'], { raw: ['hsl(', ', 80%, 60%)'] }) as unknown as TemplateStringsArray;
this.hslString = useMotionTemplate(strings, this.hueMV);
```

## Values: useCycle

```ts
export class CycleDemo {
  readonly cycleState: ReturnType<typeof useCycle<{ scale: number; rotate: number }>>[0];
  readonly cycleFn: () => void;

  constructor() {
    const [state, cycle] = useCycle(
      { scale: 1, rotate: 0 },
      { scale: 1.5, rotate: 90 },
      { scale: 1, rotate: 180 },
      { scale: 1.5, rotate: 270 },
    );
    this.cycleState = state;
    this.cycleFn = cycle;
  }
}
// Template: <div ngmMotion [animate]="cycleState()" [transition]="spring">
```

## Values: useReducedMotion

```ts
export class ReducedMotionDemo {
  readonly prefersReduced = useReducedMotion();
  toggle = signal(false);
}
// Template:
// [transition]="prefersReduced() ? { duration: 0 } : { type: 'spring', ... }"
```

## Imperative: Stagger Sequence

```ts
export class StaggerSequenceDemo {
  private readonly barMVs = [0, 1, 2, 3, 4].map(() => useMotionValue(0));
  readonly barTargets = [160, 100, 190, 70, 140];

  run(): void {
    for (const mv of this.barMVs) mv.set(0);
    for (let i = 0; i < this.barMVs.length; i++) {
      animate(this.barMVs[i], this.barTargets[i], {
        duration: 0.8,
        delay: i * 0.12,
        ease: [0.22, 1, 0.36, 1],
      });
    }
  }
}
```

## Imperative: useAnimate Scope

```ts
@Component({
  template: `<div #box></div>`,
})
export class ScopeDemo {
  private readonly boxRef = viewChild<ElementRef<HTMLElement>>('box');
  private readonly scopeRef: { current: HTMLElement; animations: unknown[] };
  private readonly scopedAnimate: ScopedAnimate;

  constructor() {
    const [scope, scopedAnimate] = useAnimate<HTMLElement>();
    this.scopeRef = scope as { current: HTMLElement; animations: unknown[] };
    this.scopedAnimate = scopedAnimate;

    afterNextRender(() => {
      const el = this.boxRef();
      if (el) this.scopeRef.current = el.nativeElement;
    });
  }

  async run(): Promise<void> {
    const el = this.scopeRef.current;
    await this.scopedAnimate(el, { scale: 1.4 }, { duration: 0.4 }).finished;
    await this.scopedAnimate(el, { rotate: 180 }, { duration: 0.5, type: 'spring' }).finished;
    await this.scopedAnimate(el, { scale: 1, rotate: 0 }, { duration: 0.6, type: 'spring' }).finished;
  }
}
```

## Reorder: Drag-to-Reorder List

```ts
@Component({
  imports: [NgmMotionDirective, NgmReorderGroupDirective, NgmReorderItemDirective],
  template: `
    <div [ngmReorderGroup]="items()" [axis]="'y'" (reorder)="items.set($event)">
      @for (item of items(); track item.id) {
        <div
          [ngmReorderItem]="item"
          ngmMotion
          [layout]="true"
          [drag]="'y'"
          [dragSnapToOrigin]="true"
          [transition]="{ type: 'spring', stiffness: 400, damping: 30 }"
        >
          {{ item.label }}
        </div>
      }
    </div>
  `,
})
export class ReorderDemo {
  items = signal([
    { id: 'a', label: 'First' },
    { id: 'b', label: 'Second' },
    { id: 'c', label: 'Third' },
  ]);
}
```

## CSS Tips for Drag

Always add `touch-action-none` (or Tailwind class) to draggable elements to prevent scroll conflicts on mobile:
```html
<div ngmMotion [drag]="true" class="touch-action-none cursor-grab active:cursor-grabbing">
```
