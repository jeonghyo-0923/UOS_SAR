# 사용방법
- 드론 기종: 매빅3
```bash

```

# 데이터 처리

# 데이터
- input/imgaes
  - JPG형식으로 저장
- result
  - images
  - json

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
├── requirements.txt              # Python 패키지 요구사항


```
# 구성파일
