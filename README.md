# OCR_Receipts
## Total Amount Retrieval from Receipt Images :
In this solution I have tried to retrieve total billing amount from a receipt image.In real life we would see that different organization/vendor/company print their receipt or bill in different templates due to which components like "Total Amount" get printed in different section of the receipt and also come with different identifier such as , Amount Due/Drive Through Amount/Due Amount/Total Bill/Payable Amount etc. This difference in bill templates create problem to normal OCR pipeline to pick Total Amount from a receipt.
I am trying to make Image Segmentation/Detection algorithms to learn to identify Total Amount from different bill templates. I will make my model to provide a bounding box around Total Amount in a receipt and then that bounding box coordinate would be feed into Text Extraction model which will try to read the extact text covered by that bounding box.

Main challenge of the problem is that total amount would be in different positions in different vendors/organizations bill so we have to create a model which could identify them from different billing templates.
## My Solution Flow:

![image](https://user-images.githubusercontent.com/56585442/126036574-74e3d8e6-f208-4ce9-8347-c6f7b498a4dd.png)

	

## Detail Solution :

### Data Collection  and Annotation -- 
I have collected around 200 images for receipts and around 20 receipts for OLA/Uber from different internet sources. I have then annotated the Total amount value in those images and collected bounding box coordinates from makesense.ai website.
### Preprocessing -- 
I have used Denoising AutoEncoder to denoise the input images and make them more easily identifiable for the OCR process. I have used a 2 layer CNN with Batch Normalization  as Encoder and Decoder.
### Total Amount Detection -- 
I have used Faster RCNN model to detect Total Amount in receipts. I have used around 175 images for training , 15 images for validation and another 10 images for testing purpose. I have used Albumentation library for Image augmentation to increase number of training images. I have trained it for 8 epochs. I couldn't implement callbacks due to time constraint. FRCNN has predicted the Total Amounts in the receipts and produced coordinates of bounding boxes. I have collected coordinates of the box which is predicted with highest confidence by the model. Out of 11 test receipts , this model was able to correctly detect Total Amount in 9 of them. I believe with more training data and LR scheduling with larger training epoch , it could improve more.
Similar architecture to above, I have created a separate model with OLA/Uber receipts using Faster RCNN to capture Total values from receipts. Both the model is identical only their input values are different. I have trained it for 6 epochs only. Very small training data has been a road block here.
### Text Extraction -- 
Once my model have predicted the bounding boxes I have feed those bounding boxes coordinates to Tesseract and retrieved Total amount from them. Tesseract is not working as per expectation, we could use other Text Detection techniques also such as a Deep Learning model with CNN etc.
So , in conclusion, we could split our data in two or more segments based on receipt templates if they are entirely different each other. Then we could apply DAE on them to get them denoised which would help our text detection model and train our text detection model to predict bounding boxes. Once we get those bounding boxes we could apply text extraction model to extract characters/numbers from them.






