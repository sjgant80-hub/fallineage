# CLAUDE.md · fallineage

Instructions for any agent working in this repository. See `SPEC.md` for the contract. The LINEAGE
descent of the fallkard ecosystem.

## Invariants to preserve — these are security-critical

1. **The signature covers the canonical claim, and the id content-addresses the record.** Never sign
   or hash a different set of fields than `canon()` defines. Changing what is signed silently breaks
   forgery detection.
2. **Verify against the record's OWN author key** (imported from `rec.author`), then check the id
   matches. Both checks are required: the id catches tampering, the signature catches forgery.
3. **Parent links are by record id, not by index or content.** A fork must point at the previous
   record's `id`. Do not "repair" a broken link automatically — report it in `breaks`.
4. **contentHash ≠ id ≠ author.** Keep the three separate. Collapsing content-address into provenance
   would let anyone holding the bytes claim authorship.
5. **Real Web Crypto Ed25519, never a mock.** Tests exercise the actual engine. A change that reddens
   `npm test` — especially the tamper/forge/broken-link tests — does not ship.
6. **The private key never leaves the identity object.** Only `pub` (hex) goes into records.

## Run
```
npm test
```
CI runs `npm test` on every push.

## Seam

Public, general-purpose provenance/signing tool. Ed25519 / content-address / lineage language only.
Estate vocabulary (fork/mint/lineage/card) is fine; do NOT introduce the private cosmology (no
κ/θ/Ψ, no element or dyad references, no "the Thirteen").
