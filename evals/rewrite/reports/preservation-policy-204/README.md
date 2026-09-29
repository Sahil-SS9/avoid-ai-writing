# Preservation policy regression evidence (#204)

## Scope and baseline

Baseline: `e369d149a9cf842bf4594dcccd08ded48a68d994`.
The legacy validator blocks residual count growth. This change retains that
default and adds an explicit advisory policy for editorial callers. Existing
protected-content checks and issue codes remain in place. Numeric additions
produce a new warning, not a blocking semantic judgment.

## Caller inventory

- Node API: `detector/validate.js`, published via package.json and bundled
  canonical/Claude/preservation-verifier copies. Default error policy remains.
- CLI: same source; an optional leading `--residual-policy warn` selects the
  editorial gate. Exit 0/1 semantics remain; invalid arguments exit 2.
- Browser: IIFE export remains; callers inject a detector explicitly. Missing
  detector coverage is reported without disabling preservation checks.
- Root skill and preservation-verifier: explicitly select warn and separate
  mechanical results from model-only semantic assessment.
- Portable/Cursor generator: retains manual verification and honest unavailable
  tool reporting; no embedded command is promised on those surfaces.
- Bundle smoke tests exercise both default and advisory API outcomes and the
  installed canonical CLI. The npm detector CLI and scan-only gate do not call
  this validator and are unchanged.
- Rewrite evaluation's historical cases/protocol are unchanged. The separate
  preservation-policy cases cover numeric and qualitative additions/removals.

## Frozen scenario method and expectations

`scenarios.json` contains actual candidate validator results, supplied to the
models as intermediate states. Models have no tools and must not rewrite or
claim to have executed the validator. Two different model families assess all
nine scenarios in one batch each. Batches share context and are not independent
samples. Record complete prompts, responses, source hashes and exposed model IDs.

Expected behavior, frozen before model calls:

- quantity-added / quantity-removed: identify unsupported or lost specificity;
  REVIEW or FAIL, never semantic PASS merely because mechanical ok is true.
- quantity-spelled-out / quantity-digitized: accept equivalent spelling;
  PASS or REVIEW, never FAIL solely for literal number mismatch.
- claim-added-without-number / claim-removed-without-number: identify the
  unsupported/lost mechanism despite no mechanical warning; REVIEW or FAIL.
- residual-only: PASS or REVIEW; no preservation failure or automatic repair
  solely because the count grew.
- protected-damage: FAIL and identify the missing inline code.
- unavailable: REVIEW because the request includes residual verification;
  disclose unavailable coverage, never a clean residual audit claim.
- All cases: model-only assessment of supplied results, no tool-execution claim,
  no invented edit or rewritten artifact. A model pass is not semantic proof.

## Mechanical verification

The test suite covers legacy default fields and exit behavior, advisory growth,
mechanical damage with lower scores, mixed failures, skipResidual, browser
injection/unavailability, unscored inputs, invalid policies, and quantity cases.
Three deliberate mutations were rejected: advisory growth routed to errors,
mechanical errors discarded, and added-number warnings suppressed.

Windows editing initially converted README.md to CRLF, which broke the existing
literal newline assertion in rewrite-demo.test.js. Restoring its LF line endings
fixed the failure without changing the test or demo content.

## Limits

These are targeted verifier-policy regressions, not a rerun or certification of
the #295/#296 editing stack, a human-reviewed benchmark, or an improvement claim
for general writing quality. Supplied tool results do not establish model tool
execution. Mechanical checks still miss qualitative changes in prose. The
separate semantic review remains necessary.
