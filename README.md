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
