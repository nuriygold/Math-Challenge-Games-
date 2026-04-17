# Connect the Dots: Math Challenge Games → GED Bestie

This is the concrete wiring path from game events to persisted learner progress.

## 1) Host the game inside GED Bestie
- Serve/copy this repo as a static app (or mount route assets).
- Route example: `/games/math-challenge`.
- Ensure users are authenticated before ranked play.

## 2) Create the host-side bridge object
In GED Bestie, define `window.GED_BFF_BRIDGE` **before** the game script runs.

```js
// Example host bridge (GED Bestie app shell)
const API_BASE = '/api/games/math-challenge';

async function postJson(path, payload) {
  const response = await fetch(`${API_BASE}${path}`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    credentials: 'include',
    body: JSON.stringify(payload)
  });

  if (!response.ok) {
    const text = await response.text();
    throw new Error(`Bridge API ${path} failed: ${response.status} ${text}`);
  }

  return response.json().catch(() => ({}));
}

window.GED_BFF_BRIDGE = {
  onGameStart(payload) {
    return postJson('/start', payload);
  },

  onQuestionAnswered(payload) {
    return postJson('/question', payload);
  },

  onGameComplete(payload) {
    return postJson('/complete', payload);
  }
};
```

## 3) Implement backend endpoints
Create three endpoints in GED Bestie backend:
- `POST /api/games/math-challenge/start`
- `POST /api/games/math-challenge/question`
- `POST /api/games/math-challenge/complete`

### Minimum backend behavior
- Resolve authenticated `userId` from session/JWT.
- Validate payload shape and enums (`gameMode`, `difficulty`).
- Store attempt/session rows.
- Aggregate profile stats (best score, streak, accuracy trend).

## 4) Map emitted payloads to tables
Suggested mapping:
- `game_sessions`: start/end, mode, difficulty, score, accuracy, maxStreak.
- `game_events`: per-question telemetry (latency, correctness, answer, question number).
- `learner_metrics`: rolling aggregates for profile/dashboard.

## 5) Feature-flag rollout
- Add flag: `games.mathChallenge.enabled`.
- Enable for internal users first.
- Verify event volumes and error rates.

## 6) Validate end-to-end quickly
1. Open game route while logged in.
2. Play one short session.
3. Confirm 3 event types hit backend.
4. Confirm attempt appears in learner profile/progress feed.
5. Confirm failures do not break gameplay (bridge errors are non-fatal).

---

## Important note
This repository already emits the required events:
- `onGameStart`
- `onQuestionAnswered`
- `onGameComplete`

So your GED Bestie PR should focus on the host bridge and backend endpoint wiring.
