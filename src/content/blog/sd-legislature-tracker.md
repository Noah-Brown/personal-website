---
title: "Tracking the South Dakota Legislature Because Nobody Else Will"
description: "I built a civic tool that shows where bills are, what they change in existing law, and where the money goes."
date: 2026-02-24T12:00:00-06:00
tags: ["projects", "claude-code", "infrastructure"]
---

South Dakota's legislature meets for 38 days a year. In that window, roughly 350 bills get introduced, debated, amended, and either signed into law or quietly killed. If you want to follow along, your options are: the official state website (a JavaScript-heavy SPA that barely renders on mobile), LegiScan (powerful but designed for lobbyists, not citizens), or the Argus Leader (good reporting, but no interactive tools). Nobody presents the information in a way that answers the three questions regular people actually ask:

1. What is the legislature doing right now?
2. What would this bill actually change in existing law?
3. Where does the state's money go?

So I built something that tries to answer all three.

## What It Does

[SD Legislature Tracker](https://sdleg.vercel.app) is a web app that pulls bill data from the LegiScan API, syncs it into a SQLite database every four hours via GitHub Actions, and renders it through a Next.js frontend. There are three main views.

**The Pipeline.** Every bill is tracked through its lifecycle — introduced, in committee, passed house of origin, crossed over to the other chamber, on the governor's desk, enacted, or dead. It's a Kanban board for legislation. You can see at a glance that most bills die in committee, which is a useful thing for citizens to understand about how their government works.

**The Diff Engine.** This is the part I'm most proud of. South Dakota bills follow strict drafting conventions: underscored text is new language being added, overstruck text is language being deleted. The app parses bill text, matches it against the ~30,000 sections of SD codified law I scraped and cached, and generates GitHub-style diffs showing exactly what each bill would change. When a bill says it's amending § 22-6-1, you can see the current law side-by-side with the proposed changes. No more reading dense legalese and trying to figure out what's different.

**The Budget.** The governor proposes a budget before session, and the Joint Appropriations Committee modifies it over the next month. The app visualizes proposed spending by category so you can see where the money goes and what's changing year over year.

## How It's Built

The architecture splits cleanly in two. A Python pipeline handles data ingestion — syncing bills from LegiScan, scraping statutes from sdlegislature.gov, computing diffs with difflib, and categorizing bills by topic. A Next.js frontend reads from the SQLite database and renders everything as static pages with incremental regeneration.

I went with SQLite because the entire dataset for a state with 350 bills fits in a few megabytes. No need for Postgres. The database file gets committed directly to the repo and synced on a cron schedule. It's simple and it works.

The diff engine was the hardest piece. SD bills reference statutes with a consistent pattern (`That § 22-6-1 be AMENDED`), so regex handles the section matching. But parsing the actual markup — figuring out which text is underscored, which is overstruck, which is just context — required pulling bill text from LegiScan PDFs with pdfplumber and then running it through a multi-step extraction pipeline. The current run covers 408 bills and has produced 1,337 diffs.

## Why South Dakota

Two reasons. First, I grew up here. I vote here. I should probably know more about what my state government is doing than I currently do, and the existing tools make it hard.

Second, SD is the ideal proving ground. Small enough that the data is manageable (~350 bills vs. thousands in California or New York), short enough session that you can build and ship within a single legislative cycle, and there is genuinely zero competition in this space for South Dakota. The architecture is state-agnostic through LegiScan's 50-state API, so if it works here, it can expand.

## What's Next

The pipeline and bill views are working. The diff engine is working. The budget visualization is next — I've got the governor's FY2027 proposed numbers structured into JSON but haven't built the treemap yet. After that: AI-generated plain-English bill summaries, legislator profiles with voting records, and email alerts for keyword-matched bills.

The 101st session ends March 28. The goal is to have something polished enough to share with local reporters and civic organizations before then. If even a handful of South Dakotans use it to understand what their legislature is doing, I'll consider it a success.
