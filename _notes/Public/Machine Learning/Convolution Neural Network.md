---
title: Convolution Neural Network
feed: hide
---

- 출처 : [[딥러닝/머신러닝] CNN(Convolutional Neural Networks) 쉽게 이해하기](https://halfundecided.medium.com/%EB%94%A5%EB%9F%AC%EB%8B%9D-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-cnn-convolutional-neural-networks-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-836869f88375)

## What is CNN
- 딥러닝에서 주로 이미지나 영상 데이터를 처리할 때 사용되는 기법이다.
	- Convolution이라는 전처리가 들어간다.
- 왜 컨볼루션을 사용하는가?
	- 일반적인 DNN은 1차원 형태의 데이터를 사용한다.
		- 입력값으로 이미지등을 사용하는 경우, 이를 1차원으로 flatten해야 한다.
		- 한 줄의 데이터로 만들어야 하고, 이 과정에서 이미지의 공간적 / 지역적 정보가 손실된다.
		- 추상화 과정 없이, 바로 연산 과정으로 넘어가 학습시간과 능률의 효율성이 저하된다.
	- CNN은 이미지를 그대로 입력으로 받는다.
		- 공간적 / 지역적 정보를 유지한 채 특성의 계층을 빌드업할 수 있다.
		- 이미지 전체보다는 부분을, 이미지의 한 픽셀과 주변 픽셀들의 연관성을 살리는 것이 포인트이다.
	- 패턴을 파악하기 위해서 전체 이미지를 모두 다 볼 필요는 없다.

## Main Concepts of CNN
### Convolution의 작동 원리
- 우리는 2차원 이미지를 행렬을 통해 표현할 수 있다.
	- CNN에 넣어주는 입력값은 이렇게 표현된 행렬 값이다.
- CNN에는 필터(커널)라는 matrix가 존재한다.
	- 하나의 필터를 이미지 입력값에 전체적으로 훑어준다.
		- 입력값 이미지의 모든 영역에 같은 필터를 반복 적용해, 패턴을 찾아 처리하는 것이 목적이다.
		- matrix의 inner product로 이미지의 각 부분과 필터가 연산 처리 되는지 확인한다.
			- 전체 이미지에 대해 이러한 과정을 수행한다.
	- 입력값이 $d_1 \times d_2$ 이고, 필터가 $k_1 \times k_2$ 일 때, 결과의 차원은 $(d_1 - k_1 + 1) \times (d_2 - k_2 + 1)$ 으로 나온다.

### Zero Padding
- 이미지를 필터처리 할 때, 결과값의 크기가 줄어들 수 있다.
	- 손실되는 부분이 발생한다는 것이다.
	- 이를 해결하기 위해 0으로 구성된 테두리를 $(k_1 - 1)$, $(k_2 - 1)$ 만큼 이미지 가장자리에 적용하여 문제를 해결할 수 있다.

### Stride
- 필터를 얼만큼 움직일 것인가?
	- stride가 1인 걍우 필터를 한 칸씩 움직인다고 할 수 있다.
	- 1이 기본 값이고, 1보다 커질 수 있다.
- stride값이 커질 경우 필터가 이미지를 건너뛰는 칸이 커진다.
	- 결과값 이미지의 크기는 작아진다.
- stride를 적용하면 결과값이 $((d_1 - k_1) / s + 1) \times ((d_2 - k_2) / s + 1)$ 로 표현될 수 있다.

### Order-3 Tensor
- 입력값이 3차원인 경우도 존재한다.
	- 이미지가 컬러로 표횐된다면 한 픽셀에 대해 R,G,B 3가지 채널로 구성될 수 있어야 한다.
	- 이러한 경우 필터를 order-3 tensor로 구성하며, 연산 처리는 동일하게 Inner Product를 사용하면 된다.
		- 결과값의 모양은 동일하게 matrix가 될 것이다.

## CNN의 전체적인 네트워크 구성
- CNN은 기존의 완전 연결 계층 (Fully-Connected Layer)와는 다르게 구성되어 있다.
	- 완전 연결 계층은 이전 계층의 모든 뉴런과 결합되어 있는 Affine 계층으로 구성되어 있다.
	- CNN은 Convolution Layer와 Pooling Layer들을 활성화 함수 앞뒤에 배치하여 만든다.

### Convolution Layer
- 주어진 입력값은 입력된 이미지이다.
	- 이 이미지를 대상으로 여러개의 커널을 사용하여 결과값을 얻는다.
		- 한 개의 입력 이미지 값에 여러개의 필터를 통해 10개의 convolution 결과값을 만들어낸다.
	- 도출된 결과값들에 대해 활성함수를 적용한다.
		- 이렇게 첫번째 convolution layer가 완성된다.
- Convolution Layer는 Convolution 처리와 Activation Function으로 구성된다.

#### Activation Function
- 선형함수인 convolution에 비선형성을 추가하기 위해 사용한다.

### Pooling Layer
- 컨볼루션 과정을 통해 많은 수의 이미지를 생성했다.
	- 한 개의 이미지에서 10개의 이미지 결과값이 도출되면 값이 많아졌다는 것이 문제가 된다.
- Pooling이라는 과정을 통해 각 feature map과 dimentionality를 축소한다.
	- correlation이 낮은 부분을 삭제하여 각 결과값의 크기를 줄이는 과정이다.
	- 결과값의 크기를 반으로 줄여주게 된다.
- Pooling에는 Max Pooling과 Average Pooling이 존재한다.
	- Max Pooling인 경우 가장 큰 값을 고르게 된다.
	- Average Pooling은 평균값으로 사이즈를 결정하게 된다

- Convolution Layer와 Pooling Layer를 2번 반복하게 된다.

### Flatten (Vectorization)
- 이렇게 생긴 텐서들을 일자 형태의 데이터로 펼치게 된다.
	- 이렇게 얻은 데이터는 이미지 자체보단 이미지에서 얻어온 특이 데이터이고, 변형시켜주어도 무관하다.

### Fully-Connected Layers
- 하나 이상의 Fully-Connected Layer를 적용시킨 후, Softmax activation function을 적용한다.
- 이를 통해 최종 결과물을 출력하게 된다.

## Parameter and Hyperparameter
- 모델 파라미터
	- 모델 내부에 있다
	- 데이터로부터 값이 추정될 수 있는 설정 변수이다.
- 하이퍼파라미터
	- 모델 외부에 있다.
	- 데이터로부터 값이 추정될 수 없는 설정변수이다.
- 딥러닝의 기본은 파라미터들을 최적의 값으로 빠르고 정확하게 수렴하는 것을 목표로 한다.
	- 어떻게 모델의 파라미터들을 최적화 시키냐가 중요한 포인트가 된다.
- 모델을 학습시킬 때 좋은 Hyperparameter를 찾는 것이 중요하다.
	- 이를 하이퍼파라미터를 튜닝한다고 한다.