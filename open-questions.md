# Spoon — what still needs deciding

Answers folded in as they arrive. Answered items keep their reasoning so the
decision doesn't get relitigated.

**All answered** as of 13 Sept 2026, except 13, which is parked deliberately —
the calibration loop can learn it.

---

## 1. Scope & platform — all answered

**1. Who's it for?** — **Just you, for now.** No accounts, no onboarding for
strangers, no support burden. Numbers can be tuned to one body.

**2. Native or web?** — **Native iOS.** SwiftUI, EventKit, and
`EKEventEditViewController` for the write handoff. The HTML prototype is **the
spec, not the foundation**.

**3. Does data leave the device?** — **No.** Local-only.

**4. Sync?** — **No.** One device.

## 2. Onboarding — a quiz, not an archaeology dig

**5. Calendar connection at setup?** — **Yes, connect the real Google Calendar.**
*Shortcut worth knowing:* add the Google account to iOS Calendar and **EventKit
reads it** — no Google OAuth, no API client, no verification process. Only worth
going direct to Google's API if EventKit turns out to be missing something.

**6. Anchor weeks from calendar or memory?** — **Both offered.**

**7. How is the baseline set?** — **A short quiz at the start.** Now has to
cover: max capacity, the unit anchors (a shower = 1), and chronotype (see 11).

**8. No usable history?** — **The app says: use it for two weeks and come back.**

**9. Starter cost library?** — **Yes, ship one.** Also the main defense against
the labelling burden in 16.

## 3. The model

**10. One battery or four?** — **One.** Cross-type depletion is real (physical
exhaustion bleeds into everything). Types are annotations driving a read-only
diagnostic and recovery matching. *Saturation* parked in §5 as the upgrade path.

**11. Does time of day change cost?** — **Yes — asked in the quiz.**
Chronotype gives a multiplier curve across the day: the same meeting costs more
in your bad hours. Shows up beautifully on the day-view gradient, where a late
event visibly drops you further than the same event in the morning.

**12. Does venue change cost?** — **No.** Too granular for a personal build.
Parked, not deleted.

**13. What's a blank evening worth?** — **Still unsure — and you may not have to
decide.** This is exactly what the calibration loop is for: the check-in
correction teaches the recovery rate from your own data (§7). Suggest shipping a
placeholder and letting it learn. Currently 0.022/hr, capped at 0.10/day.

**14. Does max drift?** — **No.** 100% is the best you'll ever feel, by
definition. Consistent with "max is the top of the scale" (§4).

**15. Flare handling?** — **Low charge.** Keep it simple; no second ceiling.

**16. Retro-tagging window?** — **Two weeks — but the real answer is "don't make
me label everything."** See *The labelling burden* below; that's a design
requirement, not a window setting.

## 4. Calendar — all answered

**17. Which calendars count?** — **You pick.**

**18. All-day events?** — **They count.** Caveat: birthdays and labels are
all-day too, so they still get priced like anything else — a birthday you're not
attending prices at 0 once and stays there.

**19. Travel between events?** — **Counts, and gets flagged in advance.**
*Dependency:* travel isn't on the calendar. It needs event **locations** to be
filled in to be inferred at all; otherwise it's manual. Worth checking how many
of your real events actually carry a location.

**20. Declined / tentative?** — **Declined ignored; tentative counted at a
discount.**

**21. When does it notify?** — **Only right after you create an event.**
*One thing to check:* the post-event "how did it go?" was designed as a
notification an hour after the event ends. Under this rule it's in-app only — as
currently built. That works, but see *The labelling burden*.

## 5. Tasks

**22. Do tasks recur?** — **No.** Scheduling laundry is itself cognitive load.

**23. Can a task be split into parts?** — **Yes, manually. No generated
suggestions.**
You add your own subtasks. What the app contributes is **the nudge**: if a task
sits untouched for a while, it gently offers *"want to break this into smaller
chunks?"* and you fill them in. That drops the on-device-model dependency
entirely, and the nudge is the actual insight — a task sitting untouched usually
means it's too big or too vague, and naming that is the help.

*Recommendation:* once a task has subtasks, **the subtasks are what appear in
"what fits right now"**, with the parent as a context label. That's the whole
point of breaking down — a 4 doesn't fit, but the 1 inside it does. No progress
bars, no completion counts.

*This also answers the old "what happens to a stale task" question:* nothing
ages out. It gets offered a breakdown, once, gently.

**24. Categories** — **deadline / ASAP / whenever**, where *deadline* means a
genuine hard date only: taxes, filing paperwork. Not self-imposed urgency.

Because real deadlines are rare, the useful behavior is capacity-aware rather
than punitive: not *"overdue"* but ***"taxes are due in 9 days, and you have
three days before then you could afford it on."*** Categories order things
within what already fits; they never nag and nothing goes red.

*Open sub-question:* does **ASAP** still earn its place? Soft-urgency tiers tend
to inflate until everything is ASAP, at which point it means nothing and only
generates guilt. Deadline-vs-everything-else may be the whole system.

## 6. Recovery & payoff — open

**25. Repertoire?** — **Both:** a starter list plus your own additions.

**26. Schedule recovery?** — **Yes, it suggests when.**
It already knows which days have room. Creating a calendar block is also the
*well-supported* deep-link path (§10), so this is the easiest write there is.

**27. Export / summary to show someone?** — **No.** Not needed.

**28. Is the forecast enough of a payoff?** — **Yes, it's the most helpful
part.**
Settles a lot: **no patterns screen**, in v1 or later. §11's "what it gives
back" is the forecast itself. The type diagnostic is as far as insight needs to
go. The app stays small.

## 7. Notifications

**29. Notifications** — **two: on event creation, and after an event to ask how
it went — but the second one decays.**
Refines 21. **The decay rule falls out of machinery that already exists:** stop
asking about an event name once it has enough consistent history to flag from
(3+ samples, agreeing). The app already computes exactly that. So it asks about
unfamiliar things, keeps asking where your ratings are inconsistent, and goes
quiet on the stuff it has learned. **The better it knows you, the less it
speaks** — which is the right direction for an app this audience will otherwise
mute. Cap it at one or two a day regardless.

**30. Unprompted warnings?** — **In-app only, never a notification.**

## 8. Accessibility

**31–33.** — **All parked while this is single-user.** Recorded as the work
that has to happen first if it ever reaches anyone else: color is currently the
only channel carrying affordability; dark-only causes halation for some readers;
and reduced motion / dynamic type / screen reader are not optional for an app
aimed at disabled people.

---

## The labelling burden — the requirement behind 16 and 21

*"Ideally I wouldn't want to have to label everything manually."*

Three things have to carry this, and two already exist:

1. **The starter library (9)** covers common things from day one.
2. **Recurring events price themselves.** Once an event name has enough
   consistent history, new instances should **inherit the cost silently** and
   show as inferred rather than unpriced. *Not built — the sheet suggests the
   historical mean, but still waits for confirmation.*
3. **One thing at a time.** The ask-on-open handles new events singly rather
   than presenting a backlog.

**Resolved by 29:** the post-event notification is back, with decay — so the
history builds quickly at first and the app falls silent on anything it has
learned.

**And reversed by a better insight:** delayed rating is *more* accurate for this
body, not less — processing takes time, and an event's real cost includes
payback that arrives later. So the ask moves to **next morning, batched**, "not
now" means *later* rather than *never*, and past days stay correctable. See
concept.md §7.

## Settled — for reference

No gamification. Battery not budget. Charge floors at empty, debt as recovery
time. One user-chosen scale everywhere. The app never writes to the calendar.
Tasks are untimed events filtered by what fits. Single user, native iOS,
local-only. One battery. Max never drifts. Flare = low charge. No recurring
tasks. Task categories order, never nag. Notifications: event creation, plus a
decaying post-event ask, batched next morning. Unprompted warnings stay in-app.
Subtasks are manual, nudged not generated. No export. No patterns screen — the
forecast is the payoff.
