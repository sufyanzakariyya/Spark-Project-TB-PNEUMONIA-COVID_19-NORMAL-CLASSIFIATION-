

Mimic-aware tuberculosis detection in Nigerian chest X-rays using external TB-versus- normal pretraining Does external TB-versus-normal pretraining improve a model's ability to distinguish tuberculosis (TB) from its radiological mimics — pneumonia and COVID-19 — in a Nigerian four-class chest X-ray dataset?
This repo contains the full experiment notebook for that question, built for the SPARK Academy 2026 Hackathon (Project Card 19).

Research Question
Most TB chest X-ray models are trained and evaluated on simplified TB vs. Normal datasets. High performance on that split says nothing about whether a model can tell TB apart from other abnormal lung conditions that look similar on a radiograph. This project instead evaluates on a four-class dataset (COVID-19, Pneumonia, TB, Normal) collected from Aminu Kano Teaching Hospital, Nigeria — and directly tests whether pretraining a backbone on an external TB-vs-Normal dataset before fine-tuning improves mimic-aware TB detection, not just abnormal-vs-normal separation.

TL;DR — What we found
	Model A (TB-pretrained + LLRD fine-tuning)	Model B (ImageNet-only baseline)
Held-out test accuracy (n=600)	98.83%	98.50%
TB recall (n=150)	99.33%	97.33%
Mimic-differential accuracy (COVID/Pneumonia/TB only)	98.44%	98.00%

TB-pretraining shows a consistent, directional improvement in TB recall specifically — the exact failure mode this study was designed to probe (Model A never underperformed Model B across 10,000 bootstrap resamples of the test set). This falls just short of conventional statistical significance (McNemar's p=0.25), most likely due to the small TB test set (n=150) combined with near-ceiling baseline performance. No reliable difference was found in overall accuracy, mimic-differential accuracy, or Grad-CAM attention pattern.
This is a modest, honest, statistically-tested claim — not the inflated original result. See docs/results_and_discussion.md for the full writeup, including everything that went into getting here.

Repo Contents
.
├── final_experiment.ipynb              # Full pipeline: EDA → preprocessing → pretraining →
│                                          fine-tuning → evaluation → statistical testing → Grad-CAM
├── docs/
│   ├── results_and_discussion.md       # Compiled results, tables, and suggested paper narrative
│   └── full_documentation.docx         # Complete rebuild log with every diagnostic figure and finding
└── README.md

Datasets
This project uses two datasets, both hosted on Kaggle (not included in this repo — see Setup below):
•	Nigeria Chest X-ray Dataset (cxr-kano) — the primary fine-tuning and evaluation dataset. 2,600 radiologist-verified images from Aminu Kano Teaching Hospital: 650 each of COVID-19, Pneumonia, TB, and Normal (500 train / 150 test per class).
•	Tuberculosis Chest X-ray Database (niaid-cxr) — the auxiliary pretraining dataset. 3,500 Normal + 700 TB images. Note: the Normal and TB images in this dataset come from two unrelated external sources (see Known Limitations below) — this is documented and partially mitigated in the notebook, not ignored.

Methodology
1.	Phase 1 — TB-vs-Normal pretraining: a DenseNet121 (ImageNet-initialized) is trained on the NIAID binary dataset, with CLAHE contrast normalization and blur augmentation applied to mitigate a cross-source digitization confound (see below).
2.	Phase 2 — Four-class fine-tuning: two models are fine-tuned on the Kano dataset using identical 5-fold stratified cross-validation splits, identical loss/augmentation/epoch budget — differing only in (a) whether the Phase 1 checkpoint is loaded, and (b) whether layer-wise learning-rate decay (LLRD) or a uniform learning rate is used.
3.	Evaluation: 5-fold ensemble (soft-voted) predictions on a held-out test set, with McNemar's test, paired bootstrap confidence intervals, an explicit TB-misclassification breakdown, and quantitative Grad-CAM lung-attention scoring.

Known Limitations
•	NIAID dataset provenance confound: the Normal and TB sub-collections in the pretraining dataset come from two unrelated sources (an NCBI-archived collection and a separate Belarusian TB imaging database), which introduces a statistically significant blur/sharpness difference (p ≈ 7×10⁻²⁸) unrelated to actual pathology. This is mitigated via blur augmentation during pretraining but not eliminated. Phase 1's own validation accuracy should not be read as evidence of genuine TB-detection ability — only downstream Kano performance is treated as reliable.
•	Test-set size: 150 images per class limits statistical power, particularly for the TB-recall comparison.
•	Grad-CAM lung mask: uses a coarse anatomical-prior ellipse mask rather than a learned lung segmentation model.
Full details, diagnostics, and the reasoning behind each fix are in docs/full_documentation.docx.

Setup

Mimic-aware tuberculosis detection in Nigerian chest X-rays using external TB-versus- normal pretraining Does external TB-versus-normal pretraining improve a model's ability to distinguish tuberculosis (TB) from its radiological mimics — pneumonia and COVID-19 — in a Nigerian four-class chest X-ray dataset?
This repo contains the full experiment notebook for that question, built for the SPARK Academy 2026 Hackathon (Project Card 19).

Research Question
Most TB chest X-ray models are trained and evaluated on simplified TB vs. Normal datasets. High performance on that split says nothing about whether a model can tell TB apart from other abnormal lung conditions that look similar on a radiograph. This project instead evaluates on a four-class dataset (COVID-19, Pneumonia, TB, Normal) collected from Aminu Kano Teaching Hospital, Nigeria — and directly tests whether pretraining a backbone on an external TB-vs-Normal dataset before fine-tuning improves mimic-aware TB detection, not just abnormal-vs-normal separation.

TL;DR — What we found
	Model A (TB-pretrained + LLRD fine-tuning)	Model B (ImageNet-only baseline)
Held-out test accuracy (n=600)	98.83%	98.50%
TB recall (n=150)	99.33%	97.33%
Mimic-differential accuracy (COVID/Pneumonia/TB only)	98.44%	98.00%

TB-pretraining shows a consistent, directional improvement in TB recall specifically — the exact failure mode this study was designed to probe (Model A never underperformed Model B across 10,000 bootstrap resamples of the test set). This falls just short of conventional statistical significance (McNemar's p=0.25), most likely due to the small TB test set (n=150) combined with near-ceiling baseline performance. No reliable difference was found in overall accuracy, mimic-differential accuracy, or Grad-CAM attention pattern.
This is a modest, honest, statistically-tested claim — not the inflated original result. See docs/results_and_discussion.md for the full writeup, including everything that went into getting here.

Repo Contents
.
├── final_experiment.ipynb              # Full pipeline: EDA → preprocessing → pretraining →
│                                          fine-tuning → evaluation → statistical testing → Grad-CAM
├── docs/
│   ├── results_and_discussion.md       # Compiled results, tables, and suggested paper narrative
│   └── full_documentation.docx         # Complete rebuild log with every diagnostic figure and finding
└── README.md

Datasets
This project uses two datasets, both hosted on Kaggle (not included in this repo — see Setup below):
•	Nigeria Chest X-ray Dataset (cxr-kano) — the primary fine-tuning and evaluation dataset. 2,600 radiologist-verified images from Aminu Kano Teaching Hospital: 650 each of COVID-19, Pneumonia, TB, and Normal (500 train / 150 test per class).
•	Tuberculosis Chest X-ray Database (niaid-cxr) — the auxiliary pretraining dataset. 3,500 Normal + 700 TB images. Note: the Normal and TB images in this dataset come from two unrelated external sources (see Known Limitations below) — this is documented and partially mitigated in the notebook, not ignored.

Methodology
1.	Phase 1 — TB-vs-Normal pretraining: a DenseNet121 (ImageNet-initialized) is trained on the NIAID binary dataset, with CLAHE contrast normalization and blur augmentation applied to mitigate a cross-source digitization confound (see below).
2.	Phase 2 — Four-class fine-tuning: two models are fine-tuned on the Kano dataset using identical 5-fold stratified cross-validation splits, identical loss/augmentation/epoch budget — differing only in (a) whether the Phase 1 checkpoint is loaded, and (b) whether layer-wise learning-rate decay (LLRD) or a uniform learning rate is used.
3.	Evaluation: 5-fold ensemble (soft-voted) predictions on a held-out test set, with McNemar's test, paired bootstrap confidence intervals, an explicit TB-misclassification breakdown, and quantitative Grad-CAM lung-attention scoring.

Known Limitations
•	NIAID dataset provenance confound: the Normal and TB sub-collections in the pretraining dataset come from two unrelated sources (an NCBI-archived collection and a separate Belarusian TB imaging database), which introduces a statistically significant blur/sharpness difference (p ≈ 7×10⁻²⁸) unrelated to actual pathology. This is mitigated via blur augmentation during pretraining but not eliminated. Phase 1's own validation accuracy should not be read as evidence of genuine TB-detection ability — only downstream Kano performance is treated as reliable.
•	Test-set size: 150 images per class limits statistical power, particularly for the TB-recall comparison.
•	Grad-CAM lung mask: uses a coarse anatomical-prior ellipse mask rather than a learned lung segmentation model.
Full details, diagnostics, and the reasoning behind each fix are in docs/full_documentation.docx.

Setup
1.	This notebook is built to run on Kaggle with the two datasets attached (cxr-kano and niaid-cxr), using paths of the form:
/kaggle/input/datasets/<owner>/cxr-kano/cxr-kano/{train_folder,test_folder}/{COVID,NORMAL,PNEUMONIA,TB}
/kaggle/input/datasets/<owner>/niaid-cxr/niaid-cxr/{Normal,Tuberculosis}
Update KANO_BASE / NIAID_BASE in Cell 1 to match your dataset paths if different.
2.	Requires a GPU runtime (the notebook checks for CUDA and will fall back to CPU, but training will be slow).
3.	Key dependencies: torch, torchvision, opencv-python, scikit-learn, pandas, matplotlib, seaborn, statsmodels, scipy.
4.	Run cells sequentially from top to bottom — later cells depend on variables/functions defined earlier (dataset paths, load_and_preprocess(), CLASS_NAMES, etc.).

Citing / Context
Built for SPARK Academy — The Sprint AI Training for African Medical Imaging Knowledge Translation, 2026 Hackathon, Project Card 19.

License
(Add your preferred license here — e.g. MIT — before publishing.)


1.	This notebook is built to run on Kaggle with the two datasets attached (cxr-kano and niaid-cxr), using paths of the form:
/kaggle/input/datasets/<owner>/cxr-kano/cxr-kano/{train_folder,test_folder}/{COVID,NORMAL,PNEUMONIA,TB}
/kaggle/input/datasets/<owner>/niaid-cxr/niaid-cxr/{Normal,Tuberculosis}
Update KANO_BASE / NIAID_BASE in Cell 1 to match your dataset paths if different.
2.	Requires a GPU runtime (the notebook checks for CUDA and will fall back to CPU, but training will be slow).
3.	Key dependencies: torch, torchvision, opencv-python, scikit-learn, pandas, matplotlib, seaborn, statsmodels, scipy.
4.	Run cells sequentially from top to bottom — later cells depend on variables/functions defined earlier (dataset paths, load_and_preprocess(), CLASS_NAMES, etc.).

Citing / Context
Built for SPARK Academy — The Sprint AI Training for African Medical Imaging Knowledge Translation, 2026 Hackathon, Project Card 19.


