# CNN
CNN은 합성곱 계층 (convolutional layer)과 풀링 계층 (pooling layer)이라고 하는 새로운 층을 fully-connected 계층 이전에 추가함으로써 원본 이미지에 필터링 기법을 적용한 뒤에 필터링된 이미지에 대해 분류 연산이 수행되도록 구성된다.

합성곱 계층은 이미지에 필터링 기법을 적용하고, 풀링 계층은 이미지의 국소적인 부분들을 하나의 대표적인 스칼라 값으로 변환함으로써 이미지의 크기를 줄이는 등의 다양한 기능들을 수행한다.

### Image classification
이미지 분류란 입력 이미지에 대해 미리 정해진 라벨을 부여해줌으로 입력 이미지가 어떤 카테고리에 들어 갈 것인가를 판단하는 방법이다.

### Image classification pipeline
- 보편적으로 Input(입력), Learning(학습), Evaluation(평가)의 세 단계를 통해 이미지 분류를 수행한다
  - Input: N개의 image, 각 image는 k개의 class로 라벨화 되어있다.
  - Learning: 특정 방법을 통해 Input image를 학습 시킨다.
  - Evaluation: Imput image가 아닌 새로운 image에 대해 얼마나 잘 예측 할 수 있는지 평가한다.

위의 파이프라인을 염두해 두고 Neural Network의 구조를 보면, Input layer, Hidden Layer, Output Layer로 구성 되어있는 Neural Network의 각 Layer에서 수행하는 작업을 조금 더 쉽게 이해할 수 있다.

## Convolution layer
Convolution(합성곱)이라고 하는 것은 3x3 행렬 두 개를 곱할 때 (0,0)부터 (2,2)까지 각 원소끼리 곱하여 나온 값을 더하는 행렬 연산 방법이다.

추가로 5x5 행렬에 3x3 필터를 합성곱할 수도 있으며 이것은 아래 그림과 같이 최종 결과가 2x2행렬로 표현된다. 보통 이를 막기 위해 zero padding 등의 기법을 사용하여 Input image의 크기를 키운다.

하나의 합성곱 계층에는 입력되는 이미지의 채널 개수만큼 필터가 존재하며, 각 채널에 할당된 필터를 적용함으로써 합성곱 계층의 출력 이미지가 생성된다.

![Alt text](/img/conv.jpg)

위의 그림이 convolution을 설명하는 그림이다. 주변의 회색 0은 이미지 사이즈의 감소를 막기 위한 zero padding이며, 이미지의 각 원소(ex. (1,0))와 Filter의 각 원소끼리 곱한 것의 합을 output으로 나타낸다.

(해당 그림에서 zero padding을 했음에도 이미지 사이즈가 줄어드는 것(5x5 -> 3x3)은 convolution을 2칸씩 건너뛰며 수행했기 때문이다. 즉, stride(보폭)가 2인 경우)

## Activation Function
CNN에서는 주로 ReLu라는 함수를 활성함수로 사용하며 이는 Convolution 이후의 값들을 활성시키기 위해 사용한다. 활성화 된다는 것은 0 이상의 값들만 유효한 값(ReLu 함수에 한해서)으로 사용 될 수 있도록 해준다. 

활성함수의 예로는 sigmoid function, ReLu, ReLu6 등이 존재한다.

![Alt text](/img/ReLu.jpg)

위의 그림이 ReLU 함수이다.

## Pooling layer
풀링 계층은 주로 max-pooling을 기반으로 구현되며 이미지의 특성상 인접 픽셀들간의 유사도가 매우 높아 국소 영역 중 가장 큰 값을 대표값으로 설정하는 Layer이다.

Pooling Layer를 사용하는 이유는 이미지가 옆으로 이동하는 등의 변경 사항에도 올바르게 작동하도록 도와준다.

또한 이미지의 크기를 줄여주어 계산량을 줄여주는 효과를 얻을 수 있다.

## Fully Connected layer
CNN에서 Fully connect layer를 적용하게 되면 3차원(높이x넓이x색상) 행렬을 1차원 행렬로 변경하게 되면서 이미지의 특성을 잃게 된다. 

쉽게 말해 그림을 위에서부터 미분하듯이 쪼개어 한 줄로 이어붙이게 되면 입술 등의 이미지의 공간적 특징을 잃게 되는 것이다. 그렇기에 CNN에서는 마지막에 어느 클래스와 유사한지 판단할 때만 Fully connected layer를 사용한다.

# Example
## AlexNet
위의 Layer에 대한 이해를 바탕으로 AlexNet을 설명하고자 한다.

- 1번 Layer(Conv): 96개의 11 x 11 x 3 사이즈 필터커널로 입력 영상을 컨볼루션해준다. 컨볼루션 보폭(stride)를 4로 설정했고, zero-padding은 사용하지 않았다. zero-padding은 컨볼루션으로 인해 특성맵의 사이즈가 축소되는 것을 방지하기 위해, 또는 축소되는 정도를 줄이기 위해 영상의 가장자리 부분에 0을 추가하는 것이다. 결과적으로 55 x 55 x 96 특성맵(96장의 55 x 55 사이즈 특성맵들)이 산출된다. 그 다음에 ReLU 함수로 활성화해준다. 이어서 3 x 3 overlapping max pooling이 stride 2로 시행된다. 그 결과 27 x 27 x 96 특성맵을 갖게 된다. 그 다음에는 수렴 속도를 높이기 위해 local response normalization이 시행된다. local response normalization은 특성맵의 차원을 변화시키지 않으므로, 특성맵의 크기는 27 x 27 x 96으로 유지된다. 

- 2번 Layer(Conv): 256개의 5 x 5 x 48 커널을 사용하여 전 단계의 특성맵을 컨볼루션해준다. stride는 1로, zero-padding은 2로 설정했다. 따라서 27 x 27 x 256 특성맵(256장의 27 x 27 사이즈 특성맵들)을 얻게 된다. 역시 ReLU 함수로 활성화한다. 그 다음에 3 x 3 overlapping max pooling을 stride 2로 시행한다. 그 결과 13 x 13 x 256 특성맵을 얻게 된다. 그 후 local response normalization이 시행되고, 특성맵의 크기는 13 x 13 x 256으로 그대로 유지된다. 

- 3번 Layer(Conv): 384개의 3 x 3 x 256 커널을 사용하여 전 단계의 특성맵을 컨볼루션해준다. stride와 zero-padding 모두 1로 설정한다. 따라서 13 x 13 x 384 특성맵(384장의 13 x 13 사이즈 특성맵들)을 얻게 된다. 역시 ReLU 함수로 활성화한다.

- 4번 Layer(Conv): 384개의 3 x 3 x 192 커널을 사용해서 전 단계의 특성맵을 컨볼루션해준다. stride와 zero-padding 모두 1로 설정한다. 따라서 13 x 13 x 384 특성맵(384장의 13 x 13 사이즈 특성맵들)을 얻게 된다. 역시 ReLU 함수로 활성화한다. 

- 5번 Layer(Conv): 256개의 3 x 3 x 192 커널을 사용해서 전 단계의 특성맵을 컨볼루션해준다. stride와 zero-padding 모두 1로 설정한다. 따라서 13 x 13 x 256 특성맵(256장의 13 x 13 사이즈 특성맵들)을 얻게 된다. 역시 ReLU 함수로 활성화한다. 그 다음에 3 x 3 overlapping max pooling을 stride 2로 시행한다. 그 결과 6 x 6 x 256 특성맵을 얻게 된다. 

- 6번 Layer(FC): 4096개의 6 x 6 x 256 커널을 사용해서 전 단계의 특성맵을 컨볼루션해준다. 결과적으로 1 x 1 x 4096 특성맵(4096장의 1 x 1 사이즈 특성맵들)을 얻게 된다. 그리고 ReLU 함수로 활성화한다. 결과적으로 4096개의 성분(뉴런)으로 구성된 벡터가 만들어진다. 

- 7번 Layer(FC): 4096개의 뉴런으로 구성되어 있다. 전 단계의 4096개 뉴런과 촘촘히 연결되어 있다. 출력 값은 ReLU 함수로 활성화된다. 

- 8번 Layer(FC): 1000개의 뉴런으로 구성되어 있다. 전 단계의 4096개 뉴런과 촘촘히 연결되어 있다. 1000개 뉴런의 출력값에 softmax 함수를 적용해 1000개 클래스 각각에 속할 확률을 나타낸다. 

위의 예시를 통해 알 수 있듯이 Convolution Layer에서는 Convolution, ReLu, max pooling 등이 적용됨을 알 수 있고, 마지막 3개의 FC Layer를 통해 결과적으로 어느 클래스와 가장 유사한지를 판단한다.

cf. Convolution과 Pooling Layer를 별개의 Layer으로 보기도 한다.

![Alt text](/img/AlexNet.jpg)

위의 그림은 위에서 설명한 AlexNet의 구성을 나타내는 그림이다.

[출처](https://bskyvision.com/421) 

## VGG-16
VGGNet은 옥스포드 대학의 연구팀 VGG에 의해 개발된 모델로써, 2014년 이미지넷 이미지 인식 대회에서 준우승을 한 모델이다. 여기서 말하는 VGGNet은 16개 또는 19개의 층으로 구성된 모델을 의미한다(VGG16, VGG19로 불림).

![Alt text](/img/vgg16_1.jpg)
![Alt text](/img/vgg16_2.jpg)

위의 그림은 VGG-16의 전체 구성이다.

이와 같이 conv, pooling, FC Layer 등으로 구성된 다양한 종류의 CNN이 존재함을 알 수 있다.

# numpy
고차원의 수 계산과 과학 연산을 위해 만들어진 파이썬 라이브러리로써, 벡터 및 행렬계산에 있어 편리한 기능(함수)를 제공한다.

주로 array(행렬)단위로 데이터를 생성 및 색인, 처리, 연산 등의 기능을 수행하는데 사용된다. 파이썬으로 수치해석, 통계와 같은 기능을 구현하려고 할때 가장 기본이 되는 라이브러리이기 때문에 이에 대한 기초를 쌓아두는것이 중요하다.

# openCV
실시간 컴퓨터 비전(이미지에서 정보 추출하는 작업)과 머신러닝을 목적으로 한 라이브러리로, 이미지 프로세싱에 중점을 두었다.

얼굴을 인식하고, 물체를 식별하고 이미지를 결합하는 등의 작업이 가능하며, 이러한 기능을 바탕으로 객체ㆍ얼굴ㆍ행동 인식, 독순(입술이 움직이는 모양을 보고 상대편이 하는 말을 알아내는 방법), 모션 추적 등의 응용 프로그램에서 사용한다.