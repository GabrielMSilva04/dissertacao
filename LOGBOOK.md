# Logbook

Weekly record of work on the dissertation, newest first. One entry per ISO week.

Supervision meeting notes, the open-work list and the detailed write-ups of the pipeline work
are kept in the working repository; see [README.md](README.md) for where that is.

## Where things stand — 4 Oct 2026

Two strands, both live:

- **Thesis.** Rescoped on 23/09 at Gonçalo's direction to **image-to-world mapping and
  camera calibration**, specifically the difficulty introduced by object *height*. Radar as
  the reference sensor is parked. The literature search was reoriented to match; the
  dissertation chapters are still a skeleton.
- **Pipeline (`vision-foundry`).** Model export from ONNX to a TensorRT engine on a Jetson,
  published to Harbor so any machine can find the engine built for it. Working end to end
  against a live Harbor, with one caveat below.

**Open caveat:** the export has still never run on real Jetson hardware — every engine so far
came from a faked `trtexec`. The Jetson became reachable on 27/09, so this is unblocked but
not yet done.

---

## 2026-W40 · 28 Sep – 4 Oct

**Focus:** replacing Jenkins with an in-house queue and worker; code quality; reorienting the
literature.

- Reviewed the whole `feat/export-targets-and-model-catalog` branch against `main` and fixed
  **8 confirmed findings** — among them a container running as the wrong uid, so every route
  failed and the healthcheck could never pass, and an environment variable Jenkins sets
  node-wide that silently overrode the per-target builder image, defeating the branch's main
  feature.
- Replaced Jenkins dispatch with a **SQLite job queue plus a worker service**: workers claim
  one job at a time, run as a systemd user unit, refuse to start beside another worker on the
  same config, and guard against starting a build without enough free memory.
- Taught `resolve` to match on the **TensorRT version** that built an engine, to cover the
  asking machine's batch size, to offer version-compatible engines from an earlier TensorRT,
  and to refuse an engine built for a different device model or L4T release.
- **Literature reoriented** to the 23/09 scope. Pillars are now mapping / height /
  calibration / detection-distance, with radar parked but kept classified. Added a
  `--reclassify` pass, since the rescope stranded papers under a pillar name no query could
  assign again. Fixed a gate that was admitting an animal-breeding paper at high relevance —
  "height", "distance" and "bias" are words every field uses. **73 → 148 candidates.**
- Reorganised `notes/` into per-task folders and deleted three superseded documents after
  folding their still-true content forward.

**Next:** define a second export target — there is still only one, so the target abstraction
has never had to discriminate between two machines; then run a real export on the Jetson.

## 2026-W39 · 21 – 27 Sep

**Focus:** the 23/09 supervision meeting, and building what it asked for.

- **22/09** — added timelapse export to `vision-harvester`: server-side rendering with
  cached FFmpeg output, plus the player and controls in the capture archive UI.
- **23/09 — meeting with Gonçalo.** Two
  outcomes. For the thesis: focus on **mapping for calibration**, because distances are
  computed from different points today and *it becomes difficult with heights*; radar set
  aside. For the pipeline: the real problem with `scp` is that exporting a new model type
  means **copying files by hand from the Jetson to Jenkins** — in the *build* direction, not
  deployment, the opposite of what I had assumed. Jenkins can be removed if something lists
  the Harbor models and sends them to the worker.
- **24–27/09** — built that. Export targets became configuration objects rather than
  hardcoded YOLO assumptions; a Harbor catalog over the native OCI referrers API; a
  parameterised dispatch to the Jenkins job; a model puller image; `resolve`, which picks the
  engine that suits the asking machine; a read-only web catalogue of models, engines and
  their artefacts; and a precedence chain of pins, aliases and deprecations in front of the
  resolver.

## 2026-W38 · 14 – 20 Sep

**Focus:** orientation on the group's systems, and the annotation work.

- **14/09 — meeting with Gonçalo.**
  Introduced to Vision Foundry, Harbor and the Jetson. The calibration problem stated for the
  first time: triangulating from three geographic points still carries error, and **it is hard
  to determine the physical point of the object**. Given access to a Jetson (`nap-619`).
  Also: anonymization must come *after* annotation, and DeepStream keeps the whole pipeline on
  the GPU with NVMM zero-copy buffers.
- **16/09 — group gathering.** Did the CVAT
  annotation work that was meant for Hugo; the cross-camera **ID mapping** problem in Vision
  was raised as an open issue.
- Reading and setup; no code committed this week.

## 2026-W37 · 7 – 13 Sep

**Focus:** standing the platform up as a deployable whole.

- Prepared an **x86 controller** with published Harbor and Jenkins images, and brought both up
  under a single root Compose project.
- Added scoped cleanup scripts for worker and server, Harbor initialisation and provisioning,
  and agent bootstrap with architecture-specific `kit` installation.
- Refactored the export pipeline to support **dynamic batch** processing.

## 2026-W36 · 31 Aug – 6 Sep

**Focus:** first work on `vision-foundry` — getting a model exported and published at all.

- Dynamic **Jetson environment detection** and version-based Docker tagging; the builder image
  parameterised by JetPack, L4T, CUDA and TensorRT versions; upgraded to JetPack 6.2.1.
- **Replaced MLflow orchestration with a Harbor webhook → Jenkins pipeline**, and packaged
  KitOps exports with verified provenance and replay protection.
- Added a webhook-to-download smoke test, and validated real Harbor-triggered YOLO exports
  including engine reload, restart and duplicate-event handling.
