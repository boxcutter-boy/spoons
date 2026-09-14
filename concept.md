# Spoon — concept & model

_Working notes. Brainstormed 11 Sept 2026. Nothing built yet._

A spoon-theory app for planning and recovery, built around a battery — not a
budget, not a game, not a habit tracker.

---

## 1. What it is

A tool you open **when you're worried about the week ahead**, or **when you're
already wrecked and need to decide what to drop.**

It is not a daily-habit app. Someone should be able to open it after three weeks
of silence and get an immediately useful answer. The calendar keeps accruing
useful data in the background whether or not they ever visit.

One sentence, the whole app:

> _You're at 22 of 30, going into a week that needs 26._

## 1b. Who it's for and what it runs on — settled

- **One user: Ferri.** Not a product, for now. No accounts, no onboarding for
  strangers, no starter library, nothing that has to survive a stranger.
- **Native iOS, SwiftUI.** So EventKit for reading calendars (one permission
  prompt, covers iCloud/Google/Outlook together) and
  `EKEventEditViewController` for the write handoff in §10 — which is what lets
  it edit *existing* events rather than only create new ones.
- **Local-only.** No server, no sync, no data leaving the phone.
- **The HTML prototype is the spec, not the foundation.** None of it ports.

The audience thinking below still stands as *rationale* — it's why the app is
shaped like this — but it's no longer a spec for strangers.

## 2. Who it's for

Neurodivergent people who are externally coping — "high functioning" — and who
already keep a calendar. That audience choice does real work:

- **The entry list already exists.** Never make a calendar-keeper type their
  week in twice. Anything that demands double entry gets abandoned in nine days.
- **They systematically overestimate capacity.** Chronic overcommitment is the
  thing being designed against. Someone who accurately knew their limits
  wouldn't need this.
- **Their depletion is invisible to others.** Masking, transitions, sensory
  load, and executive cost don't show up as obvious exertion — so they don't get
  budgeted for, by them or anyone around them.

## 3. Design commitments

Settled, not up for re-litigation:

- **No gamification.** No streaks, no points, no score, no badges. Standard game
  mechanics reward doing more, which is the harmful direction for this group.
- **No guilt language.** Never "you overdid it." Overspending is information and
  gets stated as a forecast adjustment, not a failure.
- **Low effort to log.** Costs are one tap. The app guesses and gets corrected;
  it never presents a blank form.
- **Rest is a first-class entry**, never a gap in the data.
- **Works with incomplete data.** Estimates come from a starter library plus
  whatever has been tagged before. Cold opens still produce a real number.
- **No nagging.** No "you haven't logged in 5 days," no empty-state shame.
- **Not a wellness app.** It does arithmetic and reflects the user's own stuff
  back at them. It never prescribes, and never suggests anything that isn't
  already on their own list.

## 4. Core mechanic: the battery

Not a weekly allowance. A rechargeable charge level.

- **Max capacity is a personal constant.** Set once at setup; stable furniture.
- **Charge swings.** It drains with load and refills with recovery.
- **Charge is continuous — it never resets.** No Monday zeroing. A bad week
  hands its deficit to the next one, which is why bad fortnights compound.
- **You can't bank.** Max is a ceiling. Unspent capacity doesn't accumulate.
- **Debt carries, savings don't.** This asymmetry is the honest mechanic and the
  reason a weekly-allowance model can't represent real ND life.
- **Charge never goes negative.** It floors at empty and stays there. You can't
  have negative energy — you run on empty and pay in time.
- **Recovery is non-linear.** 24 → 28 is an evening. 8 → 20 is days, not a nap.
  A curve, not a flat rate — the difference between estimates that feel true and
  estimates that feel insulting.

**The week is the lens, not the accounting period.** It's the planning horizon
because that's what a calendar shows and what people actually worry about. The
battery is the state.

### One scale, chosen by the user

**Everything in the app is displayed in a single unit, and the user picks it.**
Charge and costs always share a scale, which removes the confusion of having two
comparable-looking numbers on screen.

Offer at least:

| Scale | For |
|---|---|
| **X/10** | Default. Matches how people talk about energy. Coarse for pricing. |
| **X/12** | People attached to the spoon count — twelve is the canonical number. |
| **X/100** | Percentage thinkers. Fine granularity, slightly clinical. |
| **No numbers** | Battery, zones and blurb only. For anyone who'd fixate on a 3. |

**The scale and its *units* are two settings, not one.** They were fused — the
third option used to read "12 spoons" — which meant choosing a granularity and
choosing what to call the things were the same decision. Split: **Scale** says
how many, **Scale units** says what they're called — *spoons / energy / mana*.
The name is the part that makes the number feel like yours, and it has no
business being locked to a count of twelve.

**Three words, no write-in.** There was a fourth option — *your own*, with a text
field — and it went. Three words already cover the ground: the borrowed metaphor,
the plain one, and the playful one. A free-text field bought very little and
charged for it everywhere downstream: every sentence that names the unit had to
survive a noun nobody had seen, which meant guessing plurals from a trailing "s"
and writing around agreement. With a closed set, `unitPlural()` is a lookup, and
copy like *"Your spoons **run** out"* / *"Your energy **runs** out"* is just
correct. Configurability that makes the copy worse isn't a feature.

The unit shows in the places the app states your level — the charge on the
forecast, the check-in readout, the day summary — and nowhere else. Repeating it
on every cost stepper would be noise.

**Under "no numbers" the whole Scale units section disappears.** There are no
numbers to have units, so naming them is a setting with nothing behind it.

That last one isn't a novelty. Part of this audience will genuinely spiral over
a low figure, and a purely qualitative mode carries the same information without
handing them something to stare at.

**Max is the top of the scale.** On /10 you're always out of 10. This deletes
the hardest question in setup — nobody has to estimate their capacity, because
full is definitionally full. It also makes capacity non-comparable between
people: everyone is out of 10, and only the *costs* differ. Worth having, for a
group prone to measuring themselves against others.

**Round for display only.** Store one canonical high-resolution value and render
it to the chosen scale. Rounding as you go means five activities each losing 0.4
and a day's spend that visibly doesn't add up — the classic budget-app bug where
the parts don't sum to the total. Compute totals unrounded, round once at the
end.

**A scale can be too coarse to plan with.** On /10, a shower and a work social
compress into a 1–5 range and lose the distinctions that rehearsing a week
depends on. Natural check: if the two anchor weeks land only a couple of points
apart on the chosen scale, that scale can't support planning, and the app should
say so.

**/100 implies precision the model doesn't have.** Either step it (nearest 5) or
accept that the resolution is cosmetic.

### Empty is a floor, and debt is measured in time

Overspending past empty must never show as a negative number. Nobody can act on
"−8 spoons," and being shown one while already wrecked is the exact failure this
app exists to avoid.

The deficit doesn't disappear, though — it moves into **recovery time**:

> **You're at empty.** Anything you spend now comes out of recovery — about four
> low-demand days to get back above 20.

Same information, but stated as a climb rather than a hole. Actionable, and it
keeps the compounding truth from §4 intact: the week that overspends hands a
longer recovery to the next one.

### Zones, not a line

Approaching the number is the warning, not the goal — this is a ceiling, not a
target. So: a comfortable zone, a **"doable, but you'll be paying for it"** zone,
and over-the-line. Most real weeks live in that middle band, and naming it
honestly ("this fits, but plan Sunday as a write-off") beats a number going red.

### Reading the number in plain language

The number on its own is abstract. Every charge reading is paired with a short
blurb that says what it actually means:

> **You're at a 7.** Good charge, and the week as it stands fits — taking it
> easy is still the smart call.

**With no number, the state phrase becomes the heading.** The 64px figure is the
screen's headline; take it away and the page opened on a bar and a paragraph,
with nothing to land on. So an eight-step ladder in the same tone as the rest of
the app — *One thing at a time / Let's take it a day at a time / Go gently today
/ There's not much spare / Enough to work with / Feeling good / Feeling great* —
sits **above** the bar, where the number was — the phrase is the headline and the
bar is the detail under it, not the other way round. Both open Settings: they're
two halves of one statement, so tapping either should lead to the same place.
It uses the **same bands as the check-in's readout word**, so the home screen and
the slider can never describe one charge two ways.

**The forecast tracks the check-in slider as you drag it**, rather than waiting
for release. The old rule — never re-render mid-drag — was protecting the
*sheet* (a redraw takes the thumb with it); applying it to the whole app left the
screen behind the sheet stale while you were deciding, which is exactly when you
want to see what the number would mean. The sheet's readout is still patched in
place, and the views redraw once a frame.

**And the blurb drops its own opener when the headline is showing.** The blurb
always led with a state phrase ("Middling.", "Good charge,") because it was
carrying that job alone; with a headline above it, saying it twice reads as a
stutter. Same sentence, one opener.

**Two scales, on purpose.** You *speak* in 1–10 — it's how people describe their
energy, and it's already the check-in slider's scale. The app does *arithmetic*
in spoons. Same as a battery showing both "70%" and "about 3 hours left": the
/10 orients you, spoons buy things. The blurb is where the /10 lives.

**It reads three inputs, not one.** A 7 with a light week ahead and a 7 with a
brutal one mean opposite things, so the sentence is a function of:

1. **Charge** — where you are now.
2. **Upcoming load** — does the week as scheduled fit in what's left?
3. **Direction** — a 7 climbing out of a crash is reassuring; a 7 dropping fast
   is a warning. "Steady," "climbing," "dropping."

**Sentence shape:** _state → what it means for what's coming → permission._
Three clauses at most, and clauses get dropped as charge falls.

| Charge | Voice | Draft copy |
|---|---|---|
| 9–10 | Plain, no push | "You're at a 9. Plenty of room in the tank for the week you've got planned." |
| 7–8 | Reassuring, light caution | "You're at a 7. Good charge, and this week fits — taking it easy is still the smart call." |
| 5–6 | Honest about slack | "You're at a 5. Enough for the week as planned, but there's no slack in it. A quiet Sunday would buy you some." |
| 3–4 | Arithmetic, no alarm | "You're at a 3, and this week is asking for more than that. Worth looking at what could move." |
| 1–2 | Short and quiet | "Running very low. Just today, then." |
| Empty | Factual, no blame | "You're at empty. Anything you spend now comes out of recovery time." |

**At 1–2 the score disappears.** Consistent with recovery mode in §8 — a bleak
number to stare at is not useful information when you're crashed. Present tense,
practical, one thing at a time.

### Tone rules for the blurb

This is the highest-risk copy in the app: it's a sentence about someone's body,
generated by a guess.

- **Descriptive, not diagnostic.** "Your charge is low," never "you're
  exhausted." The app doesn't know that.
- **Permission, not instruction.** "An easy day would be well earned" rather
  than "you should rest." Nobody needs an app's permission, but being handed it
  lands very differently from being told.
- **Hedge the app's confidence where it's earned.** "Based on what you've
  logged" is honest. Immediately after a check-in the app *does* have a
  self-report and can reflect feeling back; days later it should talk about
  charge and arithmetic, not feelings it's inferring.
- **Quieter as it gets worse, not more concerned.** An app fussing over you
  while you're crashed is another thing to manage.
- **No cheerfulness at low charge.** Upbeat copy at a 2 reads as mockery.
- **Never congratulate a high number.** A 9 isn't an achievement and a 3 isn't a
  failure — it's weather.

## 5. Costs are typed

A day can be physically trivial and executively devastating. One number can't
express that, and typed costs are what make the patterns actionable.

- **social** — people, masking, being perceived
- **sensory** — noise, light, crowds, texture
- **executive** — decisions, transitions, admin, task-switching
- **physical** — the body stuff

**Sensory often belongs to the venue, not the activity.** Same lunch, same
people — one restaurant costs 2, the other costs 6. So sensory cost can attach
to place/context, which eventually lets the app pre-warn: _"anything at the
office runs +2 sensory for you."_

Kept cheap: **one cost by default, the type tag as one optional extra tap.**
Pattern data without turning logging into data entry.

### One battery, not four — settled

**Types are annotations. They change no arithmetic.**

The reasoning: depletion bleeds across types. A physically wrecked day leaves
you less able to do *anything*, not just less able to lift things — so a single
charge is an honest model, and four separate tanks would be a fiction dressed as
precision.

The cost of four batteries was also concrete: the forecast becomes four curves,
"can I afford Thursday" gets four answers, logging demands a type every time,
and *"you're at 22 of 30 going into a week that needs 26"* stops being sayable.
That sentence is the app.

**What types do instead:** a **read-only diagnostic** — four greyscale bars
showing what the week ahead asks of you, ordered, with a line like *"Social is
over half of what the next 7 days ask of you."* No arithmetic, no second number
to track. Greyscale deliberately, so it never competes with the red/amber/green
affordability signal. Types also still drive recovery matching (§7).

**Considered and parked: saturation.** One battery, but recent same-type load
raises the *exchange rate* — after a socially heavy week the next social thing
costs more than its base price. It's truer to masking fatigue and needs no
second number on screen. Worth revisiting if the flat model starts feeling
wrong; not worth the complexity yet.

## 6. Where the numbers come from

### Max capacity — largely moot now (see §1b)

The apparatus below exists to derive a *stranger's* capacity without asking them
to know it. For a single user who knows his own weeks, setup is "set the numbers
and adjust them." Kept for the reasoning, and for if this ever goes wider.

The one piece still worth doing: **anchor the unit to something concrete** (a
shower = 1), so the scale doesn't drift over months.

### Max capacity — anchor on remembered weeks, not an abstract number

Two failed approaches, for the record:

- _User declares a number_ → aspirational, and unanswerable in the abstract
  ("35 of what?"). The app then cheerfully confirms that next week fits.
- _Derive from logged spend_ → **spend is not capacity.** The weeks you spent 40
  include the weeks you spent 40 and then collapsed. The app would learn your
  overdraft and call it your income.

**The only data that reveals capacity is the stretches that ended okay.** So
setup walks through two real weeks pulled from their actual calendar: one that
felt manageable, one that wrecked them.

Because max is the top of the chosen scale (§4), those weeks aren't used to
discover a capacity *number* — they **size the costs** as fractions of a full
tank. The wrecking week sums to over full; the manageable week sums to under.
Everything else calibrates from that bracket. Same three minutes, easier
question, works on day one with zero history.

### Anchor the unit too

A spoon is meaningless until it's pinned to something real. At setup, name one
thing that costs ~1 (a shower, a load of laundry) and one that costs ~5 (a work
social, a day of errands). Every other estimate is relative to something they
actually know — personal scale without arbitrariness.

### When the app disagrees with your estimate

The calibration loop, made visible. If history disagrees with what you just
typed, the app says so — with its evidence.

- **"You've put these at about 4 afterwards, 4 times running — not 2."** Shown
  in the cost sheet, with a one-tap *Use 4*.
- **It only speaks with grounds:** at least 3 past instances, a gap worth
  mentioning, and a consistent direction. One sample claiming "actually it's
  higher" is noise, and an app that cries wolf gets muted.
- **The two directions are different messages.** Underestimating is the
  dangerous one — that's the overcommitment mechanism. Overestimating gets the
  kinder line: *"these usually land nearer 2 — less than the 3 you're bracing
  for."* That's permission, not a warning.
- **Advisory, never a silent override.** An explicit estimate is yours. Where
  learning *is* applied automatically is **unpriced** events, which open at the
  historical mean — there's no choice there to overrule.
- **Unprompted, on the forecast:** a *Worth a second look* card lists upcoming
  events whose estimates read low, because those are the ones that change the
  week — with the arithmetic attached: *"taken at those rates, Thursday needs
  about 3 low-demand days to clear rather than 1."*
- **Flagged events carry a small dot** in the agenda and day view, so they're
  spottable without opening each one.

### Rate it late — processing takes time

**Delayed ratings are more accurate here, not less.** Working out how something
landed takes a while, especially when overwhelmed — and the cost of an event
isn't only how it felt at the time. Much of it is payback that hasn't arrived
yet: you can leave dinner feeling fine and be flattened next morning. **An
immediate rating systematically misses that.**

So the backlog is not a fallback for ignored prompts. It's the better path.

- **Ask the next morning, batched** — one prompt covering yesterday, not a ping
  per event. Fewer interruptions *and* better data. With the decay rule (§ask),
  some mornings it says nothing at all.
- **"Not now" means later, not never.** Deferring is the normal case. It returns
  on the next open, and only drops into the silent backlog after two or three
  passes.
- **The lag applies to the charge reading too.** A slider set while overwhelmed
  is soft. Hence being able to correct *past* days — the calibration loop
  pointed backwards — within the two-week retro window.

**The limit:** attribution blurs with time. Next morning you can still say
*which* thing cost you; a week later you're rating a mood. Next-morning is the
sweet spot; two weeks is the outer bound for *corrections*, not for first
ratings.

### Where the actuals come from

The history has to grow from real use or the flags never earn their authority.

- **An overlay when you open the app**, not a card parked at the top of the
  screen. "How did Coffee with Jo go? You put it at 2 beforehand."
- **A slider from *easier* to *harder*, not three buttons.** The three words were
  a forced choice between coarse buckets; a track lets you say "somewhere between
  about right and harder", which is usually the honest answer. The three labels
  stay as the anchors under it.
- **Nothing is filed until Done.** Committing on touch made the overlay vanish
  mid-gesture — you never saw where you'd left the thumb, and rating a second
  event was impossible. Touching a slider instead marks that row and says the
  answer back in words beside the name (*harder*, *about right*); **Done** files
  every row you touched and leaves the rest in the queue for next time.
- **One tap is still enough to agree.** A tap that doesn't move the thumb counts
  as an answer — that's why the row listens for `click` as well as `input` —
  so *about right* costs a tap and Done, not a drag away and back.
- **A centred window, not a drawer.** Every other panel slides up because you
  reached for it; this one is the app asking *you* something, so it arrives as a
  dialog in the middle of the screen. *(A closed drawer is off-screen and harmless;
  a closed window is still sitting in the middle at opacity 0, and opacity doesn't
  stop clicks — so the sheet is `pointer-events:none` unless it's open. Without
  that, taps meant for the arc were landing on an invisible **Done**.)*
- **One question on screen at a time.** Two sliders at once is a form to fill in.
  Answering one brings up the next, and the footnote says how many are left
  (*"One more after this."*) so the end is always in sight — nothing is hidden,
  but nothing is stacked up in front of you either.
- **"Not now" ends the whole run**, at any point, in one tap. That's what keeps
  the chain from being an interrogation: the exit is always the second button.
- **Dismissable** — "Not now", or tap outside — and it never asks twice about
  the same thing.
- **No backlog list.** There was one on the Log screen — *"How did these land?"*
  with a row per unrated event — and it's gone. The app already asks on open;
  asking the same question again in a second place is exactly how a
  low-cognitive-load app stops being one. The cost is real and accepted: an
  event you decline three times is never asked about again, so it never joins
  the history.
- In a real build the same question is a **notification an hour after the event
  ends**, answered from the lock screen without opening the app at all.

### Calibration loop — one pattern, used everywhere

Estimate → actual → the gap quietly improves the estimate. Nothing is scored.

- Event costs: estimated at creation, corrected at reflection.
- Recovery rate: inferred from calendar white space, corrected at reflection.
- Charge level: computed by the model, corrected by the human slider.

Consistency matters here — it's one learning loop, not three separate systems.

## 7. Recovery: inferred *and* confirmed

The app credits unscheduled time as recovery at a discount. The reflection
corrects it, and **the corrections teach the rate** — say "that weekend did
nothing" a few times and blank evenings get credited less; if Sunday mornings
alone reliably move the charge, those get weighted up.

This matters because inferred recovery is magic when right and infuriating when
wrong. A blank Thursday evening spent doomscrolling and arguing was not
restorative, and an app that credits it 3 spoons loses trust immediately. The
inference doesn't need to be right on day one — only correctable.

### Sleep is the recharge, not a separate input

Sleep isn't another metric to track — it **is** the nightly refill the battery
already assumes. Normally it's invisible. A bad night simply means the recharge
didn't happen, so the credit the model was about to apply gets withheld.

- **Logged by exception only.** One tap, and only when it was genuinely bad. No
  nightly prompt, no sleep score, no hours.
- It shrinks the day ahead rather than recording the night behind.
- No new mechanic required — it's a missing refill, not a new cost.

### The repertoire comes from them

**Ask what they find restorative** — at setup, and addable any time. Everything
the app ever suggests is drawn from that list, which is what keeps it out of
wellness-app territory: it can't tell anyone to try yoga, because it only knows
what they told it.

It should also grow passively. If the journal shows charge climbing after
something they did, offer to add it — opt-in, never silently.

**A personal recovery repertoire**, typed to match the currencies above, built
from their own answers and their own history.

### Two kinds of recovery, and they behave differently

The model only had one — passive rest, suggested once you're already depleted.
The quiz surfaced a second: *"gently nudged to schedule a massage, a museum day,
a hobby day."*

| | Passive | Planned |
|---|---|---|
| e.g. | lie down in the dark, walk alone | massage, museum day, hobby day |
| available | now | needs a slot, often booking ahead |
| suggested | when you're depleted (triage) | **ahead of time**, when a day with room is coming up |

**"Room" means the day is largely empty and won't be pushed over — not that
you're thriving.** Booking something restorative while depleted is precisely the
point of it, so the test is emptiness, not a green day.

**The list is yours, not a fixed set.** The three seeds (massage, museum day,
hobby day) are only seeds — a "+ add" on the card opens the full list, where you
name your own, say how much it gives back, what kind it is, and roughly when you
tend to do it. Anything in the list becomes a one-tap booking. The app should
never be limited to restorative things someone else guessed at.

This is also the app's only feature that points somewhere good. Everything else
is about limits; this is the one that says *"Sunday has room, and nothing in it
yet — worth putting something good in while it's still free."* Dismissable per
week, and it goes through the same calendar handoff (§10), entered as a
restorative event so the forecast counts it in your favour. If you're socially bankrupt but physically fine, a solo
walk restores you and a "quiet dinner with two friends" does not — even though
both read as restful. Generic rest advice fails because depletion is typed.

## 8. Three modes — the horizon shrinks as the battery drains

One rule, three surfaces. Charge level on open routes you to the right one.

### Week ahead — planning (decent charge)

The "can I afford next week?" tool. Upcoming commitments with estimated costs
against available charge, and the ability to **rehearse changes** non-
destructively: move this, drop that — does it fit now? A what-if on your own
week.

### Triage — recovery decisions (low charge)

Nobody needs an app to tell them to rest. This does the arithmetic they can't
face doing:

1. **Price the recovery.** "At 8 of 30, you've historically needed about three
   low-demand days to get back above 20."
2. **Show the collision.** Next week already contains 14 spoons of commitments,
   so recovery is not going to happen by default. Here's the proof.
3. **Name the specific thing to renegotiate.** Not "do less" — _"Thursday's
   dinner is your most expensive movable commitment at 6; move it and you land 2
   under instead of 8 over."_ People avoid this decision because they can't see
   the tradeoff clearly. Making it one legible comparison is the whole job.
4. **Defend the space.** Write the recovery block into the calendar so the gap
   doesn't get filled by Wednesday.

### Burnout mode runs on Ferri's own recovery tree

Source: a flowchart Ferri made before this project, photographed 13 Sept 2026.
The creative branch is excluded. Categories: Physical, Sensory, Mentally
taxing, Emotionally taxing, Social, Directionless — plus an *Overwhelmed* state
split into frozen and anxious.

**Two things it exposed that the model had wrong:**

1. **Depletion doesn't always mean "needs rest".** The tree splits each category
   into *too much* and *too little* — ran around all day → lie down, but sat
   still all day → move; overwhelmed by people → alone time, but lonely → reach
   out. Half of these branches prescribe doing **more**. The app's recovery
   logic assumed rest was always the answer, which is wrong for those.
2. **The helping taxonomy needn't match the costing one.** The tree includes
   *emotional* and *spiritual*, which aren't cost types. That's fine — what
   drains you is tagged four ways; what helps you doesn't have to be.

**Available at any charge, not only at the bottom.** *"What kind of tired are
you?"* is the first thing in the log dialog's optional half, because you can be
at a 7 and still be a specific kind of tired, and the app shouldn't have to
decide you're crashed before it will help. Its subtitle makes the point:
*"However it feels — the numbers don't have to agree."*

**The flow:**

1. **Check all that apply** — tiredness isn't one thing at a time, and picking
   only one would misdescribe most bad days.
2. **Follow up only where it's needed.** Physical and Social split into too much
   / too little, so those get a question each ("2 to go"). The others go
   straight through.
3. **The chart**, grouped by what you picked.

*Not sure* hands it to the same inference burnout mode uses.

**Anything answering two kinds of tired at once is hoisted to the top** under
*"Covers more than one"* rather than printed twice. Pick socially drained and
emotionally wrung out and *Alone time* surfaces as the highest-leverage thing on
the list — which is real information, not just de-duplication.

**The chart is the deliverable, so it stays on the forecast** for the rest of
the day, with *check in again* and *clear*. You shouldn't have to hold it in
your head or re-run the flow to see it again — and needing to is exactly the
state it's designed for.

**How it's used, at the bottom band:**

- **Permission first,** before any list: *"Give yourself permission to stop what
  you're doing and breathe."* Straight from the Frozen branch.
- **One guided step, not a walk down a tree.** Three levels of questions is too
  much when crashed — so the app infers the branch from load it already tracks
  (which type went, and whether a type has been hammered or *neglected*) and
  shows that leaf directly. One tap to a list.
- **Corrigible in one tap:** *"no — lonely, not enough that meant anything"*
  flips to the opposite branch, and **"See what can help"** opens the whole
  check-in. Same estimate-then-correct move used everywhere else in the app.
  Once you've answered it, the tailored chart replaces the guess.
- **And an offer to book recovery into a day that still has room.** Stripping the
  week away must not strip away the one forward-looking thing worth doing while
  crashed: arranging something good is exactly what you can't face doing
  unprompted. *"Room on Sunday — Sunday has room, and nothing in it yet."*
  This required fixing what counts as room: the test used to also require the day
  not to end over, which silently disabled the feature at the bottom band, since
  when you're crashed every day ends over. A near-empty day is only over because
  of where you're *starting* from, and what gets booked into it gives energy
  back — so emptiness is the whole test.
- **The anxious branch is the app itself.** *"Prepare for the week ahead"* is
  what the forecast does, so that branch points back at it.

Everything suggested is Ferri's own writing. The app invents nothing.

### Recovery mode — day by day (severe)

When things are bad, a week view is actively harmful: seeing next week when you
can't manage tomorrow is paralysing. So the horizon collapses.

- **Today only** — ideally just the next thing.
- **Deficit numbers hidden.** "You're 22 underwater" is not useful information
  when crashed.
- **Cost-estimate prompts suppressed.** Being asked to price events is the last
  thing needed here.
- Shows: what's on today, what can be dropped, one recovery action from their
  own list, and a day with room to put something good into.
- **Tasks are hidden, help is not.** The list of things you owe the world is the
  right thing to take away here; the things that might make you feel better are
  not. An empty screen is not a restful one.
- **The check-in survives too.** It was being stripped along with the rest of the
  furniture, which made the one state you most need to correct the one state you
  couldn't tell the app about — and since the check-in is how the charge goes
  back up, burnout mode was a room with no door out of it.
- **Entry is suggested, never forced** — an app declaring you to be in crisis is
  patronizing. Also manually enterable, because the user usually knows before
  the data does.
- **Exit is quiet.** The horizon widens back out. No "you're back!" fanfare.

## 9. The check-in — a handshake, not a chore

Since the app gets opened occasionally rather than daily, the reflection *is*
the open. *"It's been a minute — quick, where are you at?"* Deliberately not
"it's been twelve days": a count of the days you didn't open an app is a small
reproach, and this one's whole premise is not adding those.

**It opens as a dialog over the forecast, from the pill marked *Check in*.**
Correcting the number is what you do *because* of what the forecast just told
you, so sending you to another screen to do it taxed the app's main loop; the
dialog keeps the number you're reacting to on screen behind it.

**Check in and Log answer different questions, and stay apart.** The check-in is
*how you are* — the slider, what kind of tired, bad sleep. The Log is *what
happened* — the things that cost you something and were never on the calendar.
Briefly they were one screen, and it answered neither question first. The
check-in ends with a quiet way across: *"Something happened — log it."*

1. **Where's your charge right now?** (slider — the human correcting the model)
2. **How did that stretch leave you?** (fine / rough / wrecked)
3. **Anything big that wasn't on the calendar?** (optional)

Three jobs at once: re-syncs state after a gap, feeds the calibration, and
decides which mode to open. Not a weekly task to fail at — the thing that makes
the app accurate when it's needed.

Q3 is the catch-all for the expensive stuff that is never an event: bad sleep, a
difficult phone call, a meltdown, a surprise cancellation.

### The Log — what happened

**Two rows of one-tap chips**, because the point is that logging one costs you
nothing — the thing you want should already be there.

*Took away energy*: difficult conversation, unplanned errand, mental overload,
change of plan, travel, visitor, hosting, bad news, sensory overload, masked all
day, shutdown, meltdown.

*Gave me energy*: lie down in the dark, went for a walk, an evening with no
decisions, nap, long bath, massage, chill hangout. Without this half the screen
was promising "or gave something back" and offering no way to say it.

**Both lists are Ferri's own words** (13 Sept 2026) — the same rule as the
recovery tree (§8): the app never offers a phrase for your day that you didn't
choose. A first draft written by the app was replaced wholesale.

**They're sized and typed.** A meltdown is not a difficult conversation, and
flattening them to one price made the log lie to the forecast. Each carries a
kind too, so a logged thing feeds the type breakdown (§11) like any event —
and the edit sheet opens on the right kind instead of guessing *social*.

**"+ something else"** for anything not on that list: a name, a size, a kind, and
a toggle for *it gave something back instead*. Same sheet shape as pricing an
event, entered from the other end — there you rate a thing the calendar knew
about, here you name a thing it never did.

**Both paths join the history**, so a phone call logged often enough starts
pricing itself like any learned event (§6). That's the real reason the Log is
worth having: without it, everything expensive that isn't an appointment stays
invisible to the model forever.

**A list of what you've logged today** sits under it, and reads *"Nothing logged
today. That's okay."* when it's empty — an empty log is not a failed one.

**A chip that has been tapped stays lit, and tapping it again un-logs it.** It
reads that state from the log itself rather than keeping its own, so the chip and
the × in the list can never disagree. The trade: the same chip can't be tapped
twice in one day — a second difficult conversation means editing the first entry
up, or using *+ something else*.

**Every entry can be removed or edited.** Logging is one tap, so undoing it has
to be too: a mis-tapped chip shouldn't need a trip to the slider to put right.
Tapping an entry reopens the same sheet to change its size, its kind or its
name — what something cost you is a guess like any other, and the first number
you reach for while it's still happening is rarely the one you'd pick an hour
later (§7: delayed rating is *more* accurate here, not less).

Both paths keep the model honest: they reverse the charge the entry took and
rewrite the history row it left, so one mistake never quietly skews the learned
price of a name.

## 10. Calendar's role

- **Read** the events — they're the entry list.
- **Prompt at creation.** Add "Dentist, Thursday 2pm" and a notification asks
  the expected cost. One tap from the lock screen; the app builds a model of
  your life while you never open it.
- **Don't ask about everything.** The weekly standup gets guessed silently at 2
  because it's been answered eleven times. Ask only for the unfamiliar, the
  long, or things landing on an already-heavy day. Fourteen notifications a week
  gets muted by Wednesday, and a muted app is a dead app for a tool people only
  open occasionally.
- **White space is a resource.** Unscheduled time is the recovery input. A week
  packed wall-to-wall has no refill in it, and that's knowable in advance.
  Nothing else on the market reads empty calendar space as an asset.
- **Write-back** of recovery blocks is the killer feature and the big fork —
  you're not just seeing the overload, you're defending against it.
- **Privacy:** spoon costs must never leak into a shared or work calendar.

### Writes: hand off, don't sync

**Decided: Spoon never writes to the calendar.** It prepares the change and
hands it to the calendar app, where the user saves it — an overlay saying
*"Move Dinner at Sam's to Sunday? This opens Google Calendar with the change
ready."*

This is a first-class native pattern, not a workaround: iOS provides
`EKEventEditViewController`, a system edit sheet your app prefills and presents
in-app for the user to save. Android has the equivalent calendar intent.

It removes the three worst problems at a stroke:

- **No write scope.** Read-only is a much smaller ask and skips the verification
  burden that comes with write access.
- **The attendee grenade defuses itself.** The calendar shows its own "tell your
  guests?" prompt when the *user* saves. Spoon never silently emails Sam — which
  was the scariest part of the move interaction, given moving things is the core
  of triage.
- **No sync loops, no divergence.** The calendar is the single source of truth;
  Spoon only reads.

**The cost, and how it's handled:** you might tap move and never save it. So a
change is **pending** until a calendar read confirms it — shown as a pending tag
on the event rather than assumed to have happened.

**Caveat if this ever becomes a web app:** plain URL deep links reliably
*create* a prefilled event (perfect for recovery blocks) but can't land on an
existing event's edit screen. Editing existing events properly needs the native
edit sheet.

### Two-way sync: possible, but not chosen

Recorded for completeness — full API sync is feasible everywhere, and was
rejected in favour of the handoff above.

| Platform | Route | Two-way? |
|---|---|---|
| iOS (any account) | **EventKit** | Yes — full local read/write |
| Google Calendar | Calendar API (`events.insert/patch`) | Yes, with a write scope |
| Outlook / M365 | Microsoft Graph | Yes |
| Anything else | CalDAV | Yes, more work |

**EventKit is the shortcut worth knowing:** on iOS it reads and writes whatever
calendars the user has already configured in the system Calendar app — iCloud,
Google, Outlook, all of them — through one permission prompt and no server. A
native iOS app may never need to talk to Google's API at all.

Why it wasn't chosen — the API is the easy part, and none of these go away:

1. **Attendees.** Writing a move *notifies the guests* without the user
   necessarily realising. The handoff makes this the calendar's problem.
2. **Recurring events.** This instance or the whole series? No safe default.
   Still an issue either way, and still needs explicit UI.
3. **Sync loops.** Writing *and* watching means tagging your own writes so you
   don't react to yourself.
4. **Permission friction.** A write scope on first run is a big ask.
5. **Divergence on failure.** Succeeds locally, fails upstream, two truths.
6. **Where app-created blocks live.** Recovery blocks on a shared work calendar
   are readable by colleagues — they need a separate private calendar. Relevant
   to the handoff too.

**Still needed regardless:** read access. EventKit on iOS reads whatever the
user already has configured — iCloud, Google, Outlook — through one permission
prompt and no server, so a native iOS app may never touch Google's API at all.

## 10b. Calendar rules — settled

- **Connect the real Google Calendar.** On iOS, adding the Google account to the
  system Calendar means **EventKit reads it** — no Google OAuth, no API client,
  no verification. Go direct to Google's API only if EventKit proves lacking.
- **You pick which calendars count.** Work, personal, a partner's shared one and
  birthdays are not equivalent.
- **All-day events count**, and get priced like anything else — a birthday
  you're not attending prices at 0 once and stays there.
- **Declined events are ignored. Tentative ones count at a discount.**
  So do invitations you haven't answered — at **60%** of their price. Counting
  an unanswered invite in full makes the week look worse than it is; dropping it
  makes it look better. It counts, at a discount, until you answer. Like the
  chronotype curve, the discount lives in the forecast and not in the printed
  price: the dinner costs what it costs *if you go*.
- **Travel between events counts, and is flagged in advance.** It's often the
  expensive part and never on the calendar. *Depends on event locations being
  filled in* — otherwise it can't be inferred.
- **One notification only: right after you create an event.** Nothing else
  interrupts. The post-event "how did it go?" is in-app, on open.

## 10bb. The labelling burden — solved by inference

The requirement was *"I don't want to label everything manually."* Three things
carry it, and all three are built:

1. **A starter cost library** covers common things from day one.
2. **Events price themselves.** Once a name has enough *consistent* history
   (3+ ratings within a narrow spread) new instances **inherit the cost
   silently** and read as inferred rather than unpriced. Scattered history
   infers nothing — the app only assumes what it actually knows.
3. **The ask decays.** Nothing is asked about once its name prices itself. The
   app asks about unfamiliar things, keeps asking where ratings are
   inconsistent, and goes quiet on the rest. **The better it knows you, the less
   it speaks** — the right direction for an app this audience would otherwise
   mute.

## 10c. Setup — a quiz

No archaeology. A short quiz sets:

1. **Max capacity** — and it never drifts afterwards. 100% is the best you'll
   ever feel, by definition (§4).
2. **The unit anchors** — name something that costs you 1, something that costs
   5. Keeps the scale from drifting over months.
3. **Chronotype** — when your good hours are. This gives a **multiplier curve
   across the day**: the same meeting costs more in your bad hours. It shows up
   directly on the day-view gradient, where a late event drops you further than
   the same event would in the morning.

   Built, and the effect is substantial: on the same seeded Thursday, a morning
   person goes under at **3:18pm** and an evening person at **11:10am**. Applied
   to the charge curve, not to an event's printed price — a standup shouldn't
   appear to cost different amounts on different days. Untimed things carry no
   multiplier, since they happen "sometime".

### Answers from the quiz — 13 Sept 2026

- **Scale: all of them.** Not a one-time setup choice — *"I'd want the option to
  change it depending on how I'm feeling."* So the scale is a mood-dependent
  control, and it's one tap from the charge readout rather than buried.
- **Good hours:** slow mornings, most productive through the afternoon and
  evening, **hard stop after 10pm**. None of the presets fit, so the curve is
  bespoke: ~1.28× at 8am, ~0.89× at 6pm, ~1.45× at 10:30pm. The same meeting at
  8am costs nearly half again what it costs at 6pm.
- **Recovery: all four kinds** — quiet and dark, alone, nothing to decide, and
  lying down. Plus a second category the model didn't have (below).
- **Starting charge:** running low.

Anchor weeks can be pulled from the calendar *or* described from memory — both
offered. With no usable history, the app says: use it for two weeks and come
back. A **starter cost library** ships so day one isn't blank.

## 11. What it gives back

No score, so the payoff is **language and evidence**:

- Per-type spend. "Your social budget is blown by Wednesday every week."
- Which days are reliably expensive.
- What actually precedes a bad stretch.
- **Something to point at.** Many people in this group have to justify their
  limits to a partner, a manager, or a doctor. "I've tracked this for eight
  weeks and social is 40% of my capacity" is a very different conversation than
  "I'm tired."

**Settled: the forecast is the payoff.** No patterns screen, in v1 or later.
The per-type diagnostic under the arc (§5) is as far as insight goes, and no
export or shareable summary is wanted. The app stays small.

**Caveat to honour in the copy:** if the app is mostly opened during worrying
weeks, the data over-represents bad weeks. Insights stay descriptive — "in the
stretches you've logged…" — and never claim to know someone's whole life.

## 12. Open questions

- Does a flare need a temporarily lowered **max**, or is "sitting at low charge"
  enough? (Currently: low charge. Simpler, behaves correctly.)
- How is a blank evening valued concretely? (Sleep is settled — see §7. The
  question left is the credit rate for unscheduled time.)
- Does max capacity ever drift, or is it genuinely set-once-and-edit-manually?
- Retro-tagging a past day: how far back can you edit before it stops mattering?
- What's the coarsest scale the app should allow before planning breaks down?
  (Units themselves are settled — see §4.)

## 13. Screens

Three tabs. Kept deliberately small — most of the model lives inside these
rather than earning screens of its own.

### Forecast (home) — cut back, 13 Sept 2026

Less is more; the homepage is for **how you are and what's coming**, not for
everything the app happens to know. In order:

Laid out from Ferri's sketch, 13 Sept 2026:

1. Date, with a **gear** rather than the word "settings"
2. **A centred column** — the charge large and alone, the sentence beneath it,
   then a pill **Log**. The placeholder artwork box is gone; the number is
   the thing you see first.
3. **This week's arc**, chart and day cells **inside one bordered container**,
   divided rather than floating as separate boxes
4. **Heads up**
5. Tasks, unlabelled

**Heads up names the day and suggests a move — no numbers.** The card used to
read "Monday is asking for more than you'll have" over two figures. Both were
wrong. The sentence overstated: a day is flagged when it *ends* in the red,
which happens long before its cost exceeds the charge you start with — Monday
takes 3 of the 5 you'll have and finishes on 2. And the figures didn't earn
their space: 3-against-5 doesn't look hard, so the numbers argued against the
warning sitting above them, and reading them was work that led nowhere.

What you actually do with this card is move something. So it says the thing and
then says that — as advice, not as a hint:

> **Monday is going to be tough.**
> Cancel or move something if you can. If you can't, build in recovery time on
> either side.
> [ See what could move ]

**Prescriptive is the point.** The no-guilt rule bans blame for what's already
happened; it was never a ban on telling you what to do next. A pacing app that
only ever *observes* leaves the whole job — reading the forecast, working out
the implication, deciding the response — with the person who opened it because
they had no capacity to spare. Saying "cancel something" costs the reader
nothing to understand. The two branches matter as much as the instruction: the
advice assumes you often *can't* cancel, and gives you the fallback instead of
repeating itself.

The card no longer needs a "no numbers" branch, because it has no numbers in
either mode. The same overstatement in the home-screen blurb was softened the
same way, keeping its severity ladder (tough one → still a stretch → heavy enough to matter).

### One day, one screen

This took four passes, and the first three all built a screen that shouldn't
exist.

1. **A sheet of three ranked suggestions** — *Move Deep work block to Wed 16 →
   4*. The app planning on your behalf and showing you its homework.
2. **The whole week, in rows** — every event legible, a page and a half of
   saturated bars. True, and far more than anyone asked for.
3. **The whole week, in a grid** — calmer, 146px, but still answering "what does
   my week look like?" when the question was narrower.
4. **The day you're worried about.** Which turned out not to need a new screen at
   all: the day view already drew the day. It just wasn't saying which events
   were doing the damage.

> **Mon 14 September**
> Your spoons run out on Monday evening. The events marked in red are costing
> you the most. Start there.
>
> ▬ Replenish mana
> ▬ 🤒 Flare · max 6
>
> `8am` ─○─ Standup −1 → 4
> ▌      ─○─ **Deep work block** −2 → **3**

**Most draining is relative to the day.** A 2 is the heavy one on a light day and
unremarkable on a heavy one, so the mark is a share of the day's load (25%),
floored at 0.15 so a quiet day can't promote a standup into a villain.
`heavyOn(date)` returns the predicate; Thursday marks three of five, Monday one.

**Tapping a marked event asks the second question.** Where it goes only exists
once you've said what you'd give up:

> **Deep work block** — 10:00 · Mon 14 September · costs 2
> **Move it to**  Tuesday 15 — that day ends on 1
> Wednesday 16 — that day ends on 0
> *Any of them leaves Monday on 4 instead of 2.*
> **Or**  Cancel it altogether — Monday ends on 4
> Change what it costs ›

Only later days are offered. Moving a cost earlier drains the run-up and
cascades straight back into the day you were rescuing — so those days simply
aren't on the list, rather than appearing with a warning attached. Cancelling is
a peer of moving. Pricing stays reachable from the same sheet, so the tap doesn't
have to mean two things.

**"I can't move anything" is a different job, so it's a different sheet.** Most
of the time you *can't* move it — it's work, or it's someone else's day — and an
app that only knows how to say "move it" has nothing to offer exactly when you
need it most. The day won't get lighter, so the answer is the two days touching
it, each offering something restorative to book. The day labels are bare dates —
*Sunday 13*, *Tuesday 15* — because "the run-up" and "the recovery" were labelling
what the sentence above had already said.

**Factory reset day** and **Nothing day** joined the restorative list, and they're
the two biggest restores on it (−.35 and −.30 against the massage's −.25). Both
are whole days rather than appointments, and both are defined by absence — which
is the point. A day with nothing in it isn't a wasted day; it's the one that pays
for the others, and a pacing app that couldn't name that was missing its most
important move. They're Ferri's words, and they show up everywhere
`plannedList()` does, including the "Room on Thursday" card.

Booking one changes the forecast immediately: a Nothing day on Sunday moved
Monday from *"your spoons run out in the evening"* to *"holds together, but
leaves you nothing to spare."* The run-up is where the day gets rescued.

**A window, not a drawer.** Tapping an event opens a centred modal over the
dimmed day — the same treatment the post-event ask uses. The distinction is
whether the app is holding one thing still in front of you or handing you a tray
you reached for: deciding about Deep work block is the former, so the panel sits
over the day rather than sliding up from the bottom. *"I can't move anything"*
stays a drawer, because that one **is** a tray you reached for.

`openSheet(html, ctx, asModal)` now treats an omitted `asModal` as "whatever's
already up". Tapping *Change what it costs* inside the window used to drop the
panel to the bottom of the screen mid-flow, which reads as a bug rather than a
transition; the cost editor opened fresh from the calendar is still a drawer.

Everything that had been a page is now a sheet over the day, which is what the
week views were failing to earn: you never leave the thing you're deciding about.

**Tab icons, not placeholder squares:** a sun behind a cloud, a calendar, a
pencil on a page. Drawn as inline SVG at the same stroke weight as the type
glyphs, inheriting `currentColor` — so the selected tab lights its icon and its
word together rather than needing a second rule.

**Type glyphs, not abbreviations.** Two-letter codes in a 13px circle were
unreadable. Four line glyphs, chosen to stay distinct at 14px: **two heads**
(social), **a signal** (sensory), **a list** (executive), **a pulse**
(physical). The Kind picker in the cost sheet shows glyph and word together and
so doubles as the legend; so does the arc popup.

*Worth noting:* asked to guess the four, Ferri read them as "physical /
emotional / sensory". **Emotional isn't a cost type** — it exists only in the
recovery tree (§7). If the instinct persists, that's evidence the costing
taxonomy is missing one.

**Day cells read like a calendar week view** (Google Calendar as the
reference): day name, date, then the day's events as coloured chips.

- **Chips take the calendar's own colour**, Google's palette, so the week looks
  like the calendar it mirrors. **Affordability is the cell background** —
  green / amber / red tint on the whole container. One colour system describes
  *where an event came from*, the other *how the day lands*, and they occupy
  different surfaces (fill vs. chip) so they can't be confused.

  *Parked 13 Sept 2026:* per-event colour isn't worth chasing until the app is
  in real use on a phone. Revisit then.

  *Constraint worth knowing:* **EventKit exposes the CALENDAR's colour, not
  per-event colours.** Google's per-event `colorId` is only available through
  the Google Calendar API directly — which would reopen the OAuth path §10 closed.
  So on the chosen native route, chips are coloured by which calendar an event
  sits on (Work / Personal / Social / Health), not by individual event colour.
- **Labels appear only when they'd be readable.** At seven columns a cell is
  ~50px, so chips are bare colour bars; at three or four they carry names. The
  strip has to stay aligned to the arc above, so the cells can't scroll to make
  room.
- **Three chips then "+2"**, so a heavy day doesn't stretch the row.
- **Ordered as a calendar week view:** chart, then the **day/date header**, then
  the all-day **banner band**, then the chips. The day columns — tint and
  dividers — run continuously *through* the banner band, so days without a
  banner don't leave a gap in the grid. The header sits above the banners,
  matching Google Calendar — an earlier build had them the wrong way round.
- **Restorative events are drawn like any other event.** They were dimmed at
  first, which made the one good thing on the strip the faintest — a restore is
  a *different* kind of thing, not a lesser one. The ↗ carries it; it needs no
  second treatment.
- **A hollow chip means exactly one thing: an invitation you haven't answered.**
  It's the outline Google itself uses, and borrowing it for anything else — a
  restore, a tentative price — makes it mean nothing. Elsewhere the same state
  reads as a quiet `invited` tag, deliberately less alarming than `pending`
  (nothing is waiting on the app; it's waiting on you).
- **Trend arrows** on the chips — the literal characters **↗ ↘ ↻**, not drawn
  icons. A trending-chart glyph has three direction changes crammed into 10px
  and turns to mush; the characters are a single clean stroke. ↘ for a drain, ↗
  for a restore, **nothing for neutral or unpriced** — an unpriced event shouldn't claim a direction it
  hasn't been given. Conditions use their declared direction; a recurring one
  shows the repeat glyph alongside it.
  Arrows appear only with labels (1–4 day horizons); at seven columns the chips
  are 7px bars with no room, and restores stay distinguished by opacity.

**The per-day charge number left the cell.** The arc directly above carries the
shape, Heads up names the numbers for the day that matters, and the day view has
the detail. Affordability survives as the colour of the date.

### The horizon is switchable

Bold **"3 day ▾"** on the right of the section's own label row — sharing the line
with *Today* / *The next few days* / *This week's arc* — opening 1 / 3 / 7.
Defaults to 3. It sat inside the arc frame at first, where it covered the plot
and had to carry a background patch to stay legible over the grid; on the label
row it obscures nothing and needs none.

**The horizon is yours to pick, full stop** — there's no "auto" option. The
shrink-as-you-drain behaviour from §8 survives in its strongest form (below the
bottom threshold the week is hidden entirely and you get today only), but the
intermediate 7→3 step is gone. A view that silently changes length is worse than
one you chose, and having to hand control *back* to the app was the odd part of
the earlier version.

The section heading follows the choice: *Today* / *The next few days* / *This
week's arc*. And at 1 and 3 days the cells are wide enough for event labels,
which makes the short horizons genuinely more readable rather than just
shorter — a real reason to switch down beyond feeling bad.

### Multi-day events are conditions, not costs

A week-long entry — a holiday, a flare, a deload, "Replenish mana" — isn't a
spend. It's a **condition of the week**, and pricing it per day would be wrong
in both directions: it doesn't cost you a fixed amount daily, and it often
changes what *everything else* costs.

So it renders as a **dashed banner spanning its days**, between the arc and the
cells, with no per-day cost. Dashed rather than filled precisely to say *this
one isn't priced*. Banners running off the left edge of the window are marked
with a leading "‹".

### Conditions carry a ceiling — and only a ceiling

**Settled.** A banner can impose a **ceiling**: *"Flare · max 6"* — you can't
get above 6 while it lasts.

Three modifiers were considered — a rate multiplier, a recovery delta, and a
ceiling. **Only the ceiling survived, and precisely because it isn't a
multiplier.** It's a clamp, which means:

- **Overlapping conditions compose trivially** — take the lower. No compounding,
  no surprises.
- **It never interacts with the chronotype curve.** Rate modifiers would have
  meant two multipliers on every cost, and with saturation (§5) a third. Three
  multipliers is where a model stops being explainable.
- **It says itself in words.** "You can't get above 6" needs no arithmetic to
  understand; "everything costs 1.2×" can only be felt as the forecast being
  mysteriously worse.

**This also reopens and settles the flare question from §12 properly.** Flares
were modelled as "sitting at low charge" because a permanently editable max was
messy. Scoped to a date range and self-expiring, a ceiling is clean — it can't
get stuck, and *"this week you can't reach full"* is a truer description of a
flare than *"you happen to be low."*

Stated on the banner and in the sentence, which takes it over entirely while
it's in force.

**Banners look like calendar bars** — solid fills in the event's colour, exactly
like the chips. An earlier build distinguished capped from uncapped conditions
with a dashed vs solid border; that was a distinction the label already makes
("Flare · max 6"), so it went.

### Calendar spans and conditions are different things

| | Calendar span | Condition |
|---|---|---|
| comes from | your calendar | Spoon |
| e.g. | a holiday, a trip you've booked | a period, a flare, seeing your parents monthly |
| name / dates / colour | **read-only** — edited in the calendar | yours |
| direction, ceiling | yours (Spoon-side annotations) | yours |
| repeats | no — the calendar handles that | yes |
| delete | in your calendar | here |

**This falls straight out of the write-handoff rule (§10):** if Spoon never
writes to your calendar, it must not offer to edit calendar events either.
Tapping a calendar span shows its details flat, with *Open in Google Calendar*
for anything structural — and lets you set the two things that are genuinely
Spoon's: which way it pushes you, and whether it caps you.

**The marker is an emoji you choose**, and it's what tells the two kinds apart
at a glance — 🩸 🌙 🤒 🛏️ ✈️ 🏠 and so on, or none. Better than another border
style: it's personal, it carries meaning nothing else on the strip does, and it
never competes with the colour system.

**Conditions are pills; calendar spans are rectangles** like the chips. Shape
carries the difference before you've read anything — and unlike colour or a
border style, it costs nothing and can't be confused with the affordability
signal.

**At seven columns a calendar span is drawn exactly like the chips below it** —
same height, no label. There's no room to read one at that width, and the bar
already says the thing that matters: this stretch is spoken for. Its name is one
tap away in the read-only sheet, and it comes back in full at one and three
days, where the chips are labelled too.

**A condition keeps its words at every width.** *"Flare · max 6"* is a claim
about the week you can't check anywhere else, so it stays legible even when the
calendar's own spans have gone quiet.

**Conditions stack above calendar spans**, with a gap between the two groups —
yours on top, the calendar's below. So the banner band reads as two bands, and
you know which half you can edit before you tap anything.

**A condition needn't exist in any calendar at all**, which is the point. A cycle
isn't an appointment. Neither is "the week I always crash after seeing family".
The **Conditions** card on the Calendar screen lists only these — your own — so
it never fills up with calendar entries.

### Creating and editing conditions

Tap a banner on the forecast, or the **Conditions** card on the Calendar screen,
which lists them all and adds new ones. The editor takes: name, a colour from
Google's palette, start date, length in days, **which way it pushes you**,
and whether it **repeats** (never / weekly / every 28 days). Delete lives there
too.

**Direction comes before the ceiling, and gates it.** *helps ↗ / neither /
limits ↘* — and only a limiting condition can set a ceiling, because only a
limit *has* one. A holiday and a flare are both week-long conditions; saying
which is which is the first thing worth knowing about either, and it keeps the
ceiling from being the only way to express that a stretch of days matters.

A condition is stored as a multi-day event with no cost — so it never enters the
spend arithmetic, only the ceiling does.

**Conditions recur.** Settled — a cycle is the clearest case: you're simply
going to feel worse that week, and it's knowable in advance. Recurring *tasks*
were rejected because scheduling laundry is itself cognitive load; a recurring
condition is the opposite — **set once, and the forecast knows forever with no
logging at all.** It's also the only thing in the app that can be right about a
bad week *before* it happens; everything else learns from what already did.

Marked with ↻ on the banner, and the sentence says so: *"it comes round again,
so this is a ceiling you can plan for."*

**Moved off it:**

- **What it asks of you** → behind a tap on the arc, and improved by the move:
  the sheet now names **the actual events** behind each bar, so it's something
  you can act on rather than four abstract bars. Tapping an event goes to its
  day.
- **Worth a second look** and **Room on Sunday** → the Calendar screen. Both are
  planning-shaped, and planning is what that screen is for.

**Also cut:** the placeholder artwork boxes in the day strip. Day letter, number
and colour bar carry it; the illustrations can come back when they exist.

### Forecast (home)

Weather framing, which does real work: forecasts are allowed to be wrong,
they're glanceable, and nobody gets blamed for a storm. That solves the tone
problem better than careful copy can.

- **Current conditions** — charge, the plain-language blurb (§4).
- **The week's arc** — a continuous charge curve across the coming days: it
  falls through each day and climbs overnight, so the shape is a sawtooth that
  visibly sags when the week is too full. The day-view gradient (below)
  generalised to a week, and the clearest single picture of the battery model.
  Drawn on **one column per day, sharing the day strip's axis**, so the graph and
  the strip read as a single component rather than two charts. Marks the week's
  lowest point.
- **Days ahead** — each day's icon derived from predicted charge *after* what's
  scheduled.
- **The shrinking horizon (§8) lives here as a property of one screen**, not as
  separate modes: seven days out when fine, three when low, today only when
  crashed.
- **Triage** (§8) appears as a card beneath the forecast at low charge — the
  specific thing worth moving.

### Calendar

Where costs get set: tap an event, price it. No separate estimation flow.

- **The month is a month.** One continuous hairline grid, tall cells, the date
  centred at the top, the day's events as thin colour bars inside it, and the
  last week padded out so the block is a rectangle. It was a row of small
  rounded boxes with the number jammed in a corner, which read as a toolbar and
  left no room to show what was in a day — the thing you open a calendar to see.
  It now takes the top half of the screen instead of a fifth of it, with the
  agenda below.
- **Today is a filled circle, the selected day a ring** — Google's own shorthand,
  and it frees the cell edge to stay part of the grid rather than carrying state.
- **The grid line is its own token**, brighter than the hairline used for every
  other border. A month grid has to be readable *as a grid*; borrowing the
  quietest rule in the app meant it vanished wherever a cell carried a tint.
- **The weeks either side of the month are real days, not blanks.** A week
  doesn't stop because a month does, and blanking those cells made the first and
  last rows read as broken grid rather than as the turn of the month. They carry
  their tint and their events, quietened by an inset wash — not by `opacity`,
  which would have dimmed their grid lines too, recreating the exact problem.
  Tapping one moves the calendar to that month.
- **Colour goes on the day block, not the events.** Matches the weather framing,
  survives half the events being unpriced, and keeps a busy surface quiet.
- **The day block and the forecast icon are two expressions of one number** — not
  "how busy is this day" but *how affordable is this day given the charge you'll
  have going into it.* Straight out of the zones in §4.
- **Four states:** comfortable / doable-but-you'll-pay / over the line / not
  enough info. Unestimated must be visibly unestimated — painting it neutral
  would be a lie.
- **No dashed or dotted rules anywhere.** A half-estimated day used to carry a
  dashed outline; the unpriced event now shows as a **grey chip** among the
  coloured ones instead, which says *which* thing has no estimate rather than
  just that something doesn't. The crossing line, the threshold line on the arc
  and the "add" chip lost their dashes too — they were all reaching for the same
  "provisional" idea with a texture that reads as damage on a dark ground.
- **Selecting a day brightens the grid around it.** The cell is what you picked,
  so the cell is what lights up — better than ringing the number, which had to
  compete with today's filled circle for the same 19px.
- **The tint rises from the bottom of the cell.** A month cell is tall enough to
  hold a gradient, and a flat wash of saturated colour at this size fights the
  day number and the chips for the same space. Ramping it leaves the top of every
  cell quiet enough to read while the colour still arrives at full strength.
  The forecast strip does the same thing, with one difference: its tint is
  painted **once, behind the whole column** — header, banner band and chips —
  rather than per row. Painting each row separately restarted the ramp at every
  seam; behind the stack, a day reads as one tall cell, exactly like a day in
  the month grid.
- **The calendar hues are lightened** to hold against that stronger ground, which
  flipped their labels: chip and banner text is now dark navy rather than white.
- **Every event carries an indigo stroke.** Against a saturated day the pale
  chips were melting into the colour behind them. The stroke is pitched at the
  value of the ground near the *top* of the screen, not the darkest navy — at
  near-black it read as a cut-out punched through the day; at this value it
  reads as the background showing between the events.
  Same treatment on the month grid's bars and on the condition banners.
- **Events keep a cost figure in the day's agenda, not a colour.** The day block
  answers "how heavy," tapping it answers "because of what" — which is what
  triage needs.
- **Type** (social / sensory / executive / physical) rides a small glyph. Colour
  can only carry one variable legibly, and intensity is the scan question.
- **Open blocks are shown.** White space is the recovery input; the gaps are
  data.
- **One palette shared with the forecast.** If Thursday is a storm on home, it
  must look heavy here.

### Day view

Reached by tapping a date. **A charge gradient running down the day** — you
start green, a hard 1:1 at 10:30 drops you, and the afternoon is yellow from
there.

It answers a question none of the other surfaces can: not *does this day fit*
but **when do I run out.** Those come apart — a day can total up fine and still
leave you empty by 3:40, which makes the cheap evening thing the real problem.

- **Order becomes visible.** The same three events in a different sequence give
  a different curve. A hard meeting first thing genuinely costs you the
  afternoon, and nothing else in the app can show that.
- **Open time slopes back up**, so recovery is legible as shape, not just a
  number.
- **The crossing point is marked** — "you go under around 3:40pm" is far more
  actionable than a day total.
- **The floor rule is visible here** (§4): the 4-cost dinner lands at 0, not −2.
- **Rehearse-a-change lives at the bottom** — "what if I moved something?" This
  is the screen where moving one thing visibly repairs the curve.
- **A now line**, on today only — a current-time marker on a future day means
  nothing. Deliberately **neutral in colour**: green/amber/red mean affordability
  here, so borrowing one for the clock would read as a judgement about the time
  of day. The arc carries a matching tick.
- **At one day the arc is a 24-hour clock**, edge to edge: a faint line every
  hour, a stronger one with a label every four — **12am** / 4am / 8am / 12pm /
  4pm / 8pm / **12am**, midnight pinned at both ends so it reads as a whole day
  rather than a window onto one. The now line then reads as a *time* rather than
  a bare position — which is why the forecast strip below it carries no clock of
  its own; once the arc is a dial with a dot walking it, a second time marker
  among the chips is the same fact twice. The waking
  hours sit where they belong on the dial and the line runs flat either side of
  them — the day used to be squeezed into 72% of the frame with dead space after
  it, which read as a chart that had run out rather than a day that had ended.
  **Three and seven day use the same clock, one per column.** Each day's column
  is 24 hours wide, so the waking curve runs from 29% to 96% of it and the gap
  to the next column is the overnight climb at its true width. The columns used
  to squeeze the waking day into 72% and give the night 28%, which put the "you
  are here" dot well to the left of where the clock said you were — 4:45pm
  landed at 44% of the day instead of 70%. One axis now, at every horizon.
- **The dot marks where you are now**, and walks the curve as the day goes by. It
  used to mark the week's lowest point — but the colour and the heads-up card
  already say where the trouble is, and a marker that never moves teaches you
  nothing.
- **Nothing is filled under the curve.** The shaded area added weight without
  adding information, and on a chart whose whole meaning is *height*, a filled
  mass reads as a quantity you've used up rather than a level you're at.

### Tasks — its own screen

**Tasks are a fourth tab, not a section of the forecast.** They sat at the bottom
of the forecast, which is a *status report* — putting the things you owe the
world underneath the reading of how much you've got made one a demand on the
other. The forecast now ends at "heads up", and reads as what it is.

The screen is what the old "whole list" sheet was: **what fits right now**, then
**more than you've got right now**, then **done recently**, with *+ add* between.
No sheet any more — a list you keep is a place, not a popup.

**Burnout mode gets its protection for free.** Tasks used to be explicitly
stripped from the forecast below 25%; now they were never there. They're a tab
away if you want them, which is the right distance: not shoved at you, not
locked away from you either.

### What fits right now

**Ticking a task doesn't erase it.** It moves to **Done recently** at the bottom
of the whole list, struck through, with an **undo** that hands the spend back.
Vanishing on the spot made a mis-tap unrecoverable, and left no record of the
day's work — which on a bad week is the only evidence you did anything.

**They clear themselves after three days**, so the list can never become an
archive that needs maintaining. No "completed" screen, no clearing ritual, no
count to feel bad about.

**Two categories only:** things with a genuine hard deadline (taxes, paperwork),
and everything else. No self-imposed urgency tier — soft-urgency labels inflate
until everything is urgent and all they produce is guilt.

**Deadlines get the capacity framing, never the punitive one:** not *"overdue"*
but ***"due in 18 days · 13 days before then you could afford it."*** And when
there's no room at all, *"no day before then has room for it"* — which is a
planning prompt rather than a telling-off. Distant deadlines sort on cost like
anything else, so they don't crowd the list for weeks.

**Breaking down is nudged, not generated.** No language model, no suggested
subtasks. If a task has sat untouched for three weeks the app offers, once,
*"sitting a while — break it into smaller chunks?"* and you write the pieces.
A task sitting untouched usually means it's too big or too vague, and naming
that is the whole of the help.

**The pieces replace the parent.** Once broken down, the subtasks are what
appear in the list — with the parent as a context label — and the big one stops
being the thing you have to face. That's also the point of doing it: a 4 doesn't
fit, but the 1 inside it does. Flat, no hierarchy UI, no progress bars, no
completion counts.



**Not a to-do list.** A general task list is a cognitive-load *generator* for
this audience: it accumulates, it implies obligation, and the count at the top
becomes the dread. The brief is the opposite — lower the load.

**Tasks are just untimed events.** Same cost, same type tag, same calibration
loop; the model already carried them. Undated ones sit in a pool; give one a
date and it counts against that day like anything else. No new concept.

The one question worth asking, and the only one this app is uniquely able to
answer:

> **What can I actually do right now?** — the list filtered by what your
> remaining charge affords.

Instead of fourteen items and dread, three that fit.

- **The biggest affordable thing leads.** Spend the charge on something that
  matters while you have it, rather than defaulting to busywork. The lead line
  moves with charge: *"You've room for the bigger ones"* → *"Only the small ones
  fit right now."*
- **Things you can't afford aren't greyed out, they're gone** — with a quiet "3
  more" so nothing feels lost. The full list is one tap away and groups them
  under *"More than you've got right now."*
- **The section disappears entirely in recovery mode.** Nobody at 1/10 needs to
  see a list.
- **A day is optional, and suggested rather than demanded.** No due dates — the
  options are *sometime*, *today*, or a light day the model picks
  (*"Sun — has room"*). Due dates manufacture guilt; capacity doesn't.
- **No counts, no overdue, no streaks.** Nothing ever turns red for being undone.
- **Completing one costs you** — charge drops by the estimate — **and feeds the
  same post-event ask**, so tasks calibrate exactly like events do.

### Log

- **A slider** is the charge input, reading out a word as you drag ("running
  low") alongside the number. It commits on release, never mid-drag.
  (Considered big state buttons instead — fewer, larger targets — but the slider
  won: the words keep it from feeling like a precision instrument, and it can
  express "somewhere between okay and low", which four buttons can't.)
- **Notched track, and the thumb snaps to the notches.** The scale is whole
  numbers, so the slider's own range *is* the scale — one step per mark. Without
  that, a drag smears a value rather than picking one, and the readout word is
  the only sign anything discrete happened. The coloured track is drawn *behind*
  the input rather than by it, so the marks can sit on the track while the thumb
  still passes over the top of them — painted the native way, the thumb dragged
  underneath its own tick marks.
- **The words run** empty / crashing / running low / spread thin / okay / good /
  great. Plain, and none of them a verdict on you.
- **Bad sleep** (§7) sits below, optional. The off-calendar catch moved out to
  the Log, which is the screen for things that happened.

### Not screens

- **Rehearse-a-change** — no surface of its own; it lives at the foot of the day
  view, where moving one thing visibly repairs the curve.
- **Recurring tasks** — deliberately not built. Scheduling laundry is itself
  cognitive load.
- **Patterns / insights (§11)** — cut from v1. Least urgent thing here.
- **Setup (anchor weeks), scale choice, repertoire** — onboarding and settings
  sheets.

## 14. Out of scope for now

- Real calendar OAuth. **Fake the calendar in the prototype** and prove the
  interaction is worth it first.
- Social features, sharing, accounts, sync.
- Notifications infrastructure (the prompt is designed, not implemented).
- Desktop. Mobile only.

---

## Visual direction — set 13 Sept 2026, from Ferri's moodboard

**Sections are clusters.** A label, its choices, and the note explaining them are
one object: ~7px inside a group, ~26px between them. Tuned per-section it kept
drifting — a hint would sit equidistant from the chips above and the heading
below, so it read as belonging to whichever you looked at first. It's one shared
rule now (`.group`), used by both Settings and the Log, so the rhythm survives a
section appearing or disappearing.

**A grainy gradient ground.** Soft blooms of indigo and violet over a near-black
navy, with a fine grain over the top — built as CSS background layers (the grain
is an inline SVG turbulence, so the file stays self-contained and asset-free)
rather than an overlay element, so nothing had to be restacked around it.

**Containers are a deep navy solid.** Cards and the arc frame were outline-only,
which works on a flat ground and disappears on a gradient. They're filled now,
with a soft drop shadow, so they read as objects sitting on the ground — which
is the moodboard's whole idea.

**Garamond for display, system sans for everything working.** The serif carries
the date, the charge number, the state word, the plain-language sentence, the
day numbers and the sheet headings — the app's *voice*. Everything that's a
control, a label or a table stays sans. The dividing line is what the text is
*doing*: the sentence under the charge is the app speaking, so it's serif; the
line under a screen title is describing the screen, so it's sans, and bright
enough to read as instruction rather than as a footnote. Garamond runs small, so the display
sizes went up a step or two; nothing else about the layout moved.

**The zone colours are teal / magenta / gold**, from Ferri's palette — the green
skewed teal, the red skewed magenta, the amber skewed golden, and all three
deeper and more saturated than the muted set they replaced. On a dark ground a
desaturated hue just reads as grey with a tint, and these have to carry meaning
at chip size. Gold is the brightest of the three, so its tint is pulled down to
sit level with the others: *"you'll pay for it"* must not outshout *"over"*.

**The calendar hues are tuned into the palette too** — Google's Sage becomes a
teal, Flamingo a rose, Grape and Peacock warmer and cooler respectively. The
seven stay as distinguishable from each other as Google's own, so the
categorical signal is intact; they just belong to the screen they sit on. In a
real build these arrive from EventKit and this becomes a mapping, not a palette.
Reverting to Google's literal hues is a one-line change if the fidelity ever
matters more than the coherence.

*Caveat:* EB Garamond loads from Google Fonts, so the display face falls back to
Hoefler Text / Times offline. Embedding it as base64 would make the file truly
standalone at a cost of a few hundred KB.

## Build setup

- Single-file HTML/CSS/JS prototypes, mobile frame, no build step.
- **Dark only.** It began deliberately neutral — greys, not a styled dark theme
  — to keep the visual direction open. That direction is now set (below).
- The prototype stays inside a phone frame at every viewport; it never goes
  full-bleed. The frame is a fixed 390×812 and **scales to fill whatever room
  the window has** (0.55–1.6×), recomputed on resize — so the preview is as
  large as it can be without stopping being a phone.
- `python3 dev-server.py` serves the folder at :5173 with live reload.
- Screen-by-screen iteration, same as Classy and Bloom.

### Files

| File | What it is |
|---|---|
| `wireframe-v1.html` | Static four-frame wireframe. Structure reference, no logic. |
| `prototype.html` | Working prototype — live model, all four screens, sheets, localStorage. |

### What the prototype implements

Charge and costs are stored as fractions of max at full resolution and rounded
once at render (§4). Working: the projection, the blurb engine, day-block
colouring, the week arc, the computed day gradient, the cost sheet,
rehearse-a-change with real what-if simulation, the charge slider, the
rough-night refill withholding, all four scales, and recovery mode below the
bottom threshold.

**Debt needed a mechanism the doc didn't spell out.** Flooring charge at 0 alone
makes overspend vanish — an 8-point day and a 20-point day both end at empty and
recover identically, which kills the compounding. So overspend accrues as a
separate `debt`, and recovery pays that off *before* charge starts climbing. No
negative number is ever displayed; the cost surfaces as days of delay, which is
§4's "debt is measured in time" made mechanical.

Also working: the calibration flags (cost sheet, forecast card, row dots), the
post-event ask with its chaining backlog, recording actuals, the calendar
handoff overlay with its pending state, and capacity-filtered tasks.

Not implemented: onboarding / the anchor weeks, real calendar sync, the
event-creation prompt, notifications, patterns.
