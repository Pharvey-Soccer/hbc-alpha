# Head Ball Coach — Narration Lines

The line pools the broadcast draws from. Edit freely: add lines under any
pool, and they are picked up the next time a game is played. Built from
`docs/design/narration_beats_study.md`; see `engine_implementation_status.md`
§174 for how the pools are used.

### How this file is read

- `## poolName` starts a pool; any other heading ends one. Only the first word counts; anything after it
  on the same line is a note for whoever is writing.
- `- ` starts a line. Anything else (like this paragraph) is ignored.
- A trailing `*` marks a big play, `**` a huge play. `**` only takes effect
  in the nine pools on the closed list (touchdown, sack, fumbleForced,
  breakaway, turnoverOnDowns, fourthDownConversion, safety, deepCompletion,
  monsterHit), and every line in those pools gets it whether it is marked or
  not. Everywhere else, `**` counts as `*`.
- A leading `D - ` puts the line in the defense's colour band.
- A leading `~ ` marks a placeholder written on the engine side to fill a pool
  the study had not seeded yet. It plays like any other line; it is marked so
  it can be found and replaced.
- A pool name with a dot (`throw.deep`) is a variant: extra lines that only
  fit some plays. When the play fits, the variant's lines join the plain
  pool's and one is picked from all of them -- so a variant adds flavour
  without taking over. A pool whose lines do *not* also fit the plain case
  gets its own name instead (`tackleLoss`, not `tackle.loss`).
- A line is only used when every tag in it can be filled, so a line that names
  `{{defender}}` is skipped on a play where nobody was the defender.

### Tags

Names are last names. An UPPERCASE tag gives an uppercase value
(`{{NICKNAME}}` -> `GALLOWGLASSES`).

| tag | meaning |
|---|---|
| `{{team}}` / `{{nickname}}` | the offense: school, nickname |
| `{{defense}}` / `{{defense_nickname}}` | the defense: school, nickname |
| `{{qb}}` `{{carrier}}` `{{receiver}}` | quarterback, whoever has the ball, the target |
| `{{defender}}` `{{tackler}}` `{{blocker}}` | the defender in the beat, who made the tackle, the blocker |
| `{{player}}` | the subject of an after-the-play line (injured, fired up, flagged) |
| `{{kicker}}` `{{punter}}` `{{returner}}` | special teams |
| `{{spot}}` | where the ball ended: `17`, `50`, `line of scrimmage`, `goal line` |
| `{{catch_spot}}` | where the ball was caught (or picked off) |
| `{{return_start}}` / `{{depth}}` | where a returner fields it; how deep in the end zone (`4 yards`) |
| `{{yards}}` / `{{yardage}}` | `3` / `3 yards` (`a yard`) |
| `{{route}}` | the target's route, with its preposition: `on the slant`, `in the flat`, `on the checkdown` |
| `{{formation}}` | `I formation`, `shotgun`, `pistol`, ... |
| `{{direction}}` | `left` / `right` |
| `{{new_position}}` | where a man moves after an audible: `wide receiver`, `tight end`, `running back`, `fullback` |
| `{{mark}}` | a yard marker on a long run: `35`, `20` |
| `{{number}}` | how many would-be tacklers he got past, in words: `two`, `three` (only when there were two or more) |
| `{{penalty}}` `{{side}}` | the foul and `offense` / `defense` |
| `{{caller}}` `{{left}}` | who called a timeout, and how many they have left: `two`, `none` |
| `{{He}}` / `{{he}}` | the pronoun, as written |

---

# Every play (procedural)

These are not detected triggers: every play has some version of them.

## formation
- {{team}} lines up in the {{formation}}
- {{qb}} in the {{formation}}

## tempo  (the first snap of a drive's hurry-up -- said once, not every snap)
- ~ {{team}} goes no-huddle
- ~ {{team}} hurries to the line

## tempo.twoMinute
- ~ {{team}} is in the two-minute drill

## kneelLineup  (kneel out: the whole play is these two lines)
- ~ {{team}} lines up in the victory formation

## kneel
- ~ {{qb}} takes a knee
- ~ And he takes a knee

## timeout  (after the play, in the colours of whoever called it)
- ~ {{caller}} calls timeout - {{left}} left
- ~ Timeout, {{caller}} - they have {{left}} left

## audible  (pre-snap adjustment)
- {{qb}} is making adjustments at the line

## formationShift  (an audible that changed the formation)
- {{team}} shifts to the {{formation}}

## personnelShift  (after an audible, a man moves to a spot that is not his: a tight end to the slot)
- ~ {{player}} moves out to {{new_position}}
- ~ {{player}} shifts over to {{new_position}}

## snap  (a quarterback run, before he keeps it)
- The ball is snapped
- Ball is snapped
- {{qb}} gets the snap
- The center snaps the ball

## dropback
- {{qb}} drops back
- {{qb}} drops back to pass

## playAction
- {{qb}} fakes the handoff

## rollout  (a designed boot, or flushed and still looking to throw)
- He rolls out to the {{direction}}
- He rolls out to his {{direction}}

## motion  (the jet sweep's man in motion, before the snap)
- {{carrier}} motions into the backfield

## turnsTheCorner  (a sweep or toss that got to the edge, before he is past the line)
- ~ {{carrier}} gets the corner
- ~ He turns the corner

## wallHolds  (§210: short yardage or the goal line, and the pile held him short -- told only when the defence won it; before the tackle line, which names the defender; never the yards)
- ~ {{carrier}} runs into a wall
- ~ Nowhere to go for {{carrier}}
- ~ {{carrier}} slams into the pile

## pileBreaks  (§210: a pile formed in front of him and broke -- the line got the push; told on a short gain or a goal-line score, before the result, and it names him)
- ~ {{carrier}} pushes through the pile
- ~ The line surges and {{carrier}} goes with it

## burst  (§206: a running back who survived a moment in the second level finds another gear; the author's line)
- {{carrier}} with a burst of speed

## cutsUpfield  (a sweep or toss cut back inside, before he is past the line)
- ~ {{carrier}} cuts upfield
- ~ He cuts it upfield

## pastTheLine  (a toss, a sweep or a scramble that gets back to the line and beyond it)
- He's past the line of scrimmage

## roomToRun  (a quarterback who gets 8+ yards before anybody touches him, once he is past the line)
- He's got room to run
- ~ {{carrier}} has daylight in front of him

## handoff  (a line naming the quarterback is used when nothing before it has)
- {{carrier}} gets the carry
- {{carrier}} gets the handoff
- Hands off to {{carrier}}
- ~ {{qb}} hands off to {{carrier}}

## handoff.inside
- {{qb}} hands off to {{carrier}} up the middle

## handoff.outside
- {{carrier}} gets the handoff around the outside
- ~ {{qb}} hands off to {{carrier}} around the outside

## jetSweepHandoff  (a jet sweep names itself)
- ~ {{qb}} hands off to {{carrier}} on the jet sweep
- {{carrier}} gets the carry on the sweep
- ~ {{carrier}} takes the jet sweep

## sweepHandoff  (any other sweep off a handoff)
- ~ {{qb}} hands off to {{carrier}} on the sweep
- {{carrier}} gets the carry on the sweep

## rpoRun  (an RPO where he kept it on the ground)
- Hands it off to {{carrier}} instead

## toss
- {{carrier}} gets the toss going {{direction}}
- ~ {{qb}} tosses it to {{carrier}} going {{direction}}

## pitch
- {{qb}} pitches it to {{carrier}}
- ~ Pitches it to {{carrier}}

## draw  (the fake, after the dropback line -- the carrier is named by the next line)
- No wait, it's a draw

## drawRun  (who took it: the back after the handoff, or the quarterback himself)
- {{carrier}} takes off up the middle

## optionFake  (an option with a dive back, when he does not give it: before the keep or the pitch)
- {{qb}} fakes the handoff
- Fakes the handoff
- ~ {{qb}} rides the mesh and pulls it

## optionKeep  (the quarterback keeps it -- never on a give or a pitch)
- {{qb}} with the keep
- ~ {{qb}} keeps it himself
- ~ Keeps it himself

## optionDive  (the give -- one line, and nothing before it)
- {{qb}} hands off to {{carrier}}

## qbRun  (a designed quarterback run, after the snap)
- ~ {{qb}} keeps it and heads upfield

## qbRun.sweep  (outside, not up -- its own lines only)
- {{qb}} keeps it and sprints outside the tackle
- ~ {{qb}} keeps it and races for the edge

## qbRun.sneak
- ~ {{qb}} pushes ahead on the sneak

## throw  (the ball leaving his hand -- says nothing about how it ends, and always says the route)
- Throws to {{receiver}} {{route}}
- {{qb}} throws to {{receiver}} {{route}}

## throw.deep
- Throws it deep to {{receiver}} {{route}}
- Goes deep downfield
- Slings it upfield to {{receiver}} {{route}}

## throw.bomb  (a deep ball in the air -- its own beat before the catch; names nobody, so the catch does)
- ~ {{qb}} throws a bomb...
- ~ He's going long...
- ~ {{qb}} lets it fly...

## completion  (throw and catch in one line -- completions only; every catch says where)
- He hits {{receiver}} {{route}} at the {{catch_spot}}
- Hits {{receiver}} {{route}} at the {{catch_spot}}

## completion.checkdown  (a short one to a back)
- Finds {{receiver}} on the checkdown at the {{catch_spot}}
- He finds {{receiver}} {{route}} at the {{catch_spot}}
- He hits {{receiver}} on the checkdown at the {{catch_spot}}

## catch
- Complete to {{receiver}} at the {{catch_spot}}
- {{He}} catches it at the {{catch_spot}}

## catch.deep  (a deep ball coming down -- after throw.bomb)
- ~ {{receiver}} hauls it in {{route}} at the {{catch_spot}}
- ~ {{receiver}} runs under it at the {{catch_spot}}

## catch.deep.open  (and nobody near him)
- ~ {{receiver}} is all alone and hauls it in at the {{catch_spot}}

## catch.inStride  (caught on the run, and he kept going)
- {{receiver}} catches it in stride at the {{catch_spot}}

## afterTheCatch  (room to run after the catch, short of a breakout)
- {{receiver}} keeps his feet moving

## afterTheCatch.turn  (the same, not after a deep ball he ran under -- he is already going upfield)
- {{receiver}} turns upfield

## tackle  (the tackle -- a line with {{yards}} in it is also the summary)
- Brought down by {{tackler}} at the {{spot}}
- Brought down at the {{spot}} by {{tackler}}
- Tackled after a gain of {{yards}}
- Taken down after a gain of {{yards}}
- D - {{tackler}} drags him down at the {{spot}}
- D - Brought down after a gain of {{yards}} by {{tackler}}
- D - Brought down by {{tackler}} after a gain of {{yards}}

## tackle.spotOnly  (inside the 5, when a hit line just named the tackler: the spot, and nobody twice)
- ~ Brought down at the {{spot}}

## gainSummary  (the last line of every play that gained: the net, so nobody does the math -- a first down is added to it from firstDownSuffix)
- Gain of {{yards}} on the play
- ~ {{Yardage}} on the play

## gainSummary.middle  (a short inside run)
- Up the middle for {{yardage}}

## gainSummary.long  (15 yards or more)
- {{yards}} yard gain in all

## lossSummary  (the defence's -- a loss is a defensive win)
- ~ D - Loss of {{yards}} on the play
- ~ D - Dropped for a loss of {{yards}}

## noGainSummary
- ~ No gain on the play

## spotSummary  (in place of the gain summary inside the 5: where he is, not how far he came)
- ~ Down at the {{spot}}
- ~ Ball at the {{spot}}

## shortOfTheGoalLine  (tackled less than a yard short -- the result and the net in one)
- Tackled just short of the goal line
- ~ D - {{tackler}} stops him just short of the goal line

## outOfBoundsShortOfTheGoalLine
- ~ Pushed out just short of the goal line

## touchdownSummary
- ~ {{Yardage}} for the score

## returnSummary  (the returning team's colours)
- D - {{Yardage}} on the return

## tackle.gang
- Gang tackled at the {{spot}}

## tackleNoGain
- D - {{tackler}} drags him down at the line of scrimmage
- ~ D - Brought down at the line of scrimmage

## tackleLoss  (a tackle for loss is the defence's, and names the tackler -- the line without one is only for a play where he was already named)
- ~ D - {{tackler}} drops him at the {{spot}}
- ~ D - {{tackler}} brings him down behind the line at the {{spot}}
- ~ D - Brought down behind the line at the {{spot}}

## outOfBounds  (he got out on his own -- nobody pushed him)
- ~ He steps out at the {{spot}}
- ~ {{carrier}} gets out of bounds at the {{spot}}

## outOfBounds.clock  (a hurrying offence: he got out to stop the clock)
- ~ {{carrier}} gets out of bounds at the {{spot}} to stop the clock
- ~ He gets out at the {{spot}} and stops the clock

## outOfBoundsPushed  (a defender forced him out -- names him)
- ~ D - {{tackler}} pushes him out of bounds at the {{spot}}
- ~ D - Knocked out of bounds by {{tackler}} at the {{spot}}

## outOfBounds.loss  (out of bounds behind the line)
- ~ D - Forced out of bounds behind the line at the {{spot}}

## incomplete
- Incomplete
- Incomplete pass

## yardMarker  (every ten he passes, 5+ yards into his run)
- He's past the {{mark}}
- ~ Now past the {{mark}}

## yardMarker.midfield
- Still going past midfield
- He's past the 50

## yardMarker.ten
- He's inside the 10*

## yardMarker.five  (the 10's backup, when the run started too close to it)
- He's inside the 5*

## yardMarkerSticks
- He's past the sticks*
- First down and still going*

## longTouchdown  (a breakaway that goes all the way, before the call)
- {{carrier}} goes {{yards}} yards to paydirt!

## goalLineDive  (a short scoring run, a scramble, or a catch-and-run short of the open field, before the call)
- Dives for the end zone
- {{He}} dives for the touchdown

## interceptionRunBack  (picked and not down -- before the yard markers; §182)
- D - He's running it back
- ~ D - {{defender}} is running it back

## allTheWay  (a pick six, at half the distance from the pick to the goal line)
- D - He could go all the way!*

## pickSix  (an interception returned for a touchdown -- in place of the touchdown call)
- D - PICK SIX!**

## interceptionReturn
- ~ D - {{defender}} brings it back to the {{spot}}

## interceptionDown  (picked and down where he caught it)
- ~ D - {{defender}} goes down with it

---

# Trigger families (detected)

## pressureEscalates  (Tier 1 -- also said before every sack, so it does not come out of nowhere)
- D - {{qb}} is under pressure
- {{qb}} under pressure
- He's feeling the heat
- He's getting pressured
- D - Pressure is coming

## pressureEscalates.heavy  (the pocket at its worst)
- D - {{qb}} is under heavy pressure
- The pocket is collapsing
- D - Pressure coming fast

## pressureEscalates.blitz  (six or more coming)
- D - {{defense}} sends the house

## freeRusher  (Tier 2)
- {{defender}} has a clear lane to the QB

## sackContact  (§216: a rusher gets to him -- the same first beat for a sack, a strip sack and a broken sack, so it does not tip which; the outcome follows)
- D - {{defender}} gets to the QB
- ~ D - {{defender}} gets to {{qb}}

## sack  (** closed list -- the outcome, after sackContact)
- ~ D - {{qb}} goes down!**
- ~ D - Sacked!**

## sackEscape  (§207, §216: the outcome after sackContact -- he could not bring him down, and the play goes on, so never say where it ends up)
- ~ {{qb}} breaks free!*
- ~ {{qb}} shakes him off!*
- ~ {{qb}} slips out of it!*

## stripSack  (§216: the outcome after sackContact -- the ball, never "he goes down" first)
- ~ D - And the ball comes out!**
- ~ D - The ball is out -- strip sack!**

## scrambleExtends  (Tier 2 -- he is running, not rolling out to throw: the line has to say so)
- {{He}} abandons the pocket and takes off*
- ~ {{qb}} tucks it and runs*
- ~ {{qb}} takes off running*

## readsTheField  (Tier 1, new -- a clean pocket and time)
- {{qb}} looks for the pass
- He's surveying the field
- Scans the field
- He's got all day
- {{qb}} waits in the pocket
- Steps up in the pocket

## hotRoute  (Tier 2 -- no detector yet: the read list does not report which read fired)
- {{qb}} sees the blitz and get rid of it fast

## throwaway  (Tier 1)
- {{qb}} just throws it away

## cleanSeparation  (Tier 1 -- used as the throw-and-catch line)
- He finds a wide open {{receiver}} at the {{catch_spot}}

## cleanSeparationCheckdown
- Finds {{receiver}} wide open on the checkdown at the {{catch_spot}}

## doubleCoverage  (Tier 1 -- after the throw, before whatever became of it)
- There's heavy traffic
- Forces it in double coverage

## zoneHelp  (the "double coverage" with no man on him -- §182: the help is the only coverage, so say where it came from)
- D - There's help from the {{position}}
- ~ D - The {{position}} comes over to help

## breaksOnTheBall  (§211: a zone defender a cell from the catch closed on it in the air -- after the throw, before the catch or what stopped it; a defender at the catch means he was never wide open)
- ~ D - {{defender}} breaks on the ball
- ~ D - {{defender}} closes on the throw

## perfectStrike  (Tier 2 -- no detector yet: the throw phase keeps where the ball landed, not how close to perfect it was)
- Beautiful throw from {{qb}}

## deepCompletion  (** closed list -- 30+ air yards)
- {{receiver}} comes down with the deep ball at the {{catch_spot}}!!

## badThrow  (Tier 1 -- when the miss has no direction on the record)
- Can't get it on target

## badThrow.short  (§216: nobody could catch it, and it came down short)
- ~ The ball is underthrown
- ~ The throw comes up short

## badThrow.high  (§216: nobody could catch it, and it was long or high -- "his" is the receiver the throw line named)
- ~ It's thrown too high
- ~ The ball sails over his head

## badThrow.wide  (§216: nobody could catch it, and it went wide of him)
- ~ The throw is wide of him

## contestedCatch  (Tier 2)
- {{receiver}} makes the grab in traffic at the {{catch_spot}}*

## drop  (Tier 1)
- {{receiver}} drops it!
- {{receiver}} can't hold on to it
- He drops the pass

## passBreakup  (Tier 1 -- names the defender where it can)
- D - It's batted down
- ~ D - {{defender}} bats it down
- ~ D - {{defender}} breaks it up
- ~ D - {{defender}} gets a hand on it

## interception  (Tier 3, off the ** list)
- D - PICKED OFF BY {{DEFENDER}} AT THE {{CATCH_SPOT}}

## bigHitOnReception  (Tier 2 -- the ball may come loose, since §181)
- D - {{defender}} lays him out hard*
- D - {{defender}} lowers the shoulder on the hit*
- D - {{defender}} tags him with a big shot*
- D - {{defender}} with the big hit*

## holdsOn  (after a big hit on the catch -- he kept it)
- {{receiver}} holds on to the ball
- {{receiver}} still holds on

## knockedLoose  (after the hit -- he did not; §216: never "the ball is loose", which is a fumble)
- ~ {{receiver}} can't hang on
- ~ {{receiver}} can't hang on to it after the hit

## monsterHit  (** closed list -- on a catch or a carry)
- D - {{defender}} with a monster hit!!

## brokenTackle  (Tier 2)
- {{carrier}} stays on his feet
- Stays on his feet
- Shakes a tackle
- Breaks a tackle
- Breaks one tackle
- {{carrier}} breaks one tackle
- {{carrier}} cuts past a tackler
- D - {{defender}} can't get a hand on him
- {{carrier}} slips past {{number}} would-be tacklers

## brokenTackle.one  (exactly one)
- {{carrier}} slips past a would-be tackler

## brokenTackle.long  (only on a run of 10+ yards -- "they caught him just fine" otherwise)
- D - They can't get a hand on him!*
- D - They can't catch him!*

## brokenTackle.backfield
- He breaks the tackle in the backfield

## openFieldBreakout  (Tier 2)
- He's into the open field!*
- {{carrier}} has room to run
- He's got room to run
- He's got plenty of room
- He gets upfield with a full head of steam
- Runs over {{defender}} and keeps going
- {{carrier}} with a burst of speed
- He keeps his feet moving
- Can they catch him?

## breakaway  (** closed list -- a run of 30+)
- {{carrier}} breaks into the open!!

## bigHitOnCarry  (Tier 2)
- D - {{defender}} with the massive hit at the {{spot}}*
- D - Big hit by {{defender}}!
- D - {{defender}} makes the hit
- ~ D - {{defender}} wraps him up

## bigHitOnCarry.spot  (§215: the big hit a drag started from -- where it landed; then the drag)
- ~ D - {{defender}} with the big hit at the {{spot}}*
- ~ D - Big hit by {{defender}} at the {{spot}}!

## bigHitOnCarry.finish  (§212: the whole tackle -- only on a solo one, and the result after it is the net)
- D - {{defender}} wraps him up and takes him down

## bigHitOnCarry.backfield
- {{defender}} hits him behind the line

## fumbleForced  (** closed list -- somebody knocked it out; a line naming him is preferred. Say "the ball": "it" has nothing to point at)
- The ball is loose!**
- D - {{defender}} knocks the ball loose!**
- D - {{defender}} forces the fumble!**
- {{carrier}} loses the football!**

## fumbleExchange  (the handoff went down -- nobody forced it)
- He bobbles the exchange and drops it!*

## fumblePitch  (the pitch went down)
- ~ The pitch is on the ground!*

## fumbleLost  (§216: names who took it -- since §217 somebody always does; the team line is a fallback that should never be needed)
- ~ D - {{player}} takes it away!*
- ~ D - Recovered by {{player}}!*
- ~ D - {{defense}} comes up with it!*

## fumbleRecoveredByOffense  (§216: names who fell on it -- since §217 somebody always does, the fumbler himself when nobody else got it; the team line is a fallback that should never be needed)
- ~ {{player}} falls on it
- ~ {{team}} keeps it!

## cutback  (Tier 1)
- Cuts it back
- He cuts back against the grain
- He tries to cut it up field
- He sees a hole and cuts back

## bounceOutside  (Tier 1)
- Bounces it outside
- He bounces outside
- {{carrier}} bounces it outside

## laneOpens  (Tier 1, new -- the blocking win, from the runner's side)
- He's got a huge hole*
- He sees a big hole and makes the cut

## contactSpot  (§212: the hit before a runner drives through it -- where he was met, named; then extraEffort)
- ~ D - {{tackler}} hits him at the {{spot}}
- ~ D - {{tackler}} meets him at the {{spot}}

## extraEffort  (1-3 yards after contact, told after contactSpot: {{extra}} is "another yard", "another couple yards", "another 3 yards")
- {{carrier}} lowers his shoulder and grinds out {{extra}}

## extraEffort.drive  (§215: 4 or more after contact -- he is dragging them; then extraEffort.drag, or dragDown past 6)
- ~ {{carrier}} keeps driving his legs

## extraEffort.drag  (4-6 after contact, after extraEffort.drive)
- ~ He drags the defender {{extra}}

## extraEffort.dragDown  (7 or more after contact, after extraEffort.drive -- then couldNotBringHimDown)
- ~ Finally dragged down after {{dragged}} yards

## extraEffort.couldNotBringHimDown  (the defence's, after dragDown)
- ~ D - They just couldn't bring him down

## stuffedAtTheLine  (Tier 1-2)
- D - Stuffed by {{tackler}}*

## stuffedAtTheLine.loss  (the defence's, and always names the tackler)
- D - {{carrier}} brought down for the loss by {{tackler}}
- ~ D - {{tackler}} drops {{carrier}} for the loss

## missedTackleBehindTheLine  (the defender who could not finish it -- a miss, no contact)
- D - {{defender}} can't make the play on the ball

## backfieldContact  (hit behind the line, and he kept going)
- Hit in the backfield but he keeps moving
- ~ {{carrier}} takes a hit in the backfield and keeps going

---

# After the play

## firstDown  (a live runner crossing the line to gain -- the moment he makes it. Not a catch he was tackled out of: the net line says that)
- He's got the first down*
- Past the sticks
- He's past the first down marker*

## firstDownSuffix  (added to the net line: "Tackled after a gain of 16 - first down, Reno")
- first down, {{nickname}}
- first down

## thirdAndLongSuffix  (in place of firstDownSuffix, converting 3rd and 12 or more)
- huge 3rd down conversion

## firstAndGoalSuffix  (in place of firstDownSuffix, a first down inside the 10)
- first and goal

## fourthDownConversion  (** closed list)
- {{team}} converts on a big fourth down!

## touchdown  (** closed list)
- TOUCHDOWN**
- TOUCHDOWN {{NICKNAME}}**
- TOUCHDOWN, {{NICKNAME}}**
- TOUCHDOWN, {{NICKNAME}}!**

## touchdownDefense  (the defense scored)
- TOUCHDOWN**
- ~ TOUCHDOWN, {{DEFENSE_NICKNAME}}!**

## turnoverOnDowns  (** closed list)
- {{defense}} holds on 4th down!**

## safety  (** closed list)
- They bring {{carrier}} down and that's a Safety!**

## twoPointGood
- ~ The two-point try is GOOD*

## twoPointFailed
- ~ D - The two-point try fails*

## penaltyFlag  (after the net -- the flag may have come earlier, but it is told once the play has been)
- But wait, there's a flag on the play
- There's a flag on the play
- There's a flag on the play*

## penaltyFlag.preSnap
- ~ Flag down before the snap

## preSnapFalseStart
- {{player}} jumped before the snap

## preSnapEncroachment
- ~ D - {{player}} jumps into the neutral zone and makes contact

## preSnapNeutralZone
- ~ D - {{player}} jumps into the neutral zone

## penaltyAccepted  (the referee, after the play)
- PENALTY - {{penalty}}, {{side}} - {{yards}} yards
- {{penalty}}, {{side}} on {{player}}

## penaltyAcceptedFirstDown  (the referee, when it moves the chains -- always names the foul, and names it first)
- {{penalty}}, {{side}} - that penalty is accepted - {{yards}} yards and an automatic first down
- PENALTY - {{penalty}}, {{side}} - {{yards}} yards, automatic first down

## penaltyReturn  (a flag on the return, accepted -- where the ball went back to)
- ~ PENALTY - {{penalty}}, {{side}} - {{yards}} yards, back to the {{spot}}

## penaltyReturnHalfDistance  (the same, when half the distance to the goal was less than the foul's yardage)
- ~ PENALTY - {{penalty}}, {{side}} - half the distance, back to the {{spot}}

## penaltyDeclined
- ~ {{penalty}} on the {{side}} -- the penalty is declined

## penaltyOffsetting
- ~ Flags on both sides -- offsetting penalties, replay the down

## firedUp
- {{player}} is fired up!

## slowToGetUp
- {{player}} is slow to get up

## injured  (Tier 2-3, off the ** list)
- Looks like {{player}} is staying down after the play

---

# Special teams

## kickoff
- {{kicker}} kicks the ball off

## kickoffTouchback
- It goes out for a touchback

## kickReturn  (where he fields it)
- ~ D - {{returner}} takes it at the {{return_start}}
- ~ D - {{returner}} fields it at the {{return_start}}

## kickReturn.endZone  (fielded in the end zone)
- ~ D - {{returner}} takes it {{depth}} deep in the end zone and brings it out

## kickReturnTackle  (always names the tackler where there is one)
- ~ Brought down at the {{spot}}
- ~ {{tackler}} brings him down at the {{spot}}
- ~ Brought down by {{tackler}} at the {{spot}}

## returnFumble  (the return team put it on the ground -- the kicking team's colours)
- ~ {{tackler}} knocks the ball loose from {{returner}}!*
- ~ {{returner}} loses the football - {{tackler}} forced it!*

## returnFumbleRecovered
- ~ {{team}} recovers at the {{spot}}!*

## bigReturn
- ~ D - {{returner}} finds a seam!*

## muffedKick
- ~ He can't hold on! The kicking team recovers*

## onsideKick
- ~ {{kicker}} tries the onside kick

## onsideRecoveredKicking
- ~ The kicking team comes up with it!*

## onsideRecoveredReceiving
- ~ D - The receiving team has it

## punt
- ~ {{punter}} punts it away

## puntFairCatch
- ~ D - {{returner}} calls for the fair catch at the {{spot}}

## puntOutOfBounds
- ~ It rolls out of bounds at the {{spot}}

## puntDowned
- ~ Downed at the {{spot}}

## puntTouchback
- ~ Into the end zone for a touchback

## puntPinned
- ~ Pinned deep at the {{spot}}*

## fieldGoal
- ~ {{kicker}} lines up a {{yards}}-yard attempt

## fieldGoalMade
- ~ The kick is GOOD*

## fieldGoalMissed
- ~ D - No good!*

## kickBlocked
- ~ D - BLOCKED!*

## extraPointLineup
- {{team}} lines up for the extra point

## twoPointLineup
- {{team}} lines up for the two point conversion

## extraPoint
- Extra point is GOOD

## extraPointMissed
- ~ D - The extra point is no good*
