# 500cardGameContract
the api contract and other agreements btwn the client and server

Currently two JSON files both repos build against.

- `wire.json`: message type names and payload field names in each direction, `gameState` numbers, pace values,
  and error codes.
- `timeouts.json`: competitive turn deadlines in milliseconds, the relaxed multiplier, server grace, client autoplay
  margin, and trick hold.

Versioning: bump `version` in both JSON files and `package.json` together, commit, and tag `vX.Y.Z`. Consumers:

- Client: npm git dependency `github:JamesDo5/500cardGameContract#semver:^1`, imported as JSON.
- Server: `make contract` fetches the latest `v*` tag and generates Go constants.

Semantics the JSON cannot express live in the server repo's `server-contract.md`.
