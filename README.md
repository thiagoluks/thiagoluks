# Dr Thiago Lima

Medical Physicist | Nuclear Medicine | Medical Imaging | Radiation Protection

Medical physicist working at the intersection of clinical practice, 
research, medical imaging, dosimetry and computational methods.

## Research

- Molecular radiotherapy dosimetry
- PET/CT and SPECT/CT quantification
- Cardiac PET perfusion imaging and artefact correction
- Radiopharmaceutical therapy
- Medical imaging and image quality
- Radiation protection
- Monte Carlo simulation
- AI and machine learning in medical imaging
- Digital twins for interventional treatment planning
- Agentic AI workflows for scientific writing and reproducible research

## Clinical tools

Tools already in clinical use at Luzerner Kantonsspital (LUKS). They live in the clinical hub, [thiagoluks/luks-medical-physics-apps](https://github.com/thiagoluks/luks-medical-physics-apps) *(private repo)*, behind a single launcher.

### 🏥 Radiation Room Exposure Explorer
Compares radiation dose structured reports from interventional procedures (for example with and without a RADPAD shield) and, from room and staff detector logs, compares room and staff exposure between scenarios.

### 🧱 Shielding Calculator
Structural shielding for nuclear medicine rooms (PET, SPECT, radionuclide therapy) following the Swiss BAG directives, with a web interface for testing room layouts and parameters.

## Research projects

Research projects are catalogued, one folder per topic, in the research hub [thiagoluks/Research](https://github.com/thiagoluks/Research) *(private repo)*. Code moves to the clinical hub only once it is used in the clinic.

### 🧬 Molecular Radiotherapy
Tools and models for patient-specific dosimetry and pharmacokinetic analysis.

### ☢️ Nuclear Medicine
Quantification, image reconstruction and quality assurance tools for PET and SPECT.

### 💻 Computational Medical Physics
Monte Carlo simulations, image processing and data analysis.

### 🤖 AI & Medical Imaging
Machine-learning approaches for image quality, segmentation and quantitative imaging.

### ❤️ FUTURE-CARE — Prototype-Driven Quantitative Cardiac PET
Data-driven motion compensation and deep-learning synthetic attenuation maps for [82Rb] myocardial perfusion PET, evaluated against standard clinical reconstruction in a retrospective patient cohort and carried through to a blinded reader study of diagnostic and management impact. A parallel proposal, FUTURE-CARE-Siemens, extends this into a joint project with Siemens Healthineers: a larger cohort, a partially industry-funded PhD position, and four further prototypes covering scan-specific dynamic framing, data-driven extraction of cardiac contractility, AI segmentation of cardiac structures and voxel-wise myocardial blood flow imaging. Both proposals drafted — see [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/FUTURE-CARE) *(private repo)*.

### 📊 PET-IMAGE-QUALITY — Patient Image Quality in Clinical PET
How scanner technology, protocol and reconstruction determine the image quality patients actually receive. First subproject: a national survey of oncological [18F]FDG PET image quality across Switzerland, combining blinded reader scores with liver noise measurements from routine scans on about two-thirds of the country's PET/CT and PET/MR systems, with the Swiss Federal Office of Public Health. Manuscript in preparation — see [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/PET-IMAGE-QUALITY) *(private repo)*.

### 🫀 VIRTUO — Radioembolisation Digital Twin
Patient-specific virtual twins (AI vascular segmentation + computational fluid dynamics + physics-informed generative AI) for radioembolisation planning, dosimetry and treatment optimisation. SNSF proposal in preparation — see [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/VIRTUO) *(private repo)*.

### ⌚ MIRAGE — At-home Wearable Dosimetry
Single-scan, wearable-anchored and generative-AI-synthesised dosimetry for molecular radiotherapy, replacing today's 3–4 hospital SPECT/CT visits with one scan plus a continuously worn gamma detector. Horizon Europe draft proposal (three consortium variants), not yet submitted — see [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/MIRAGE) *(private repo)*.

### 📡 RADIANCE — Wearable Detector Dosimetry (superseded by MIRAGE)
Earlier Horizon Europe draft proposal jointly optimising two wearable radiation-detector platforms (WIDMApp and OpenDosimeter) for at-home molecular radiotherapy dosimetry. Not submitted; superseded by MIRAGE's AI/simulation-based approach — kept for reference at [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/RADIANCE) *(private repo)*.

### ⚛️ Dosimetry — Converting TIA and Dose between 177Lu and 225Ac, Mouse and Human
An app that converts time-integrated activity and absorbed dose between 177Lu- and 225Ac-labelled PSMA ligands, assuming the same biological kinetics, and extrapolates mouse biodistribution to the human adult. It is built on preclinical cut-and-count data for [225Ac]Ac-SibuDAB and [225Ac]Ac-PSMA-617, and reports a band for daughter equilibrium in the 225Ac chain. An image stage (serial 177Lu SPECT/CT simulated in OpenGATE 10 for 177Lu and for 225Ac with its daughters) is scaffolded. Stage 1 app working — see [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/Dosimetry) *(private repo)*.

### 🛠️ AGENT-WORKSPACE — Agentic AI as a Research Environment
Using a persistent AI agent as the working environment for research: drafting and revising proposals, verifying literature, generating figures and maintaining the research repository, under conventions that keep every change small and reviewable. Workspace established; research scope not yet defined — see [thiagoluks/Research](https://github.com/thiagoluks/Research/tree/main/AGENT-WORKSPACE) *(private repo)*.

## Software

| Project | Hub | Description | Language |
|---|---|---|---|
| [luks-medical-physics-apps](https://github.com/thiagoluks/luks-medical-physics-apps) *(private)* | Clinical | Launcher for the clinical tools above: exposure explorer and shielding calculator | Python, JavaScript |
| [Dosimetry app](https://github.com/thiagoluks/Research/tree/main/Dosimetry) *(private)* | Research | TIA and absorbed-dose conversion between 177Lu and 225Ac and from mouse to human, with uncertainty checks against the EANM guidance | Python |
| [MC-simulation-of-225Ac-in-bone](https://github.com/thiagoluks/MC-simulation-of-225Ac-in-bone) *(private)* | Research | OpenGATE Monte Carlo of 225Ac and its daughters in a voxelised vertebra, and a red-marrow dose comparison of MIRD, IDAC and Monte Carlo; part of the Dosimetry topic | Python |

## Publications

See my publications on
[ORCID](...) and [Google Scholar](...).

## Links

- [ORCID](...)
- [Institutional profile](...)
- [ResearchGate](...)
