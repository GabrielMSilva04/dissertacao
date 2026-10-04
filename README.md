# YOLO-Based Methods for Improved Object Classification and Localization in Urban Environments

MSc dissertation, DETI, Universidade de Aveiro / Instituto de Telecomunicações (NAP group).

Supervisors: Pedro Rito, Duarte Raposo, Susana Sargento, Gonçalo Lourenço Silva, Marcos Mendes.

## → [LOGBOOK.md](LOGBOOK.md)

**The weekly record of work, newest first.** That is the main content of this repository.

## What this repository is

The dissertation diary, kept for the PDE. It is deliberately small: the working material it
describes lives elsewhere, and this explains where.

| What | Where it lives | Why not here |
|---|---|---|
| Dissertation LaTeX source, bibliography, reading notes, supervision notes | Private working repository | Working material, shared with supervisors on request |
| `vision-foundry`, `vision-control`, `deepstream-vision`, `vision-harvester`, `vision-calibration` | The group's internal GitLab | Group-owned; not mine to republish |
| Camera and radar captures | ATCLL infrastructure | Footage of public space, pending anonymization |

## Subject

Two strands, which meet in the dissertation's integration chapter.

**Detection and localization (the thesis).** Detecting and classifying road users from fixed
urban cameras, and turning a detection into a position in the world. Since 23/09 the centre
of the work is **image-to-world mapping and camera calibration** — specifically the error
introduced by object *height*. Projecting an image point to the world needs an assumption
about depth; the usual one is that the point lies on the ground plane, which holds for the
contact point of a wheel and fails for anything with height. Choosing *which* point on a
detection to project is part of the research question, not an implementation detail.

**The model pipeline (infrastructure).** Taking a trained model from ONNX to a TensorRT
engine compiled for a specific edge device, published so that any machine in the fleet can
find the engine built for it, with its provenance attached.

## Work plan

| # | Activity |
|---|---|
| 1 | State of the art: YOLO, detection, classification, localization; the existing ATCLL pipeline |
| 2 | Familiarization with cameras, radars and datasets |
| 3 | Dataset preparation and annotation |
| 4 | Training and evaluation of YOLO models for urban conditions |
| 5 | Image-based localization, and comparison against a reference |
| 6 | Integration into the existing sensing pipeline |
| 7 | Validation and analysis of accuracy, robustness and performance |
| 8 | Dissertation and scientific paper |
