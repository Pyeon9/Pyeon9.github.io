---
layout: post
title: "[Azure] MS Azure Studio 튜토리얼"
description: >
   Microsoft Azure의 AutoML을 개발할 수 있는 환경인 Studio 활용 튜토리얼
---

[이전](https://pyeon9.github.io/blog/artificial-intelligence/2021-04-17-MS-Azure-AutoML-tutorial-(1)/)에는 웹 기반 콘솔인 Azure Portal에서 실습을 진행했다. Azure는 Portal 이외에도 Machine Learning Studio라는 환경도 지원하는데, 각 작업이 block diagram으로 되어 있어 GUI 측면에서 사용하기 용이하며 보다 직관적이다. Studio 환경에서 MNIST 이미지 분류 실습을 진행해본다.

## 환경 생성
[링크](https://studio.azureml.net/)를 눌러 Microsoft Machine Learning Studio (classic) 사이트로 이동하고, workspace를 생성한다. 생성한 workspace를 누르면 Studio 환경으로 이동한다. 

![01-workspace](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/01-workspace.png?raw=true)

왼쪽 하단의 **+** 버튼을 누른 후, Blank Experiment를 선택하여 Experiment 환경을 생성한다.

![02-create-new-experiment](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/02-new-experiment.png?raw=true)

![03-experiment-created](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/03-experiment-created.png?raw=true)


## 실험 설계

이제 좌측의 메뉴들을 적절히 활용하여 실험을 진행할 수 있다. 좌측 메뉴 중 **Saved Datasets** > **Samples** 에 Azure에서 기본적으로 제공하는 데이터셋이 존재하므로, 해당 메뉴를 클릭하여 MNIST 데이터 셋을 찾는다. Train 데이터와 Test 데이터가 별도로 있는데, 우선 Train 데이터를 drag & drop한다. 28x28 크기의 이미지가 60,000장(60k) 포함되어 있다.

![04-mnist-train-data](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/04-mnist-train-data.png?raw=true) 

다음으로 실제로 훈련할 데이터와 검증(validation) 데이터를 나눈다. 마찬가지로 좌측 메뉴에서 **Data Transformation** > **Sample and Split** > **Split Data**를 선택하여 앞서 선택한 데이터 아래로 drag & drop한다.

![05-split-data](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/05-split-data.png?raw=true)

각 block diagram의 상단은 입력, 하단은 출력을 의미하는데, Split Data는 하단에 두 개의 출력이 존재한다. 우측 Properties에서 분할 비율을 설정할 수 있고, 두 개로 분할된 데이터가 각각 좌우측 port를 통해 연결된다.

diagram에 빨간색 느낌표가 떠있는데, 마우스를 가져다 대 확인해보면 "Input port Dataset is unconnected"라는 메세지가 나타난다. 말그대로 Split할 데이터셋이 존재하지 않아 발생하는 메세지로, 해결하지 않으면 후에 동작하지 않는다. 아래 그림과 같이 MNIST Train 데이터 아래의 원을 클릭한 후 Split Data의 위에 존재하는 원에 이어준다. 

![06-connect](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/06-connect.png?raw=true)

이제 빨간색 느낌표가 사라졌다. 다음으로는 데이터를 학습할 모델을 선택해야 한다. 좌측 메뉴의 **Machine Learning** > **Train** > **Train Model**을 선택하여 추가한다. Train Model diagram은 상단에 입력 port가 두 개 존재하는데, 마우스를 가져다 대어 확인해보면 왼쪽 port는 학습되지 않은 모델, 우측 port는 데이터셋이라고 나타난다. 데이터셋은 준비했으므로 실제 학습할 모델을 추가해주어야 한다. 

![07-train-model](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/07-train-model.png?raw=true)

**Machine Learning**의 **Initialize Model** 메뉴에는 Anomaly Detection, Classification 등이 있는데, MNIST 이미지는 분류 문제이므로 **Classification**을 선택한다. 분류 모델도 여러가지가 있는데, MNIST 데이터셋은 라벨이 총 10개이므로, **Multiclass Neural Network**를 사용하면 된다. 마찬가지로 추가해준 후, Train Model diagram의 좌측 상단에 연결해준다. 원할 시 우측 Properties 메뉴에서 hidden node의 수, learning rate등의 hyperparameter를 변경 가능하다.
추가로 앞서 준비한 Split Data의 좌측하단 port를 Train model의 우측 상단에 연결해준다. 

![08-model-ready](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/08-model-ready.png?raw=true)

Train Model에 또 빨간색 느낌표가 나타났는데, 메세지를 확인해보면 "Value required"라고 나타난다. 이는 모델이 학습할 때 정답으로 사용할 값을 지정해주지 않아서 나타난다. diagram 선택 후 우측 Properties를 보면 Label column이라는 항목이 있다. 아래의 Launch column selector를 선택한 뒤 "Label"을 입력하여 선택하고 하단의 체크 표시를 누른다. 

![09-label](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/09-label.png?raw=true)

모델의 학습 준비는 모두 완료되었다. 이제 학습된 모델의 성능을 측정하는 것을 추가해보겠다. 좌측 **Machine Learnig** 메뉴의 **Score** > **Score Model**을 선택하여 아래에 추가한다. 입력 port가 2개 존재하는데, 각각 학습된 모델과 데이터셋을 받는다.(마우스를 갖다 대어 확인 가능하다.) Train Model의 출력과, Split Data의 남은 출력 port를 각각 연결해준다.

![10-score](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/10-score.png?raw=true)

추가로, 좌측 **Machine Learnig** 메뉴의 **Evaluate** > **Evaluate Model**을 선택하여 아래에 추가한다. 좌측 상단에는 Score Model의 출력 port, 우측 상단에는 Test 데이터의 출력 port를 각각 연결해준다.

![11-evaluate](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/11-evaluate.png?raw=true)


## 실행

이제 모든 작업이 완료되어 실제로 실행해보고 성능을 확인해야 한다. 중앙 하단의 RUN 버튼을 누르면 위에 있는 작업부터 순차적으로 진행되며, 수 분 이내로 전체 작업이 완료된다.

작업이 완료된 후 Evaluate Model를 우클릭한 후 Evaluation results > Visualize를 선택하면 모델 평가 결과를 시각화하여 볼 수 있다. 우선 상단에 정확도와 recall-precision이 수치로 나와 있다. 

![12-accuarcy](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/12-accuracy.png?raw=true)

또한, 분류 문제이기 때문에 각 클래스에 대한 오차 행렬도 제공하고 있다.

![13-confusion](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-08/08-10-ms-azure-studio-mnist-tutorial/13-confusion.png?raw=true)

간단한 모델을 사용했지만 모든 클래스를 고루 맞추며 상당히 높은 성능을 보이는 것을 확인할 수 있다.


<br/>

Azure의 Studio 환경을 사용하여 간단한 MNIST 이미지 분류 실습을 진행했다. Web Portal에서 하는 것보다 GUI가 깔금하고 메뉴들이 한데 모여 있어 필요한 항목들을 찾아서 바로 적용하기가 더 편했던 것 같다. 각 모듈들을 선으로 연결하는 것도 매우 간단했는데, 약간 더 복잡한 작업을 할때는 헷갈리거나 오류가 나는 경우도 있을 것 같아 잘 읽어보고 할 필요가 있을 것 같다.

<!-- Last modified: 21-08-10, 09:21 -->