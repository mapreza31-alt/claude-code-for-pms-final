# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Answer only the question the student asks, from the sources they
  name. Don't investigate beyond it or offer a theory about what
  Release 4.2 changed unless they ask for one.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch**. Priya left on 21 Aug 2026 without overlapping with me.
Sources: rook-wiki (About Rook, Glossary, Team directory, Releases, Q3 roadmap,
Product briefs) and `00-rook/company/notes/handoff-from-priya.docx`.

### Company
- Rook Industries sells coordination and provisioning software to independent masked
  responders and the handlers and quartermasters who support them. Rook employs no
  responders. Founded 2014, about 241 staff, HQ at Site Aleph, mostly remote.
- Revenue is a subscription priced per active responder.
- **Confidentiality is a hard rule:** Rook never stores or reconstructs a responder's
  legal identity, only capability tags, availability and callout history. Read Security
  Policy 4.1 before designing anything that touches responder records.
- The release train ships monthly as 4.x. Routing config ships *in the release*;
  handlers can't change it at runtime.

### Products
- **Rook Dispatch (mine):** a handler's web console plus a responder phone app. An
  incident comes in, responders are ranked by routing priority, the top one gets a
  ping, and they take it or turn it down; if they don't answer it's missed. Either way
  the callout moves to the next responder.
  Metrics: **acceptance rate** (headline, reported weekly), **time-to-accept**
  (median), **coverage gap**.
- **Rook Supply:** gear provisioning (requisitions → quartermaster approval →
  fulfillment → maintenance schedule; field failure reports). It *reads* the Responder
  Availability Record that Dispatch writes, so Dispatch changes reach Supply's
  maintenance scheduling with no change on their side.

### Vocabulary
- **Responder:** takes callouts; not an employee. **Handler:** looks after a responder
  and is the main console user. **Quartermaster:** owns stock and approvals (Supply).
- **Cover identity:** the public persona. Rook holds no link from it to a legal identity.
- **Callout:** a request to attend an incident. **Ping:** one callout offered to one
  responder. Its outcome is **taken**, **turned down** or **missed** (the ping wait ran
  out). Missed and turned down are counted separately.
- **Ping wait:** time before a ping counts as missed. The same for everyone, set per
  release.
- **Routing priority:** the ranking score. Inputs are proximity (travel-time estimate),
  availability, capability match and recent acceptance history. Turning down or missing
  a ping lowers your future rank.
- **Capability tags:** flight, structural-entry, hazmat-tolerant, cold-weather, aquatic,
  crowd-management, de-escalation.
- **Mutual aid**, also called "shared cover between responders": not supported yet;
  being explored for Q4.

### People (Dispatch team, as of 2 Sep 2026)
On the wiki, comments are signed with roles. This is who each role is:
| Role in comments | Person | Notes |
|---|---|---|
| Engineering manager | **Marcus Oyelaran** (Site Aleph) | Runs Dispatch eng. Priya: "start here." Can pull rough numbers. |
| Staff engineer | **Wen Li** (Berlin) | Built the ranking logic. Nothing about it is written down, so ask her. Prefers DMs. |
| Product designer | **Sofia Marino** (Site Aleph) | Console and phone app; ran the September interviews. |
| Support lead | **Nadia Hoffmann** (Berlin) | Owns tickets; hears from handlers first. Priya suggests a standing 15 min. |
| Director of Product | **Helen Achebe** (Site Aleph) | My director; owns roadmap and commitments. |
| Data analyst | **Ravi Menon** (Singapore) | Owns the official weekly acceptance numbers. Not in the 4.2 thread or the handoff. |
| (former PM) | Priya Raghunathan | Left 21 Aug. Marcus has her number. |

### Releases
- **4.0** (7 Apr): new console navigation, profile redesign, routing override audit log.
- **4.1** (16 Jun): proximity now uses a travel-time estimate, bulk callout, push reliability.
- **4.2** (12 Aug): proximity weighted up relative to recent acceptance history;
  **ping wait cut from 90 to 60 seconds**; console filters persist; three defect fixes.

### Where things stand on 4.2 (facts only; there's no agreed explanation yet)
- Since 4.2, fewer pings are being taken and handler complaints are up. On 18 Aug
  callout tickets were running at about 3× normal. By 26 Aug the split was about ⅔
  "my phone never goes off" and ⅓ "it was gone before I could answer." Nadia linked
  the second theme to the 60-second ping wait and said she couldn't explain the first.
  A handler emailed support directly, which is unusual.
- On 14 Aug Marcus asked whether the 4.2 ranking change was *meant* to apply to
  responders who have been turning jobs down. The config doesn't seem to tell them
  apart. **Nobody has answered.** Wen said she'd look once she was back on 24 Aug.
- Priya's view: the drop is mostly the usual August dip and should recover in
  September. Don't make this a conversation about reverting 4.2; the ranking change
  was asked for by responders covering wide areas. Nobody has pulled real numbers to
  check this. Marcus offered only rough ones, and Ravi's weekly report wasn't cited.
- On 28 Aug Marcus planned to "regroup properly on the 4.2 picture" about a week after
  the new PM started. I haven't found any record that it happened.

### Roadmap (Q3; last reviewed 30 Jun, so stale; every item still lists Priya as owner)
- 4.2 commitments: the ranking change and ping timeout tuning **shipped**.
  **Availability Confidence** (a confidence score next to a responder's stated
  availability) was committed to 4.2 but **isn't in the 4.2 release notes**.
- **Requisition approval chains** (Supply), committed for 4.3. The brief asks for a
  second approval over $2,000 but then piles on scope: a 48-hour reroute, a cross-site
  dashboard, handler visibility and replacing Halloran's spreadsheet. Phasing is TBD.
- Exploring for Q4: **Handler phone app** (brief from 8 Sep; Sofia is exploring it but
  it isn't staffed; scope is notifications, status view and one-tap contact) and
  **Shared cover** (mutual aid).
- Other briefs: **Bulk callout** (shipped in 4.1; its brief said to get a baseline for
  time-to-full-coverage first). **Routing override audit log** (shipped in 4.0).

### My open to-dos from the handoff
1. Agree with Helen which items squeezed out of 4.2 are still Q3 commitments
   (Priya says this conversation never happened).
2. Write down how Dispatch decides who gets pinged. There's no doc; Wen is the source.
3. Expect tickets about filter persistence. Priya calls them cosmetic noise.
4. Re-assign owners on the roadmap. Every item still says Priya.

- The rook-database has 5 tables (pings, callouts, support tickets, responders, handlers) covering 29 Jun–7 Sep 2026; the Research page holds only the 4 Sept interviews (Aunt Dot, Mr. Ambrose, Halloran, Kip).
- Weekly acceptance was 75–78% before release, 54% in the release week, then 66%, 67%, 73%. Misses jumped on 12 Aug and the week of 3 Aug was the best on record, which contradicts Priya's "August is always soft". With no data before 29 Jun, past years can't be checked.
- The data stores missed and turned down separately, but the code (`history.py`) penalizes both the same way. Recent-acceptance scores aren't in the database.
- The responders pinged least after 4.2 were Farlight, Meteor Mite, The Undertow and Vesper. Their handlers filed no tickets, so their drop shows only in the data and the interviews.
- Contradictions to raise: cover names are stored in the `responders` table, against "never stored"; the approval-chains brief clashes with Halloran's complaint about approval speed; the phone app brief goes beyond what Aunt Dot asked for; two tickets say maintenance was booked on a bad day.
- Still open: Availability Confidence (no trace anywhere), Ravi's weekly report, and time-to-accept and coverage gap, which can't be computed from the database.
