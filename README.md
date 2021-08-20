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

## Form Classifier Building
1. Convolutional Neural Network(CNN) model:

For dealing with images, CNN is known as one of the most powerful models for training
with images. Therefore, we are going to build the image classifier based on CNN.

2. Classifications determination:

Although the dataset has two main types of forms, the handwritten forms seem to cause
high error because of their high variance. As a result, our classifier is going to be a multiple
classifier for 3 form types:
- Form 1: Organized form
- Form 2: Older form with printed writing and lines
- Form 3: Older form with handwritten content

To prepare for model training, 120 images are divided into ‘train_images’ and
‘test_images’ with a ratio of ‘7/3’, which to say the first 36 images are generated to the test
dataset and 84 images are put into the train dataset. Both train and test images are divided into 3
folders with labels ‘form1’, ‘form2’, ‘form3’, as shown below.

![image](https://user-images.githubusercontent.com/38795845/130284784-33c966ab-79dd-47d8-8e73-3a593f4835a2.png)

CNN Model initialization:

For the CNN mode, ‘relu’ is going to be applied as the activation function.
Structure of the model:
- Two convolutional layers with activation function and followed by two pooling layers to
feed forward the image data.
- One flatten() layer to connect all nodes.
- One dense layer with ‘relu’ function to generate the result of parameter weights.
- One Softmax() layer with ‘softmax’ function to sparse the result into one of the 3
classifications set before.
- Compile with ‘RmsProp’ optimization function

![image](https://user-images.githubusercontent.com/38795845/130285143-114ef170-774b-4aed-88f1-0bc8a70d045b.png)

Classifier Performance:

Training parameters: {train_set, steps_per_epoch = 21, epochs = 50, validation_data = test_set,
validation_steps = 3}

With a validation dataset with 3*batch_size = 12 images, the model reaches 1.0 accuracy
on the test set. The performance seems satisfying. Further image improvement will be based on
the classification result.
![image](https://user-images.githubusercontent.com/38795845/130285164-41c98edb-41b5-45a4-98b5-7db72d8eec0d.png)

## Image Preprocess
Image improvement with OpenCV:

Resize all images to a shape (900, 500). Crop all images with only ‘essential variables’ area.
Process all images through grayscale, denoise and filtering of OpenCV.
Recheck the size of processed images: ‘Form1’: all sized (300, 1800)
‘Form3’: most sized (380,1620). However, ‘Form2’ have sizes distinct in a wide range.
Therefore, to cut small fields of a single variable, we need to cut with ratio. (For
example: How much percentage does the ‘Decedent’ block covering the processed Form 1
image)
![image](https://user-images.githubusercontent.com/38795845/130285280-c02a0300-8d8b-4972-b876-01e2a12ddd1e.png)

#### Cropped outputs
![image](https://user-images.githubusercontent.com/38795845/130286328-0bd31bed-a06a-4663-934d-6f32f53683f6.png) Form Type 1
![image](https://user-images.githubusercontent.com/38795845/130286395-86ba9598-ad80-478a-9304-a6a1dcf7e40b.png) Form Type 2
![image](https://user-images.githubusercontent.com/38795845/130286448-f4a4f1d0-fb48-4e3c-ae43-25c09a73bf99.png) Form Type 3




## Text Extraction with Py-tesseract

For a single variable, extract the string from the cropped block and tune slightly with re.sub()
![image](https://user-images.githubusercontent.com/38795845/130285391-a2c7e678-2427-440e-9557-a02b6c954a5a.png)

- Text Extraction Performance:

Surely, tesseract could extract text from the designed area. However, the results obviously
need much more tuning and improvement. I was trying to include as much information as
possible to adapt the most images. That causes there is much noises text involved and extracted
with needed information, as shown below:

![image](https://user-images.githubusercontent.com/38795845/130285480-680a2eaf-0f2b-47d0-a307-79fbc86db31a.png)


## Apply Azure FormRecognizer API
FormRecognizer(FR) is an AI-powered text extractor deployed by Microsoft on their
cloud platform, Azure. FormRecognizer could be trained to recognize the layout of certain forms
and automatically drag paired information from forms with the same layout.

Step 1: Manually draw regions on the train set images and attach labels to them

![image](https://user-images.githubusercontent.com/38795845/130285619-5da6a5bf-8b38-4b2c-815b-1b8587197af0.png)

Step 2: Train the model

![image](https://user-images.githubusercontent.com/38795845/130285652-8bf8d06e-afdb-452a-97df-1f7e271c9b9d.png)

Step 3: Check the performance by predicting a new image
![image](https://user-images.githubusercontent.com/38795845/130285673-a56bf8e2-f8da-45f1-890c-0c7f24e989f6.png)

For building a workflow for batch processing:

After training a FormRecognizer model manually, I generate a
workflow on Power Automate platform to run the pipeline through processed images to excel
data.

Step 1: Upload data to Azure Blob storage
![image](https://user-images.githubusercontent.com/38795845/130285976-8d7ca778-936a-4627-ade9-874942f88ecf.png)

Step 2: Conduct workflows with function nodes on Power Automate
![image](https://user-images.githubusercontent.com/38795845/130285988-cfa25d30-bea9-4ade-8bed-ea07b0211444.png)


Step 3: Run the flow and export the result
![image](https://user-images.githubusercontent.com/38795845/130286010-19dec31a-ad4a-4121-933d-809e0ba986c4.png)

## Azure FormRecognizer Performance
FR works better than the original tesseract. Results come in a well-organized format with
clean format. That is because FR firstly recognizes text content clusters in the whole image, then
starts to learn the layout though text clustering. That helps it avoid many noise pixels. And I’m
using this dataset as my final submission.

![image](https://user-images.githubusercontent.com/38795845/130286573-11c38c7f-b275-4c86-9a75-08e9ba23c86b.png)
