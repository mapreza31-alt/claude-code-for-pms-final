# Quiet responders: what we'd build instead of the quick fix

**To:** Helen Achebe, Director of Product  
**From:** Dispatch PM  
**Date:** 7 Nov 2026 (rough draft, not polished)  
**Re:** Your note asking to see the fix from the user's side before anyone touches the code

---

## The short version

The quick fix changes one number. What we'd actually build gives **handlers** and **responders** a way to see what a missed ping means, and gives a responder a way back after one. The code changes (scoring a miss separately, letting the score ease back, a floor, a one-time reset) ship together in 4.3 so that what people see on screen is actually true underneath.

**My recommendation:** don't ship the one-number change this afternoon. It puts responders back on the list but doesn't fix why they keep missing, and nobody can see that it happened.

*Responder names are replaced with Responder A–D until you decide whether cover names can appear in briefs (decision 4).*

## What a handler like Kip would notice

- Each responder card in the console shows **where they sit in the order and why**: "Missed 3 of the last 5 pings, so lower in the order for Harborside."
- When a callout moves on, the console says what happened: "No answer in 60 s. Went to the next responder. Counted as a miss, not a no."
- The console **flags a responder who has gone quiet** before the handler has to file a ticket, and flags callouts that went unfilled.
- Handlers see only their own responders, by cover name, and never anyone else's score (Security Policy 4.1).

## What a responder who's gone quiet would feel differently about

- A missed ping tells them plainly: "That one went to someone else. Missing it doesn't count against you like a no."
- After a quiet stretch, the app tells them "You're back in rotation," because misses now ease back over a few days.
- If someone keeps missing even when they're first in line, the app runs a quiet check ("Tap if you got this") that doesn't affect their score. This tells us whether their phone is actually getting the ping.

**Click-through:** [prototype.html](prototype.html) shows the console and the phone side by side. Some numbers in it are made up for the mock-up.

## Why this is happening

- **The code treats a miss like a no.** Our glossary records a ping as **taken**, **turned down** or **missed** (the ping wait ran out), but says both misses and turn-downs lower the score. The code does exactly that: −0.12 either way, with no recovery over time. Wen's 2019 note in the code asks whether the score should ease back: yes for "somebody who had a bad month," no for "somebody who has stopped taking work." *(Glossary; `history.py`)*
- **4.2 made that gap matter.** The ping wait went from 90 to 60 seconds. Misses rose from 2.3% to 18% of pings, while turn-downs *fell* (21% → 18%). Four responders (A–D) dropped down the ranking and had no way back, and their work moved to three others. Weekly acceptance went from 75–78% to 54% in the release week, then 66%, 67% and 73%. The four haven't recovered: since 19 Aug they miss 75% of pings even when first in line. *(4.2 release notes; rook.pings, not yet checked against Ravi's weekly report)*
- **This answers Marcus's 14 Aug question, pending Wen.** He asked whether the 4.2 ranking change was meant to apply to responders who'd been turning jobs down. Our working answer: 4.2 made the score count *less*, but because the code can't tell a miss from a "no," the shorter wait ended up penalising people who never said no. *(4.2 release comments)*
- **It likely explains much of what Support saw.** By 26 Aug, about ⅔ of tickets were "my phone never goes off" and ⅓ were "it was gone before I could answer." Nadia linked the second to the 60-second wait and couldn't explain the first. The ranking drop fits much of the first, but not all: two of the four have no tickets at all, and some areas with many tickets saw only small drops. *(4.2 release comments; support tickets)*

## What changes underneath (4.3)

| Change | Why people would notice |
|---|---|
| Score a miss separately from a turn-down, with a smaller penalty | "A miss isn't a no" becomes true, not just a message |
| Misses ease back over a few days; turn-downs stay until the next take | Covers both sides of Wen's 2019 note |
| A score floor, so nobody drops off the order entirely | A quiet card never says "no pings, no end date" |
| One-time reset for the four responders | They get pings again, but **only after we fix why they miss** (75% missed when first, against 16% for everyone else), or they'll drop straight back down |
| An alert when a callout goes unfilled | Handlers and support hear about it before a ticket comes in |

**What stays the same:** the 60-second ping wait and the 4.2 proximity weighting. This isn't a revert. Wide-area responders asked for the ranking change, and as Priya put it, reverting "just trades one angry group of responders for another." *(Handoff)*

## How we'll know it worked

- Misses back near 2–3% of pings (18% today), and unfilled callouts back to about 5.5% (11.1% today).
- The four back to first place on most of their home-area callouts (75–85% before 4.2, 4–16% now).
- "Phone never goes off" tickets back to their pre-4.2 level.
- Time-to-accept and coverage gap tracked too. We can't compute them from our data today, so Ravi would need to supply them.

## Who does what

| Person | Ask |
|---|---|
| **Wen Li**, Staff engineer | Confirm how the score works, and why the four miss even when first: slow answers, pings not reaching the phone, or wrong availability. |
| **Marcus Oyelaran**, Eng manager | Size the 4.3 package. Check whether scores reset when the service restarts. |
| **Sofia Marino**, Product designer | Turn the prototype into real screens. Check the overlap with the Handler phone app she's exploring. |
| **Nadia Hoffmann**, Support lead | Contact the handlers who wrote in. Review ticket themes once this ships. |
| **Ravi Menon**, Data analyst | Check our numbers against his weekly report, and supply time-to-accept and coverage gap. He wasn't in the 4.2 thread or the handoff. |

## Decisions I need from you

1. **Package, not patch:** hold the one-number change and ship the package in 4.3?
2. **Reset the four** once the cause of their misses is known?
3. **Owners:** every roadmap item still lists Priya. Can we agree new owners, and which items squeezed out of 4.2 (including Availability Confidence) are still Q3 commitments? Priya notes this conversation never happened.
4. **Naming:** can responders' cover names appear in briefs like this one under Security Policy 4.1?

## Questions you might have

- **What does waiting until 4.3 cost us?** Unfilled callouts have doubled (5.5% → 11.1%), and each callout takes more pings to fill (1.23 → 1.39). Callouts per day also fell, from about 20 to 17, but we don't know why yet.
- **Isn't this just the August dip?** We can't tell. Our data starts on 29 Jun, so we can't check earlier years, and the week of 3 Aug was the best on record.
- **What could go wrong?** A floor also keeps pinging someone who has really stopped taking work, which is why turn-downs keep their full penalty.
- **Does this affect Supply?** Supply reads the availability record Dispatch writes. The score changes shouldn't touch it, but I'll confirm with Wen before we design the "gone quiet" check. Four maintenance tickets (servicing booked on a bad day, late reminders) are still unexplained.
- **Did real users say this?** Yes. Ticket 3043 and Mr. Ambrose's interview describe the same callout vanishing mid-answer, and Aunt Dot and Mr. Ambrose both asked to be told when a callout for their responder is live.
- **How big is 4.3, when does it ship, and when will we know why the four miss?** Not known yet. Marcus is sizing the package, and the cause waits on Wen's answers, with no date yet.
- **What do we tell handlers until then?** Routing config only changes in a release, so nothing changes on screen before 4.3. Nadia and I would contact the handlers who wrote in and explain what we've found and what we're doing about it.
- **Is Availability Confidence the same idea as the "gone quiet" signal?** Possibly. It was committed to 4.2 but isn't in the release notes, and I haven't found any trace of it yet.

---

*Sources: rook-wiki Glossary (updated 4 Aug 2026), Team directory (updated 2 Sep 2026) and 4.2 release page and comments, checked live on 7 Nov 2026; `00-rook/company/notes/handoff-from-priya.docx`; `00-rook/code/dispatch-routing/history.py`; earlier analysis of rook.pings, support tickets and the September interviews.*
