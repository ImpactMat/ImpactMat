# Checkpoints

Put the downloaded paper checkpoint here. Model weights are intentionally not
tracked by Git.

Download: [`impactmat.pt`](https://github.com/ImpactMat/ImpactMat/releases/download/v1.0.0/impactmat.pt)

> The direct link above immediately starts a 947 MB download.

Release page: [ImpactMat v1.0.0](https://github.com/ImpactMat/ImpactMat/releases/tag/v1.0.0)

Expected files:

```text
checkpoints/
  Cnn14_mAP=0.431.pth
  impactmat.pt
```

`impactmat.pt` is the model used for the paper's main results. The
`Single` and `Blend` columns in the paper evaluate this one checkpoint on two
test subsets; they do not require different weights.

After downloading `impactmat.pt`, verify it from this directory with:

```bash
sha256sum -c SHA256SUMS
```

Training runs save internal snapshots as `best.pt` and `latest.pt`. The stable
public release filename is:

```text
impactmat.pt
```
