# service-minimal — Container security PoC

This repository exercises the shared `EmVee-hub/github-actions` Grype +
OpenVEX security action on representative container images. CI builds local
images; it does not publish or deploy them.

## Controlling decision model

The shared policy uses **CISA BOD 26-04** as its authoritative base decision
table (KEV, Publicly Exposed, Automatable, Technical Impact). The EPSS
**percentile-quartile amplifier** in the pinned central
`policy/epss-amplifier.json` is applied afterward; it does not replace
BOD with fixed 5%/10% probability thresholds.

Guardrails: KEV preserves the BOD decision; base 3D/3DF cannot be downgraded;
EPSS cannot create a new 3DF forensic result. Missing EPSS uses neutral Q2.

The workflow runs `mode: enforce`: evaluated final **3D/3DF → BLOCK**,
FSU/60D/14D → ALLOW and incomplete assessments → REVIEW_REQUIRED.
A red CI job can mean the security gate correctly rejected an image, not
that Docker or Grype malfunctioned. Check each uploaded `security-*` artifact
before changing any policy or VEX.

The `cae-live` example uses a separate scoped VEX. Statements claiming
`not_affected` require independently reviewed applicability and provenance.
This PoC does not validate live AKS/EKS workload coverage or deployment safety.

## Report revision

New runs use the BOD-first policy and a compact findings table. Historical
GitHub Actions reruns preserve their original commit and action pin; they can
still show removed `POC_*` probability-policy rules. To validate the current
policy, choose **Run workflow** on `main` instead of **Re-run jobs** on an old run.
