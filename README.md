
<img src="https://user-images.githubusercontent.com/42075039/152969598-177c81ee-16a7-4eb0-99e2-a69cdc81d40f.png" width="500" height="300">


# Forged signatures verification using Deep learning

The goal of this project is to distinguish between genuine and forged signatures, using deep learning cnn model with SiameseNet architecture.



Even in the digital age, customers still use their signatures as a primary form of authentication for a range of transactions. 
To minimize the risk of fraud, financial institution needs the right solutions to detect forgeries quickly and accurately. Manual signature verification may let a skilled forgery slip through and you’re likely to suffer both financial and reputational losses.




## Data source:
Kaggle - Signatures of Dutch Users for checking forgery: 

 - [https://www.kaggle.com/robinreni/signature-verification-dataset](https://www.kaggle.com/robinreni/signature-verification-dataset)


## EDA process:
300MB image files, 2 csv files: 1649 signatures from 68 classes, 23205 pairs of signatures
- Original train csv file contained all the classes unordered; thus, it was necessary first to remove the test classes and to reorder the records to prevent leakage to validation set.
- Dataset is fairly balanced between original and forged samples: 0-9830, 1-7628
- Image files have different dimensions; thus, we need to resize and down scale all of them.
- Signatures are written in various angles which may affect model ability to generalize.
- Due to long training time, work was done with a smaller sample datasets.


## Feature engineering:
The framework used in this project was pytorch.
- Prepare sets of two images that are from the same class: a similar set labeled as 0, and a dissimilar pair labeled as 1.
- Images were converted to gray scale (one channel) for dimensionality reduction.
- Images resized to 128X128 pixels and minor random rotation augmentation applied.

<img src="https://user-images.githubusercontent.com/42075039/152969918-46f6a10a-dc62-4361-8794-bc83c9772f81.png" width="600">

## Siamese Network Model:
CNN Networks requires large data and are typically able to classify an image it already seen during training; where’s One Shot Learning requires limited number of samples and can classify a new image it hasn’t seen before.
Instead of learning which image belongs to which class, Siamese network learns how to determine the “similarity” between two images aka. “pairwise learning”.

### Siamese network architecture:
Two or more identical subnetworks having same parameters and weights.
FC layer with extracted featured vectors

<img src="https://user-images.githubusercontent.com/42075039/152967565-14b00826-a8a4-459e-a4a9-c5acfb3d1d31.png" width="600">

### Main principle for finding similarity:
If the two input images belong to the same class, then their feature vectors must also be similar, else they would be different. Thus, the element-wise absolute difference between the two feature vectors must be very different in both cases .

Poplar loss functions: Contrastive Loss, Triplet loss, Binary cross entropy and more.

### Contrastive Loss:
Contrastive loss takes a pair of inputs and minimizes the embedding distance when they are from the same class but maximizes the distance otherwise. A regularization parameter called “margin”, helps to set apart the classes.

<img src="https://user-images.githubusercontent.com/42075039/152968485-52b30009-72bb-4bca-8727-5b6206e0db3a.png" width="400">

### Binary Cross-Entropy With Logit Loss:
Binary cross entropy compares each of the predicted probabilities to actual class output which can be either 0 or 1. It then calculates a score that penalizes (using log) the probabilities based on the distance from the expected value. Meaning - how close or far from the actual value.

<img src="https://user-images.githubusercontent.com/42075039/152968635-271a2084-f4b5-461b-a995-fd1cee9daac0.png" width="400">

## Siamese model Architecture:

<img src="https://user-images.githubusercontent.com/42075039/152968857-4e32941c-5b80-48a5-9bc4-1485712d3df8.png" width="600">

**Model elements:**
- 2 inputs
- 3 convolutional layers with: ReLU activation, BatchNorm, Maxpool, DroupOut
- FC with 3 linear layers: ReLU activation, DroupOut
 - 2 outputs (16 vector)
Total trainable params: 851,876

## Results and conclusions:
 - Two loss functions were tested and the one finally used: BCEWithLogitsLoss
- Keeping track of tensor dimensions!!! An extra dimension of the label significantly hampered loss decreasing rate.
 - Redesigned net architecture after countless experiments with layers param.
- Beside experiment with learning rate, using L1/L2 regularization with Adam optimizer impaired model performance; thus, not used.

<img src="https://user-images.githubusercontent.com/42075039/152970608-f2e1e80c-9258-4272-8ce8-190b3d667674.png" width="500">
<img src="https://user-images.githubusercontent.com/42075039/152970666-ff9cb402-6282-428e-b2cb-f27c1cfac8cb.png" width="200">
