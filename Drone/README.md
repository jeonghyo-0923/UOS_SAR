# 드론 기반 선박 탐지 및 지오레퍼런싱 시스템
- 드론 기종: 매빅3
- Docker 사용방법

# 주요 기능
1. **메타데이터 추출**: 이미지에 포함된 촬영 시점, GPS 정보, 카메라 파라미터 등 XMP 데이터 추출
2. **선박 탐지 (YOLOv8)**: 미리 학습된 모델(`best.pt`)을 활용해 선박을 탐지
3. **지오레퍼런싱**: 탐지된 객체의 중심 위치를 실제 GPS 좌표로 변환
4. **객체 정보 출력**: 객체 단위로 이미지 crop + JSON 파일 생성
---
# 폴더구조
```
sar_uav/
│
├── input/                         # 입력 데이터 폴더
│   ├── images/                   # 드론 촬영 원본 이미지
│   └── extracted_metadata.csv    # 추출된 메타데이터(csv)
│
├── result/                        # 출력 결과 폴더
│   ├── images/                   # 탐지된 객체 잘라낸 이미지
│   └── json/                     # 객체 정보(JSON, GPS 포함)
│
├── runs/                          # YOLOv8 실행 결과
│   └── detect/
│       └── predict/              # YOLOv8 추론 결과 (라벨 포함)
│
├── best.pt                       # YOLOv8 훈련된 모델 가중치
├── data.yaml                     # YOLOv8 클래스 설정 파일
└── requirements.txt              # Python 패키지 요구사항
```
# 디렉토리 정보
- `input/images`: 입력 이미지 (드론 촬영 .JPG 파일)
- `scripts/`: 각 처리 단계를 위한 파이썬 스크립트
- `runs/`: YOLOv8 예측 결과
- `result/images`: 객체 단위로 잘라낸 이미지
- `result/json`: 객체 정보 (위치, 클래스 등) 포함한 JSON
---
# 최종 목표
<img src="https://github.com/user-attachments/assets/03bc1914-5972-4345-812d-1fa192b4b7c3" alt="최종 목표 가시화" width="500"/>


