# fallineage

**Live:** [sjgant80-hub.github.io/fallineage](https://sjgant80-hub.github.io/fallineage/)

**Ed25519-signed provenance chains.** Answers one question un-forgeably: *who made this, from what, in
what order?* A lineage is a chain of records — a root **mint** and successive **forks** — each signed
by its author's key and pointing at its parent by the parent's own content-address. `verifyLineage()`
rejects any tampering, any broken parent link, and any forged signature.

The LINEAGE descent of the fallkard ecosystem: real signatures split cleanly from content-address.

## Three identifiers, never conflated

- **contentHash** — *what* the artifact is (SHA-256 of its bytes). Two people can hold the same
  content; that says nothing about who authored the lineage.
- **record id** — the content-address of the provenance record itself. A child points at this, so a
  forged parent can't be swapped in without breaking the chain.
- **sig** — an Ed25519 proof that *this author* minted *this record*. Not transferable, not forgeable
  without the private key.

## Use

```js
import { generateIdentity, mint, fork, verifyLineage } from './lineage.mjs';

const alice = await generateIdentity();
const bob   = await generateIdentity();

const root = await mint('the original build', alice);
const v2   = await fork(root, 'bob improved it', bob);

const result = await verifyLineage([root, v2]);   // { valid, depth, root, tip, authors, breaks }
```

## Test

```
npm test
```

Real Web Crypto Ed25519 (browser + Node ≥ 20). Zero dependencies. The private key never leaves the
identity object.
