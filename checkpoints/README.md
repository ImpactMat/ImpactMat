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

The PANN file is the official 32 kHz AudioSet Cnn14 checkpoint from
[`qiuqiangkong/audioset_tagging_cnn`](https://github.com/qiuqiangkong/audioset_tagging_cnn),
hosted in [Zenodo record 3987831](https://zenodo.org/records/3987831). Download
it from the repository root with:

```bash
curl -L \
  'https://zenodo.org/records/3987831/files/Cnn14_mAP%3D0.431.pth?download=1' \
  -o checkpoints/Cnn14_mAP=0.431.pth
```

Expected SHA-256:
`0dc499e40e9761ef5ea061ffc77697697f277f6a960894903df3ada000e34b31`.

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
