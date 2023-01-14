# Cloth Classification

## In this file we are going to work on classifying different items of clothing

<details>
<summary>Dataset introduction:</summary>
<br>
- In this data you will find 10 popular items of clothing.
  <br>
- The images shape is (533, 400, 3).
  <br>
- Classes in the dataset = [shirt, longsleeve, dress, skirt, tshirt, pants, outwear, hat, shoes, shorts].
  <br>
- A sample of each class.

![cloth](https://user-images.githubusercontent.com/61900536/212160337-3b64403c-6733-4df3-90d8-864467fb3190.png)
  <br>
- And looking at the figure below we can notice that there is a slight class imbalancing problem which we solve later on in the code
  
![image](https://user-images.githubusercontent.com/61900536/212409964-a3df148b-efcf-4508-bb80-f0fda86c36b4.png)
 
</details>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
<details>
<summary>Preprocessing steps:</summary>

- Resize the images to 256,256

- Add rotation and zoom augmentation

- Data split: Training=**3068** images, testing=**372** images, and validation=**341** images.
</details>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
<details>
<summary>Model:</summary>
<br>
We used two approaches to choosing a model:
 <br> 
1. A CNN which we built and trained from scratch
  <br>
2. A pretrained MobileNetV2, which we trained on our data
  <br>

Keeping in mind having the weights as low as possible for easy deployment of the model, which is why I choose MobileNetV2.
</details>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
<details>
<summary>Model 1 (CNN trained from scratch):</summary>

### model architecture
  
![image](https://user-images.githubusercontent.com/61900536/212165446-2b6b9631-4a09-44b3-b2e2-978e2c951c11.png)

- I used learning rate decay reduction and early stopping to prevent overfitting
  <br>
- I also used class weight balancing methods to prevent biassing towards one class

### Receptive field
In short, receptive field is the size of the region in the input that produces the feature.
<br>
receptive field per layer ![image](https://user-images.githubusercontent.com/61900536/212172115-56919600-e0ff-4862-8cf7-bdf9de1028b2.png) 
<br>
<br> 
receptive field of model 
  ![image](https://user-images.githubusercontent.com/61900536/212172169-8c792922-a491-4b8d-b4f5-03d3e9f973dd.png)
<br>

### Methods to increase the receptive field:

1. **Add more convolutional layers (make the network deeper)**: 

![image](https://user-images.githubusercontent.com/61900536/212171863-077b1c7a-4d14-4946-b39c-45a2ffefcc5c.png)

2. **Add pooling layers or higher stride convolutions (sub-sampling)**
  <br>
3. **Use dilated convolutions:** Dilations introduce “holes” in a convolutional kernel [3]. The “holes” basically define a spacing between the values of the kernel. So, while the number of weights in the kernel is unchanged, the weights are no longer applied to spatially adjacent samples. Dilating a kernel by a factor of rr introduces a kind of striding of rr.
below is an image of how sub-sampling and dilated conv affects the receptive field.

![Receptive-field-pooling-vs-dilated-conv](https://user-images.githubusercontent.com/61900536/212173854-f864e29b-215a-4869-a971-4b4d22ab6e06.png)

| Receptive field | Value |
| ----------- | ----------- |
| RF | 1 |

This model's rf is 1 because we have the same stride=1, padding='valid', and kernel=2, constant across all our layers

### FLOPs & MACCs:
One way to get an idea of the speed of your model (inference time) is to simply count how many computations it does. We typically count this as FLOPS, floating point operations per second. A slight variation of this is MACCs or multiply-accumulate operations, also known as MADDs.
<br>
**The below tabel contains the values of FLOPs and MACCs for every convolution and dense layer in our model**

| Layer name | FLOPs | MACCs |
| ----------- | ----------- | ----------- |
| conv2d   | 25,165,824 | 12,582,912 | 
| conv2d_1   | 66,064,384 | 33,032,192 | 
| conv2d_2   | 65,028,096 | 32,514,048 | 
| conv2d_3   | 62,980,096 | 31,490,048 | 
| Dense  | 7,372,800 | 3,661,400 | 
| Dense_1  | 2,560 | 1,280 | 
| **Total**  | **228,523,408** | **113,306,880** |

###  Decreasing FLOPs & MACCs: 
Before we discuss how we can decrease FLOPs, we first have to understande how its calculated.
- Convolutions - FLOPs = 2x Number of Kernel x Kernel Shape x Output Shape
- Fully Connected Layers - FLOPs = 2x Input Size x Output Size

**Keeping these equations in mind lets look at the example below**
First Convolution - 2x5x(3x3)x26x26 = 60,840 FLOPs
Second Convolution -2x5x(3x3x5)x24x24 = 259,200 FLOPs
First FC Layer - 2x(24x24x5)x128 = 737,280 FLOPs
Second FC Layer - 2x128x10 = 2,560 FLOPs

**So after looking at the above equation and example** we conclude that to optimize our model (reduce FLOPs):
- Reduce the model size
- Reduce the number of operations with: 1)Pooling (2)Separable Convolutions (3)Model Pruning

Note: A more detailed FLOPs and MACCs tabel of each layer can be viewed inside flops_calculator notebook

###  Results: 
Training was stopped at epoch **20** by early stopping to avoid overfitting

We got the following learning curves

![acuraccy](https://user-images.githubusercontent.com/61900536/212305010-dae0f925-1411-491b-8619-c4d5324f7626.png)

![loss](https://user-images.githubusercontent.com/61900536/212305034-3e60fc4d-9015-4b04-bb86-d93656179c3f.png)

With the following results
  ![image](https://user-images.githubusercontent.com/61900536/212306038-1457de1a-1656-4d3d-a66b-43d13bb17d03.png)

And the below confusion matrix

![image](https://user-images.githubusercontent.com/61900536/212306321-d95f333c-87ef-4bd6-8f7a-eba06712957e.png)


The results aren't great, with 39% accuracy and poor precision and recall. And that's why we moved to the second architecture
</details>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
<details>
<summary>Model 2 (using a pretrained network):</summary>

### model architecture
- I used a standard MobileNetV2 and changed the fully connected layers to `Dense(10,activation='softmax')`
- MobileNetV2 full architecture https://arxiv.org/pdf/1801.04381v4.pdf
- And got the following parameters
  ![image](https://user-images.githubusercontent.com/61900536/212308268-e15989ee-af89-4344-a17c-b097f87c3a67.png)

- All the layers weights were freezed during training except for the fc layer added. 

### FLOPs & MACCs:

| Layer name | FLOPs | MACCs |
| ----------- | ----------- | ----------- | 
| **Total**  | **784,609,720** | **392,163,548** |

A detailed explanation of each layers FLOPs can be viewed in the flops_calculator notebook
  
###  Results: 
  
Training was stopped at epoch **4** by early stopping to avoid overfitting

We got the following learning curves

![acc2](https://user-images.githubusercontent.com/61900536/212313398-03dd6ef0-b565-4783-b92f-b04a71ccdf5b.png)

![loss2](https://user-images.githubusercontent.com/61900536/212313410-2da59b16-208c-4183-bdd4-c55ab2d8153d.png)


With the following results

  ![image](https://user-images.githubusercontent.com/61900536/212313479-63416ca3-9d82-4832-bee2-ba4007cb9775.png)

And the below confusion matrix

![image](https://user-images.githubusercontent.com/61900536/212313536-637688a8-6348-43b3-a0c9-8ca29464f6d4.png)

 This model was able to achieve **85%** accuracy on the testing data. And better results generalizing.
</details>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

<details>
<summary>Conclusion</summary>

 So in coclusion the second model (pretrained mobilenetv2) achieved much better result on our clothing dataset. Indeed it had alot more FLOPs, however this can be tolerated with respect to the huge difference in the model performance.
</details>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

<details>
<summary>Future Work</summary>


If there was a small tolerance for the model size one can even give transformers a shot, since they seem to do quite well in CV tasks since the publishing of the ViT model.

However in our task we could try MobileVit found at: https://huggingface.co/docs/transformers/model_doc/mobilevit


</details>





