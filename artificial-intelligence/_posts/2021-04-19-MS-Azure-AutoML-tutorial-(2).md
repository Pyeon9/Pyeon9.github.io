---
layout: post
title: "[AutoML] MS Azure AutoML 튜토리얼 (2)"
description: >
   Microsoft Azure에서 제공하는 기계학습 모델 개발 자동화 프로세스인 AutoML의 튜토리얼
---

[이전 글](https://pyeon9.github.io/blog/artificial-intelligence/2021-04-17-MS-Azure-AutoML-tutorial-(1)/)에서 AutoML을 활용하여 간단한 분류 모델을 학습시켜보았다. 이 글에서는 학습이 완료된 모델을 분석해보겠다.

## 모델 분석

학습이 완료되면 아래와 같이 상태가 `완료됨`으로 바뀌고, 소요된 시간도 알 수 있다. 

![01-learning-done](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/01-learning-done.png?raw=true)

상단의 **모델** 탭으로 이동하면 앞서 메트릭으로 선택했던 AUC 가중치가 큰 순서대로 모델이 표시된다.

![02-models](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/02-models.png?raw=true)

모델의 알고리즘 이름을 선택하면 세부 정보를 확인할 수 있다. 이름을 통해 몇 번째로 시도한 모델인지 알 수 있고, 소요 시간도 표시된다. 학습 시 설정하지 않았던 다른 메트릭에 대한 결과도 제공한다.

![03-specific-info](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/03-specific-info.png?raw=true)

**메트릭** 탭을 선택하면 여러 메트릭에 대한 결과 그래프를 볼 수 있다. 

![04-metrics](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/04-metrics.png?raw=true)

학습 시 설정한 메트릭은 물론이고, **세부 정보** 탭에서 봤던 다른 메트릭에 대한 결과도 확인할 수가 있다. confusion_matrix를 선택하여 살펴보겠다. confusion marix는 우리말로 오차 행렬이라 하는데, 각 정답 클래스에 대해 모델이 어느 클래스로 예측했는지를 나타낸다. 아래를 보면 정답이 'No'인 데이터는 거의 다 올바르게 예측했지만, 정답이 'Yes'인 데이터는 절반을 겨우 넘긴 정도만 올바르게 예측했음을 알 수 있다.

![05-confusion-matrix](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/05-confusion-matrix.png?raw=true)

confusion matrix 이외에도 많은 메트릭을 제공하고 있으니 필요에 따라 활용할 수 있을 것 같다.

다시 **모델** 탭으로 이동하면 설명이 없는 알고리즘들이 많이 있을 것이다. 궁금한 알고리즘에 대하여 직접 모델 설명을 생성하는 방법은 다음과 같다. 

1. 알고리즘을 선택 후 상단의 **모델 설명** 클릭
 
![06-request-explanation](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/06-request-explanation.png?raw=true)  
 
2. 생성했던 컴퓨팅 클러스터 선택 후 **만들기**
 
![07-request-explanation-(2)](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/07-request-explanation-(2).png?raw=true)  
 
**만들기**를 누른 후 **자식 실행** 탭을 눌러보면 설명 생성이 실행되는 상태를 볼 수 있다.

![08-request-explanation-(3)](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/08-request-explanation-(3).png?raw=true)

1~2분 정도면 설명 생성이 완료된다. 그 후에 **설명(미리 보기)** 탭으로 이동하면 설명이 생성된 것을 확인할 수 있다. 설명 ID를 클릭하면 모델 성능, 데이터 세트 탐색기, 기능 중요도 집계, Individual feature importance 탭이 나타난다. 각 탭을 눌러 모델의 성능이나 어떤 feature들이 작업에 중요한 영향을 미쳤는지 확인할 수 있다.

![09-explanation](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/09-explanation.png?raw=true)

## 모델 배포

AutoML 인터페이스를 사용하여 최적 모델을 **웹 서비스**로 배포할 수 있다. 우선 화면 상단의 **실행 1**을 클릭한다. 그러면 아래와 같이 **최적의 모델 요약** 항목이 있을 것이다. 해당 항목에 나타난 알고리즘을 선택하여 모델별 페이지로 이동한다.

![10-best-model](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/10-best-model.png?raw=true) 

왼쪽 상단의 **배포** 버튼을 누른 후 아래와 같이 작성한다. 작성이 완료되면 하단의 **배포** 버튼을 클릭하여 배포를 시작한다.

![11-deploy](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/11-deploy.png?raw=true)

**배포** 버튼을 클릭하면 상단에 녹색으로 성공 메시지가 출력되고, 모델 요약 항목 하단에 배포 상태가 표시된다. 새로고침을 통해서 상태를 업데이트 할 수 있는데, 배포에는 약 20분 정도 소요된다고 한다.

![12-deploying](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/12-deploying.png?raw=true)

웹 서비스에서 모델을 활용하는 방법은 이 글에서 소개할 범위를 넘어, [공식 설명 페이지](https://docs.microsoft.com/ko-kr/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)를 참고하면 된다.

## 모델 다운로드

모델을 웹 서비스로 배포할 수도 있지만, 직접 다운로드 기능도 제공한다. 배포 바로 옆에 있는 **다운로드** 버튼을 누르면 매우 빠르게 다운이 완료된다. 다운로드 받은 알집 파일의 압축을 풀면 아래와 같은 파일들이 들어있다. 

![13-download](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-19-ms-azure-automl-tutorial-(2)/13-download.png?raw=true)

학습시킨 모델이 담겨져있는 `model.pkl` 파일이 있고, 예제로 보이는 `scoring_file_v_1_0_0.py` 파일이 존재한다. `model.pkl` 파일을 load하여 살펴보려 했으나, python 환경에서 파일을 사용하려면 결국 `azureml` 모듈의 설치가 필요하다며 ModuleNotFoundError가 뜬다. 
이전 글과 이 글에서는 직접 코딩을 하지 않고 기계학습을 할 수 있도록 인터페이스 환경에서 튜토리얼을 진행했는데, 모델을 학습시킨 후 웹이 아닌 로컬 환경에서 활용하려면 결국엔 관련 모듈들을 설치하고 코딩을 피할 수 없는 것 같다. 모듈을 설치하여 직접 살펴보는 것은 이 튜토리얼의 범위를 벗어나므로 python으로 AutoML 활용하는 [설명서](https://docs.microsoft.com/ko-kr/azure/machine-learning/how-to-configure-auto-train)를 링크하는 것으로 대체한다.


<br/>

이렇게 학습시킨 모델을 분석하고, 최적의 모델을 찾아 배포하거나 다운로드하는 방법을 알아보았다. 인터페이스를 활용해 직접 코딩을 하지 않고 몇가지 설정만을 통해서 기계학습 모델을 학습시킬 수 있다는 점에서는 참 편리한 것 같다. 하지만 인터페이스를 활용하더라도 데이터 전처리는 직접 해야 하고, 학습시킨 모델을 직접 활용하려면 결국엔 코딩을 해야 하여 장점이 많이 사라지는 것 같다. 그래도 비전공자나 관심있는 일반인이 간단하게 기계학습을 다뤄보거나, 직접 모델을 분석할 필요 없이 바로 서비스를 제공하려는 입장에서는 아주 편하게 사용할 수 있을 것 같다.
공식 설명서를 보면 인터페이스가 아니더라도 Python 코딩을 통해서 기계학습을 쉽게 해주는 서비스들이 있는 것 같은데, 차차 살펴보고 좋은 것이 있으면 공유하도록 하겠다. 


<!-- Last modified: 21-04-19, 14:30 -->