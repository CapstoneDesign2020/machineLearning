# 🤖 Soundee Machine Learning 🤖

**소리를 보다 Soundee**

청각 장애인을 위한 딥러닝을 이용한 일상생활 소리 분류 및 알림 애플리케이션
> 💻 2020 IT Media Engineering CapstoneDesign Project

### 기획의도

사물 인터넷이 활성화되어 생활에 많은 부분들이 편리해졌지만, 청각장애인은 이 편리함에 소외된 것이 현실이다. 
소리를 듣지 못하는 농인들은 냉장고를 오래 열어놨을 시 발생하는 경보음, 잊어버리고 끄지 못한 드라이기, 물소리 등의 소리를 인지하지 못한다. 농인에게도 이를 감지하고 인식할 수 있는 시스템이 필요하다.

이를 해결하기 위해 본 프로젝트는 가정에서 발생하는 소리를 인지하여 분류하여 현재 발생한 소리에 대한 정보를 알려주며 소리 정보에 대한 통계 서비스를 제공하는 애플리케이션을 기획하였다. 이를 통해 농인이 인지하지 못하는 위험과 낭비를 미리 예방하여, 농인에게 좀 더 안전하고 편리한 생활을 제공하고자 한다.

### 작품소개

무지향성 마이크를 통해 일상 소리를 감지한다. PC에 연결한 마이크로 수집한 음향 데이터를 서버에 송신하고, 서버는 수신한 소리를 딥러닝을 기반으로 한 음향 예측 알고리즘을 통해 예측 결과를 안드로이드 요청에 따라 기기에 송신한다. 안드로이드 기기는 수신한 데이터를 푸시알림과 통계 등 다양한 방법으로 제공하여 청각장애인을 위한 서비스를 제공한다.

본 프로젝트로 사회가 생각해보지 못한 청각장애인의 일상생활의 불편함에 대한 문제를 제기하여 이에 대한 관심과 해결방안의 마련을 도모한다. 더불어 앞으로의 4차 산업 기술의 발전이 비장애인의 편리함뿐만이 아닌, 장애인의 접근성을 고려해야한다는 사회적 인식을 확산시키는데 도움이 될 것이라 예상한다.

## Architecture
<img src="https://user-images.githubusercontent.com/45596615/97182829-5d475080-17e0-11eb-9d16-614e783db7d9.png" width="500" />

* * *
프로세스 : 전처리(Librosa) → feature 추출 → 2D 이미지화 → CNN Resnet → endpoint 생성

class 종류 :
- 0 = baby (아기 울음 소리)
- 1 = siren (사이렌 소리)
- 2 = motor (드라이기, 청소기 등 모터 소리)
- 3 = water (물 떨어지는 소리)

무지향성 마이크로 일상 소리 감지 후 음향 데이터에서 Mel-Spectogram 특징 벡터를 추출하여 Convolutional Neural Network (CNN) 모델의 Resnet 알고리즘을 진행한다.
서버에서 소리에 대한 분석을 진행한 후 그 결과를 안드로이드에서 실시간으로 5 초마다 확인하여 사용자에게 알림 서비스를 제공한다. 이를 통해 낭비를 줄이고 위험에 대처할 수 있게 한다. 청각 장애인의 소리에 대한 접근성을 다양한 측면으로 고려해 야 한다는 사회적 인식을 확산시키고자 한다.

#### 전처리
* 어느 정도 정제 되어있는 Kaggle 데이터를 기준으로, 음향 데이터 길이를 5 초로 정하고 그 이상이면 잘라주는 방식으로 정규화 진행
#### feature 추출 및 2D 이미지화
* 참고한 [논문](http://www.kibme.org/resources/journal/20181212101228163.pdf)에 따라 1 단계로 mel-spectrogram, 2 단계 filter banks 로 하여 특징 벡터를 추출함
* 녹음한 16kHz 의 입력 오디오 데이 터를 40ms 윈도우 단위로 0.01초 마다 frequency를 뽑은 melspectogram을 추출하였으며 이에 대하여 mel값을 얻어내기 위한 filter bank를 적용하여 2 차원 입력 이미지 데이터를 구성
* 만들어진 이미지
  
  water class

  ![data2-water-4062](https://user-images.githubusercontent.com/45596615/97175039-3b48d080-17d6-11eb-9ab5-fbad7f63946e.png)

#### CNN 모델 학습
* Resnet 구조 50개의 layer로 학습 진행
* 파라메터 구성은 다음과 같다
  ```
  num_layers = 50
  image_shape = '3,256,256'
  num_training_samples = 7550
  num_classes = 4
  epochs = 20
  top_k = '2'
  train_instance_type='ml.p3.2xlarge'
  ```
