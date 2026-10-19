# 500cardGameContract
the api contract and other agreements btwn the client and server

Currently two JSON files both repos build against.

- `wire.json`: message type names and payload field names in each direction, `gameState` numbers, pace values,
  and error codes.
- `timeouts.json`: competitive turn deadlines in milliseconds, the relaxed multiplier, server grace, client autoplay
  margin, and trick hold.

Versioning: bump `version` in both JSON files and `package.json` together, commit, and publish a GitHub release tagged `X.Y.Z` (plain semver, no prefix). Consumers:

- Client: npm git dependency `github:JamesDo5/500cardGameContract#semver:^1`, imported as JSON.
- Server: `make contract` fetches the highest semver tag and generates Go constants.

Semantics the JSON cannot express live in the server repo's `server-contract.md`.

## Semantics added in 1.1.0

- `playCard.suit`: required only when leading the joker in a no-trump hand, one of `s c d h`; the trick is then
  played as that suit and others must follow it. Ignored otherwise. Bad or missing value: `BAD_SUIT`.
- `cardPlayed.suit` and `syncState.jokerSuit`: the nominated suit for the current trick, empty when none.
- `restartGame`: host only, any phase; the room returns to the lobby with scores cleared and the same seats.

## Semantics added in 1.3.0

- `addBot { seat }` / `removeBot`: host only, lobby only. `seat` is the relative seat to fill (optional; the first
  empty seat is used when it is missing or occupied). At most `lobbyState.maxBots` bots per room (1 in production).
  Errors `NOT_HOST`, `WRONG_PHASE`, `BOT_LIMIT`, `NO_BOT`.
- `setBotSkill { skill }`: any seated player, lobby only, one of `rote` or `smart` (default `smart`), else `BAD_SKILL`.
  Carried on `lobbyState.botSkill`.
- `handReview`: sent to every player right after `handScore`, positions relative to the recipient.
  `moves` is `[4][10]` of `{ card, grade, best }` in play order per seat, `grade` one of `only` (no real choice),
  `blunder` (clearly worse than the best play given what that player had seen), `gamble` (won the trick with a play
  that was unlikely to work), `close` (statistically sound but within a few percent of the best play), `best`
  (the evaluator's own choice with other options available). `scores` is `[4]` integers 0 to 100 for play quality.
  `bids` is `[4]` of `{ bid, grade, suggested }` grading each seat's bid against the evaluator's bid for that hand.
- `timeouts.handEndHoldMs`: after the tenth trick the server holds the table this long before the dealer's `turn`,
  so every client can show the result and the review; plays and deals in the gap are rejected.
