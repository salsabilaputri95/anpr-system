# ANPR System Based on YOLOv8 and EasyOCR for Wrong-Way Vehicle Detection & License Plate Recognition Under Various Lighting Conditions

An Automatic Number Plate Recognition (ANPR) system specifically designed to detect vehicles violating traffic flow direction (driving against traffic) and automatically read the license plates of violating vehicles. Built using the integration of YOLOv8 object detection models and EasyOCR text extraction engine, this system is optimized to be robust and accurate in extracting license plate characters under various lighting conditions (harsh lighting/bright daylight, normal lighting, and low light/night conditions).

This research was developed as a cost-effective (open-source) solution to support traffic discipline enforcement and reduce accident rates caused by wrong-way violations.

## Features

✅ **Vehicle Direction Detection** - Uses YOLOv8 to classify vehicle movement direction (correct direction vs. wrong direction).  
✅ **License Plate Localization**: Accurately detects and crops the license plate area on vehicles identified as traffic violators.
✅ **Intelligent Image Pre-processing**:
   - Grayscale conversion for input normalization
   - Adaptive image inversion based on background detection (bright/dark)
   - Denoising using Non-local Means (NLM) method to reduce noise
   - Contrast enhancement using Histogram Equalization to improve text sharpness
   - Sharpening kernel to enhance license plate character details
✅ **Text Extraction (EasyOCR) & Post-Processing**:
   - Character whitelisting (reads only capital letters A-Z, digits 0-9, and spaces)
   - Automatic text cleaning and space format normalization
   - Multi-attempt OCR with 2 preprocessing methods × 2 OCR parameter configurations
   - Similar character correction: O↔0, I↔1, DD↔D0, DW, DP to improve accuracy
✅ **License Plate Format Validation** - Strict validation based on South Sulawesi format with regex pattern: `^(DD|DW|DP)\s?\d{4}\s?[A-Z]{2,3}$`  
✅ **Result Visualization** - Display detections with valid/invalid labels on images  
✅ **Result Export** - Save detected images to the `hasil uji` folder  

## Requirements

### System
- Python 3.8 or higher
- Minimum 8GB RAM
- GPU (optional, but recommended for faster performance)

### Dependencies
See `requirements.txt` file:
```
ultralytics      # YOLOv8
torch            # Deep Learning Framework
opencv-python    # Computer Vision
easyocr          # Optical Character Recognition
numpy, pandas    # Data Processing
matplotlib       # Visualization
```

## Project Structure

```
anpr-system/
├── coding-final.ipynb              # Main notebook file
├── requirements.txt                # Python dependencies
├── README.md                       # This documentation
│
├── model/                          # Folder containing trained models
│   ├── model-arah-kendaraan.pt    # YOLOv8m - Vehicle direction detection
│   └── model-plat-nomor.pt        # YOLOv8s - License plate detection
│
├── data uji/                       # Folder containing test input images
│   └── *.jpg, *.png                # Vehicle images for analysis
│
└── hasil uji/                      # Folder for detection result output
    └── *.jpg, *.png                # Images with detection annotations
```

## Setup & Installation

### 1. Clone/Download Project
```bash
# Ensure all files are in the anpr-system folder
cd "anpr-system"
```

### 2. Create Virtual Environment (Optional but Recommended)
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux/Mac
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Verify Installation
```bash
python -c "import torch; print(torch.__version__)"
python -c "import cv2; print(cv2.__version__)"
python -c "import easyocr; print(easyocr.__version__)"
```

## Usage

### Method 1: Jupyter Notebook (Recommended)

**Step 1: Open Notebook**
```bash
jupyter notebook coding-final.ipynb
```

**Step 2: Prepare Input Data**
- Place images to be analyzed in the `data uji/` folder
- Format: JPG, PNG, JPEG
- Ensure images contain vehicles with clearly visible license plates

**Step 3: Run Notebook Cells**

1. **Cell 1-2**: Markdown - Project description
2. **Cell 3**: Import libraries
3. **Cell 4**: Load YOLO models and initialize EasyOCR reader
4. **Cell 5**: Scan `data uji` folder and list images
5. **Cell 6**: Define helper functions:
   - `optimize_bbox_for_ocr()` - Optimize bounding box with margin
   - `preprocess_plat_image()` - License plate image preprocessing
   - `bersihkan_teks()` - Normalize and clean OCR text
   - `validasi_plat_nomor()` - Validate license plate format
   - `baca_plat_nomor()` - Read and extract license plate text

6. **Cell 7**: Main process - Detect direction & plates, visualize results
7. **Cell 8**: Display list of valid detected license plates
8. **Cell 9**: Export result images to `hasil uji` folder

**Step 4: View Results**
- Detected images are saved to `hasil uji/` folder
- Color-coded labels:
  - 🟢 **Green** = Valid license plate
  - 🔴 **Red** = Invalid plate/failed to read
- Console output displays:
  - Number of wrong-way vehicles detected
  - Detected license plates
  - Valid/invalid status for each plate

### Method 2: Command Line (If modified to script)
```bash
python main.py --input data uji --output hasil uji
```
*Note: Currently not available, only supports Jupyter Notebook*

## Model Explanation

### 1. Vehicle Direction Detection (YOLOv8m)
- **Model File**: `model/model-arah-kendaraan.pt`
- **Architecture**: YOLOv8 Medium (YOLOv8m)
- **Input**: Vehicle image
- **Output**: Vehicle bounding box with class:
  - Class 0: Normal direction / Normal vehicle
  - Class 1: Opposite vehicle direction / Wrong-way vehicle
- **Confidence Threshold**: 0.25 (can be adjusted in cell 7)

### 2. License Plate Detection (YOLOv8s)
- **Model File**: `model/model-plat-nomor.pt`
- **Architecture**: YOLOv8 Small (YOLOv8s)
- **Input**: Vehicle image
- **Output**: License plate bounding box
- **Confidence Threshold**: 0.35 (can be adjusted in cell 7)

### 3. License Plate Reading (EasyOCR)
- **Library**: EasyOCR
- **Language**: English (en) - alphanumeric support
- **Preprocessing**: 
  - Grayscale conversion
  - Intensity normalization
  - Denoising & sharpening
  - Resizing if plate is too small
- **Multi-attempt OCR**: Attempts 2 preprocessing methods × 2 OCR configurations

## Supported License Plate Format

This system is optimized for **South Sulawesi** license plates with the format:
```
DD 1234 XX
```
Where:
- **DD**: Region code (DD/DW/DP) - 2 letters
  - DD = Makassar
  - DW = Wajo
  - DP = Pangkajene & Kepulauan
- **1234**: Identity number - 4 digits (0-9)
- **XX**: Registration code - 2-3 letters (A-Z)

Valid examples: `DD 1234 AB`, `DW 5678 CDE`, `DP 9999 XY`

## Output & Results

### Console Output
```
=== image1.jpg ===
Vehicle1
Wrong-Way Vehicle Detection: opposite_vehicle_direction On the box 100,50,500,400
Valid Plate (Wrong-Way Vehicle): DD 1234 AB

=== image2.jpg ===
No wrong-way vehicle detected. Process all plates in the image.
Valid Plate: DW 5678 CD
Invalid Plate: XXXX

List of Valid License Plates:
DD 1234 AB
DW 5678 CD
```

### Image Output
- Each image is displayed with detection bounding boxes
- License plate labels appear above the bounding box with color-coded background:
  - 🟢 Green: Valid plate successfully read
  - 🔴 Red: Invalid plate or failed validation
- Images are saved to `hasil uji/` folder with the same name as input

## Usage Tips

### For Optimal Results
1. **Image Quality**: Use high-resolution images (min. 640×480)
2. **Lighting**: Ensure adequate lighting for clear license plate reading
3. **Image Angle**: License plate should be visible frontally, not too tilted
4. **Plate Size**: Plates that are too small in the image will be automatically enlarged (min. 60×180 pixels)

### Parameter Adjustment
Edit Cell 7 to change:
```python
# Confidence threshold for direction detection
results_arah = model_arah.predict(source=img_rgb, conf=0.25, save=False)

# Confidence threshold for plate detection
results_plat = model_plat.predict(source=img_rgb, conf=0.35, save=False)

# Margin for OCR bounding box optimization (ratio)
optimize_bbox_for_ocr(..., margin_ratio=0.15)
```

### Debug Mode
If OCR fails, check the debug output from Cell 7:
```
[OCR DEBUG] attempt=1, cfg=1, ocr_result=[...]
[OCR DEBUG] teks_gabungan='DD 1234 AB' -> teks_bersih='DD 1234 AB'
[OCR DEBUG] fallback results=[...]
```

## Troubleshooting

### Problem 1: Model not found
**Error**: `FileNotFoundError: model/model-arah-kendaraan.pt not found`
**Solution**: Ensure `.pt` files exist in the `model/` folder

### Problem 2: Out of Memory
**Error**: `RuntimeError: CUDA out of memory`
**Solution**: 
- Use CPU: `device = 'cpu'` (slower)
- Reduce batch size
- Use GPU with larger VRAM

### Problem 3: License plate not readable
**Cause**: Low image quality, angle too tilted, insufficient lighting
**Solution**:
- Take images with better quality
- Try adjusting margin_ratio in `optimize_bbox_for_ocr()`
- Debug: Check `[OCR DEBUG]` output in console

### Problem 4: False Positive (incorrect detection)
**Solution**: 
- Increase confidence threshold in cell 7
- Filter results based on bounding box size
- Verify plate format before saving

## License & Attribution

- **YOLOv8**: Ultralytics (MIT License)
- **EasyOCR**: JaidedAI (MIT License)
- **OpenCV**: Open Source Computer Vision Library

---

**Last updated**: May 2026  
**Version**: 1.0  
**Status**: Production Ready


