# 드론 기반 선박 탐지 및 지오레퍼런싱 시스템
- 드론 기종: 매빅3
- Docker 사용방법

# 최종 목표
<img src="https://github.com/user-attachments/assets/84cb42f8-61c9-45d0-b362-ad4bc6e4f9f0" alt="최종 목표 가시화" width="500"/>

# 주요 기능
1. **메타데이터 추출**: 이미지에 포함된 촬영 시점, GPS 정보, 카메라 파라미터 등 XMP 데이터 추출
2. **선박 탐지 (YOLOv8)**: 미리 학습된 모델(`best.pt`)을 활용해 선박을 탐지
3. **지오레퍼런싱**: 탐지된 객체의 중심 위치를 실제 GPS 좌표로 변환
4. **객체 정보 출력**: 객체 단위로 이미지 crop + JSON 파일 생성

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

# 디렉토리 정보 및 입출력 예시
- `input/images`: 입력 이미지 (드론 촬영 .JPG 파일)
```bash
input/images/
├── DJI_0775.JPG
├── DJI_0776.JPG
```

- `result/images`: 선박 단위로 잘라낸 이미지
<img src="https://github.com/user-attachments/assets/85ad56e3-ddb9-45f2-ab1e-54b25c678fb3" alt="최종 목표 가시화" width="200"/>

- `result/json`: 선박 정보 (위치, 클래스 등) 포함한 JSON

```bash
{
    "class_id": 0,
    "x_center": 0.669799,
    "y_center": 0.51583,
    "BBOX": 0.00551150110495,
    "frame_time": "2025-06-27T04:54:31.023493",
    "image_path": "./result/images/DJI_0775_0.jpg",
    "drone_lat": 35.924652214,
    "drone_lon": 126.515188839,
    "gps_lat": 35.92450570883135,
    "gps_lon": 126.51777891662977,
    "capture_time": "2023-09-19T09:07:49"
}
```

