# OCR_form_recognization
This project is a computer vision project. The purpose is to recognize texts from image files with similar patterns. The dataset used is a set of burial records from a cemetery. 

## Data Description
The data set consists of 120 scanned images in type ‘.jpg’. Each image is one burial
record. A record normally contains information like decedent name, burial date and location. There are two types of layout among all images.

 Type 1: Organized layout with detailed information
 
 ![image](https://user-images.githubusercontent.com/38795845/130281811-9f4625e1-3637-4902-8f91-eff0d1266fce.png)
 
 Type 2: Old-fashioned layout with only essential information
 
 ![image](https://user-images.githubusercontent.com/38795845/130282269-2b75db51-b2cb-4787-9b1e-c19c731a3b82.png)

One thing to notice for type 2 forms is that there exist several forms with handwritten
contents, which could heavily interfere with the machine's cognition ability as shown below.

## Target Information Wanted from the Forms

Because type 2 forms do not have as much information as type 1 do, the target information is limited to what both forms have. The information is going to be [Decedent Name, Burial Date, Burial Section, Burial Lot, Burial Grave], 5 attributes in total. Extracted data is going to be stored in a CSV file.

## Methodology

This digitization project could be divided to two parts:
1. Image preprocess: train a classifier to recognize different types of forms and process the
images separately through image preprocessing tools.
Techniques applied:
- CNN model for form classifier.
- OpenCV for reading and improving image quality.

2. OCR progress: extract text content from treated images and record values into a
dataframe file for remaining.
Techniques applied:
- Py-tesseract-OCR for text extraction.
- Azure Form Recognizer API for forms recognition.
- Power Automate, platform to generate workflow for FormRecognizer.

