# Upstream, license, and repository notes

This repository is the SO-101 project: imitation learning and reinforcement
learning on a real robot arm, built on
**[LeRobot](https://github.com/huggingface/lerobot)**. The same project was
published at
**[atomictan/lerobot-so101](https://github.com/atomictan/lerobot-so101)**.

本仓库是 SO-101 项目，基于 LeRobot。实验、演示视频、数据集记录和项目脚本属于该项目；
LeRobot 库属于 Hugging Face 及其贡献者。这里没有另记一轮新的独立复现实验。

## Authorship and results

- SO-101 project work, demonstrations, experiment logs, the reported 96.7% ACT
  success rate, and the SAC/HIL-SERL results are this project's record, as
  described in the README and project logs.
- The underlying **[LeRobot](https://github.com/huggingface/lerobot)** library is
  credited to **Hugging Face and its contributors**.
- The public dataset remains
  [`Atomictan/grab_tube_merged_v3`](https://huggingface.co/datasets/Atomictan/grab_tube_merged_v3).
- Those results are the original project record. This repository does not add a
  newly logged reproduction run.

## Source snapshot and changes

- Source: <https://github.com/atomictan/lerobot-so101>
- Snapshot date: **2026-10-30**.
- Source commit:
  [`db3ad8a0c35d079a8e73bfda54615c32870f75eb`](https://github.com/atomictan/lerobot-so101/commit/db3ad8a0c35d079a8e73bfda54615c32870f75eb).
- All **1,544 source commits**, original authorship metadata, and all **992 files**
  tracked at that commit are preserved. The source exposed one branch (`main`)
  and no tags.
- Changes after the snapshot: project-facing README, and these upstream notes.
  The original README body, code, configurations, fixtures, and media are retained.
- This is a snapshot; automatic synchronization is not configured.

## License and notices

The source declares **Apache-2.0** for LeRobot and its project additions.
The original [LICENSE](LICENSE), copyright statements, and third-party license
text are retained unchanged. No separate `NOTICE` file was present in this
source snapshot. This repository does not imply endorsement by Hugging Face.

## Assets and external links

- Ordinary Git history is approximately **120.94 MiB**. All tracked media,
  including `media/so101/grab_tube.webp`, the still image, and the upstream README
  animations, are preserved without changing their attribution.
- The current snapshot references **50 Git LFS files (72,464,891 bytes)**,
  including five MP4 test clips. The full history references **899 distinct LFS
  objects (3,478,064,333 bytes)**.
- The source repository's LFS endpoint returned missing-object errors. These
  objects were recovered from the public original `huggingface/lerobot` LFS
  endpoint using the existing pointer hashes. Every historical LFS object was
  checked for the exact expected byte count and SHA-256 digest before upload.
- LFS objects remain in LFS, and the original `.gitattributes` is unchanged.
  Cloning all historical LFS resources requires substantial disk space and
  transfer. Standard setup is `git lfs install` followed by `git lfs pull`;
  `git lfs fetch --all` additionally retrieves historical objects.
- Linked external videos, GitHub user attachments, Hugging Face datasets,
  models, and interactive Spaces are **linked at their original locations**.
  Their payloads are not tracked in this repository. Separate redistribution
  authorization was not established, so they were not downloaded and reuploaded.
  In particular, the public
  [`Atomictan/grab_tube_merged_v3`](https://huggingface.co/datasets/Atomictan/grab_tube_merged_v3)
  dataset remains an external dataset with its own terms and attribution.
- Links can become unavailable; the original history also contains an already
  expired signed media URL. Retaining a link is not a guarantee of archival
  availability.
- The source had **no releases, release assets, or wiki** to transfer. GitHub
  issues, pull requests, stars, and Actions run records are service metadata and
  are not copied by Git history. Source workflow files are preserved, while
  **GitHub Actions is disabled** on this repository to avoid running inherited jobs.

## Checks and limits

- Git object integrity was checked with `git fsck --full --no-reflogs`.
- Gitleaks 8.30.1 scanned the complete Git history with redacted reporting and
  scanned all 3.48 GB of recovered LFS payloads. No usable credentials were
  identified. Two historical AWS-pattern findings referred to the same S3
  signed download URL, which expired on **2024-05-05 at 17:34:24 UTC**; no AWS
  secret access key was identified. Scanner reports and local credentials are
  not included in this repository. Automated scans are not a guarantee that
  every possible secret is absent.
- Packaging this repository did not re-run the robotics software, training,
  hardware behavior, or the reported success rates.
