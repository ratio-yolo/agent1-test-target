# Phase 2 CI-red verification

This file exists to give the PR a non-workflow diff so reviewers see the intent.

The accompanying `.github/workflows/test-ci.yml` is intentionally a failing CI
job — it exercises `scripts/wait_for_ci.py` (Phase 2 of the Builder Push-Only
+ Stateless CI design, spec §7.2) on a CI-red branch.

Expected behavior when `review.yml` is dispatched against this PR with
`ci_gate_enabled=true`:

1. `wait_for_ci.py` polls `/repos/ratio-yolo/agent1-test-target/commits/{sha}/check-runs`,
   sees the `intentional-failure` job complete with conclusion=failure.
2. `build_ci_feedback.py` fetches the failed run's logs and assembles a
   feedback doc.
3. `trigger_builder_retry.py` is invoked with `CI_FEEDBACK` populated and
   `REVIEW_BODY` empty (single-signal feedback per spec §7.2).
4. The Gemini reviewer step is **skipped** (CI-red short-circuits the gate).
