# ng-motion — Claude Code Skill

A [Claude Code](https://claude.ai/claude-code) skill for building Angular animations with the [@scripttype/ng-motion](https://www.npmjs.com/package/@scripttype/ng-motion) library.

Gives Claude deep knowledge of ng-motion's API: directives, hooks, gestures, presence/exit animations, layout transitions, scroll-linked effects, drag-to-reorder, and imperative animation sequences.

## Install

```bash
# Add the marketplace
/plugin marketplace add ScriptType/ng-motion-skill

# Install the plugin
/plugin install ng-motion@ng-motion-skill
```

## What's included

- **SKILL.md** — Core reference covering the full directive API, reactivity model, exit animations, motion values, layout, scroll, drag, imperative animation, route transitions, performance rules, and testing
- **references/patterns.md** — 27 copy-paste patterns (fade-in, springs, gestures, presence, layout, scroll, reorder, etc.)
- **references/api-reference.md** — Complete export listing organized by module

## Usage

Once installed, Claude automatically uses this skill when you work with ng-motion code. You can also invoke it explicitly:

```
/ng-motion
```

## Links

- [ng-motion docs](https://ng-motion.dev)
- [ng-motion on GitHub](https://github.com/ScriptType/ng-motion)
- [ng-motion on npm](https://www.npmjs.com/package/@scripttype/ng-motion)
