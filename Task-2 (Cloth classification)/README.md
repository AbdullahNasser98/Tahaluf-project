# Cloth Classification

## In this file we are going to work on classifying different items of clothing

## Dataset introduction:
- In this data you will find 10 popular items of clothing
- The images shape is (533, 400, 3)
- Classes in the dataset = [shirt, longsleeve, dress, skirt, tshirt, pants, outwear, hat, shoes]
- A sample of each class
![cloth](https://user-images.githubusercontent.com/61900536/212160337-3b64403c-6733-4df3-90d8-864467fb3190.png)
- And looking at the figure below we can notice that there is a slight class imbalancing problem which we solve later on in the code
![distribution](https://user-images.githubusercontent.com/61900536/212162555-9b21c9b3-ee9c-4b75-9e12-da0559258fed.png)
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Preprocessing steps:
- Resize the images to 256,256
- Add rotation and zoom augmentation
- Data split: Training=**3068** images, testing=**372** images, and validation=**341** images.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Model:
We used to approaches to choosing a model.
1. A CNN which we built and trained from scratch
2. And MobileNetV2
Keeping in mind having the weights as low as possible for easy deployment of the model
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Model 1 (CNN trained from scratch):
### model architecture
![image](https://user-images.githubusercontent.com/61900536/212165446-2b6b9631-4a09-44b3-b2e2-978e2c951c11.png)
- I used learning rate decay and early stopping to prevent overfitting
- I also used class weight balancing methods to prevent biassing towards one class
### Receptive field
In short, receptive field is the size of the region in the input that produces the feature.
receptive field per layer ![image](https://user-images.githubusercontent.com/61900536/212172115-56919600-e0ff-4862-8cf7-bdf9de1028b2.png)  receptive field of model ![image](https://user-images.githubusercontent.com/61900536/212172169-8c792922-a491-4b8d-b4f5-03d3e9f973dd.png)


Methods to increase the receptive field:
1. **Add more convolutional layers (make the network deeper)**: 

![image](https://user-images.githubusercontent.com/61900536/212171863-077b1c7a-4d14-4946-b39c-45a2ffefcc5c.png)
2. **Add pooling layers or higher stride convolutions (sub-sampling)**
