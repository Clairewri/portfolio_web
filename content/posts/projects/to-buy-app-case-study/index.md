---
title: "To Buy: From Running-out Washing Liquid to a Shipped App"
date: 2026-07-10T10:00:00+10:00
draft: false
categories: ["technical-writing", "projects", "business-analysis", "product-management"]
tags: ["requirements", "PRD", "PWA", "case study", "firebase", "technical writing"]
summary: "A shared grocery-list app I designed, scoped, and shipped with AI-assisted coding: requirements, trade-offs, and a product-owner retrospective."
ShowToc: true
TocOpen: false
---

*A shared grocery-list app I designed, scoped, and shipped with AI-assisted coding. Live on two iPhones, two languages, $0 running cost. I can't code.*

**Live app:** [idyllic-swan-cf12fc.netlify.app](https://idyllic-swan-cf12fc.netlify.app/)

## The problem

The other day I noticed the dish washing liquid was down to its last fifth. I'd need another one next time I was at Coles, and I wanted it on a list so I wouldn't forget it in the aisle.

Within a few seconds several options passed through my mind, and none of them worked:

- **The whiteboard on our fridge.** Too small. Corn, avocado, chicken, and today's menu already live there.
- **A paper notebook.** I don't carry one. I don't even own one anymore.
- **Apple's built-in Notes.** Too plain, and I know I won't open it in a supermarket. My partner and I tried sharing a note and it failed. We never figured out why.
- **Notion.** Syncing for two people needs a paid plan, and pages take too long to load. I'm not waiting for a page to open in a grocery aisle.

So I built one. With Claude, of course.

## Requirements: what's in, what's out, and why

Two core features: note and sync.

**In:**

- Add and remove items, and tick the box once you've got it.
- Sync between users. It's a household list (one person can be a household too), so a household code lets two or more people join the same list.
- Categories that match how a supermarket is laid out. Bananas and paper towels live in different aisles, so: groceries (milk, bread, meat), consumables (washing liquid, paper towels), and a third category for household one-offs (a plate, a bulb).
- Bought history, with a "buy again" button. Tastes don't change much, so re-buying a regular should be one tap, not retyping.

**Out, deliberately:**

- Reminders.
- Notifications.

Apps should make life easier, not nag me into doing things. That's a product principle applied to my own product, at least.

**Design constraints, with reasons:**

- **Phone only.** Nobody does the weekly shop with a laptop open in the trolley. The UI is built for a phone screen, and the first version targets iPhone because that's what we both use.
- **One trip, everything.** A weekly shop is real effort and fuel is dear. The app's job is making sure you leave the store with everything you came for.
- **Real shopping is messy.** Items go out of stock, prices turn ridiculous, you grab bananas instead of apples. So items can be removed mid-shop, not just ticked. And you might duck into a shop on the way to get fuel, or travel for three weeks with no shopping in between, so there's no date ticking or countdown pressure.

### Requirements at a glance

**Functional requirements** (what it does):

- Users can add, edit, remove, and tick off items.
- Two or more people join one shared list via a household code.
- Every edit syncs to all members in real time.
- Items are grouped into groceries, consumables, and household.
- Bought history supports one-tap "buy again."

**Non-functional requirements** (the qualities it has to meet):

- **Sync and load are fast enough to rely on mid-shop.** *Critical.* For a list you open in a busy aisle, responsiveness is make-or-break, so this ranks as high as any feature.
- Runs on a phone browser and installs to the home screen (PWA).
- Runs at $0 cost at household scale.
- Available in English and Simplified Chinese.
- Works offline and degrades gracefully when there's no connection.

## Key decisions and trade-offs

**PWA over native app.** A native iOS app means the Apple Developer Program at US$99 a year. A PWA (progressive web app) is a website you save to your home screen: it gets an icon, opens instantly, works offline, and behaves like an app, but it ships as a link instead of an App Store download. At a trial stage, $0 wins. I'll revisit this if the app ever needs App Store distribution.

**Firebase for sync.** Two phones editing one list need a server in the middle, and I don't run servers. Firebase, Google's cloud database service, stores the list and pushes every edit to both users in real time. Free at this scale.

**Static app, hosted on Netlify.** The whole app is plain files: HTML, CSS, JavaScript. A host stores those files and hands them to the browser as-is when someone opens the link. Netlify does this, also free.

The mental model I keep for how they fit together:

> **Key**
>
> 1. When I release a new version, the *files* change. I deploy the updated files through the **host**.
> 2. When users add, edit, or remove items, that's *data*. The **database** handles it and syncs it in real time.

Specifically: `index.html` for everything on screen, `store.js` decides where the list is stored, `firebase-config.js` holds the keys that connect to Firebase, `manifest.webmanifest` and `service-worker.js` make it installable and usable offline, and `icons/` holds the icons.

## How I ran it

I can't code, so I do everything around the code: requirements, scope, versions, and records. That turns out to be most of the job.

**PRD.** Before Claude wrote a line, I wrote a PRD (product requirements document) specifying what each version should do. Think of it as the guidebook the development team works from. In a commercial team the cycle runs roughly: plan and milestones, split into versions, PRD draft, review and sign-off, development, test and debug, release, retrospective, repeat. I ran a simple version of that loop for a lightweight household app.

**Version control.** Every version lives in GitHub. The mindset: development should be planned, staged, and flexible, and testing and debugging get messy. Clean file management is what keeps a project easy to manage and hand over, even when you're only handing it to future you.

**Backlog.** One living list for every idea, so no thought gets lost. Each item carries version, priority, effort, category, status, date, and notes.

**Changelog.** After each version: what was actually built versus what the PRD planned, split into Added, Changed, Deferred, Fixed, and Rejected. The gap between the plan and the build is where the honest learning is.

Unlike the PRDs, which are one per version, the backlog and changelog are single living documents for the whole project.

## Outcomes

**v1 shipped.** A grocery tracker plus a countdown-based consumables tracker, with bought history. Installed on two iPhone home screens, syncing through Firebase, in English and Simplified Chinese. Total running cost: $0.

**v2 shipped.** The three categories merge into one color-coded screen. Consumables get urgency tags (Could wait / Good to buy / Next buy) and sort themselves. Favorites arrive: star an item yourself, or let the app auto-tag anything bought three times in a month. And when one partner removes an item, the other sees it greyed out with "Removed by [name]" and a choice to add it back. Trust matters, even in a grocery list.

**v2.1 in progress.** Mostly a density-and-polish pass, plus one small foundation for the future. The screen shows more at a glance: section headers drop away so color alone marks the groups, the item card gets a cleaner layout (name top-left, specs bottom-left, who added it bottom-right), the star shrinks and moves to the top-right, and the add-item sheet gets shorter (30 to 40 percent of the screen) so the list stays visible behind the keyboard. A font-size setting (Small, Normal, Large) lets you trade text size for how many items fit per screen. The one new capability is an optional Menu/Dish field on groceries, with a filter on the home screen. It's small now, but it's the seed for a recipe system later.

**Rejected, on purpose.** A "skipped / unavailable" item state, and a separate tab for removed items. Both solve problems we don't actually have, and every feature is a maintenance cost. Saying no is cheaper than deleting later.

## Retrospective

The product-owner mindset is what actually gets things done: decide what matters, ship small, test, repeat.

### The most frustrating part

Debugging. v1 took me two hours or more of back-and-forth with Claude to track bugs down. Worth building some computer basics, and for a web app, checking the browser's `Developer Tools` would never be a bad idea.

### What I'd tell a first-timer

Don't start with something ambitious. If your goal is to learn how the pieces fit together, a big first project is the fastest way to get lost and give up. Start small, ship it, then add.
