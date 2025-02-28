# Azure Document Intelligence - Strikethrough Text Handling

## Overview
Azure Document Intelligence (formerly known as Form Recognizer) is a powerful tool for extracting text, key-value pairs, tables, and other structural elements from documents. However, it does not natively support the detection of strikethrough text.

## User Reports
A notable issue has been discussed in the Microsoft community, where users experienced incorrect data extraction due to strikethrough text:

1. [Form Recognizer doesn't recognize the correct text](https://learn.microsoft.com/en-us/answers/questions/1460697/form-recognizer-doesnt-recognize-the-correct-text?utm_source=chatgpt.com)

2. [Azure strike through detection](https://learn.microsoft.com/en-au/answers/questions/1811177/detect-strikethrough-text)

![Azure Document Intelligence](img\strike_through_detection_not_working.png)

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

