## Shape Based Deep Estimation of Future Plant Images

<br/>

## Abstract

2개의 subnet shape estimation, color reconstruction으로 구성되며 4 시점 (t, t-1, t-2, t-3)의 그레이 스케일 이미지를 입력으로 함.
그레이스케일 이미지들은 STN을 거쳐 전처리되는 과정을 거침. (`Four gray time-series images are first aligned to a future target using a spatial transformer network (STN) for shape estimation.`)

4개의 입력들은 U-Net, 2개의 LSTM을 통해 결합되어 식물 형상에 대한 예측 그레이스케일 이미지를 생성하게 됨.

Color reconstruction에서는 위의 과정을 통해 예측된 식물 형상과 RGB 식물 이미지를 결합하여 색상 정보를 복원.

<br/>

## 1. Introduction

![image](https://user-images.githubusercontent.com/44194558/147727091-b49f2364-120b-4feb-ad08-9224b26757ff.png)

식물의 형상 예측은 gray domain에서 이루어짐. (`This reduces the interference of the texture and background in the plant image during the shape prediction`)
 - 개별 입력 그레이스케일 이미지는 STN을 통해 align되는 전처리 과정을 거침

1. 4개의 align된 그레이스케일 이미지는 U-Net과 LSTM을 거쳐 예측 형상 이미지를 생성. 
   
   - 형상 예측에 불필요한 텍스쳐, 색상, 배경 정보를 배제하기 위해 그레이 스케일 활용
   - 모든 입력은 네트워크에 입력으로 제공되기 전 STN을 통한 전처리 거침 (`geometically transformed into the future target shape`)

![image](https://user-images.githubusercontent.com/44194558/147727334-8f2928c6-e63c-4e3e-8b22-0f4a2aede639.png)

2. 1의 예측 이미지는 텍스쳐, 색상 측면에서 불충분하기 때문에 color subnet(auto-encoder)를 통해 복원.
   - RGB prediction

<br/>

## 2. Related works

### A. STN based video prediction

네트워크는 frame, flow 2개의 branch로 분류됨. 2개 branch의 출력들을 결합하여 최종 예측을 수행함. 

STN 없이 작업을 수행하는 경우에도 motion, content가 각각 encoder를 거친 후 결합되는 과정을 거침.


### B. Plant image prediction

개별 입력에 대응되는 다수의 auto-encoder를 활용한 후 ConvLSTM계층에서 결합됨. (hierarchically fused)

<br/>

## 3. The proposed method

1. STN을 통해 affine transform 적용 
   - 색상, 배경 등은 불필요한 신호이기 때문에 사전에 잎 부분에 대한 segmentation 적용

   ![image](https://user-images.githubusercontent.com/44194558/147729184-1f0927d6-268d-4029-a1b4-0fea9728d33f.png)

![image](https://user-images.githubusercontent.com/44194558/147728639-c59bacee-4e6f-446a-8721-db1d248d69f0.png)

2. STN을 통해 align된 이미지들을 shape subnet의 입력으로 제공하여 형상을 예측

3. color reconstruction에서는
   - Auto-encoder는 2의 예측 결과와 affine transformed RGB 이미지를 결합 (hierarchically fused)

### Shape estimation

U-Net with 2 LSTMs

![image](https://user-images.githubusercontent.com/44194558/147728917-dd23eac0-74e3-41de-bc4e-5ed5ef534e3c.png)

- 2개의 hierarchical layer에서 sequential combination이 이루어짐

### Color reconstruction

![image](https://user-images.githubusercontent.com/44194558/147729144-61dab483-1a0c-46d7-9dfd-9a878a26397b.png)
   