# fallineage · design note

> Spec: **fallineage-spec-v1**. The engineering contract of the provenance-chain engine.

## Surface

`lineage.mjs` exports:

- `generateIdentity()` → `{ keyPair, pub }` — a fresh Ed25519 identity; `pub` is hex, the private key
  stays in `keyPair`.
- `mint(content, identity)` → root record `{ contentHash, author, parent:null, seq:0, sig, id }`.
- `fork(parent, content, identity)` → child record pointing at `parent.id`, `seq = parent.seq + 1`.
- `verifyRecord(rec)` → `{ valid, reason? }`.
- `verifyLineage(chain)` → `{ valid, depth, root, tip, authors, breaks }`.
- `sha256Hex(str)`.

## The record

Signed canonical form is `{ contentHash, author, parent, seq }`. Then:

- `sig` = Ed25519 over that canonical form.
- `id`  = SHA-256(canonical + sig) — the record's own content-address, which children reference.

## Invariants

1. **Un-forgeable authorship.** A record's `sig` must verify against its `author` public key; swapping
   the author (keeping a stale sig) fails.
2. **Tamper-evident.** `id` content-addresses the record; altering any field breaks the id.
3. **Linked by id.** A fork's `parent` must equal the previous record's `id`; `seq` increments by 1;
   the root has `parent:null`, `seq:0`.
4. **Content-address split from provenance.** Same `content` ⇒ same `contentHash` but distinct records
   (`id`, `author`) — holding the bytes is not authoring the lineage.
5. **Real crypto, no mock.** Tests run against the Web Crypto Ed25519 engine. Zero dependencies.

## Verification

`npm test` — identity shape, mint/fork verify, and the three attacks (tampered content, forged
authorship, broken parent link) plus seq/empty-chain guards. CI runs it on push.
