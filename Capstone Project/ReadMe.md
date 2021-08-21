# Capstone Project - Utilizing DETR and Panoptic Segementation:

## Questions:

As a part of the CAPSTONE project you need to explain missing:
We take the encoded image (dxH/32xW/32) and send it to Multi-Head Attention (FROM WHERE DO WE TAKE THIS ENCODED IMAGE?)
We also send dxN Box embeddings to the Multi-Head Attention
We do something here to generate NxMxH/32xW/32 maps. (WHAT DO WE DO HERE?)
Then we concatenate these maps with Res5 Block (WHERE IS THIS COMING FROM?)

Then we perform the above steps (EXPLAIN THESE STEPS)
And then we are finally left with the panoptic segmentation

![Process of Used Segmentation Model](https://user-images.githubusercontent.com/50147394/130328616-0c7c424e-7a68-45a0-88db-5b310f790b8e.png)

## Q1.Encoded Image:

The encoded image of dxH/32xW/32 is the output of the DETR Transformer Encoder process, and how it's is arrived is explained below.

## Q2.Attention Maps:

We take the output of transformer decoder for each object and use it to produce multi-head (with M heads) attention scores of this
embedding over the output of the encoder which is the encoded image of dxH/32xW/32, generating M attention heatmaps
per object.

## Q3.RESNET Layer:

the output attention maps of the previous layer passed through RESNET layers to make the final prediction and for scaling which is a Feature Pyramid Network type architecture to get the output mask logits,the final resolution of the masks has a stride of 4 and each mask is supervised independently using the DICE/F-1 loss and Focal loss.

## Q4.End to end process of the DETR Panoptic segmentation model:

1. From the input image of(dxHxW) goes through the RESNET Backbone and then the output gets flattened and along with positional encodings it passes through the DETR Transformer encoder model where it goes through the self attention and feed forward network to get output.

2. Then the output of the encoder processed to create Key and Values for the decoder model, the learnt postional embeddings which act as object queries which goes through the masked multihead attention layer of the DETR Decoder model which is the first attention layer in the decoder model which is the self attention one which helps improving the queries.
Then it goes through next attention which is the guided attention layer along with the key and values from encoder layer to generate the box embeddings for each of the classes/objects separately.

3. We take the output of transformer decoder for each object and use it to produce multi-head (with M heads) attention scores of this
embedding over the output of the encoder which is the encoded image of dxH/32xW/32, generating M attention heatmaps
per object.

4. the output attention maps of the previous layer passed through a set of RESNET layers to make the final prediction and for scaling which is a Feature Pyramid Network type architecture to get the output mask logits,the final resolution of the masks has a stride of 4 and each mask is supervised independently using the DICE/F-1 loss and Focal loss.

5. Final panoptic segmentation is achieved by using an argmax over the mask logits at pixel level and the corresponding category is assigned to each of the masks.

## My Approach to the problem:

Below are the steps which I've planned to follow to solve this problem. I might improvise on it going forward.

1. Get the dataset for the problem which will have both stuff and things. Things are from the images we annotated and stuff from default clasess.
2. Convert the dataset to COCO like dataset as DETR was trained on COCO.
3. Train the dataset to predict BBoxes to predict for BBoxes for both stuff and things.
4. On the output BBoxes will apply a Masking Head.
5. Design the RESNET layer and Freeze the weights of BBoxes Prediction.
6. The predicted BBoxes will be passed through Multi Head attention model to get the attention maps which then will be passed to the RESNET layer to generate the scaled segmentation.
7. The scaled maps are then concateneted to generate the Panoptic segmented images.

## References:

https://www.ecva.net/papers/eccv_2020/papers_ECCV/papers/123460205.pdf
https://huggingface.co/transformers/model_doc/detr.html
https://wandb.ai/veri/detr/reports/DETR-Panoptic-segmentation-on-Cityscapes-dataset--Vmlldzo2ODg3NjE
https://github.com/facebookresearch/detr/blob/master/models/segmentation.py

## Contributer:

Saroj Raj Das
(sarojrajdas@gmail.com)

