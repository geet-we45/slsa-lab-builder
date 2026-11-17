# slsa-lab-builder

Trusted **SLSA Build L3** builder for the [SLSA Provenance Lab](../slsa-lab).

This repository hosts a **reusable workflow** that builds a Go binary and generates a Sigstore-signed build provenance attestation. Learners call it from their own copy of the lab template; they never modify this repo.

## Why this is the L3 builder

Per [GitHub's artifact attestation guidance](https://docs.github.com/en/actions/how-tos/secure-your-work/use-artifact-attestations) and [SLSA v1.2](https://slsa.dev/spec/v1.2/onepage):

| Level | How you get it on GitHub |
|---|---|
| Build L2 | `actions/attest-build-provenance` in a normal workflow the developer can edit |
| Build L3 | Build **and** attest inside a **reusable workflow** in a separate repo the developer cannot modify; verify with `--signer-workflow` pointing at that workflow |

This repo is that separate, frozen builder. Signing identity (`job_workflow_ref`) is the reusable workflow here — not reachable by user-defined steps in the caller's job.

## Frozen tag policy

- Callers use: `ORG/slsa-lab-builder/.github/workflows/build.yml@v1`
- **Never move `v1`.** Ship breaking or behavioral changes as `v2`, `v3`, …
- Learners and trainers verify against the immutable tag, not `main`.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `artifact-name` | yes | — | Output binary name (and artifact upload name) |
| `go-dir` | no | `app` | Directory containing `go.mod` and the main package |

**No input accepts arbitrary commands, scripts, or build flags.** That restriction is what preserves build isolation (Build L3).

## How callers use it

```yaml
jobs:
  l3:
    permissions:
      contents: read
      id-token: write
      attestations: write
    uses: ORG/slsa-lab-builder/.github/workflows/build.yml@v1
    with:
      artifact-name: hello-l3
```

## How to verify against this builder

```bash
gh attestation verify path/to/binary \
  --repo LEARNER_OWNER/LEARNER_REPO \
  --signer-workflow ORG/slsa-lab-builder/.github/workflows/build.yml \
  --deny-self-hosted-runners
```

If you have the downloaded bundle:

```bash
gh attestation verify path/to/binary \
  --repo LEARNER_OWNER/LEARNER_REPO \
  --signer-workflow ORG/slsa-lab-builder/.github/workflows/build.yml \
  --deny-self-hosted-runners \
  --bundle path/to/binary.bundle.jsonl
```

A valid signature means **"this builder produced it"**, not "this code is safe". Provenance protects nobody unless you verify it against expectations (repo, ref, builder).

## Lab context

Part of the SLSA Provenance Lab (Build track L0 → L3). See the companion template repo `ORG/slsa-lab` for the walkthrough.
