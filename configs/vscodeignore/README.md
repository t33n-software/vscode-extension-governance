# configs/vscodeignore

The fragment catalog of the packaging ignore surface: the canonical
composition model from which every extension tenant's `.vscodeignore` is
rendered, pinned, and proven byte-exact by the canonical conformance verifier.

## Layout grammar

The catalog is organized by theme area, mirroring the governed fragment
library of the version-control ignore surface:

- `core.vscodeignore` sits at the fragment root — the universal core, always
  the first fragment of every composition.
- Every other fragment lives under its area directory as
  `<area>/<concern>.vscodeignore`, with exactly one of the areas
  `node/`, `package-manager/`, `bundler/`, `testing/`, `linting/`.
- Fragment identifiers in tenant bindings use the same grammar: `core`,
  or `<area>/<concern>` in kebab case. No deeper nesting, no brace
  patterns anywhere in the pattern content.

## Composition law

A tenant binds an ordered fragment list. Composition is deterministic:

1. `core` first — the manifest schema enforces it at position zero.
2. After `core`, the fragments appear **in the order the tenant declared
   them**. There is no catalog-imposed order: the list itself is the
   contract, and its sequence is what the rendered bytes pin against.
3. A Node extension conventionally follows `core` with `node/core`, then
   its capability fragments (one package manager, then bundler, testing,
   linting concerns); the convention is authoring guidance, not a schema
   law — the order above is the law.
4. Each fragment is emitted with its own seam header naming the fragment;
   the composed file is the governed region a tenant carries
   byte-identically.

## Areas are multi-bindable

An ecosystem is a conglomerate, not a monolith. A repository may
legitimately bind fragments from several areas — a Next.js application
carries Node surfaces and static-asset surfaces, a Python service carries
Python surfaces and shell-script surfaces. The single-valued axes of the
platform are different contexts: the configuration seam declares one
language for **CI routing**, and the package manager is one **derived
fact**; neither compresses the packaging content to one area. The catalog
therefore lists areas independently, and the verifier never restricts how
many areas a tenant binds.

## Scoping laws

- **Narrowest-fragment rule.** A pattern belongs in the narrowest fragment
  whose scope guarantees its correctness. `tsconfig*.json` is a Node-area
  pattern, not a core one; `**/*.map` is universal, therefore core.
- **Deliberate overlap between alternative fragments.** The
  `package-manager/` area is the single **structurally exclusive** choice:
  exactly one package manager is proven, and that fact is **derived from
  the owner surface** (`package.json#packageManager`), never declared in
  the tenant's ignore binding. The verifier therefore rejects any
  composition whose package-manager fragment diverges from the derived
  identity — and because exactly one is ever composed, `.npmrc` and
  `.node-version` legitimately repeat across the three package-manager
  fragments (the same holds for a bundler area fragment that repeats a
  pattern owned by no other area). The overlap guard operates **per
  composed file**, where never-co-composing patterns cannot collide.
- **No cardinality fields anywhere.** The bound list is the declaration of
  multiplicity: one entry means one, two entries mean two. Selection
  sources (derived versus declared) are verification logic owned by the
  fleet interpreter and the catalog's prose below — never tenant-side
  flags.
- **Capability completeness.** The catalog lists capabilities a consumer may
  lawfully adopt. An organization default change adds or annotates rows and
  never deletes or substitutes another capability's row; a fragment retires
  only through a supersession decision.
- **Pattern portability.** Patterns use the closed dialect-safe form set:
  path class (`scripts/**`), starred contract name (`tsconfig*.json`),
  extension class (`**/*.ts`), exact name (`lefthook.yml`). Brace expansion
  is forbidden because the ignore grammar matches braces literally — a
  brace pattern fails open into shipping what it means to exclude.

## The registry

`registry.json` is the machine-readable membership catalog of the library:
`schemaVersion`, and one entry per area carrying a human-readable `title`
and the `concerns` the area publishes (`core` has none — the root fragment
is the area). It answers exactly one question for consumers and the
verifier: **which fragment identifiers exist in which area**.

It deliberately carries nothing else:

- **No patterns** — pattern content lives exclusively in the fragment
  files, so no second authority exists.
- **No composition order** — the tenant's declared list is the order (§
  Composition law).
- **No selection or cardinality flags** — how a value is proven (derived
  from `package.json#packageManager`, or freely declared) is fleet
  interpreter logic plus the laws in this README, never a tenant-facing
  field.
- **No empty mirrors** — a concern row is born only when at least one
  pattern legitimately belongs to it, mirroring the territory registry's
  content-driven birth rule.

## Verification surface

The canonical conformance verifier proves, fail-closed: every bound
identifier exists in this library's registry (`core` or
`<area>/<concern>`); `core` sits at position zero; the composed region is
byte-identical to the tenant's `.vscodeignore`; no pattern is restated
inside one composition; and the composed package-manager fragment matches
the identity derived from the tenant's `package.json`. An unimplemented
proof is a finding, never a skipped proof.
