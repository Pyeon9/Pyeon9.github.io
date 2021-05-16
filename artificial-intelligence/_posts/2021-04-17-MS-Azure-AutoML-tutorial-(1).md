---
layout: post
title: "[AutoML] MS Azure AutoML 튜토리얼 (1)"
description: >
   Microsoft Azure에서 제공하는 기계학습 모델 개발 자동화 프로세스인 AutoML의 튜토리얼
---

Microsoft는 2010년 Azure라는 클라우드 컴퓨팅 플랫폼을 만들어 여러가지 서비스를 제공중이다. 리눅스/윈도우 가상 머신, 스토리지 서비스, 데이터 관리 등 현재 200개 이상의 제품 및 600여가지 이상의 서비스를 제공하고 있다고 한다. 구체적인 서비스가 궁금하다면 [공식 홈페이지](https://azure.microsoft.com/ko-kr/)를 참고.

오늘은 그 중에서도 **AutoML**(Automated Machine Learning)이라는 서비스를 사용하는 방법을 알아보겠다. 기존에 기계학습 모델 개발은 layer와 unit의 수, 필터의 크기 및 개수, 옵티마이저의 종류 등의 하이퍼파라미터를 여러가지로 조합해보고 가장 좋은 성능을 내는 모델을 찾는 방식이었다. 이는 아주 오랜 시간을 필요로하고 반복적인 작업으로 피로감을 유발하는데, Azure의 AutoML은 자동으로 여러 하이퍼마라미터를 조합하여 수십 개의 모델을 생성하고 비교하여 가장 좋은 모델 구조를 찾아준다. AutoML을 사용하면 기계학습 모델을 매우 쉽고 효율적으로 얻을 수 있다고 한다.
특히 모델의 최적 구조를 찾는 것은 기계학습 분야에 대한 이해와 지식이 필요한 일인데, AutoML은 비전공자 및 기계학습이 익숙하지 않은 사람들에게 큰 도움이 된다고 설명한다. 

그럼 이제 본격적으로 AutoML을 사용하는 방법과 절차를 알아보겠다.

## 체험 계정 생성

Azure 서비스를 구독하지 않고 있다면 우선 체험 계정을 생성해야 한다. [이곳](https://azure.microsoft.com/en-us/free/services/machine-learning/)에 접속하여 아래의 'Start free' 버튼을 누른 후 Microsoft 계정으로 로그인을 한다. GitHub 계정으로 로그인도 가능하므로 Microsoft 계정이 없어도 사용이 가능하다. 

![01-Azure-free-trial](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/01-Azure-free-trial.png?raw=true)

아래의 profile을 작성하고 Next 버튼을 누른다. 

![02-Azure-profile](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/02-Azure-profile.png?raw=true)

그러면 휴대전화를 이용해서 verification하는 창이 나오는데, 국가코드를 +82로 하고 아래에 본인 핸드폰 번호의 맨 앞 0을 제외한 나머지를 입력한 후 Text me 혹은 Call me를 눌러 진행한다.
Phone number 입력 창에 회색 글씨로 x xxxx xxxx의 형식으로 입력하라고 나오는데, 무시하고 내가 말한 방법대로 입력하면 된다. (ex. 010-1234-5678 > 1 0123 45678로 입력)

그 후에는 card 정보로 verification을 해야하는데, 본인 확인하는데 사용하고 upgrade하지 않으면 비용이 청구되지 않는다고 하니 걱정하지말고 진행하자. 해외결제가 가능한 카드 중 하나를 골라 사용하면 된다. 카드 verification까지 완료했다면 Agreement의 항목을 모두 체크하고 Sign up.

아래와 같은 화면이 뜨고 우측 상단에 이름이 표시되었다면 성공적으로 체험 계정 생성이 완료된 것이다.

![03-Azure-sign-in](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/03-Azure-sign-in.png?raw=true)


## 작업 영역 만들기

이제 작업 영역을 만들 차례다. 작업 영역을 만드는 방법은 [여러가지](https://docs.microsoft.com/ko-kr/azure/machine-learning/how-to-manage-workspace?tabs=python)가 있지만, 오늘은 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만들어보겠다.

아래 gif를 참고하여 다음 과정을 진행한다. (영문 사이트여서 정확히 같지는 않다)
  1. [Azure Portal에 로그인](https://portal.azure.com/?quickstart=true&CAF=true#blade/Microsoft_Azure_Resources/QuickstartAnchorServicesBlade/goalId/start-data-analytics)
  2. 왼쪽 상단의 메뉴 클릭 후  **+ 리소스 만들기**클릭
  3. 기계 학습 검색 후 선택
  4. **만들기**를 선택하여 시작
![create-workspace](https://docs.microsoft.com/ko-kr/azure/includes/media/aml-create-in-portal/create-workspace.gif)

  5. 아래 사항들을 작성한 후 **검토 + 만들기** 후 **만들기** 클릭.

![04-create-workspace](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/04-create-workspace.png?raw=true)

아래와 같이 '배포 진행 중'이라고 뜨며, 몇 분 정도 기다리면 배포가 완료되었다고 나온다. **리소스로 이동**을 클릭하면 생성된 작업 영역을 볼 수 있다.

![05-deploying](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/05-deploying.png?raw=true)
![06-deployed](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/06-deployed.png?raw=true)


## Azure ML Studio

데이터 과학 시나리오를 수행하기 위한 기계학습 도구를 포함하는 [통합 웹 인터페이스](https://ml.azure.com/)에서 Azure ML Studio를 통해 다음 실험 설정 및 실행 단계를 완료한다. 

위 통합 웹 인터페이스 링크에 접속하여 앞서 생성해둔 작업 영역을 선택하고 시작을 누른다.

![07-begin-studio](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/07-begin-studio.png?raw=true)

왼쪽 메뉴에서 **자동화된 ML**을 선택하고, **새 자동화된 ML 실행**을 누른다.

![08-begin-studio](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/08-start-automl.png?raw=true)

### 데이터 세트 생성 및 로드

데이터 파일을 Azure Machine Learning 데이터 세트 형식으로 작업 영역에 업로드한다. 이렇게 하면 데이터의 형식이 실험에 맞게 적절히 지정되도록 할 수 있다.
우선 [이곳](https://docs.microsoft.com/ko-kr/azure/machine-learning/tutorial-first-experiment-automated-ml#prerequisites)에서 **banchmarketing_train.csv** 파일을 다운 받는다. 자신의 데이터 세트이 있다면 사용해도 되지만, 이 포스트에서는 위 파일을 기준으로 튜토리얼을 진행한다.

  1. 데이터 세트 만들기 > 로컬 파일에서 를 통해 새 데이터 세트를 생성

      ![09-make-dataset](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/09-make-dataset.png?raw=true)

      - 데이터 저장소는 자동으로 설정된 기본 데이터 저장소인 **workspaceblobstore (Azure Blob Storage**를 선택
      - 설정 및 미리 보기 항목은 아래와 같이 설정하고 다음으로 진행

      ![10-configure](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/10-configure.png?raw=true)

      - 스키마 항목에서는 `day_of_week`를 포함하지 않도록 설정하고 진행

      ![11-day-of-week](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/11-day-of-week.png?raw=true)

잘 따라왔다면 아래와 같이 데이터 세트가 생성이 된다. 데이터 세트를 선택하고 하단의 다음을 누르면 데이터 세트를 미리보기 할 수 있다. `day_of_week`가 포함되지 않았는지 확인하고, 닫기를 선택한 후 다음으로 진행한다.

![12-dataset-created](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/12-dataset-created.png?raw=true)
![13-dataset-preview](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/13-dataset-preview.png?raw=true)
![14-no-day-of-week](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/14-no-day-of-week.png?raw=true)

새 실험 이름을 작성하고, 대상 열은 드롭다운 박스를 눌러 예측하려는 항목인 **y (String)**을 선택한다. **새 컴퓨팅 만들기**를 눌러 아래 이미지와 같이 설정을 하고 **만들기**를 선택한다. 구성이 완료되는 데 몇 분이 소요된다. 

![15-computing-cluster-(1)](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/15-computing-cluster-(1).png?raw=true)
![16-computing-cluster-(2)](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/16-computing-cluster-(2).png?raw=true)

만들기가 완료되면 컴퓨팅 클러스터로 선택을 해주고 다음으로 넘어간다.
기계학습 작업 유형으로 **분류**를 선택하고, 하단의 **추가 구성 설정 보기**를 선택하여 그림과 같이 설정을 해준다. 따로 설정하지 않아도 기본값으로 설정이 되지만, 효율적인 학습을 위해서 설정을 진행한다.

![17-additional-setting](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/17-additional-setting.png?raw=true)

저장하고 마침을 누르면 잠시 뒤 실험 준비가 시작되고, 준비가 완료되면 자동으로 학습이 실행된다. 실행이 되면서 아래와 같이 상태가 업데이트된다.

![18-learning](https://github.com/pyeon9/images-for-github-page/blob/main/aritificial-intelligence/2021-04/04-17-ms-azure-automl-tutorial-(1)/18-learning.png?raw=true)

실험 준비는 10~15분 정도가 소요되고, 학습이 실해오디면 각 반복에 대해 2~3분이 더 소요된다고 한다.


AutoML을 사용하여 학습을 진행하는 방법 및 과정을 알아보았고, 학습이 완료된 모델을 분석하는 방법은 다음 글에서 자세하게 다루겠다.


<!-- Last modified: 21-04-17, 15:36 -->