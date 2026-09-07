# VIRTUO: Virtual Twins for Patient-Specific Radioembolisation Planning, Dosimetry and Treatment Optimisation

**Research Plan — SNSF Project Funding Scheme**

**Applicants (Principal Investigators):** Thiago Lima (Lucerne Cantonal Hospital, LUKS), Prof. Dr. med. De-Hua Chang, Javier Montoya (Zurich University of Applied Sciences, ZHAW), Antoine Leimgruber (Neuchâtel Hospital Network, SMN, and LUKS), Klaus Strobel (LUKS)

**Collaborating partners:** Dr. Angelica I. Aviles-Rivero (Yau Mathematical Sciences Center, Tsinghua University, China); Prof. Guang Yang (Imperial-X and Department of Bioengineering, Imperial College London, United Kingdom)

**Collaborator:** Andrea Zander (LUKS)

---

## 0. Summary

Transarterial radioembolisation (TARE, also termed selective internal radiation therapy, SIRT) with Yttrium-90 (Y90) microspheres is a guideline-recommended locoregional treatment for unresectable primary and secondary liver cancer [1]. Its efficacy and safety depend critically on how precisely the delivered radioactivity is confined to tumour tissue while healthy liver, lung and extrahepatic organs are spared — a task that hinges entirely on accurate, reproducible dosimetry [1,2,3]. Current clinical practice estimates this dose distribution using a surrogate: a diagnostic angiography followed by intra-arterial injection of Technetium-99m macroaggregated albumin (⁹⁹ᵐTc-MAA), imaged by SPECT/CT, whose perfusion pattern is assumed to mimic that of the therapeutic Y90 microspheres two weeks later. This assumption is only partially valid: catheter tip repositioning, differences in particle size, shape and injection dynamics between MAA and microspheres, and the fundamentally trial-and-error nature of selecting an injection position during angiography all introduce variability that is well documented in the nuclear medicine literature [1,5]. Each attempt to test an alternative catheter position requires a separate invasive procedure on a separate day, making systematic optimisation of the injection site clinically impossible today.

VIRTUO proposes to replace part of this empirical process with a patient-specific computational "virtual twin" of the liver vasculature that predicts, before any catheter is introduced, how an injection at a given intra-arterial position will perfuse the liver parenchyma and tumour. The project combines (i) automated AI-based segmentation of the hepatic arterial and portal venous trees from routinely acquired contrast-enhanced CT/MRI, (ii) patient-specific computational fluid dynamics (CFD) of particle transport calibrated to the physical properties of ⁹⁹ᵐTc-MAA and Y90 microspheres, and (iii) a physics-informed generative AI surrogate that learns the mapping between injection position and perfusion pattern directly from paired position–perfusion data. To de-risk the project and make efficient use of the first year, the segmentation component is deliberately front-loaded on **publicly available, already-annotated datasets** — the Medical Segmentation Decathlon Task08 Hepatic Vessel dataset (443 contrast-enhanced CT scans with hepatic vessel and tumour annotations) [14] and the 3D-IRCADb-01 database (20 contrast-enhanced CT scans with liver, hepatic and portal vein annotations) [15] — before any clinical data transfer or ethics approval is required, allowing method development to start immediately in month 1. This public-data model is then transferred to and fine-tuned on a retrospective cohort of approximately 200 SPECT/CT and PET/CT perfusion studies of patients who underwent radioembolisation at LUKS, which is also used to develop and validate the CFD and generative surrogate components. If successful, the virtual twin will allow in-silico exploration of multiple candidate injection positions from diagnostic imaging alone, with the long-term goal of reducing the number of invasive planning procedures and improving the precision of dose delivery to tumour while sparing healthy liver.

---

## 1. Current state of research

### 1.1 Clinical background: liver cancer and the role of radioembolisation

Primary liver cancer (hepatocellular carcinoma, HCC, and intrahepatic cholangiocarcinoma) and liver metastases from colorectal and other primaries represent one of the largest and most lethal tumour burdens worldwide, with liver cancer among the leading causes of cancer-related death globally [13]. A substantial proportion of patients present with disease that is not amenable to resection or ablation, either because of tumour extent, vascular invasion, or insufficient hepatic reserve. For these patients, locoregional intra-arterial therapies exploit the fact that liver tumours are predominantly perfused by the hepatic artery, whereas normal liver parenchyma receives the majority of its blood supply from the portal vein — a difference that can be used to selectively deliver therapy to tumour tissue while relatively sparing healthy liver.

TARE/SIRT with Y90-labelled microspheres (glass or resin) is one such locoregional therapy, endorsed by international societies and detailed in the 2022 EANM procedure guideline for the treatment of liver cancer and liver metastases with intra-arterial radioactive compounds [1] and in the AAPM Medical Physics Practice Guideline 14.a on Y90 microsphere radioembolization [3]. Y90 is a pure beta-emitter with a short tissue range (mean ~2.5 mm, maximum ~11 mm) and a physical half-life of 64.2 hours, properties that allow delivery of high, localised absorbed doses to tumour with limited irradiation of surrounding tissue when the microspheres are correctly positioned [1,2].

### 1.2 The radioembolisation workflow

The clinical pathway, as also formalised in the EANM guideline [1], is a two-stage procedure separated by an interval of one to several weeks:

1. **Diagnosis and multidisciplinary decision.** Tumour burden and vascular anatomy are characterised on contrast-enhanced CT and/or MRI, and the case is discussed in a multidisciplinary tumour board, which decides on eligibility for radioembolisation.

2. **Planning (work-up) angiography and ⁹⁹ᵐTc-MAA SPECT/CT.** A diagnostic hepatic angiography maps the arterial supply to the tumour(s) and surrounding liver segments. Based on the angiographic appearance, an interventional radiologist selects one or more catheter positions and injects a small activity of ⁹⁹ᵐTc-MAA, intended as a surrogate for the eventual Y90 microsphere distribution. A SPECT/CT acquisition then quantifies the perfusion pattern, which is used to (a) detect extrahepatic uptake and hepatopulmonary shunting (lung shunt fraction), both of which constrain the maximum administrable activity, and (b) calculate tumour and normal-liver absorbed doses that determine the prescribed Y90 activity, following standardised dosimetric methodology such as the EANM Dosimetry Committee's operational procedure for ⁹⁹ᵐTc-MAA pre- and Y90 peri-therapy dosimetry [2].

3. **Treatment.** Typically one to two weeks later, the patient undergoes a second angiography, during which the catheter is repositioned as closely as possible to the planning position, and the prescribed Y90 activity is infused.

4. **Post-treatment imaging.** A PET/CT (or, historically, bremsstrahlung SPECT/CT) is acquired to verify the actual delivered distribution and confirm the administered dose.

### 1.3 The centrality — and the limitations — of dosimetry

Dosimetry is not an ancillary quality-control step in radioembolisation; it is the therapeutic lever. The randomised, multicentre DOSISPHERE-01 trial demonstrated that a personalised dosimetric approach — targeting an absorbed dose of at least 205 Gy (up to 250–300 Gy) to the index tumour using glass microspheres, versus a standard fixed-activity approach — significantly improved objective response rate and more than doubled median overall survival (26.6 vs 10.7 months) in patients with locally advanced HCC, without increasing toxicity [4]. This trial provides level-1 evidence that the precision with which the prescribed dose actually reaches the tumour, rather than the nominal administered activity alone, drives clinical outcome, and both the EANM and AAPM guidelines now recommend personalised, voxel- or lesion-based dosimetry as standard of care [1,2,3].

However, the entire dosimetric calculation rests on the assumption that ⁹⁹ᵐTc-MAA perfusion at planning faithfully predicts Y90 microsphere perfusion at treatment. This assumption is imperfect for well-characterised physical and procedural reasons:

- **Particle mismatch.** ⁹⁹ᵐTc-MAA aggregates are irregular, heterogeneous in size (typically 10–100 µm) and mechanically fragile, whereas Y90 glass and resin microspheres are rigid, near-monodisperse spheres (20–60 µm). Their haemodynamic behaviour in a bifurcating, pulsatile arterial flow field — governed by particle inertia, density and size relative to vessel calibre — is not identical, and CFD analyses show that microsphere distribution is highly sensitive to particle properties as well as to the exact catheter tip position and injection velocity [6,7].
- **Catheter reproducibility.** Because planning and treatment angiographies are performed on different days, the catheter tip cannot always be repositioned with sub-millimetre accuracy. Kafrouni et al. found that the mean absolute deviation between predicted (MAA-based) and delivered (Y90 PET-based) tumour dose was significantly lower when the catheter position was identical at planning and treatment than when it differed (16 Gy vs 37 Gy, p = 0.007), and that concordance was significantly correlated with the distance between the catheter tip and the nearest arterial bifurcation [5]. In other words, a large share of the discrepancy between planned and delivered dose is attributable not to biology but to the mechanical reproducibility of catheter placement.
- **Trial-and-error catheter selection.** During the single planning angiography, the interventionalist can typically test only the one or two catheter positions that appear most promising on real-time contrast fluoroscopy. Because each new position requires re-injecting contrast and re-evaluating flow subjectively, a systematic comparison of the perfusion consequences of several candidate positions is not feasible within one procedure, and it is not feasible at all without a second invasive procedure.

The consequence is that the current planning paradigm couples an imperfect surrogate (MAA) with an unrepeatable, subjective search for the best injection site, and can commit a patient to a suboptimal microsphere distribution that only becomes apparent after treatment, when it can no longer be corrected without a further invasive intervention. Figure 1 contrasts this current pathway with the workflow VIRTUO aims to enable.

![Figure 1. Current radioembolisation workflow (top) compared with the VIRTUO-enabled workflow (bottom): the invasive, trial-and-error MAA planning step and its separate angiography are replaced by an in-silico virtual twin that tests multiple candidate injection positions before any catheter is placed, so that a single angiography delivers the optimised treatment; post-treatment PET/CT feeds back to continually refine the model.](figures/fig1_workflow.png)

**Figure 1.** Current clinical radioembolisation workflow compared with the VIRTUO-enabled workflow. In current practice (top), the injection position is chosen empirically during a first, planning angiography, tested with a single ⁹⁹ᵐTc-MAA injection, and then reproduced as closely as possible during a second, treatment angiography 1–2 weeks later — a process in which catheter tip reproducibility is a documented source of dose discordance [5]. In the VIRTUO-enabled workflow (bottom), the virtual twin evaluates multiple candidate injection positions computationally from diagnostic imaging alone, so that a single angiography delivers the treatment at the pre-planned, optimised position; post-treatment PET/CT verification feeds back into the model to support continual refinement.

### 1.4 Computational and AI foundations available to address this problem

Three technological developments make it now feasible, for the first time, to address these limitations computationally rather than only procedurally.

**AI-based vascular segmentation.** Deep learning has matured to a point where hepatic vessel segmentation from routine contrast-enhanced CT or MRI can be performed automatically and with high accuracy. Recent deep-learning pipelines trained on portal-venous phase CT achieve Dice similarity coefficients of 0.86–0.94 for hepatic vein, portal vein and inferior vena cava segmentation against expert manual reference [8]. Generic, self-configuring segmentation frameworks such as nnU-Net [9] provide a robust, widely validated starting architecture that adapts automatically to the geometry, resolution and modality of a given dataset, and have become a de facto standard against which task-specific networks are benchmarked in medical image segmentation challenges.

**Patient-specific computational fluid dynamics of intra-arterial particle transport.** A body of work, reviewed comprehensively by Aramburu et al. [6], has modelled hepatic arterial haemodynamics and microsphere transport in patient-specific vascular geometries reconstructed from angiographic or cross-sectional imaging, showing that segment-to-segment microsphere distribution is strongly and predictably dependent on catheter tip position, injection velocity and particle properties. Critically, Antón et al. performed an in-vivo proof-of-concept validation of such a CFD model against actual clinical ⁹⁹ᵐTc-MAA SPECT/CT distributions, demonstrating that image-based, patient-specific fluid dynamics simulation can reproduce clinically observed perfusion patterns with useful accuracy [7]. This body of work establishes both the feasibility and the current limitations (principally computational cost and dependence on angiographic geometry acquired invasively) of CFD-based perfusion prediction, which VIRTUO will extend to non-invasive, purely diagnostic-imaging-based prediction.

**Digital twins and physics-informed generative AI in radiology.** The digital twin concept — a patient-specific, continuously updatable computational model that mirrors and predicts individual physiology — has recently been reviewed systematically across radiology applications, including interventional planning, with the review noting both the strong potential for pre-procedural simulation of therapeutic strategies and the technical challenges of validation and computational latency in time-critical clinical settings [10]. In parallel, deep generative models are increasingly used to learn complex physical or physiological mappings directly from imaging data, complementing or accelerating first-principles simulation once enough paired data are available; team members bring direct experience in this area, including generative adversarial reconstruction of undersampled MRI [11] and graph-based, physics- and geometry-aware learning frameworks for medical image analysis under limited supervision [12]. VIRTUO will draw on this expertise to move, in its later phase, from a purely physics-based (CFD) perfusion model to a hybrid AI-physics generative model that is both faster (enabling near-real-time, interactive exploration of injection sites) and capable of capturing perfusion determinants not fully represented in a first-principles fluid model.

No published work to date, to the knowledge of the applicants, integrates all three elements — automated vascular segmentation from purely diagnostic (pre-angiographic) imaging, patient-specific particle-transport simulation calibrated separately for MAA and Y90 microspheres, and a learned generative surrogate — into a single pipeline aimed at predicting and optimising radioembolisation injection position before the first invasive procedure. This is the gap VIRTUO addresses.

### 1.5 Preliminary work and consortium expertise

The consortium combines the clinical and technical competencies required for this project. LUKS (Lucerne Cantonal Hospital) is a high-volume centre for hepatic radioembolisation and will provide the retrospective clinical cohort — approximately 200 paired planning/treatment SPECT and PET perfusion studies with corresponding angiographic and cross-sectional imaging — together with interventional radiology, nuclear medicine and radiology expertise (Leimgruber, Strobel, Chang, Zander, Lima) needed to define clinically meaningful segmentation targets, injection scenarios and validation endpoints. ZHAW (Montoya) contributes expertise in computational modelling and image-based simulation required for the CFD and AI-physics work packages. The Tsinghua (Aviles-Rivero) and Imperial College (Yang) groups contribute complementary, internationally leading expertise in physics-informed and generative deep learning for medical imaging [11,12], which will be central to the transition from CFD to a learned surrogate model in the later project phase.

---

## 2. Research questions and objectives

**Overarching research question:** Can a patient-specific computational model, built from routinely acquired diagnostic contrast-enhanced CT/MRI, predict the intrahepatic perfusion pattern resulting from a given intra-arterial injection position with sufficient accuracy to support, and eventually optimise, radioembolisation treatment planning?

This is addressed through four specific objectives:

- **O1 — Segmentation.** Develop and validate deep-learning models that automatically segment the hepatic arterial tree, portal venous tree and liver parenchyma (including tumour delineation) from diagnostic contrast-enhanced CT and MRI, first pretrained on publicly available annotated datasets [14,15] and then transferred to the clinical cohort.
- **O2 — Prediction.** Develop a patient-specific computational fluid dynamics model of particle transport, parameterised separately for the physical properties of ⁹⁹ᵐTc-MAA and of Y90 microspheres, that predicts perfusion distribution for a specified catheter tip position, and validate it against the retrospective cohort of clinically acquired planning (MAA-SPECT/CT) and treatment (Y90-PET/CT) perfusion data.
- **O3 — Optimisation.** Using the validated forward model, formulate and evaluate an in-silico search over candidate injection positions to identify the position (or positions) predicted to maximise tumour dose while constraining dose to normal liver, lung and extrahepatic tissue, and compare optimised positions retrospectively against those actually chosen by interventionalists.
- **O4 — Learned surrogate.** Train a physics-informed generative AI model, using the CFD simulations and clinical data as ground truth, that reproduces the position–perfusion mapping at a fraction of the computational cost of full CFD, enabling near-real-time, interactive exploration of injection scenarios, and evaluate whether this model generalises to predicting treatment (Y90) perfusion directly from diagnostic imaging without an intermediate MAA planning step.

---

## 3. Research plan and methods

The project is organised into five work packages (WP1–WP5) over a planned duration of 48 months, structured so that each stage produces a validated deliverable feeding the next, while allowing the AI-physics work (WP5) to begin in parallel once sufficient CFD training data exist. Three doctoral/postdoctoral positions are embedded directly in the work packages that carry the technical core of the project (see Section 5 for the full staffing and budget plan): a postdoctoral researcher hired by Montoya at ZHAW leads the day-to-day implementation of WP2; a PhD student hired by Lima at LUKS (co-supervised by Montoya) carries out the WP3 CFD work; and a PhD student hired by Montoya at ZHAW, co-supervised by Aviles-Rivero and Yang, carries out the WP5 generative-modelling work. Figure 2 summarises how these work packages connect data, methods and personnel into a single pipeline.

![Figure 2. VIRTUO methodology: from public and clinical imaging data to a validated, patient-specific virtual twin, showing the two-phase data strategy (public datasets, then the LUKS clinical cohort), the AI segmentation, CFD and generative-surrogate work packages, and the calibration loop against clinical dosimetry.](figures/fig2_methodology.png)

**Figure 2.** VIRTUO methodology overview. Public datasets [14,15] are used to pretrain the segmentation network before the LUKS clinical cohort (WP1) becomes available under ethics approval; the resulting patient-specific vascular model feeds a computational fluid dynamics (CFD) particle-transport model (WP3), calibrated against clinical dosimetry; the validated forward model then supports both retrospective injection-position optimisation (WP4) and a physics-informed generative surrogate (WP5), which together define the VIRTUO virtual twin.

### WP1 — Data curation and infrastructure (months 1–12; leads: Lima, Leimgruber, Strobel, Montoya)

**Introduction.** Every downstream component of VIRTUO depends on well-curated imaging data, and the two data sources used in this project (openly available research datasets and the LUKS clinical cohort) have very different governance requirements. WP1 exists to decouple these, so that technical development is not blocked while clinical data-sharing agreements and ethics approval are being finalised.

**Objectives.**
- Assemble and quality-control the public datasets needed to pretrain the WP2 segmentation network with no clinical data governance overhead.
- Obtain ethics approval and set up compliant data transfer for the LUKS retrospective radioembolisation cohort.
- Build a single, harmonised imaging database linking diagnostic imaging, angiographic catheter position and post-treatment verification imaging for every patient in the cohort.

**Methodology.**

*Phase 1 (months 1–6) — public datasets, no patient data required.* Work starts immediately using two publicly available, already-annotated hepatic imaging datasets: the Medical Segmentation Decathlon Task08 Hepatic Vessel dataset, comprising 443 contrast-enhanced CT scans (303 with public training annotations) with expert-labelled hepatic vessels and tumours [14], and the 3D-IRCADb-01 database of 20 contrast-enhanced CT scans with liver, hepatic vein and portal vein segmentations [15]. These datasets are used to establish the imaging pre-processing pipeline, train and benchmark the baseline segmentation architecture (WP2), and validate the overall software infrastructure before any clinical data are involved, since they require no ethics approval or data transfer agreement to access.

*Phase 2 (months 4–12, overlapping Phase 1) — LUKS clinical cohort.* In parallel with the later part of Phase 1, retrospective identification at LUKS of approximately 200 patients who underwent radioembolisation, with their paired planning (contrast-enhanced CT/MRI, angiography, ⁹⁹ᵐTc-MAA SPECT/CT) and treatment (angiography, Y90 PET/CT or bremsstrahlung SPECT/CT) imaging. This phase requires: local ethics committee approval (Kantonale Ethikkommission) for retrospective use of coded clinical imaging and pseudonymised outcome data; data protection and data transfer agreements between LUKS, ZHAW and the international partners, ensuring that only de-identified imaging and derived quantitative data leave the clinical site, consistent with Swiss and EU data protection requirements; construction of a curated, harmonised imaging database (diagnostic CT/MRI, angiographic road-maps, SPECT/CT and PET/CT volumes, dosimetric reports) with a common coordinate registration framework linking diagnostic imaging to the angiographic catheter position documented in the procedure report; and definition, with the clinical team, of a manual/semi-automatic reference segmentation protocol for hepatic arteries, portal veins and tumour(s) on a representative subset, to serve as clinical ground truth for WP2.

**Deliverables.**
- D1.1 (month 6): pre-processing and quality-control pipeline validated on the public datasets [14,15].
- D1.2 (month 9): ethics approval obtained and data transfer agreements signed.
- D1.3 (month 12): curated, harmonised LUKS imaging database (~200 patients) with manual reference segmentations on a representative subset.

### WP2 — AI-based hepatic vascular and tumour segmentation (months 1–24; leads: Montoya, Yang; clinical validation: Chang, Leimgruber; personnel: 1 postdoctoral researcher, 100%, 36 months, hired by Montoya at ZHAW)

**Introduction.** Every subsequent work package (CFD simulation, optimisation, generative modelling) requires an accurate, patient-specific 3D map of the hepatic arterial tree, portal venous tree, liver parenchyma and tumour. WP2 develops and validates this segmentation capability, deliberately in two phases so that architecture development and benchmarking happen on open data before any clinical fine-tuning is required.

**Objectives.**
- Establish a segmentation architecture that is competitive with the published state of the art on public benchmarks before any clinical data are used.
- Adapt and fine-tune this architecture to the LUKS cohort's scanners, protocols and clinical segmentation targets (including MRI, and small tumour-feeding arterial branches relevant to catheter positioning).
- Deliver a validated, patient-specific 3D vascular and tumour model usable as direct geometric input to the WP3 CFD simulations.

**Methodology.**

*Phase 1 — pretraining on public data (months 1–8).* Adaptation and training of convolutional/transformer segmentation architectures, using the self-configuring nnU-Net framework [9] as a robust baseline, for multi-class segmentation of hepatic artery, portal vein, liver parenchyma and tumour, trained and cross-validated on the public Medical Segmentation Decathlon Task08 Hepatic Vessel and 3D-IRCADb-01 datasets [14,15], building on reported approaches for automated hepatic vessel segmentation [8]. This phase delivers an openly benchmarked baseline model and de-risks the architecture choice before any clinical data are required, and is led day-to-day by the postdoctoral researcher.

*Phase 2 — transfer learning and fine-tuning on the LUKS cohort (months 8–24).* The public-data-pretrained model is fine-tuned on the LUKS clinical cohort (WP1 Phase 2) to adapt to local scanner protocols, MRI in addition to CT, and the specific anatomical detail (small tumour-feeding arterial branches) needed for catheter-position planning. Because arterial-phase, high-resolution hepatic artery segmentation is comparatively less standardised than portal/venous segmentation, particular methodological attention will be given to arterial tree completeness and to cross-modality (CT/MRI) consistency.

Quantitative validation against the manual reference segmentations from WP1 (Dice similarity coefficient, centreline distance, branch-completeness metrics), reported separately for the public-data baseline and the fine-tuned clinical model, and qualitative review by interventional radiologists (Chang, Leimgruber) for clinical usability (i.e., whether the automatically segmented vasculature is adequate to plan a catheter trajectory).

**Deliverables.**
- D2.1 (month 8): public-data-pretrained segmentation model, benchmarked on the Medical Segmentation Decathlon Task08 Hepatic Vessel and 3D-IRCADb-01 test partitions [14,15].
- D2.2 (month 20): clinically fine-tuned segmentation model validated against the LUKS manual reference segmentations.
- D2.3 (month 24): peer-reviewed publication and open release of the segmentation code (public-data components only).

### WP3 — Patient-specific perfusion prediction by computational fluid dynamics (months 1–36; leads: Montoya, Lima; clinical validation: Strobel, Zander; personnel: 1 PhD student, 100%, 48 months, hired by Lima at LUKS, co-supervised by Montoya)

**Introduction.** WP3 is the scientific core of the project: it tests whether a patient-specific computational fluid dynamics (CFD) model of particle transport, built purely from segmented diagnostic imaging, can reproduce the perfusion patterns actually observed clinically with ⁹⁹ᵐTc-MAA and Y90 microspheres. Without this validated forward model, neither the optimisation (WP4) nor the generative surrogate (WP5) has a reliable foundation.

**Objectives.**
- Build a patient-specific arterial flow and particle-transport model from the WP2 vascular segmentation.
- Parameterise the model separately for the physical properties of ⁹⁹ᵐTc-MAA and of Y90 glass/resin microspheres.
- Quantify, across the full ~200-patient LUKS cohort, how accurately the model reproduces clinically observed planning (MAA) and treatment (Y90) perfusion, and characterise the sources of residual discordance.

**Methodology.** The PhD student (months 1–12: literature review, mesh-generation and CFD-pipeline training, working initially on the public-data segmentations from WP2 Phase 1; months 12 onward: patient-specific modelling on the LUKS cohort) reconstructs patient-specific 3D arterial geometries from the WP2 segmentation, performs meshing and boundary condition assignment (inflow/outflow conditions informed by the literature-validated approaches reviewed in [6]), and implements a Lagrangian particle-transport model parameterised separately for (a) ⁹⁹ᵐTc-MAA (size distribution, density, deformability approximated as reported in the literature) and (b) Y90 glass/resin microspheres, following the modelling strategy validated in-vivo by Antón et al. [7], to predict the fraction of injected activity reaching each liver segment/tumour for a specified catheter tip position and injection protocol.

**Validation against clinical ground truth (central milestone).** For each of the ~200 patients in the WP1 cohort, the CFD model will be run using the documented planning catheter position and compared, segment-by-segment and voxel-cluster-by-voxel-cluster, against the clinically acquired ⁹⁹ᵐTc-MAA SPECT/CT perfusion map; where the treatment catheter position is documented, the model will additionally be run with the Y90 microsphere particle parameters and compared against the Y90 PET/CT distribution. Agreement will be quantified using dose-volume-histogram-based metrics and segmental perfusion correlation, following methodology consistent with the EANM dosimetry standard operating procedure [2], and will be stratified by catheter-position reproducibility between planning and treatment, replicating and extending the analysis of Kafrouni et al. [5] to test whether the model correctly attributes discordance to catheter displacement versus other factors. This work package directly tests O2, and its outcome (a validated forward model of position → perfusion) is the prerequisite for both WP4 and WP5.

**Deliverables.**
- D3.1 (month 14): CFD pipeline validated on a pilot subset (~20 patients) against clinical MAA-SPECT/CT.
- D3.2 (month 30): full-cohort validation report (~200 patients) quantifying CFD-versus-clinical agreement for both MAA and Y90, stratified by catheter-position reproducibility.
- D3.3 (month 36): PhD thesis chapters and peer-reviewed publication(s) on patient-specific CFD validation; CFD simulation outputs handed over as training data for WP5.

### WP4 — In-silico optimisation of injection position (months 24–40; leads: Lima, Leimgruber, Montoya)

**Introduction.** Once WP3 has established that the CFD model reliably predicts perfusion for a given injection position, the natural next question is whether it can also be used the other way round: to search, before any catheter is placed, for the injection position that would be expected to give the best achievable dose distribution.

**Objectives.**
- Formulate injection-position selection as a constrained optimisation problem over anatomically and procedurally reachable catheter positions.
- Quantify, retrospectively across the LUKS cohort, how the positions this optimisation would have selected compare with the positions interventionalists actually chose.

**Methodology.** Formulation of an optimisation problem over feasible catheter tip positions (constrained to positions that are anatomically and procedurally reachable, as judged with interventional radiology input) that maximises predicted tumour absorbed dose subject to constraints on normal liver dose, predicted lung shunt fraction and extrahepatic deposition, using the WP3 forward model as the objective function evaluator. Retrospective comparison, across the cohort, between the position(s) identified by the in-silico optimisation and the position(s) actually selected during the clinical procedure, assessing whether the optimisation would have been predicted to improve the tumour-to-normal-liver dose ratio actually achieved. This work package does not propose prospective clinical use of the optimiser within the project period; it establishes and quantifies, retrospectively, the potential clinical benefit, which is the necessary evidence base for any subsequent prospective validation study.

**Deliverables.**
- D4.1 (month 32): optimisation algorithm implemented and integrated with the WP3 forward model.
- D4.2 (month 40): retrospective cohort-wide comparison of optimised versus clinically chosen injection positions; peer-reviewed publication.

### WP5 — Physics-informed generative AI surrogate model (months 1–48; leads: Aviles-Rivero, Yang, Montoya; personnel: 1 PhD student, 100%, 48 months, hired by Montoya at ZHAW, co-supervised by Aviles-Rivero and Yang)

**Introduction.** Full patient-specific CFD is accurate but computationally expensive, which limits how many candidate injection positions can realistically be evaluated per patient. WP5 asks whether a generative model, trained on CFD simulations and clinical data, can learn the same position-to-perfusion mapping at a small fraction of the computational cost, and whether it can eventually bypass the MAA planning step altogether.

**Objectives.**
- Train a physics-informed generative model that reproduces WP3 CFD predictions at near-real-time speed.
- Benchmark this surrogate against full CFD for accuracy and computation time.
- Assess, as an exploratory feasibility analysis, whether the surrogate can predict Y90 treatment perfusion directly from diagnostic imaging without an intermediate MAA planning injection.

**Methodology.** The PhD student (months 1–12: training in physics-informed and generative deep learning methods under Aviles-Rivero and Yang, and contributing to WP2 while WP3 simulation data are generated; months 12–48: core WP5 development) uses the CFD simulations generated in WP3 (spanning many simulated catheter positions per patient, not only the clinically used one) together with the paired clinical imaging and perfusion data as training data, to develop a generative model that learns the mapping from (vascular geometry, catheter position) to perfusion distribution, informed by the underlying transport physics (e.g., through physics-informed loss terms or hybrid architectures) rather than as a purely data-driven black box, building on the applicants' prior work in physics-aware and generative deep learning for medical imaging [11,12]. The generative surrogate is benchmarked against the full CFD model (WP3) for accuracy and computation time, with the target of enabling interactive, near-real-time evaluation of multiple candidate injection positions. As an exploratory analysis, the surrogate — once trained on sufficient paired MAA-planning/Y90-treatment data — will be tested for whether it can predict Y90 treatment perfusion directly from diagnostic CT/MRI without requiring an intermediate MAA planning injection (objective O4), reported as a feasibility analysis rather than a claim of clinical readiness, given the limited size of the available paired dataset.

**Deliverables.**
- D5.1 (month 24): physics-informed generative model architecture implemented and trained on early WP3 CFD outputs.
- D5.2 (month 40): benchmarking report comparing the generative surrogate against full CFD (accuracy, computation time) across the LUKS cohort.
- D5.3 (month 48): feasibility analysis of direct diagnostic-imaging-to-Y90-perfusion prediction; PhD thesis and peer-reviewed publication(s).

### Methodological risk assessment

The principal risks are: (i) incomplete or inconsistent documentation of the exact clinical catheter tip position in older procedure reports, which will be mitigated by co-registering angiographic road-map images with the segmented vascular model rather than relying on free-text reports; (ii) residual physical differences between MAA and Y90 microsphere transport that cannot be fully captured even with particle-specific CFD parameters, which is why WP3 explicitly quantifies and reports discordance rather than assuming perfect predictivity; (iii) the WP5 generative model requiring more paired training data than the ~200-patient cohort provides, which is why WP5 is scoped as a feasibility/benchmarking work package building on, and explicitly bounded by, the CFD simulations from WP3 rather than depending solely on clinical data volume; and (iv) computational cost of patient-specific CFD, mitigated by the mesh and solver strategies established in the literature [6,7] and by the progressive shift toward the WP5 surrogate for scenarios requiring many repeated evaluations.

---

## 4. Innovation and significance

VIRTUO's innovation is not any single component — AI vessel segmentation, CFD of intra-arterial particle transport, and generative surrogate modelling each exist as separate research lines — but their integration into a single, clinically grounded pipeline that is validated end-to-end against a substantial, real-world paired planning/treatment perfusion cohort, and that explicitly targets the two concrete clinical limitations identified in Section 1.3: the imperfect MAA-to-Y90 surrogate relationship and the impossibility of comparing multiple candidate injection sites within one invasive procedure. Scientifically, the project will produce (a) a validated, open methodology for patient-specific particle-transport prediction from purely diagnostic imaging, (b) a quantitative, cohort-level characterisation of where and why MAA-based planning succeeds or fails to predict Y90 delivery, directly extending prior single- or few-centre analyses [5,7] to a larger, harmonised dataset, and (c) a first exploration of physics-informed generative modelling for interventional treatment planning in this domain. Clinically, a validated virtual twin has the potential, in the longer term and subject to prospective validation beyond the scope of this project, to reduce the number of invasive procedures required per patient and to support more consistent, less operator-dependent selection of injection position — directly addressing the personalised-dosimetry imperative established by DOSISPHERE-01 [4] and endorsed by current EANM and AAPM guidelines [1,3].

---

## 5. Project organisation, personnel, budget and timeline

The project runs for 48 months. Work package leadership and clinical/technical responsibilities are detailed in Section 3; overall project coordination is shared between LUKS (clinical lead, Lima) and ZHAW (technical lead, Montoya), with quarterly consortium meetings including all applicants and partners. Indicative timeline: WP1 Phase 1 (public data) months 1–6 and Phase 2 (LUKS cohort) months 4–12, with rolling data additions as needed; WP2 months 1–24 (public-data pretraining months 1–8, clinical fine-tuning months 8–24); WP3 months 1–36 (PhD training and public-data piloting months 1–12, clinical cohort modelling and validation months 12–36); WP4 months 24–40; WP5 months 1–48 (PhD training and WP2/WP3 support months 1–12, core generative-model development months 12–48), overlapping WP3 so that early CFD outputs can be used for methodological development while cohort-wide validation continues. Deliberately starting WP2 on public data in month 1 means segmentation methodology is already mature by the time the clinical cohort and its ethics approval are in place, shortening the effective critical path of the project. Results will be disseminated through peer-reviewed publications in nuclear medicine, interventional radiology and medical image computing venues, presentation at EANM and international interventional radiology congresses, and open release of the segmentation and simulation code (subject to data protection constraints on the underlying clinical imaging, which will not be publicly shared).

### 5.1 Personnel

Three new positions carry the technical core of the project, each attached to the work package(s) matching their expertise (Section 3):

- **Postdoctoral researcher — AI segmentation (WP2).** 100% FTE, 36 months, hired and line-managed by Montoya at ZHAW. Leads day-to-day development of the public-data-pretrained and clinically fine-tuned segmentation models, in close consultation with the LUKS interventional radiology and nuclear medicine team (Chang, Leimgruber) for clinical validation.
- **PhD student — computational fluid dynamics (WP3).** 100% FTE, 48 months, hired and line-managed by Lima at LUKS, co-supervised by Montoya (ZHAW) for the computational methodology. Carries out the patient-specific CFD particle-transport modelling and its validation against the clinical MAA/Y90 dosimetry cohort.
- **PhD student — physics-informed generative AI (WP5).** 100% FTE, 48 months, hired and line-managed by Montoya at ZHAW, co-supervised by Aviles-Rivero (Tsinghua) and Yang (Imperial College London). Develops the generative surrogate model and its benchmarking against the WP3 CFD model.

All three positions include a structured co-supervision arrangement across the consortium (clinical co-supervision for the two ZHAW-based positions; computational co-supervision for the LUKS-based position) and planned research visits between LUKS, ZHAW, Tsinghua and Imperial College London to support methodological exchange, in addition to the applicants' own time (which is not separately costed, following standard SNSF practice for principal investigators).

### 5.2 Budget (personnel costs)

Salaries for the doctoral and postdoctoral positions are planned within the salary bands set by the SNSF for project funding [16]: an annual gross salary of CHF 47,040–55,000 for doctoral students (100% FTE; maximum four years of SNSF-funded doctoral employment) and CHF 80,000–110,000 for postdoctoral researchers, effective from 1 January 2024. The SNSF additionally pays a lump-sum supplement on top of gross salary to cover the employer's share of statutory social security contributions, at a percentage set by each host institution (published examples for Swiss institutions funded by the SNSF range from 14% to 23% of gross salary) [16]; the table below uses an indicative 16% for planning purposes, to be confirmed with the ZHAW and LUKS human-resources/finance offices at the definitive budget stage. All figures are indicative planning assumptions within the official SNSF bands, not the final salary steps, which are set jointly with the host institution in line with local scales and seniority.

| Position | Host institution | FTE | Duration | Indicative gross salary (CHF/year) | Gross salary total (CHF) | + ~16% employer social security (CHF) | Total (CHF) |
|---|---|---|---|---|---|---|---|
| Postdoctoral researcher (WP2, AI segmentation) | ZHAW (Montoya) | 100% | 36 months | 95,000 | 285,000 | 45,600 | 330,600 |
| PhD student (WP3, CFD / fluid dynamics) | LUKS (Lima) | 100% | 48 months | 50,000 | 200,000 | 32,000 | 232,000 |
| PhD student (WP5, physics-informed generative AI) | ZHAW (Montoya) | 100% | 48 months | 50,000 | 200,000 | 32,000 | 232,000 |
| **Total personnel** | | | | | **685,000** | **109,600** | **794,600** |

This personnel budget excludes consumables, computing infrastructure (GPU compute for WP2/WP5, CFD compute for WP3), travel/consortium-meeting costs, publication costs and the collaboration-partner budgets at Tsinghua University and Imperial College London, all of which are itemised separately in the SNSF budget module of the submission portal rather than in this research plan.

---

## 6. Ethical and regulatory considerations

The retrospective components of the project (WP1–WP4, and the training data collection for WP5) will be submitted for approval to the responsible cantonal ethics committee prior to data extraction, using coded/pseudonymised imaging and clinical data under a data protection concept compliant with the Swiss Human Research Act and, for the data shared with the Tsinghua and Imperial College partners, under a data transfer agreement restricting shared data to de-identified imaging and derived quantitative measures. No prospective change to patient management is proposed within this project; all optimisation and surrogate-model outputs are evaluated retrospectively against historical outcomes and are not used to guide treatment of any patient during the funding period.

---

## Bibliography

1. Weber M, Lam M, Chiesa C, Konijnenberg M, Cremonesi M, Flamen P, Gnesin S, Bodei L, Kracmerova T, Luster M, Garin E, Herrmann K. EANM procedure guideline for the treatment of liver cancer and liver metastases with intra-arterial radioactive compounds. *European Journal of Nuclear Medicine and Molecular Imaging*. 2022;49(5):1682–1699. doi:10.1007/s00259-021-05600-z. PMID: 35146577.

2. Chiesa C, Sjögreen-Gleisner K, Walrand S, Strigari L, Flux G, Gear J, Stokke C, Gabina PM, Bernhardt P, Konijnenberg M. EANM dosimetry committee series on standard operational procedures: a unified methodology for 99mTc-MAA pre- and 90Y peri-therapy dosimetry in liver radioembolization with 90Y microspheres. *EJNMMI Physics*. 2021;8(1):77. doi:10.1186/s40658-021-00394-3.

3. Busse NC, Al-Ghazi MSAL, Abi-Jaoudeh N, Alvarez D, Ayan AS, Chen E, Chuong MD, Dezarn WA, Enger SA, Graves SA, Hobbs RF, Jafari ME, Kim SP, Maughan NM, Polemi AM, Stickel JR. AAPM Medical Physics Practice Guideline 14.a: Yttrium-90 microsphere radioembolization. *Journal of Applied Clinical Medical Physics*. 2024;25(2):e14157. doi:10.1002/acm2.14157. PMID: 37820316.

4. Garin E, Tselikas L, Guiu B, Chalaye J, Edeline J, de Baere T, Assenat E, Tacher V, Robert C, Terroir-Cassou-Mounat M, Mariano-Goulart D, Amaddeo G, Palard X, Hollebecque A, Kafrouni M, Regnault H, Boudjema K, Grimaldi S, Fourcade M, Kobeiter H, Vibert E, Le Sourd S, Piron L, Sommacale D, Laffont S, Campillo-Gimenez B, Rolland Y; DOSISPHERE-01 Study Group. Personalised versus standard dosimetry approach of selective internal radiation therapy in patients with locally advanced hepatocellular carcinoma (DOSISPHERE-01): a randomised, multicentre, open-label phase 2 trial. *The Lancet Gastroenterology & Hepatology*. 2021;6(1):17–29. doi:10.1016/S2468-1253(20)30290-9. PMID: 33166497.

5. Kafrouni M, Allimant C, Fourcade M, Vauclin S, Guiu B, Mariano-Goulart D, Ben Bouallègue F. Analysis of differences between 99mTc-MAA SPECT- and 90Y-microsphere PET-based dosimetry for hepatocellular carcinoma selective internal radiation therapy. *EJNMMI Research*. 2019;9(1):62. doi:10.1186/s13550-019-0533-6. PMID: 31332585.

6. Aramburu J, Antón R, Rodríguez-Fraile M, Sangro B, Bilbao JI. Computational Fluid Dynamics Modeling of Liver Radioembolization: A Review. *CardioVascular and Interventional Radiology*. 2022;45(1):12–20. doi:10.1007/s00270-021-02956-5.

7. Antón R, Antoñana J, Aramburu J, Ezponda A, Prieto E, Andonegui A, Ortega J, Vivas I, Sancho L, Sangro B, Bilbao JI, Rodríguez-Fraile M. A proof-of-concept study of the in-vivo validation of a computational fluid dynamics model of personalized radioembolization. *Scientific Reports*. 2021;11:3895. doi:10.1038/s41598-021-83414-7. PMID: 33594143.

8. Li S, Li XG, Zhou F, Zhang Y, Bie Z, Cheng L, Peng J, Li B. Automated segmentation of liver and hepatic vessels on portal venous phase computed tomography images using a deep learning algorithm. *Journal of Applied Clinical Medical Physics*. 2024;25(6):e14397. doi:10.1002/acm2.14397. PMID: 38773719.

9. Isensee F, Jaeger PF, Kohl SAA, Petersen J, Maier-Hein KH. nnU-Net: a self-configuring method for deep learning-based biomedical image segmentation. *Nature Methods*. 2021;18(2):203–211. doi:10.1038/s41592-020-01008-z.

10. Faiella E, Pileri M, Ragone R, Grasso RF, Beomonte Zobel B, Santucci D. Digital twins in radiology: A systematic review of applications, challenges, and future perspectives. *European Journal of Radiology*. 2025;189:112166. doi:10.1016/j.ejrad.2025.112166.

11. Yang G, Yu S, Dong H, Slabaugh G, Dragotti PL, Ye X, Liu F, Arridge S, Keegan J, Guo Y, Firmin D. DAGAN: Deep De-Aliasing Generative Adversarial Networks for Fast Compressed Sensing MRI Reconstruction. *IEEE Transactions on Medical Imaging*. 2018;37(6):1310–1321. doi:10.1109/TMI.2017.2785879.

12. Aviles-Rivero AI, Papadakis N, Li R, Sellars P, Fan Q, Tan RT, Schönlieb CB. GraphX-NET — Chest X-Ray Classification Under Extreme Minimal Supervision. In: *Medical Image Computing and Computer Assisted Intervention (MICCAI) 2019*. Springer, Cham; 2019:504–512.

13. Sung H, Ferlay J, Siegel RL, Laversanne M, Soerjomataram I, Jemal A, Bray F. Global Cancer Statistics 2020: GLOBOCAN Estimates of Incidence and Mortality Worldwide for 36 Cancers in 185 Countries. *CA: A Cancer Journal for Clinicians*. 2021;71(3):209–249. doi:10.3322/caac.21660.

14. Antonelli M, Reinke A, Bakas S, Farahani K, Kopp-Schneider A, Landman BA, Litjens G, Menze B, Ronneberger O, Summers RM, van Ginneken B, Bilello M, Bilic P, Christ PF, Do RKG, Gollub MJ, Heckers SH, Huisman H, Jarnagin WR, et al.; International Medical Image Computing and Computer Assisted Intervention (MICCAI) Medical Segmentation Decathlon Consortium; Cardoso MJ. The Medical Segmentation Decathlon. *Nature Communications*. 2022;13(1):4128. doi:10.1038/s41467-022-30695-9. PMID: 35840566. (Task08_HepaticVessel: 443 contrast-enhanced CT scans with hepatic vessel and tumour segmentations.)

15. Soler L, Hostettler A, Agnus V, Charnoz A, Fasquel JB, Moreau J, Osswald AB, Bouhadjar M, Marescaux J. 3D Image Reconstruction for Comparison of Algorithm Database: A Patient-Specific Anatomical and Medical Image Database. IRCAD, Strasbourg, France, Technical Report. 2010. (3D-IRCADb-01: 20 contrast-enhanced CT scans with liver, hepatic vein and portal vein segmentations; available at ircad.fr/research/data-sets/liver-segmentation-3d-ircadb-01/.)

16. Swiss National Science Foundation. Annex XII: Salary ranges and guidelines for employees in SNSF-funded projects (Ausführungsreglement zum Beitragsreglement). Bern: SNSF; effective 1 January 2024 (salary bands and social-security lump-sum information also summarised at snf.ch, "Salary increase for doctoral students" and "Increase in salary bands for project staff"). Available at: snf.ch/media/en/yXApuFw4ml0TPYe2/Annex_XII_Ausfuehrungsreglement_Beitragsreglement_E.pdf.

---

*Note on reference verification: all references above were cross-checked against publicly indexed bibliographic sources (PubMed, journal publisher pages, and/or Crossref-indexed records) for title, authorship, journal, year and DOI/PMID accuracy prior to inclusion. No reference was inferred or completed from partial information.*
