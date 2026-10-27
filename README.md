# MMPAD: Depth-Anchored Fusion and Physically Consistent Augmentation for Multimodal Face Presentation Attack Detection

**Hamza Bouzid, Olivier Lézoray, Christophe Rosenberger**
Normandie Univ, UNICAEN, ENSICAEN, CNRS, GREYC, Caen, France

> **Code coming soon.** The training and evaluation code, the configuration files for every experiment in the
> paper and the trained models will be released in this repository. Watch or star the repository to be
> notified when it is published.

<!-- Architecture figure: export MMPAD_model.pdf to assets/overview.png and uncomment the next line.
<p align="center"><img src="assets/overview.png" width="85%" alt="MMPAD architecture"></p>
-->

## Overview

Face presentation attack detection (PAD) checks that the face in front of a camera belongs to a person who
is physically present, and not to a print, a screen replay or a mask. Multimodal PAD, which combines RGB,
depth and infrared (IR), is close to saturated when training and testing on the same dataset, but it is
still fragile when the camera, the population or the attack type changes.

Most multimodal systems use RGB as the reference stream, which the other modalities are read against. We
argue that this is the wrong default. RGB is the channel most affected by illumination and camera pipeline,
and it is the channel attack instruments are built to fool. Depth and infrared measure properties of the
object itself (its 3D shape and how its surface reflects light), which an attacker cannot fake without
physically rebuilding the artifact.

MMPAD therefore anchors fusion on **depth**: depth tokens query the RGB and IR tokens. We treat the choice of
anchor as an experimental variable and measure its effect on cross-dataset error.

## Method

- **Depth-anchored cross-attention fusion.** Two DINOv2 ViT-B/14 encoders, one for RGB and one shared by
  depth and IR. Depth tokens supply the attention query; RGB and IR are read conditionally on them.
- **Two-stage joint hyperspherical alignment.** A symmetric three-way contrastive objective with a learned
  temperature, applied both to the per-modality embeddings and again to the cross-attended ones.
- **Attack synthesis with consistent evidence in every modality.** New labeled attacks are generated from
  bona fide RGB-depth-IR triplets:
  - *partial attacks* paste a donor region in RGB and write the matching flat depth and specular IR signature;
  - *full-face attacks* model print, replay and mask jointly in the three streams (halftone and moiré in RGB,
    plane fitting in depth, flattened reflectance in IR).
- **Sensor-shift augmentation for depth and IR.** Randomizes the response curve, gain, offset, quantization,
  noise floor and depth dropout, controlled by a single severity value.
- **Score-drift penalty.** A one-sided penalty that stops a model trained on high-quality captures from
  treating any loss of image quality as evidence of an attack.
- **Leak-free reporting.** Video-level metrics at decision thresholds estimated on clean source-domain
  validation data only, never on test scores, with a lower-variance Gaussian EER estimator.

## Evaluation

| Setting | Datasets |
|---|---|
| Intra-dataset | WMCA (grandtest and leave-one-attack-out), CASIA-SURF CeFA (protocols 1, 2 and 4), CASIA-SURF |
| Cross-dataset | all six train/test pairs among WMCA, CASIA-SURF CeFA and CASIA-SURF |

Results are reported as ACER (intra-dataset) and HTER / AUC (cross-dataset), together with component
ablations, augmentation sweeps and a comparison of threshold estimators. The full results will be available
with the paper.


## Citation

The citation will be added when the paper is published.

## Acknowledgements

This work was supported by the French National Research Agency (ANR) under the FACEPAD3D project
(France 2030). Experiments used HPC resources from GENCI-IDRIS (Jean Zay) and CRIANN. We thank the Idiap
Research Institute and the CASIA authors for access to their datasets.

## Contact

Hamza Bouzid: hamza.bouzid@unicaen.fr
