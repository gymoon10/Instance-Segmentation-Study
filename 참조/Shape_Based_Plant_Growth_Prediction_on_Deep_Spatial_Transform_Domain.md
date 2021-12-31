# Shape Based Plant Growth Prediction on Deep Spatial Transform Domain

<br/>

## Abstract

**1. Shape estimation**

현재 (t), 과거 시점 (t-3, t-2, t-1)의 이미지들을 활용하여 t+1 시점의 식물 생장 이미지를 예측 (식물 형상에 초점을 맞춰서)

네트워크의 입력으로 제공되는 image sequences에 STN을 적용시키고, 그 출력인 affine transform 파라미터들을 활용하여 식물의 growth behavior를 예측

  - 모든 연속적인 이미지들에 대한 affine transform 파라미터들을 결합하여 t+1 시점의 식물 형태를 예측

**2. RGB reconstruction**

식물 이미지를 다수의 patch (부분 이미지)로 분할한 후 계층적 auto-encoder의 입력으로 제공하여 식물의 local, global growth를 예측

<br/>

## 1 Introduction

기존 연구들은 auto-encoder, ConvLSTM을 활용. 식물 잎사귀의 전반적인 모양은 식물의 생장을 정량적으로 측정하는데 적합함.

본 연구는 크게 다음과 같이 구성됨

1. shape prediction 
  - STN을 활용하여 식물 잎사귀들 간의 spatial transfrom을 포착  
  - 인접한 식물 잎사귀 이미지들간의 growth behavior를 affine transform을 통해 모델링
  - affine transform 파라미터들은 식물의 생장을 정량적으로 표현함 (`The shape prediction is made on transform parameter domain.`)

![image](https://user-images.githubusercontent.com/44194558/147724764-71c79e0b-1ee4-440c-b702-6a0120b85fbc.png)

![image](https://user-images.githubusercontent.com/44194558/147725016-e6b6130b-1a5f-4e2b-96e9-43353ec20dcc.png)

식물 생장의 경우 각 잎사귀의 생장 속도, 크기가 다르기 때문에 보다 local한 feature들을 포착할 필요가 있음

2. RGB reconstruction
  - 이미지를 patch로 분할
  - 계층적 auto-encoder를 활용하여 local growth 예측


<br/>

## 2. Related works

Spatial invariance에 강인하지 못한 CNN의 한계를 극복하기 위해 STN이 제안됨. (`The spatial transformer network (STN) [15] was proposed to learn invariance to spatial changes such as translation, scale, and rotation.`)

이와 같은 장점으로 인해 motion estimation(future flow), future frame prediction에 널리 활용되어 왔음.  
 - 연속적인 affine transform 파라미터들의 결합을 통해 예측 시점 frame에 대한 affine transform 학습 
 - 본 연구와 유사한 점이 있음

<br/>

## 3. The proposed method

### A. STN

![image](https://user-images.githubusercontent.com/44194558/147725179-bfda62c7-11d9-4081-82c2-b453c157bbd0.png)

<br/>

### B. Proposed Network Architecture

`The shape difference between adjacent plant images corresponds to the amount of growth, and can be described by the spatial transform parameters (rotation, scaling and translation) when the growth is modeled by the STN.` 

`The amount of growth is quantiﬁed by a set of afﬁne transform parameters.` 

 - i, i+1 시점 간의 affine transform parameter theta_i가 i에서 i+1시점으로의 생장을 정량적으로 모델링


본 연구는 다음 태스크들의 영향을 받음

1. motion estimation

 - 식물의 생장이라는 motion은 일반적인 영상들의 motion과는 차이점이 있음
 - 식물 잎사귀들은 texture, 모양, 색 측면에서 유사하기 때문에 motion vector를 찾기 어려움, 유용한 feature point 적음 -> STN을 사용하게된 계기

2. video frame generation

  - sequential change

인접한 이미지들 끼리 affien transform 파라미터들을 출력하고, 이를 결합하여 t+1시점으로의 spatial transform 파라미터를 추정하게 됨. 학습된 파라미터는 현재 t 시점의 이미지에 (RGB, grayscale shape) 적용됨.

![image](https://user-images.githubusercontent.com/44194558/147725725-d0e0031c-9f49-41a8-a7e4-146a82fc2a89.png)

STN 만으로 식물 생장을 정확히 예측하기에는 한계가 있음. STN을 활용한 shape estimation subnet은 식물의 global growth를 잘 포착하지만 서로 다른 크기의 잎사귀들의 다양한 생장을 구체적으로 포착하지 못함.

위와 같은 문제로 인해 STN 모듈 적용 이후 RGB reconstruction 수행. 식물 foreground 이미지에 transform 적용. (학습된 affine transform 파라미터를 활용)
Transform이 적용된 이미지는 4개의 patch로 분할되어 encoder의 입력으로 제공되고, encoding 이후에 4개의 feature map 출력을 concat하여 2개로 합침. 합쳐진 feature map은 다음 layer에 추가적인 입력으로 제공됨 (hierarchcal)

![image](https://user-images.githubusercontent.com/44194558/147726044-7bb06758-62b9-427e-8c35-b9f48480109a.png)

참고 : https://github.com/gymoon10/Plant-Growth-Prediction/blob/main/Related%20works/Network_Proposal_Hierarchical.ipynb







