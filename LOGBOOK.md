# Logbook

Weekly record of work on the dissertation, newest first. Weeks are numbered from the start
of my own work; week 1 began on 14 September 2026.


Supervision meeting notes, the open-work list and the detailed write-ups of the pipeline work
are kept in the working repository; see [README.md](README.md) for where that is.

## Where things stand — 3 Nov 2026

Two strands, both live:

- **Thesis.** Widened on 20/10: calibration is no longer the centre. The subject is keeping
  detection and classification of road users reliable from fixed urban cameras across
  conditions such as night and fog, and using the detections for traffic counts, speeds and
  events. The state of the art is organised in three approaches — fix the image before the
  detector, make the features robust, adapt the detector — plus the work that measures what
  detection feeds. 23 papers are in Zotero. The gap so far: none of the fog papers read
  reports results on an edge device. The dissertation chapters are still a skeleton.
- **Pipeline (`vision-foundry`).** Model export from ONNX to a TensorRT engine on a Jetson,
  published to Harbor so any machine can find the engine built for it. Proven on hardware:
  yolo11n/s/m/x and **RT-DETR-L**, a non-YOLO model, all built through the queue on a real
  Jetson. The catalogue runs on that Jetson and is used from the browser. The lab VM is off
  the old Jenkins-based stack, so Jenkins is retired in the lab and not only on the branch.
  No commits since 17/10.

---

## Week 7 · 26 Oct – 1 Nov

**Focus:** the state of the art for Sprint 2.

  not only staying open on the approach.
- Added **night, fog and camera-adaptation pillars** to the literature discovery script.
- Read and compared the papers on the Sprint 2 slide: the Liu 2022 enhancement survey,
  Aryashad 2026 (defogging scored by detection, real versus synthetic fog), YOLA (Hong 2024,
  lighting-invariant features), Yoo 2024 (test-time detector adaptation) and the Zhou 2025
  survey of traffic tasks built on detection.
- Built the Sprint 2 slides (29–31/10) around those papers and where each one stops.

## Week 6 · 19 – 25 Oct

**Focus:** the 20/10 supervision meeting, and the literature behind it.

- **20/10 — supervision meeting.** Focus less on calibration. Analyse options for the
  thesis: optimising images by time of day, gathering statistics, detecting events, and
  models that process only the tiles with motion, to save computation. Freedom to explore,
  and generalise the second sprint. Recommended reading: the Liu, Pedersen and Wang (2022)
  survey of natural image enhancement.
- **19/10** — made literature searches reportable, with a log of every run, added acronym and
  IEEE Thesaurus terms to the search vocabulary, and generated `references.bib` from the
  Zotero collection.

## Week 5 · 12 – 18 Oct

**Focus:** Sprint 1, setting up the working repository, and a usability pass on the catalogue.

- **Sprint 1 (13/10)** — motivation and objectives, still on the 23/09 calibration scope.
- **Working repository (13/10)**: course material, the dissertation source, the proposal and
  the sprint material in one private repository. The dissertation moved to the **official UA
  LaTeX template** (18/10), and meeting, reading and pipeline notes were brought in.
- **Literature discovery script (18/10)** on OpenAlex, with tests, added to the working
  repository.
- **Catalogue usability pass (17/10).** A build's TensorRT output reaches its job page while it
  runs; a failed build names its cause and suggests what to try; a finished build can be run
  again with the same settings; an engine links to the build that made it and shows its
  inputs, outputs and exporter metadata; uploads check the ONNX model and name before sending
  the file; a model version's page is split into Engines and Build tabs.
- Tests for the benchmark, export and provisioning failure paths and for Harbor
  initialisation (17/10). The server stack's catalogue now uploads with the worker's robot
  account (15/10).

---

## Week 4 · 5 – 11 Oct *(entry to 8 Oct)*

**Focus:** proving the pipeline is not YOLO-specific, and making the catalogue usable for
real work rather than just browsing.

- **Exported RT-DETR-L on the Jetson (05/10) — a non-YOLO model, through the whole pipeline.**
  This is the sprint's main result: dropping the YOLO assumptions was the explicit request on
  23/09, and this is the evidence it worked rather than a claim that it should. yolo11n, s, m
  and x build through the same queue.
- **Build sets (06/10).** One request now queues every buildable combination of targets,
  precisions and batch settings, and each target carries its own push list, so a newly pushed
  model gets the engines that target expects without anyone enumerating them.
- Each build records **who asked for it** — the page, the CLI or Harbor's webhook — and a
  running build now streams its newest log lines with each heartbeat, so a build can be
  watched while it runs instead of only read afterwards.
- `resolve` lists the engines it **cannot** offer and deletes them only on confirmation.
- **Upload an ONNX model from the page (07/10)**, which queues its push builds immediately;
  updated the catalogue running on the Jetson to this build the same day.
- **Download engine files, packaged files and model versions through the catalogue (08/10)**,
  streamed and digest-checked rather than trusted.

**Next:** move the lab VM off `main` so Jenkins is retired in the lab and not only on the
branch — that needs Gonçalo's go-ahead. Then back to the thesis itself: inspect the
calibration API and the pole 61 camera records, and start quantifying the height error.

---

## Week 3 · 28 Sep – 4 Oct

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
- **First real export on Jetson hardware (29/09).** Until then every engine in the work had
  come from a faked `trtexec`, so nothing had been proven end to end. `resolve` then picked
  that engine out of the lab registry, which closes the loop the pipeline exists to close.
- Rebuilt the catalogue as **server-rendered pages** instead of one JavaScript page (04/10):
  a model list, a page per model comparing its engines and showing coverage and standing
  policies, a page per engine, and job pages that refresh themselves and can cancel a build.
  State-changing requests from other sites are refused, and every API answer is now typed.

**Next:** prove the pipeline is not YOLO-specific by building a non-YOLO model, and define a
second export target — there is still only one, so the abstraction has never had to
discriminate between two machines.

## Week 2 · 21 – 27 Sep

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

## Week 1 · 14 – 20 Sep

**Focus:** orientation on the group's systems, and the annotation work.

- **14/09 — meeting with Gonçalo.**
  Introduced to Vision Foundry, Harbor and the Jetson. The calibration problem stated for the
  first time: triangulating from three geographic points still carries error, and **it is hard
  to determine the physical point of the object**. Given access to a Jetson.
  Also: anonymization must come *after* annotation, and DeepStream keeps the whole pipeline on
  the GPU with NVMM zero-copy buffers.
- Reading and setup; no code committed this week.
