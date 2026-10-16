# 열영상 기반 근육 자극 부하 영역 탐지

운동 중 촬영한 열영상에서 열 반응 영역을 찾기 위해 YOLOv5 객체 탐지 모델을 적용했습니다. 이 저장소에는 모델 학습과 탐지 결과 시각화에 사용한 Colab 노트북을 담았습니다.

## 주요 내용

- 열영상과 라벨 데이터를 준비해 사전학습 YOLOv5x 모델 학습
- 검증·테스트 이미지에서 열 반응 영역 탐지
- `CriticalHeat`, `ModerateHeat` 예측 영역을 바운딩 박스로 시각화

## 노트북

[Colab에서 열기](https://colab.research.google.com/github/younguk072023/Thermal-Imaging/blob/main/real_yolo5.ipynb) · [GitHub에서 코드 보기](real_yolo5.ipynb)

실행하려면 별도의 열영상 데이터셋(`data.zip`)이 필요합니다.
