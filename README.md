# slsa-lab-builder

Trusted **SLSA Build L3** reusable workflow for the SLSA Provenance Lab.

Learners **never edit this repo**. Their `release.yml` calls:

```yaml
uses: geet-we45/slsa-lab-builder/.github/workflows/build.yml@v1
```

## Why L3

Build + attest inside a reusable workflow in a separate repo the learner cannot modify. Verify with:

```bash
gh attestation verify path/to/binary \
  --repo LEARNER/REPO \
  --signer-workflow geet-we45/slsa-lab-builder/.github/workflows/build.yml \
  --deny-self-hosted-runners
```

## Tag policy

Callers pin `@v1`. **Never move `v1`.** Ship changes as `v2`.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `artifact-name` | yes | — | Output binary / artifact name |
| `go-dir` | no | `app` | Directory with `go.mod` |

No input accepts arbitrary commands or build flags — that preserves isolation.
