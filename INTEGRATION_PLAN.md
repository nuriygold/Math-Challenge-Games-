# Math Challenge Games → GED Bestie (GED BFF App) Integration Plan

## Short answer
Yes — the Math Challenge Games can be a **feature module** inside the GED BFF app.

The fastest path is to treat this repository as a standalone web mini-app and embed it in GED BFF first, then progressively refactor shared services (auth, analytics, progress, and theming).

---

## Recommended architecture

### Option A (recommended first release): Embedded Web Module
- Package this repo's `index.html` (plus extracted JS/CSS assets) as a static web bundle.
- Mount it in GED BFF under a route like `/games/math-challenge`.
- Use a bridge API for:
  - `onGameStart`
  - `onQuestionAnswered`
  - `onGameComplete`
  - `getUserProfile`
  - `saveAttempt`
- Persist scores to GED Bestie backend with the authenticated user context.

**Pros**: Fastest, low-risk, preserves existing gameplay.  
**Cons**: Some duplication of UI patterns and state handling.

### Option B (phase 2): Native GED BFF Feature Rewrite
- Rebuild core game views/components in GED BFF's front-end framework.
- Reuse GED BFF design system, routing, state, telemetry, and accessibility patterns.

**Pros**: Best long-term maintainability and cohesion.  
**Cons**: Slower initial delivery.

---

## Integration checklist

1. **Extract assets from monolith file**
   - Split `index.html` into:
     - `games/math-challenge/index.html`
     - `games/math-challenge/styles.css`
     - `games/math-challenge/game.js`
   - Add simple config object for environment-driven settings.

2. **Define shared contracts**
   - Attempt payload example:
     ```json
     {
       "userId": "uuid",
       "gameMode": "speed|time-attack|survival|practice",
       "difficulty": "easy|medium|hard",
       "score": 1230,
       "accuracy": 88,
       "maxStreak": 14,
       "durationSec": 60,
       "completedAt": "2026-04-17T00:00:00Z"
     }
     ```
   - Add contract tests on GED Bestie API side.

3. **Connect GED identity + persistence**
   - Require logged-in user for ranked sessions.
   - Save attempts and personal bests.
   - Optional anonymous mode for practice.

4. **Map gameplay to GED learning outcomes**
   - Tag each question with:
     - domain (Number Operations, Algebraic Thinking, etc.)
     - skill code
     - difficulty tier
   - Feed results into GED Bestie recommendation engine.

5. **Analytics + coaching loop**
   - Track events:
     - game opened
     - question answered (correct/incorrect, latency)
     - mode completed
   - Generate next-step nudges (e.g., “Practice integer subtraction — accuracy below 70%”).

6. **UX integration**
   - Add navigation entry: `Practice → Challenge Games`.
   - Add “Play 5-min warmup” CTA in daily study plan.
   - Display game-derived XP/badges in GED BFF profile.

7. **Quality + accessibility**
   - Keyboard-only play and focus visibility.
   - Screen-reader labels for controls and score updates.
   - Performance budget for low-end mobile devices.

8. **Release strategy**
   - Internal beta → 10% cohort → full release.
   - Success metrics:
     - D7 retention uplift
     - session completion rate
     - math accuracy trend over 2 weeks

---

## Suggested GED BFF feature flags
- `games.mathChallenge.enabled`
- `games.mathChallenge.ranked`
- `games.mathChallenge.dailyQuest`
- `games.mathChallenge.leaderboard`

Use flags to roll out safely by cohort.

---

## Practical migration timeline

### Week 1
- Extract assets + route mount in GED BFF shell.
- Wire auth context + score save endpoint.

### Week 2
- Add analytics events + dashboard.
- Integrate progress cards into GED Bestie learner profile.

### Week 3
- Accessibility hardening + bug fixes.
- Controlled rollout and A/B metrics review.

---

## Risks and mitigations
- **Risk**: Embedded app feels visually inconsistent.  
  **Mitigation**: Apply GED BFF theme tokens to colors/typography in phase 1.

- **Risk**: Duplicate logic between game and study engine.  
  **Mitigation**: Centralize skill taxonomy and scoring in shared service.

- **Risk**: Mobile input friction.  
  **Mitigation**: Keep numeric keypad, add large tap targets, and haptic feedback where supported.

---

## Recommendation
Ship as an embedded feature first (Option A), validate engagement and learning impact, then selectively refactor to native GED BFF components.
