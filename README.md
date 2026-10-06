# Keeping an AI support assistant's answers correct

A case study of an automation I built to keep a customer-support chatbot giving accurate answers.

**The problem.** The assistant answered customer questions by searching a library of internal help
articles. When an article was wrong, missing or ambiguous, the bot confidently told customers the
wrong thing — and nothing crashed, so nobody found out until someone happened to notice.

**What it does.** Reads incoming support tickets, works out whether each is a documentation problem
or a software bug, writes or fixes the article, gets the facts approved by whoever owns that policy,
then asks the live assistant the questions a real customer would ask and checks its replies — with a
person required to approve every irreversible step.

**[→ Open the case study](index.html)** — one HTML file, no build step, no dependencies.

```bash
python3 -m http.server 8000
```

## What's in it

| Section | What it covers |
|---|---|
| Evidence | 120 articles written or fixed, with the counts behind the number |
| Why it exists | Why this work is slow, error-prone and invisible when it breaks |
| How it works | Ten steps, clickable, with the four that wait for a person marked |
| Impact | What changed for the person asking the question — including the run that failed |
| The near miss | A flaw caught before it could give customers a wrong answer |
| Safety | Six limits enforced in code, not by instructing the AI nicely |
| The app | Four screens of the desktop tool that runs it |
| Time model | An adjustable estimate, labelled as a model rather than a measurement |
| Limits | What it doesn't do, and what's still broken |

## The interesting engineering problem

The most useful part isn't the throughput — it's the near miss.

A process that copied articles between environments turned out to identify them **by title** rather
than by a stable ID. So renaming an article didn't update its downstream copy; it created a second
one and abandoned the original. A run that reported 20/20 success had quietly left nine outdated
articles switched on next to their own replacements, and a tenth in production that didn't appear in
staging at all.

It surfaced only because the automation cross-checks three copies of the system against each other
instead of trusting any single list. The lesson generalises well past this system: carry a stable
identifier end to end, and treat "accepted" as *accepted*, never as *delivered*.

## A note on wording

This is written for readers outside the company that uses it. Internal system names, project
codenames, ticket references and industry shorthand have been replaced with plain equivalents, and
one product-specific flaw is described in general terms.

The screenshots are real screens from the running app, captured with sample data: reviewer names are
placeholders and ticket numbers are changed, while the layout, validation states and category locks
are exactly as the tool draws them. The app's own on-screen labels still use the team's in-house
wording — "SME" means subject-matter expert, the person who owns a given policy, and "grounding"
means the sourced document behind an answer.

No passwords or keys appear anywhere in this repository.

## Built with

Python automation for ticket sorting, article drafting, validation and quality checks, exposed to the
model as a fixed, audited set of 37 tools · scheduled hourly runs · integrations with the ticket
tracker, company wiki, CRM and chat · a conversational test runner for behavioural verification ·
operator app in Next.js and Electron.

---

Built by Matthew Plamp.
