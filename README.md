# clearshot

structured screenshot intelligence for AI coding tools.

## install — 30 seconds

open claude code and paste this. claude does the rest.

```
git clone https://github.com/udayanwalvekar/clearshot.git ~/.claude/skills/clearshot && cd ~/.claude/skills/clearshot && ./setup
```

### add to your project so teammates get it (optional)

```
cp -Rf ~/.claude/skills/clearshot .claude/skills/clearshot && rm -rf .claude/skills/clearshot/.git && cd .claude/skills/clearshot && ./setup
```

real files get committed to your repo (not a submodule), so `git clone` just works for your teammates. everything lives inside `.claude/`.

### alternative

also available via `npx clearshot`.

### installed before v1.4.0? reinstall

there was a small bug that was causing clearshot to not get triggered consistently. paste this in your terminal to fix it:

```
rm -rf ~/.claude/skills/clearshot ~/.clearshot && git clone https://github.com/udayanwalvekar/clearshot.git ~/.claude/skills/clearshot && cd ~/.claude/skills/clearshot && ./setup
```

### troubleshooting

if clearshot stopped working or the skill won't load, run the same command above.

activates automatically when you share a UI screenshot.

### update

```
git -C ~/.claude/skills/clearshot pull
```

## what it does

AI sucks at seeing screenshots. paste one into claude code and ask it to rebuild something — it says "i see a dashboard with some cards and a sidebar." meanwhile you're staring at a broken button, inconsistent border radius, and 6px padding where 8px should be.

clearshot fixes this by running structured analysis *before* the AI responds. not "blue color" but `#3B82F6`. not "some spacing" but `8px gap`. not "a button" but `sm/rounded-md/border-gray-200/shadow-sm`.

three analysis levels — facts and taste together, every time:

| level | what it does | when it runs |
|-------|-------------|--------------|
| map | 5×5 spatial grid, full element inventory with colors/borders/states | always |
| system | color palette, type scale, spacing patterns, design cohesion | always |
| blueprint | layout architecture, CSS patterns, interaction map, responsive context | when building |

levels 1+2 always run. level 3 escalates when you're implementing from the screenshot.

## it knows when to shut up

screenshot of a meme? stays quiet. architecture diagram? stays quiet. photo of your lunch? definitely stays quiet. only activates on UI screenshots when you're building or critiquing frontend. a screenshot shared for what it says (a slack thread, an error) gets a normal answer.

## privacy

everything runs locally. clearshot makes no network calls and sends no telemetry. no screenshots or code ever leave your machine.

## research

builds on production systems and academic research:

- **microsoft omniparser** — improved GPT-4V from 70.5% to 93.8% with structured analysis. [paper](https://arxiv.org/abs/2408.00203)
- **dcgen** — recursive divide-and-conquer screenshot decomposition. [paper](https://arxiv.org/abs/2405.16569)
- **replit** — multi-agent verification loop with structured screenshot comparison
- **google screenai** — UI-specific vision-language model. [blog](https://research.google/blog/screenai-a-visual-language-model-for-ui-and-infographics-understanding/)

## built by

[udayan walvekar](https://x.com/udayan_w) · [growthx](https://growthx.club)

## license

MIT
