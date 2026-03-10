# ng-motion API Reference

Complete listing of public exports from `@scripttype/ng-motion`, organized by module.

## Core

| Export | Kind | Description |
|--------|------|-------------|
| `NgmMotionDirective` | Directive | Main directive for declarative motion (`ngmMotion` selector) |
| `provideMotionConfig(config)` | Function | Global DI-based defaults |
| `MOTION_CONFIG` | InjectionToken | Config injection token |
| `VERSION` | Constant | Library version |
| `withMotionTransitions(config?)` | Function | Route transitions via View Transitions API (wraps `withViewTransitions()`) |

## Core Types

| Export | Kind |
|--------|------|
| `MotionValue` | Class |
| `MotionConfig` | Type |
| `Target` | Type |
| `TargetAndTransition` | Type |
| `Transition` | Type |
| `Variants` | Type |
| `Variant` | Type |
| `VariantLabels` | Type |
| `MotionStyle` | Type |
| `ResolvedValues` | Type |
| `AnimationDefinition` | Type |
| `AnimationPlaybackControls` | Type |
| `AnimationState` | Type |
| `KeyframeOptions` | Type |
| `SpringOptions` | Type |
| `InertiaOptions` | Type |
| `MotionNodeOptions` | Type |
| `TargetResolver` | Type |
| `ReducedMotionConfig` | Type |

## Motion Values and Hooks

| Export | Returns | Injection Context |
|--------|---------|-------------------|
| `useMotionValue(initial)` | `MotionValue<T>` | Yes |
| `useSpring(source, options?)` | `MotionValue<number>` | Yes |
| `useTransform(...)` | `MotionValue<T>` | Yes |
| `useVelocity(value)` | `MotionValue<number>` | Yes |
| `useTime()` | `MotionValue<number>` | Yes |
| `useMotionTemplate(strings, ...values)` | `MotionValue<string>` | Yes |
| `useMotionValueEvent(value, event, callback)` | `void` | Yes |
| `useAnimationFrame(callback)` | `void` | Yes |
| `useCycle(...items)` | `[Signal<T>, () => void]` | Yes |
| `useReducedMotion()` | `Signal<boolean>` | Yes |
| `useWillChange()` | `MotionValue<string>` | Yes |
| `useInView(target, options?)` | `Signal<boolean>` | Yes |
| `useScroll(options?)` | `ScrollMotionValues` | Yes |
| `isMotionValue(value)` | `boolean` | No |
| `TransformOptions` | Type | — |

## Gestures and Drag

| Export | Kind |
|--------|------|
| `HoverFeature` | Class |
| `PressFeature` | Class |
| `FocusFeature` | Class |
| `InViewFeature` | Class |
| `DragGesture` | Class |
| `PanGesture` | Class |
| `PanSession` | Class |
| `VisualElementDragControls` | Class |
| `DragControls` | Class |
| `useDragControls()` | Function (injection context) |
| `EventInfo` | Type |
| `PressGestureInfo` | Type |
| `PanInfo` | Type |
| `DragDirection` | Type |
| `ViewportOptions` | Type |
| `Constraints` | Type |
| `BoundingBox` | Type (from motion-utils) |

## Presence

| Export | Kind |
|--------|------|
| `NgmPresenceDirective` | Directive (`*ngmPresence` structural) |
| `PRESENCE_CONTEXT` | InjectionToken |
| `createPresenceContext()` | Function |
| `NgmPresenceContext` | Type |
| `ExitAnimationFeature` | Class |
| `useIsPresent()` | Function → `Signal<boolean>` |
| `usePresence()` | Function → `[Signal<boolean>, () => void]` |
| `usePresenceList(items, options)` | Function → `PresenceListState<Id>` |

## Layout

| Export | Kind |
|--------|------|
| `NgmLayoutGroupDirective` | Directive (`ngmLayoutGroup`) |
| `LAYOUT_GROUP` | InjectionToken |
| `LayoutGroupContextProps` | Type |

## Scroll

| Export | Kind |
|--------|------|
| `scroll(onScroll, options?)` | Function |
| `scrollInfo(onScroll, options?)` | Function |
| `ScrollOffset` | Presets (`Enter`, `Exit`, `Any`, `All`) |
| `useScroll(options?)` | Function → `ScrollMotionValues` |
| `ScrollOptions` | Type |
| `ScrollInfo` | Type |
| `AxisScrollInfo` | Type |
| `OnScroll` | Type |
| `OnScrollInfo` | Type |
| `ScrollInfoOptions` | Type |
| `UseScrollOptions` | Type |
| `ScrollMotionValues` | Type |

### ScrollMotionValues Properties
- `scrollX: MotionValue<number>`
- `scrollY: MotionValue<number>`
- `scrollXProgress: MotionValue<number>`
- `scrollYProgress: MotionValue<number>`

### useScroll Options
- `container?: ElementRef<HTMLElement> | HTMLElement` — scroll container (default: document)
- `target?: ElementRef<HTMLElement> | HTMLElement` — element to track
- `offset?: ScrollOffset[]` — scroll trigger points (e.g., `['start end', 'end start']`)

## Reorder

| Export | Kind |
|--------|------|
| `NgmReorderGroupDirective` | Directive (`[ngmReorderGroup]`) |
| `NgmReorderItemDirective` | Directive (`[ngmReorderItem]`) |
| `REORDER_CONTEXT` | InjectionToken |
| `checkReorder()` | Function |
| `autoScrollIfNeeded()` | Function |
| `resetAutoScrollState()` | Function |
| `ReorderContextProps` | Type |
| `ItemData` | Type |

### NgmReorderGroupDirective
- Input: `ngmReorderGroup` — array of items
- Input: `axis` — `'x'` or `'y'`
- Output: `reorder` — emits reordered array

### NgmReorderItemDirective
- Input: `ngmReorderItem` — the item value

## Imperative Animation

| Export | Kind |
|--------|------|
| `animate(...)` | Function |
| `stagger(...)` | Function |
| `useAnimate()` | Function → `[scope, ScopedAnimate]` |
| `ScopedAnimate` | Type |
| `createAnimationsFromSequence()` | Function |
| `AnimationSequence` | Type |
| `SequenceOptions` | Type |
| `SequenceTime` | Type |

### animate() Overloads
```ts
// MotionValue
animate(value: MotionValue, to: T, options?: Transition)
// DOM element/selector
animate(target: string | Element, keyframes: Target, options?: Transition)
// Sequence
animate(sequence: AnimationSequence)
```

## Feature Loading

| Export | Kind |
|--------|------|
| `provideMotionFeatures(bundle)` | Function |
| `ngmAnimationFeatures` | Bundle |
| `ngmAllFeatures` | Bundle |
| `NgmFeatureBundle` | Type |
| `NgmLazyFeatureBundle` | Type |
| `initNgmFeatures()` | Function |
| `loadNgmFeatures(features)` | Function |

## Visual Element Adapters (Advanced)

| Export | Kind |
|--------|------|
| `createVisualElement()` | Function |
| `mountVisualElement()` | Function |
| `updateVisualElement()` | Function |
| `unmountVisualElement()` | Function |
| `createHtmlVisualState()` | Function |
| `createHtmlRenderState()` | Function |
| `createSvgVisualElement()` | Function |
| `createSvgVisualState()` | Function |
| `createSvgRenderState()` | Function |
| `isSVGElement()` | Function |

## Utilities

| Export | Kind |
|--------|------|
| `onCleanup()` | Function |
| `resolveInput()` | Function |

## Re-exports from motion-dom

| Export | Kind |
|--------|------|
| `motionValue` | Function |
| `animateValue` | Function |
| `animateSingleValue` | Function |
| `frame` | Object |
| `cancelFrame` | Function |

## Transition Reference

### Spring
```ts
{ type: 'spring', stiffness: 280, damping: 24, mass?: 1, velocity?: 0, restDelta?: 0.01 }
```

### Tween / Duration
```ts
{ duration: 0.5, ease: 'easeOut' }
// ease values: 'linear', 'easeIn', 'easeOut', 'easeInOut', 'circIn', 'circOut', 'circInOut',
//              'backIn', 'backOut', 'backInOut', 'anticipate'
// or cubic-bezier: [0.22, 1, 0.36, 1]
```

### Delay
```ts
{ delay: 0.2, duration: 0.4 }
```

### Repeat
```ts
{ duration: 2, repeat: Infinity, repeatType: 'loop' | 'reverse' | 'mirror', repeatDelay: 0.5 }
```

### Per-property
```ts
{
  opacity: { duration: 0.3 },
  x: { type: 'spring', stiffness: 300 },
  default: { duration: 0.5 }
}
```

### Orchestration (in Variants only)
```ts
{
  staggerChildren: 0.08,
  delayChildren: 0.1,
  when: 'beforeChildren' | 'afterChildren'
}
```
