# 유인기 기반 객체 탐지 및 지오레퍼런싱 시스템
- 항공기로 촬영한 영상을 입력으로 받아, 이미지 내 존재하는 객체를 자동으로 탐지하고,
각 객체의 경위도 좌표를 계산하여 객체 단위의 크롭 이미지 및 JSON 형식 속성정보를 출력

## 최종 가시화 결과
<div align="center">
<img width="800" alt="image" src="https://github.com/user-attachments/assets/7a281e26-7e8f-4cb8-89ba-d48cde921bf7" />
</div>

- 유인기
  - 유인기의 위치 기반 수색 경로 시각화
  - 비행 중 기록된 경위도 좌표 출력
- 탐지된 객체
  - 객체 클래스 정보 표시(선박, 사람)
  - 촬영 영상 기준 탐지 객체의 경위도 좌표 제공
  - 탐지된 객체 영역을 크롭한 이미지 출력
  - 탐지 시각(타임스탬프) 정보 표시

# 주요기능
1. 동영상을 이미지로 변환: 동영상을 입력으로 하여 프레임 단위로 이미지 변환
2. 유인기 영상에서 OCR기반 메타데이터 추출
3. 미리 학습된 모델을 활용해 선박 탐지
4. 탐지된 객체의 중심 위치를 실제 경위도 좌표로 변환
5. 객체 정보 출력: 객체 단위로 이미지 crop된 이미지 및 JSON 파일(속성정보) 생성


# 폴더구조 
```
.
├── input/
│   ├── images/
│   └── video/      # 여기에 동영상 입력
├── result/
│   ├── csv/
│   ├── images/
│   └── json/
└── runs/
```

# 디렉토리 정보 및 입출력 예시 - 수정중
### (입력) `input/video`: 입력 동영상 (동영상이름.mp4 파일)
```bash
input/video/
├── VideoName.mp4
```

### (출력 - 이미지) `result/images`: 선박 단위로 잘라낸 이미지
- <원본 이미지명>_<객체순번>.JPG으로 저장(속성정보와 이미지명으로 1:1대응)
```
result/images/
├── VideoName_0000_0.JPG
├── VideoName_0000_1.JPG
├── VideoName_0001_0.JPG
├── VideoName_0002_0.JPG
```

- 예시
<div align="center">
<img width="400" alt="image" src="https://github.com/user-attachments/assets/fdfc1483-8161-4414-ae7b-b3a6e7858b55" />
</div>

### (출력- 속성정보) `result/json`: 선박 정보 (위치, 클래스 등) 포함한 JSON
- <원본 이미지명>_<객체순번>.json으로 저장(속성정보와 이미지명으로 1:1대응)
```
result/json/
├── VideoName_0000_0.json
├── VideoName_0000_1.json
├── VideoName_0001_0.json
├── VideoName_0002_0.json
```


- 예시
```bash
{
  "class_id": 0,                                               # 0:선박, 1:사람(현재는 선박 기준으로 개발됨. 추후 사람 클래스 추가 예정)
  "image_path": "result/images/VideoName_0000_0.jpg",          # 해당 이미지 경로
  "x_center_px": 529.472,                                      # 객체 픽셀 x좌표
  "y_center_px": 434.79792000000003,                           # 객체 픽셀 y좌표
  "proj_epsg": 5185,                                           # 좌표계
  "X_5185": 145963.3827057409,                                 # 객체 중심 위치(5185 기준)
  "Y_5185": 261338.09072368147,                                # 객체 중심 위치(5185 기준)
  "longitude": 124.4084526976203,                              # 객체 경도 좌표
  "latitude": 34.94666868163483,                               # 객체 위도 좌표
  "photo_time": "14:42:08L",                                   # 영상 촬영 시간
  "aircraft_X_5185": 145003.95538278137,                       # 유인기 위치(5185 기준)
  "aircraft_Y_5185": 258810.33549579047,                       # 유인기 위치(5185 기준)
  "aircraft_longitude": 124.39811666666667,                    # 유인기 경도 좌표
  "aircraft_latitude": 34.923833333333334                      # 유인기 위도 좌표
}
```

# 최종 가시화를 위한 데이터 
<div align="center">
<img width="800" alt="image" src="https://github.com/user-attachments/assets/aae644c7-ce7f-4a7e-84a0-85b41b93f1af" />
</div>

1. 유인기 정보
  - 경로: result/json
  - 유인기 위도: "aircraft_latitude"
  - 유인기 경도: "aircraft_longitude"

2. 선박 위치 추적
  - 경로: result/json
  - 선박의 위도: "latitude"
  - 선박의 경도: "longitude"
  - 촬영 시간: "phroto_time" (촬영 시간에 따른 선박 추적 가시화)
3. 선박 이미지 (속성정보와 이미지명으로 1:1대응)
  - 경로: result/images
4. 선박 속성 정보 (이미지와 이미지명으로 1:1대응)
  - 경로: result/json
  - 클래스 id: "class_id"
  - 선박의 위도: "latitude"
  - 선박의 경도: "longitude"
  - 촬영 시간: "phroto_time"
  - 해당 이미지 경로: "image_path"

# Docker 사용 방법 - 수정예정
- 폴더 이동
```
cd ./sar_air
```

- 이미지 빌드
```
  docker build -t my-pytorch-app .
```

- 도커 실행
```
docker run --rm \
  -v $(pwd)/input:/workspace/sar_air/input \
  -v $(pwd)/runs:/workspace/sar_air/runs \
  -v $(pwd)/result:/workspace/sar_air/result \
  my-pytorch-app
```
