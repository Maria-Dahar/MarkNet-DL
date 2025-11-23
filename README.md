# 📄 *MarkNet: Automated Marksheet Field Extraction & OCR Verification System*

### *YOLOv8 + CRNN | End-to-End Deep Learning Pipeline for Academic Document Automation*

MarkNet is a complete, production-ready automated system designed to detect and extract key academic fields from student marksheets. It significantly reduces time, effort, and human error in academic verification processes, enabling fast, accurate, and scalable screening during admissions.

---

# 🧠 *1. Problem & Motivation*

Manual verification of marksheets is a slow and error-prone process. During university admissions, thousands of applicants must be evaluated quickly and accurately, but challenges include:

* ❌ *High manual workload*
* ❌ *Human errors & inconsistencies*
* ❌ *Difficulty handling large applicant batches*
* ❌ *Layout variations across institutions*
* ❌ *Image distortions (blur, lighting, rotation)*

To address this, MarkNet automatically extracts four critical fields from any marksheet:

| Field                | Description                                  |
| -------------------- | -------------------------------------------- |
| *NAME*             | Student’s full name                          |
| *GROUP*            | Academic group (e.g., Pre-Medical, Commerce) |
| *PERCENTAGE*       | Final examination percentage                 |
| *DATE OF ISSUANCE* | Official document issuance date              |

This automation ensures *speed, **accuracy, and **consistent decision-making*.

---

# 🚀 *2. Project Overview*

MarkNet uses a *Two-Stage Deep Learning Pipeline*:

---

## ⭐ *Stage 1 — YOLOv8 for ROI Detection*

Detects the exact regions of the marksheet where the four fields are located.

### Output:

* Bounding box for *NAME*
* Bounding box for *GROUP*
* Bounding box for *PERCENTAGE*
* Bounding box for *DATE OF ISSUANCE*

YOLOv8 is chosen for:

* High-speed inference
* Strong generalization
* Robustness to noise, blur, and layout variations

---

## ⭐ *Stage 2 — CRNN + CTC for Text Recognition*

Each detected ROI is:

1. Cropped
2. Preprocessed
3. Sent to a *CRNN (Convolution + BiLSTM)*
4. Decoded using *CTC (Connectionist Temporal Classification)*

This allows:

* Recognition of variable-length text
* Handling of distorted and irregular shapes
* Accurate sequence modeling

---

# 🏗️ *3. System Architecture*

An end-to-end system for automatic marksheet field detection and text recognition using **YOLOv8** and **CRNN**.

**Pipeline:**
1. **YOLOv8** detects regions of interest (fields like Name, Group, Percentage, Date) in full marksheets.  
2. Detected fields are cropped into separate images.  
3. **CRNN** (CNN + RNN + CTC decoder) recognizes text in each cropped field.  
4. Extracted text is compiled into a structured output (CSV or database).

![System Architecture](images\Yolo_CRNN.png)



# 📦 *4. Dataset*

## *4.1 Collection Process*

* Collected ~700–750 real marksheets
* Image conditions:

  * Clean & clear
  * Good lighting
  * High readability

## *4.2 YOLO Annotation (Roboflow)*

Bounding boxes stored in YOLO format:


x_center, y_center, width, height, class_id


Label mapping:

| Class ID | Label            |
| -------- | ---------------- |
| 0        | GROUP            |
| 1        | NAME             |
| 2        | PERCENTAGE       |
| 3        | DATE OF ISSUANCE |

## *4.3 CRNN Labeling*

Each cropped ROI stored alongside its ground-truth text in a CSV:


image_name, label
crop_001.jpg, ZAIB ALI SHAH
crop_002.jpg, PRE-MEDICAL


## *4.4 Dataset Statistics*

| Metric                 | Count |
| ---------------------- | ----- |
| Total marksheet images | 740   |
| Total cropped ROIs     | 2927  |

## *4.5 Train / Validation / Test Split*

| Model  | Train | Validation | Test |
| ------ | ----- | ---------- | ---- |
| YOLOv8 | 70%   | 20%        | 10%  |
| CRNN   | 80%   | 10%        | 10%  |

---

# 🧹 *5. Preprocessing Pipeline*

## *5.1 Vocabulary Construction*

* Convert all text to *uppercase*
* Base charset:

  * A–Z
  * 0–9
  * Hyphen, slash, dot, space
* Any missing characters auto-added

## *5.2 Image Preprocessing for CRNN*

* Convert to *grayscale*
* *ResizePad* to 64×512 without distortion
* Normalize: mean = 0.5, std = 0.5
* Convert to tensors

### Training-specific augmentations:

* Random brightness/contrast
* Rotation ±3°
* Scale jitter
* Light noise

### Validation:

* Only resize + normalize (no augmentation)

---

# 🤖 *6. Model Architecture*

## *6.1 YOLOv8*

* Multi-class object detection
* Fast inference
* Strong generalization to unseen marksheets

![Yolo](images\Yolo.png)

## *6.2 CRNN (CNN + BiLSTM + CTC)*

### Architecture Flow:

1. *CNN:* Feature extraction
2. *Bi-LSTM:* Sequence modeling
3. *CTC Decoder:* Converts model output to readable text

This architecture is widely used for OCR tasks due to its reliability.

![Yolo](images\MarkNetArchitecture.jpg)

---

# 🛠️ *7. Installation*

bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
pip install -r requirements.txt


---

# ▶️ 8. How to Use

## 8.1 Run YOLO Detection and Crop ROIs
Detect fields in a marksheet using YOLOv8 and crop the regions of interest (ROIs).

bash
python detect_and_crop.py --input "marksheet.jpg" --output crops/
`

## 8.2 Run CRNN Recognition

Recognize text from the cropped ROIs using the trained CRNN model.

bash
python recognize.py --crop_dir crops/ --model weights/crnn.pth


## 8.3 Run the Complete Pipeline

Run the end-to-end MarkNet pipeline on a marksheet image.

bash
python marknet_pipeline.py --image marksheet.jpg
### Example Output:

NAME: ZAIB ALI SHAH <br>
GROUP: PRE-MEDICAL <br>
PERCENTAGE: 77.27 <br>
DATE OF ISSUANCE: 12-08-2023



---

# 📊 *9. Evaluation Metrics*

## *YOLOv8*

* mAP@50
* mAP@50–95
* Precision
* Recall

## *CRNN*

* CER (Character Error Rate)
* Word Accuracy
* F1 Score
* Confusion Matrix
* ROC Curve

---

# 📌 *10. Directory Structure*

### YOLO Marksheets Folder Structure

```
Marksheets_Yolo
│
├── 1_BS_ENG_2024
├── 2_BS_CS_2024
├── 3_BS_IT_2024
├── 4_BS_ED_2024
├── 5_BS_MATH_2024
├── 6_BS_COM_2024
├── 7_BS_BBA_2024
├── 8_BS_IT_2025
├── 9_BS_CS_2025
├── 10_BS_COM_2025
│   ├── images
│   │   ├── CWPH3046_JPG.rf.cd695617bab831d4a23b83e5f9bb41df.jpg
│   │   ├── DBWE9445_JPG.rf.a216df92d9c5c6eaba4b1c4aea528dd1.jpg
│   │   ├── ...
│   │   └── YCUV8516_JPG.rf.ea94823cb9d1e439414f53b46c29509e.jpg
│   └── labels
├── 11_BS_BBA_2025
├── 12_BS_ENG_2025
├── 13_BS_BED_2025
├── 14_BS_MATH_2025
├── Synthetic_1
```

### Notes:
- Each class folder (e.g., `10_BS_COM_2025`) contains two subfolders:
  - `images` → Contains all marksheet images for YOLO training.
  - `labels` → Contains corresponding YOLO annotation `.txt` files.
- Filenames are unique and include `.rf.` identifiers (likely from a dataset processing tool).
- `Synthetic_1` may contain augmented or synthetic data. requirements.txt

---
### CRNN Marksheets Folder Structure
```
Marksheets_cropped/
├── 1_BS_ENG_2024/
├── 2_BS_CS_2024/
├── 3_BS_IT_2024/
├── 4_BS_ED_2024/
├── 5_BS_MATH_2024/
│ ├── BCWM7603_JPG.rf.3a4636195f42e3566e03898c83a3475b_DATE_OF_ISSUANCE.png
│ ├── BCWM7603_JPG.rf.3a4636195f42e3566e03898c83a3475b_GROUP.png
│ ├── BCWM7603_JPG.rf.3a4636195f42e3566e03898c83a3475b_NAME.png
│ ├── BCWM7603_JPG.rf.3a4636195f42e3566e03898c83a3475b_PERCENTAGE.png
│ ├── … (other files) …
│ ├── IUQQ0450_JPG.rf.a9e3641b68526788d25b0eb7eb0c0799_DATE_OF_ISSUANCE.png
│ ├── IUQQ0450_JPG.rf.a9e3641b68526788d25b0eb7eb0c0799_PERCENTAGE.png
├── 6_BS_COM_2024/
├── 7_BS_BBA_2024/
├── 8_BS_IT_2025/
├── 9_BS_CS_2025/
├── 10_BS_COM_2025/
├── 11_BS_BBA_2025/
├── 12_BS_ENG_2025/
├── 13_BS_BED_2025/
├── 14_BS_MATH_2025/
└── Synthetic_1/
```

### Notes


- **Dataset Overview:** Cropped student marksheet images organized by program and batch year for CRNN text recognition.  
- **Folder Organization:** Each subfolder (e.g., `5_BS_MATH_2024`) represents a batch/program. Images are cropped fields: `DATE_OF_ISSUANCE`, `GROUP`, `NAME`, `PERCENTAGE`.  
- **File Naming:** `<STUDENT_ID>_JPG.rf.<hash>_<FIELD>.png`  
- **Synthetic Data:** `Synthetic_1/` contains generated/augmented images.  
- **Formats:** Images in PNG; CSV files (if any) contain labels/metadata; `notes.txt` may have batch info.  
- **Usage:** Train CRNN models for automated field recognition. Reference folder paths correctly in scripts.  
- **Best Practices:** Preserve folder hierarchy, keep original filenames, and backup dataset before processing or augmentation.
---



# 🧩 *11. Utility Features*

### *ResizePad*

Preserves aspect ratio while resizing to CRNN’s input size.

### *Flexible Decoder*

Automatically maps character indices to text.

### *Augmentation Control*

Easily toggle training augmentations.

---

# 🏁 *12. Conclusion*

MarkNet provides an end-to-end automated system for extracting crucial student information using:

* *YOLOv8* for precise field detection
* *CRNN + CTC* for accurate text recognition
* *Extensive preprocessing* for high OCR accuracy

It is a *scalable, error-free, and fast solution* ideal for universities and organizations processing large volumes of documents.

---

# 🤝 *13. Contributions*

Feel free to contribute!
Open an issue or submit a pull request.

---

# 📜 *14. License*

Licensed under the *MIT License*.



### Architecture

