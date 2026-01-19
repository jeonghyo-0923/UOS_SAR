# 드론 기반 선박 탐지 및 지오레퍼런싱 시스템
- 드론으로 촬영한 항공 영상을 입력으로 받아, 이미지 내 존재하는 객체를 자동으로 탐지하고,
각 객체의 경위도 좌표를 계산하여 객체 단위의 크롭 이미지 및 JSON 형식 속성정보를 출력
- 드론 기종: DJI M30T (※ 추후 다른 기종도 적용 예정)
- 탐지 클래스: 선박 (※ 사람 클래스는 향후 추가 예정)
- 탑재 센서: 광학 센서 (※ IR 센서는 추후 확장 예정)

## 최종 가시화 결과
<div align="center">
<img src="<img width="1032" height="599" alt="image" src="<img width="1032" height="599" alt="그림1" src="https://github.com/user-attachments/assets/d0dc5aae-afe3-4084-aaa4-bb3acbab2ace" />
" />
" alt="최종 목표 가시화" width="500"/>
</div>

- 드론
  - 드론의 위치 기반 수색 경로 시각화
  - 드론 비행 중 기록된 경위도 좌표 출력
- 탐지된 객체
  - 객체 클래스 정보 표시(선박, 사람)
  - 촬영 영상 기준 탐지 객체의 경위도 좌표 제공
  - 탐지된 객체 영역을 크롭한 이미지 출력
  - 탐지 시각(타임스탬프) 정보 표시

# 주요 기능
1. **메타데이터 추출**: 이미지에 포함된 촬영 시점, GPS 정보, 카메라 파라미터 등 XMP 데이터 추출
2. **선박 탐지**: 미리 학습된 모델을 활용해 선박 탐지
3. **지오레퍼런싱**: 탐지된 객체의 중심 위치를 실제 경위도 좌표로 변환
4. **객체 정보 출력**: 객체 단위로 이미지 crop된 이미지 및 JSON 파일(속성정보) 생성

# 폴더구조
```
sar_uav/
│
├── input/                        # 입력 데이터 폴더
│   └── images/                   # 드론 촬영 원본 이미지
│
├── result/                       # 출력 결과 폴더
│   ├── images/                   # 탐지된 객체 잘라낸 이미지
│   └── json/                     # 객체 정보(JSON, GPS 포함)
│
├── runs/                         # 탐지 모델 실행 결과
│   └── detect/
│       └── predict/              # 탐지 모델 추론 결과 (라벨 포함)
│
├── requirements.txt              # Python 패키지 요구사항
└── my-pytorch-app.tar            # Docker 이미지 파일
```

# 디렉토리 정보 및 입출력 예시
### (입력) `input/images`: 입력 이미지 (드론 촬영 .JPG 파일)
```bash
input/images/
├── DJI_0775.JPG
├── DJI_0776.JPG
```

### (출력 - 이미지) `result/images`: 선박 단위로 잘라낸 이미지
- <원본 이미지명>_<객체순번>.JPG으로 저장(속성정보와 이미지명으로 1:1대응)
```
result/images/
├── DJI_0775_0.JPG
├── DJI_0775_1.JPG
├── DJI_0776_0.JPG
├── DJI_0777_0.JPG
```

- 예시
<div align="center">
<img src="https://github.com/user-attachments/assets/85ad56e3-ddb9-45f2-ab1e-54b25c678fb3" alt="최종 목표 가시화" width="200"/>
</div>

### (출력- 속성정보) `result/json`: 선박 정보 (위치, 클래스 등) 포함한 JSON
- <원본 이미지명>_<객체순번>.json으로 저장(속성정보와 이미지명으로 1:1대응)
```
result/images/
├── DJI_0775_0.json
├── DJI_0775_1.json
├── DJI_0776_0.json
├── DJI_0777_0.json
```

- 예시
```bash
{
    "class_id": 0,                                               # 0:선박, 1:사람(현재는 선박 기준으로 개발됨. 추후 사람 클래스 추가 예정)
    "image_path": "./result/images/DJI_0775_0.jpg",              # 해당 이미지 경로
    "drone_lat": 35.924652214,                                   # 드론 위도 좌표
    "drone_lon": 126.515188839,                                  # 드론 경도 좌표
    "gps_lat": 35.92450570883135,                                # 객체 위도 좌표
    "gps_lon": 126.51777891662977,                               # 객체 경도 좌표
    "capture_time": "2023-09-19T09:07:49"                        # 이미지 촬영 시간
}
```

# 최종 가시화를 위한 데이터
<div align="center">
<img src="https://github.com/user-attachments/assets/4d7cb6f7-00b5-4109-b672-fc4001939746" alt="최종 목표 가시화" width="500"/>
</div>

1. 드론 정보
  - 경로: result/json
  - 드론의 위도: "drone_lat"
  - 드론의 경도: "drone_lon"
2. 선박 위치 추적
  - 경로: result/json
  - 선박의 위도: "gps_lat"
  - 선박의 경도: "gps_lon"
  - 촬영 시간: "capture_time" (촬영 시간에 따른 선박 추적 가시화)
3. 선박 이미지 (속성정보와 이미지명으로 1:1대응)
  - 경로: result/images
4. 선박 속성 정보 (이미지와 이미지명으로 1:1대응)
  - 경로: result/json
  - 클래스 id: "class_id"
  - 선박의 위도: "gps_lat"
  - 선박의 경도: "gps_lon"
  - 촬영 시간: "capture_time"
  - 해당 이미지 경로: "image_path"

# Docker 사용 방법
- 폴더 이동
```
cd ./sar_uav
```

- 이미지 빌드
```
  docker build -t my-pytorch-app .
```

- 도커 실행
```
docker run --rm \
-v $(pwd)/input:/workspace/sar_uav/input \
-v $(pwd)/runs:/workspace/sar_uav/runs \
-v $(pwd)/result:/workspace/sar_uav/result \
my-pytorch-app
```
