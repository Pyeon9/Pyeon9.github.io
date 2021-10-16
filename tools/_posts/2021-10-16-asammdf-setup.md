---
layout: post
title: "[asammdf] Python에서 CAN 데이터 계측 파일(mdf) 읽기"
description: >
  CAN 데이터의 계측 파일인 mdf 파일을 Python에서 읽고 다루는 간단한 방법
---

mdf 파일은 Measurement Data Format의 약자로, 1991년 벡터와 보쉬가 협업하여 개발한 계측 데이터용 바이너리 파일 포맷이다([참고](https://www.vector.com/kr/ko/products/application-areas/ecu-calibration/measurement/mdf/)). 
자동차 산업에서 사실상의 표준으로 사용되므로 자동차 관련 프로젝트를 진행하다보면 쉽게 접할 수 있는 파일이다.
`ASAM(Association for Standardisation of Automation-and Measuring System)`이라는 차량용 제어기 개발에 드는 시간과 비용을 줄이기 위해 설립된 단체가 있는데, 이 단체에서 mdf 파일을 Python에서 쉽게 다룰 수 있도록 도와주는 `**asammdf**`라는 라이브러리를 개발하였다([공식 페이지](https://pypi.org/project/asammdf/)).

## 설치
설치하기 전에 아래의 의존성 설치를 먼저 진행하면 좋다.

asammdf uses the following libraries
- numpy : the heart that makes all tick
- numexpr : for algebraic and rational channel conversions
- wheel : for installation in virtual environments
- pandas : for DataFrame export
- canmatrix : to handle CAN/LIN bus logging measurements
- natsort
- lxml : for canmatrix arxml support
- lz4 : to speed up the disk IO peformance

optional dependencies needed for exports
- h5py : for HDF5 export
- scipy : for Matlab v4 and v5 .mat export
- hdf5storage : for Matlab v7.3 .mat export
- fastparquet : for parquet export

other optional dependencies
- PyQt5 : for GUI tool
- pyqtgraph : for GUI tool and Signal plotting
- matplotlib : as fallback for Signal plotting
- cChardet : to detect non-standard unicode encodings
- chardet : to detect non-standard unicode encodings
- pyqtlet : for GPS window

의존성 설치가 끝난 후 아래 명령어를 통해 asammdf 라이브러리를 설치할 수 있다.
```
$ pip install asammdf
# for the GUI 
$ pip install asammdf[gui]
# or for anaconda
$ conda install -c conda-forge asammdf
```

asammdf gui 실행 파일(.exe)은 [이곳](https://sourceforge.net/projects/asammdf/)에서 다운받을 수 있다.


## mdf 파일 읽기

```python 
from asammdf import MDF
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# MDF 파일 읽기
path = "./data/"
data = MDF(path + "/Acceleration_StandingStart.MDF")

# CAN 신호 목록 가져오기(key)
signal_list = list(data.channels_db)
# ['t', 'VehicleSpeed', 'Throttle', 'Gear', 'EngineRpm', 'Brake']
print(signal_list)

# 특정 신호 데이터 가져오기
speed = data.get("VehicleSpeed")
# 신호 그래프 출력
speed.plot()

### 엑셀 파일 또는 CSV 파일로 출력
signals_df = data.to_dataframe()
signals_df.to_excel(path + "signals_df.xlsx")
signals_df.to_csv(path + "signals_df.csv")
```

CAN 신호는 dictionary 형태로 저장되어 있어, (key, value)로 접근이 가능하다.
CAN 신호의 이름이 key가 되고, 값이 value로 저장되어 있다.
`t`는 데이터가 계측된 시간을 값으로 가지는데, 간혹 여러 값이 들어있어 `get()` 함수로 접근이 안될때가 있다.
이전에는 값이 여러개 있어도 get()을 하면 가장 앞의 값을 반환했는데, 모호성을 없애고 사용자의 의도에 맞게 사용할 수 있도록 에러를 반환하도록 했다고 한다. 
이런 경우에는 아래와 같이 활용해야 한다.

```python
occurrences = data.whereis("t")
signals = data.select(
    [(None, gp_idx, cn_idx) for gp_idx, cn_idx in occurrences]
)
```

`get()` 함수는 `asammdf.signal.Signal` 객체를 반환하므로 바로 활용하기가 어려울 수 있다.
이럴때는 `신호.samples`를 입력하면 numpy array가 반환되어 변환하거나 matplotlib으로 plotting 하는 등 활용이 쉽다.

더욱 자세한 것은 직접 출력해가면서 확인하면 좋을 것 같다. 


<br/>


<!-- Last modified: 21-10-16, 15:02 -->