# Quiet responders: what we'd build instead of the quick fix

**To:** Helen Achebe, Director of Product  
**From:** Dispatch PM  
**Date:** 7 Nov 2026 (rough draft, not polished)  
**Re:** Your note asking to see the fix from the user's side before anyone touches the code

---

## The short version

The quick fix changes one number. What we'd actually build gives **handlers** and **responders** a way to see what a missed ping means, and gives a responder a way back after one. The code changes (scoring a miss separately, letting the score ease back, a floor, a one-time reset) ship together in 4.3 so that what people see on screen is actually true underneath.

**My recommendation:** don't ship the one-number change this afternoon. It puts responders back on the list but doesn't fix why they keep missing, and nobody can see that it happened.

---

## What's going on, in our own words

- **Our glossary already makes the distinction the code doesn't.** It says a ping is **taken**, **turned down** or **missed** (the ping wait ran out), and that a miss is recorded separately from a turn-down. The ranking code counts both as "we asked and we didn't get a yes" (−0.12 either way), and the score never recovers over time. *(Glossary; `history.py`)*
- **Wen's 2019 note is the right question.** It asks whether the score should ease back after a while: yes for "somebody who had a bad month," no for "somebody who has stopped taking work." Those are two different people, a miss and a turn-down, and today's code can't tell them apart. *(`history.py`)*
- **4.2 made that gap matter.** The ping wait went from 90 to 60 seconds. Misses rose from 2.3% to 18% of pings, while turn-downs actually *fell* (21% → 18%). Four responders (Vesper, Farlight, The Undertow, Meteor Mite) dropped down the ranking and had no way back. Their work moved to The Gale, Nightwell and Stormwrack. *(4.2 release notes; our analysis)*
- **Marcus's question from 14 Aug still hasn't been answered.** He asked whether the 4.2 ranking change was meant to apply to responders who'd been turning jobs down. Our answer: 4.2 made the score count *less*, but because the code can't tell a miss from a "no," the shorter wait ended up penalising people who never said no. *(4.2 release comments)*
- **Support saw both effects separately.** By 26 Aug, about ⅔ of tickets were "my phone never goes off" and ⅓ were "it was gone before I could answer." Nadia linked the second to the 60-second wait and couldn't explain the first. This is the explanation for the first. *(4.2 release comments)*

## What a handler like Kip would notice

- Each responder card in the console shows **where they sit in the order and why**: "Missed 3 of the last 5 pings, so lower in the order for Harborside."
- When a callout moves on, the console says what happened: "No answer in 60 s. Went to The Gale. Counted as a miss, not a no."
- The console **flags a responder who has gone quiet** before the handler has to file a ticket, and flags callouts that went unfilled (now 11.1%, up from 5.5%).
- Handlers see only their own responders, by cover name, and never anyone else's score (Security Policy 4.1).

## What a responder who's gone quiet would feel differently about

- A missed ping tells them plainly: "That one went to someone else. Missing it doesn't count against you like a no."
- After a quiet stretch, the app tells them "You're back in rotation," because misses now ease back over a few days.
- If someone keeps missing even when they're first in line, the app runs a quiet check ("Tap if you got this") that doesn't affect their score. This tells us whether their phone is actually getting the ping.

**Click-through:** [prototype.html](prototype.html) shows the console and the phone side by side. Some numbers in it are made up for the mock-up.

## What changes underneath (4.3)

| Change | Why people would notice |
|---|---|
| Score a miss separately from a turn-down, with a smaller penalty | "A miss isn't a no" becomes true, not just a message |
| Misses ease back over a few days; turn-downs stay until the next take | Covers both sides of Wen's 2019 note |
| A score floor, so nobody drops off the order entirely | A quiet card never says "no pings, no end date" |
| One-time reset for the four responders | They get pings again, but **only after we fix why they miss**. Since 19 Aug they miss 75% of pings even when first in line, against 16% for everyone else |
| An alert when a callout goes unfilled | Handlers and support hear about it before a ticket comes in |

**What stays the same:** the 60-second ping wait and the 4.2 proximity weighting. This isn't a revert. Wide-area responders asked for the ranking change, and as Priya put it, reverting "just trades one angry group of responders for another." *(Handoff)*

## Who does what

| Person | Ask |
|---|---|
| **Wen Li**, Staff engineer | Confirm how the score works today, and why the four miss even when first: slow answers, pings not reaching the phone, or wrong availability. I've drafted DM questions; the first two matter most. |
| **Marcus Oyelaran**, Eng manager | Size the 4.3 package. Check whether scores reset when the service restarts. |
| **Sofia Marino**, Product designer | Turn the prototype into real console and phone screens. Check how this overlaps with the Handler phone app she's exploring. |
| **Nadia Hoffmann**, Support lead | Contact the handlers who wrote in. Review the ticket themes once this ships. |
| **Ravi Menon**, Data analyst | Check our numbers against his official weekly acceptance report. He wasn't in the 4.2 thread or the handoff. |
| **Helen Achebe** | The decisions below. |

## Decisions I need from you

1. **Package, not patch:** hold the one-number change and ship the package in 4.3?
2. **Reset the four** once the cause of their misses is known?
3. **Owners:** every roadmap item still lists Priya. Can we agree new owners, and which items squeezed out of 4.2 (including Availability Confidence) are still Q3 commitments? Priya notes this conversation never happened.
4. **Naming:** can responders' cover names appear in briefs like this one under Security Policy 4.1?

## Still unknown

- Why the four miss even when first (waiting on Wen).
- Whether Priya's "August is always soft" is true. Our data starts on 29 Jun, so we can't check earlier years. The week of 3 Aug was the best on record.
- Whether Availability Confidence, committed to 4.2 but missing from the release notes, is the same idea as the "gone quiet" signal.

---

*Sources: rook-wiki Glossary (updated 4 Aug 2026), Team directory (updated 2 Sep 2026) and 4.2 release page and comments, checked live on 7 Nov 2026; `00-rook/company/notes/handoff-from-priya.docx`; `00-rook/code/dispatch-routing/history.py`; earlier analysis of rook.pings and support tickets.*
