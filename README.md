# 열영상 기반 근육 자극 부하 영역 탐지

운동 중 촬영한 열영상에서 열 반응이 나타나는 영역을 **YOLOv5 객체 탐지 모델**로 찾고, 탐지 위치를 바운딩 박스로 시각화한 실험입니다. 열영상의 변화를 근육 자극 부하 분석에 활용하기 위한 연구의 일부이며, 이 저장소에는 그중 **탐지 모델의 학습과 이미지 추론을 수행한 Colab 노트북**을 담았습니다.

> 이 코드는 열 반응 영역을 탐지합니다. 영상에서 근육의 실제 자극량이나 절대온도를 계산하거나, 탐지 결과만으로 운동 부하를 정량 평가하는 코드는 포함하지 않습니다.

## 무엇을 구현했나

| 단계 | 노트북에서 수행하는 작업 |
| --- | --- |
| 데이터 준비 | Google Drive의 `data.zip`을 Colab에 압축 해제하고 이미지·YOLO 형식 라벨 경로를 설정 |
| 모델 학습 | 사전학습 가중치 `yolov5x.pt`에서 시작해 열영상 데이터로 YOLOv5x 학습 |
| 영역 탐지 | 학습된 `best.pt`로 검증·테스트 이미지의 열 반응 영역을 탐지 |
| 결과 확인 | `CriticalHeat`, `ModerateHeat` 등의 예측 클래스와 바운딩 박스를 이미지에 표시 |

노트북에는 YOLOv5s/m/l/x의 지표를 비교하는 그래프 코드도 있습니다. 그래프의 값은 코드에 직접 입력한 값이며, 이 저장소에는 해당 모델들의 학습 로그와 평가 결과 파일이 포함되어 있지 않습니다.

## 저장소 구성

```text
Thermal-Imaging/
├── README.md
└── real_yolo5.ipynb    # Colab 데이터 준비, YOLOv5x 학습, 탐지 및 시각화
```

**데이터셋, 학습 가중치, 실행 결과 이미지는 저장소에 포함되어 있지 않습니다.** 노트북을 재실행하려면 별도로 데이터와 라벨을 준비해야 합니다.

## 실행 방법

1. [노트북을 Colab에서 열기](https://colab.research.google.com/github/younguk072023/Thermal-Imaging/blob/main/real_yolo5.ipynb)를 눌러 GPU 런타임을 준비합니다.
2. 아래 구조의 `data.zip`을 자신의 Google Drive 최상위 폴더에 둡니다. 다른 위치를 쓰려면 노트북의 `zip_file_path`를 수정합니다.
3. Drive 연결과 데이터 준비 셀을 실행합니다. 이 과정에서 YOLOv5 저장소와 필요한 패키지를 설치하고, 데이터를 `/content/dataset`에 풉니다.
4. `data.yaml`의 클래스 수·이름·인덱스가 **실제 라벨 파일**과 일치하는지 확인하고 학습 셀을 실행합니다.
5. 생성된 `best.pt`의 실제 경로를 확인한 뒤 탐지·시각화 셀을 실행합니다.

노트북이 기대하는 압축 파일 내부 구조는 다음과 같습니다.

```text
data/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
└── labels/
    ├── train/
    ├── val/
    └── test/
```

현재 노트북의 학습 설정은 이미지 크기 `256`, 배치 크기 `16`, 학습 횟수 `100 epochs`, 시작 가중치 `yolov5x.pt`입니다.

```bash
python train.py --img 256 --batch 16 --epochs 100 --data data.yaml --weights yolov5x.pt
python detect.py --weights runs/train/exp/weights/best.pt --img 256 --conf 0.01 --source /content/dataset/data/images/val
```

`runs/train/exp`는 노트북이 가정한 결과 폴더입니다. 이전 실행 기록이 있으면 YOLOv5가 다른 폴더를 만들 수 있으므로 실제 저장 경로에 맞춰 변경해야 합니다. 현재 `data.yaml`은 `Class0`~`Class14`, `CriticalHeat`, `ModerateHeat`의 **17개 클래스로 선언**되어 있습니다. 재학습할 때 이 설정을 데이터의 클래스 정의와 먼저 대조해야 합니다.

## 결과를 해석할 때

- 저장소의 노트북은 실행 출력과 학습 로그를 저장하지 않아, README에 재현 가능한 성능 수치를 제시하지 않습니다.
- 후반부 코드의 예측 `confidence` 평균은 **정확도(accuracy)가 아닙니다**. 같은 값을 Precision·Recall·mAP 열에 넣은 요약표도 정답 라벨과 예측 박스를 대조한 평가가 아니므로 모델 성능 지표로 인용하지 않습니다.
- 정량 성능을 보고하려면 독립된 평가 데이터의 정답 라벨을 사용해 YOLOv5의 검증 절차로 Precision, Recall, mAP를 다시 계산하고, 사용한 데이터 분할과 실행 로그를 함께 남겨야 합니다.

이 저장소는 열영상에서 관심 영역을 **탐지하고 시각화한 코드**를 공유합니다. 탐지 영역의 변화가 실제 운동 부하를 얼마나 설명하는지는 별도의 데이터와 검증 절차가 필요합니다.
