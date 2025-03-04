# Method 1-Azure Document Intelligence - Strikethrough Text Handling

## Overview
Azure Document Intelligence (formerly known as Form Recognizer) is a powerful tool for extracting text, key-value pairs, tables, and other structural elements from documents. However, it does not natively support the detection of strikethrough text.

## User Reports
A notable issue has been discussed in the Microsoft community, where users experienced incorrect data extraction due to strikethrough text:

1. [Form Recognizer doesn't recognize the correct text](https://learn.microsoft.com/en-us/answers/questions/1460697/form-recognizer-doesnt-recognize-the-correct-text?utm_source=chatgpt.com)

2. [Azure strike through detection](https://learn.microsoft.com/en-au/answers/questions/1811177/detect-strikethrough-text)

![Azure Document Intelligence](img\strike_ex2.png)

## Workarounds
- **Preprocessing the document**: Use image processing techniques (e.g., OpenCV) to detect and remove strikethrough lines before feeding the document into Azure Document Intelligence.
- **Post-processing validation**: Implement logic to verify extracted text and correct misinterpretations.
- **Alternative OCR solutions**: Consider other OCR solutions that provide richer text formatting metadata.

📌 Summary
Azure Document Intelligence extracts: \
✅ Text (content, lines, words) \
✅ Tables (structured and unstructured) \
✅ Key-Value Pairs (important fields in forms) \
✅ Checkboxes (selected/unselected) \
✅ Basic Styles (handwriting detection) \

❌ It does NOT detect: Strikethrough, bold, underline, italics.

If you need strikethrough detection, you might need OCR post-processing or a custom ML model.

🔹 Best Approach?
✔ If you need a quick solution → Use OpenCV + Tesseract OCR. \
✔ If you need high accuracy → Train YOLO/Detectron2 for strikethrough detection. \
✔ If you're using Azure OCR → Post-process results using image processing. \

## Comparison of Strike-Through Detection Methods
| Method                        | Applicability          | Accuracy         | Complexity | Tools Required             |
|-------------------------------|------------------------|------------------|------------|----------------------------|
| Graph-Based (Handwritten)     | Handwritten Documents | High (91% F-measure) | High       | Custom Implementation      |
| Center Line Detection         | Printed Text          | Medium-High     | Medium     | Tesseract, OpenCV         |
| Machine Learning Classifier   | Both                  | Potential High  | High       | Training Data, ML Frameworks |


---


# Method 2-Strikethrough Detection

## Overview
This project focuses on detecting strikethrough text in images using OpenCV techniques rather than relying on OCR-based methods. The approach involves preprocessing the image, detecting horizontal lines, and identifying words intersected by these lines.

## Steps Involved
1. **Load and Convert Image to Grayscale**
   - Converts the input image to grayscale for easier processing.
2. **Apply Adaptive Thresholding**
   - Highlights text and potential strikethrough lines.
3. **Detect Horizontal Lines**
   - Uses morphological operations to isolate horizontal lines.
4. **Extract Text Using OCR** (Optional)
   - Identifies text regions but is not the primary approach.
5. **Match Lines with Text**
   - Finds overlaps between detected lines and OCR-detected words.
6. **Visualize Results**
   - Displays intermediate steps for debugging and analysis.

## Why OCR is Not the Best for Strikethrough Detection
- **OCR struggles with obstructed text**: When text is crossed out, OCR engines may misinterpret or ignore the text.
- **OCR is computationally expensive**: Detecting strikethroughs using OCR requires text detection first, then additional processing.
- **Better suited for clean text extraction**: OCR is optimized for recognizing characters but lacks direct support for detecting visual obstructions like lines.
- **More error-prone**: Different fonts, line thicknesses, and noise levels can lead to misclassification of strikethrough text.

## Recommended Approach
Instead of relying on OCR, the method uses:
- **Morphological operations**: To detect horizontal lines effectively.
- **Dilation techniques**: To ensure connected lines are detected properly.
- **Hough Line Transform**: As an alternative approach for detecting subtle or broken strikethrough lines.

## Conclusion
This approach provides a more reliable method for detecting strikethrough text compared to OCR, focusing on direct visual analysis rather than text interpretation.

# Method 3- CNN Model training using keras

### Training Time Considerations

### Factors Affecting Training Time

### Hardware:
- **CPU:** Since you have an Intel i5 processor (no dedicated GPU), training will be slow because deep learning models perform much faster on GPUs.
- **RAM:** 16GB RAM is decent, but training speed is mostly limited by the CPU.

### Dataset Size:
- If you have a few thousand images (~256x256 resolution), it might take several hours per epoch on a CPU.
- If you have a large dataset (10,000+ images), training can take days on a CPU.

### Batch Size:
- If you use a batch size of 16, your CPU should handle it, but reducing it to 8 might help if RAM gets exhausted.

### Epochs:
- 20 epochs on CPU might take 6-12 hours for ~5000 images.
- More data = more training time.

## ⏳ Estimated Training Time (CPU)

| Dataset Size  | Batch Size | Time per Epoch | Total Time (20 Epochs) |
|--------------|------------|----------------|------------------------|
| ~1000 images | 16         | ~10-20 mins    | ~3-5 hours             |
| ~5000 images | 16         | ~30-60 mins    | ~10-20 hours           |
| ~10,000 images | 16       | ~1-2 hours     | ~1-2 days              |

## 🚀 How to Speed Up Training

🔹 **Use a GPU** – If possible, run it on a Google Colab GPU, an NVIDIA GPU (RTX 2060, 3060, or better), or an AWS/GCP instance.
🔹 **Reduce Image Size** – Training on 128x128 images instead of 256x256 speeds things up.
🔹 **Lower Epochs** – Start with 5-10 epochs and see if performance is acceptable.
🔹 **Use Pretrained Models** – Instead of training from scratch, fine-tune an existing model like U-Net trained on COCO or other segmentation datasets.

## 💡 Best Option for You?
If training locally on an i5 CPU, I recommend:
✅ **Batch size:** 8  
✅ **Lower epochs (10-15)**  
✅ **Consider Colab for free GPU usage**  

# 🚀 Train Your U-Net Model on Google Colab (GPU)

Since your i5 CPU is slow for deep learning, the best solution is to use Google Colab, which provides a free NVIDIA GPU. This will speed up training by 5-10x compared to your local CPU.

## 📌 Step 1: Set Up Google Colab
1️⃣ Go to 👉 Google Colab  
2️⃣ Click on **New Notebook**  
3️⃣ Enable GPU:
   - Click **Edit → Notebook settings**  
   - Set **Hardware accelerator** to **GPU**  
   - Click **Save**  

## 📌 Step 2: Mount Google Drive
To store your dataset and save the trained model:

```python
from google.colab import drive
drive.mount('/content/drive')
```

It will ask for Google account authorization → Accept it.

🔹 Your Drive is now accessible at **/content/drive/**

## 📌 Step 3: Upload Your Dataset to Google Drive
1️⃣ Go to **Google Drive**  
2️⃣ Create a new folder 📂 **unet_data/**  
3️⃣ Upload your dataset inside it:
   - 📂 **output_images/** (Images)  
   - 📂 **output_masks/** (Masks)  

## 📌 Step 4: Load Dataset in Colab
Run this in Colab:

```python
import os
import cv2
import numpy as np
import albumentations as A
from sklearn.model_selection import train_test_split

# Set paths inside Google Drive
IMG_DIR = "/content/drive/MyDrive/unet_data/output_images/"
MASK_DIR = "/content/drive/MyDrive/unet_data/output_masks/"
IMG_SIZE = (256, 256)
...
```