# How I AI with Pi

My personal AI coding setup, built around [Pi](https://pi.dev/) and a small set of extensions/skills I want available everywhere.

[![skills.sh](https://skills.sh/b/DennisKo/skills)](https://skills.sh/DennisKo/skills)

## Install Pi

```sh
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
# or
curl -fsSL https://pi.dev/install.sh | sh
```

Then start Pi and sign in or configure an API key:

```sh
pi
/login
```

## Install my Pi extensions

These are the core packages this setup expects:

```sh
pi install npm:pi-subagents
pi install npm:pi-web-access
pi install npm:@plannotator/pi-extension
```

This setup is intentionally manual: run the commands above on any machine where you want this Pi setup available.

## What each extension is for

- **Pi**: the terminal coding agent from [pi.dev](https://pi.dev/).
- **pi-subagents**: delegate focused research/review/implementation work to subagents.
- **pi-web-access**: web and code research tools for docs, source references, and current info.
- **Plannotator**: browser-based annotation/review flows for plans, responses, and code review.

## Local skills in this repo

### grug-brained-reviewer

Find architecture improvements before the clever architecture committee successfully summons the complexity demon and leaves future-you holding the pager. This skill walks the repo, sends focused explorer agents into suspicious caves when available, connects actual evidence to grug-brained engineering patterns, and produces a self-contained HTML report of refactoring candidates.

Inspired by and attributed to [The Grug Brained Developer](https://grugbrain.dev/).

**Use when:**

- Inspecting a project for architecture improvements before someone adds a platform layer to solve one boolean
- Looking for refactoring candidates that reduce complexity rather than laundering it through abstractions
- Producing an HTML architecture review report that names files, evidence, and pain instead of vibes
- Preparing to grill and harden a chosen refactoring candidate before implementation

## Install these skills elsewhere

```sh
npx skills add DennisKo/skills
```

To install only this skill:

```sh
npx skills add DennisKo/skills --skill grug-brained-reviewer
```

## Recommended dependency

Most skills in this repo expect Matt Pocock's `grill-me` skill to be available for design hardening, assumption checks, and the part where vague architecture confidence is politely dragged into daylight.

```sh
npx skills add https://github.com/mattpocock/skills --skill grill-me
```
