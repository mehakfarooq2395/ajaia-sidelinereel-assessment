# Ajaia — Product Manager Assessment

**Video walkthrough:** [Watch the Loom video walkthrough](https://www.loom.com/share/9593258840f142a39b71fe827233b3b7)

**Candidate:** Mehak Farooq  
**Product:** SidelineReel  
**Company:** [Ajaia](https://ajaia.ai)

## Task 1 — The roadmap call

### Decision

I would build **Smart Roster Verification** next: improve player attribution and prevent uncertain matches from reaching families through a targeted coach review workflow. SidelineReel's core promise is to deliver the right child's highlights to the right family. I would protect that promise before expanding into livestreaming or advanced editing.

### Evidence and interpretation

| Source | Observed signal | Implication |
|---|---|---|
| M1 — Ridgeline email | Largest club requests livestreaming before signups in six weeks; Carla credits highlights with half of parent renewals. | The renewal needs immediate attention, but the existing highlight experience is already commercially valuable. |
| M2 — Research | 58% of 210 survey respondents across 40 clubs request editing controls; 6% of parents opened the editor and fewer than 1% of those completed an edit. Auto-recap Share was used in 90% of recaps sent. | Automated, shareable content has strong observed engagement. Editing friction, discoverability or limited demand may explain the survey/usage gap; none is yet established. |
| M3 — Support | Three tickets across soccer, lacrosse and baseball describe incorrect player attribution involving similar jersey numbers. Separate tickets concern the app icon and watermark. | Attribution is a recurring, cross-account trust issue. Tickets establish examples, not the population-wide error rate. |
| M4 — Sales | $180k seasonal renewal at risk; Sales wants a concrete commitment. | Material commercial exposure, but a threat to shop around is not proof that renewal is lost without livestreaming. |
| M5 — Dashboard | 92% of opened personal reels are shared; roughly 30% of generated reels are unopened. Analytics identifies late notifications and wrong-child thumbnails as contributors. | The dashboard hides upstream friction. Attribution and delivery timing both warrant attention; their relative contributions are unknown. |

### Correcting the health metric

For every 100 generated personal reels, approximately 70 are opened and 92% of those are shared. Therefore, approximately **64.4% of all generated reels are shared** (70% × 92%), a gap of 27.6 percentage points from the reported conditional rate. This is an estimate based on the rounded figures provided, not a newly measured baseline.

I would replace the headline metric with unique generated personal reels shared at least once within seven days divided by all personal reels generated in the same mature cohort. I would retain open rate and share-after-open rate as diagnostic metrics, and separately track eligible reels withheld from generation to avoid hiding failures through suppression. The supplied data do not specify a seven-day window, so the new baseline must be remeasured. Team auto-recap sharing is a separate metric.

The editor completion rate is below 0.06% of parents if the reported populations are consistent (6% × less than 1%). I would inspect the funnel and interview parents before concluding that editing has no value.

### Ranked roadmap

| Rank | Candidate | Rationale and action |
|---|---|---|
| 1 | Roster tagging accuracy and exception review | Build next. Direct trust impact, repeated cross-sport evidence, and a plausible connection to unopened reels. Moderate relative complexity, subject to engineering discovery. |
| 2 | Notification timing | Investigate in parallel; test appropriate local delivery windows next. Directly addresses another reported cause of unopened reels. Likely smaller effort, but impact is unquantified. |
| 3 | Reel Editor improvements | Discovery first. Observe editing attempts and identify abandonment causes before expanding controls. |
| 4 | Livestreaming | Defer development; prioritize commercial discovery immediately. Large account exposure but no established wider demand, and likely substantial streaming, reliability, rights and moderation requirements. |
| 5 | Missing watermark | Maintenance backlog; reproduce and assess the defect. Isolated, low-urgency report. |
| 6 | App icon redesign | Defer. Isolated feedback and limited evidence of meaningful customer impact. |

This is a qualitative ranking: the materials do not provide reach, engineering estimates or attributable revenue by initiative, so numerical scoring would imply false precision.

### Ridgeline: commercial urgency without an unsupported commitment

The strongest argument against my decision is the potential loss of $180k in seasonal revenue. I would not dismiss it. However, I would not commit to building livestreaming before understanding feasibility, cost and whether it is a genuine renewal condition.

Within two business days, I would meet Carla and Sales to clarify the board's must-have requirements, alternatives and renewal conditions. Within one week, I would return with a concrete decision date, a feasibility assessment of a partner-based or limited streaming option, and the proposed roster reliability pilot. I would not promise delivery before engineering validates it.

I would change the roadmap if the renewal is demonstrably contingent on streaming and a viable option has sufficient commercial value relative to cost, displaced roadmap work and child privacy risks. I would also reconsider the top priority if labeled footage shows attribution errors are isolated and notification timing explains substantially more lost engagement. Until then, I would protect the existing product value while actively managing renewal risk.

## Task 2 — Product specification

### Smart Roster Verification (MVP)

**Outcome:** Reduce incorrect player attribution without delaying weekly recaps or requiring coaches to manually review every highlight.

**Primary user:** Authorized team coach. **Beneficiaries:** Players and families.

**User story:** As a coach, I want uncertain player matches flagged before distribution so I can correct them quickly and families receive accurately attributed highlights.

### Scope

In scope: jersey-to-roster matching safeguards, ambiguity detection, coach exception queue, confirm/reassign/exclude actions, corrected personal reel and thumbnail assignment, release gating and audit history.

Out of scope: livestreaming, facial recognition, editor expansion, notification timing changes, full manual review, and automatic model retraining from unverified coach feedback.

### Workflow and business rules

1. For each detected highlight, the system evaluates the jersey reading against the game-specific roster. It stores the highlight ID, team/game ID, candidate player IDs, detection evidence, model version and confidence.
2. Auto-approve only a unique match meeting a calibrated confidence threshold and minimum separation from the next candidate. Conflicting readings, partial numbers, missing roster matches and duplicate numbers must enter review regardless of score. Engineering and product will establish thresholds on labeled footage across sports before rollout; uncalibrated matches remain pending.
3. Show authorized coaches only their team's pending clips, with playable footage, timecode, suggested player, ambiguity reason and roster choices. The coach can confirm the suggested player, reassign to another eligible player, or exclude the clip. No player is selected by default for manual approval. An absent player requires roster correction through the existing roster workflow and re-evaluation.
4. Maintain one authoritative attribution state per highlight: pending, auto-approved, coach-approved or excluded. Only approved highlights can enter personal reels or the MVP team recap. Reassignment invalidates any draft reel or thumbnail derived from the old assignment. Do not expose a pending clip to families.
5. At the existing scheduled recap cutoff, automatically release approved content and exclude unresolved clips from that delivery; never guess an identity or delay the whole team recap. If a personal reel has no approved clips, suppress its misleading notification and record the reason. Late corrections may be included in a later eligible delivery, not silently resend the original recap.
6. Release uses a consistent snapshot of approved assignments and an idempotency key for team/week/output. Concurrent review and release must not allow pending or stale assignments to escape. Log original and final assignments, reviewer or system actor, action, timestamp and release status. Restrict footage and logs to authorized team staff and existing approved family access rules.

### Human oversight

Coaches resolve exceptions, not every match. If identity cannot be verified from footage and roster information, exclusion is preferable to guessing. Human-labeled samples of auto-approved clips are necessary to catch confident but incorrect predictions. I would avoid adding new biometric identification of children to this MVP.

### Testable acceptance criteria

| ID | Given / when | Then |
|---|---|---|
| AC01 | A unique eligible roster match passes configured thresholds with no ambiguity flags | Auto-approve and make the clip eligible for the correct reel. |
| AC02 | Similar numbers #4/#14, #1/#11, conflicting readings or duplicate jersey numbers are detected | Route to pending review; exclude from all family-facing outputs until resolved. |
| AC03 | An authorized coach explicitly confirms or selects another eligible player | Save approved assignment; remove the previous attribution and regenerate affected draft thumbnail/reel. |
| AC04 | Coach excludes an unverifiable clip | Omit it from both personal reels and team recap; record exclusion. |
| AC05 | Recap cutoff arrives with pending clips | Send eligible approved content on schedule; log pending clips as withheld for that delivery. Suppress empty personal reel notifications. |
| AC06 | A user outside the authorized team attempts to view or edit a pending highlight | Deny access without exposing footage or roster information. |
| AC07 | Release is retried, or two release requests arrive concurrently | Create at most one delivery per output key; use only the committed approved snapshot. |
| AC08 | Coach leaves without saving, or saving fails | Keep the previous state; pending content stays withheld. Show an actionable error on failed save. |
| AC09 | Any approval, reassignment, exclusion or release occurs | Record an auditable event with actor, timestamp, original and final state. |

### Top two failure modes

| Failure | What catches it / response |
|---|---|
| Confident but incorrect auto-approval | Compare a stratified random sample of auto-approved clips against human-labeled footage, including similar-number cases. Track confirmed misattribution reports. If systematic errors emerge, pause auto-approval for affected cases and recalibrate before expanding. |
| Coach review overload results in missing highlights | Monitor flagged volume per game, completion time, pending-at-cutoff and exclusion rates alongside delivery timeliness. If review burden or coverage deteriorates, improve routing and coach reminders rather than silently lowering accuracy safeguards. |

### Measurement and rollout

Primary metric: confirmed incorrect attributions / audited delivered highlights, measured on a representative labeled sample. Proposed target: reduce the baseline error rate by 80%. Support reports per 1,000 delivered highlights provide a separate diagnostic signal, not a substitute for audited accuracy.

Secondary metrics: personal reel open rate and end-to-end share rate, using consistent cohort windows. Directional targets after re-baselining: open rate 80% and share rate 72%. These are hypotheses, not committed outcomes; notification timing may influence both.

Guardrails: median coach review time (proposed target under 30 seconds per clip), on-time recap delivery (no deterioration), approved highlight coverage and withheld/excluded rate. Establish all missing baselines first.

Pilot with consenting clubs across soccer, lacrosse and baseball. Validate thresholds in shadow mode before enabling automatic decisions. Review accuracy, workload and coverage weekly. Expand only when accuracy improves without material deterioration in guardrails; otherwise revert to a safer review configuration and investigate.

## Task 3 — Working prototype

**Runnable prototype:** [Open Smart Roster Verification](https://mehakfarooq2395.github.io/ajaia-sidelinereel-assessment/sidelinereel.html).

The self-contained browser prototype demonstrates three steps:

1. **Review queue:** four synthetic highlights, two flagged for ambiguous jersey numbers and two automatically approved.
2. **Verify match:** open H01, reassign the suggested #4 to #14, then open H02 and exclude it if the identity cannot be confirmed. An explicit roster selection is required to approve.
3. **Preview and release:** inspect the final player assignments and review history, then simulate one release. The preview remains blocked while unresolved clips exist; repeated release is guarded in the demo session.

The prototype demonstrates the decision workflow, not production recognition. It uses fictional players, illustrative jersey cues and simulated confidence scores. It does not implement real video playback, authentication, persistent audit storage, scheduled cutoff processing or family notifications. The manual preview requires all exceptions to be resolved; production automatic release instead omits unresolved clips at the cutoff. This is a deliberate demo scope limitation, not a change to the specification.

## Task 4 — AI workflow note

I used ChatGPT to synthesize the five materials, challenge the dashboard denominator, draft the specification and generate a runnable HTML prototype. I reserved the final roadmap decision, commercial trade-offs and acceptance of product risks for my own judgment. One AI-generated shortcut I would not adopt is treating simulated confidence scores as production-ready approval thresholds: those values demonstrate the interaction only and must be calibrated against labeled footage before release.
