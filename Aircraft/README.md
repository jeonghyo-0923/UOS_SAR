# 사용방법
```
# 도커 이미지 빌드
docker build -t ship_detection_pipeline .

# 도커 컨테이너 실행
docker run --rm -v ${PWD}/input:/app/input -v ${PWD}/output:/app/output ship_detection_pipeline
```

# 데이터 처리
<div align="center">
<img src="https://github.com/user-attachments/assets/29c12799-7d73-4498-abe4-ed39fdc3ea72" width="500"/>
</div>

### 동영상을 이미지로 변환
1. 

### OCR기반 메타정보 추출

### 이미지에서 선박을 탐지

### 탐지 선박 지오레퍼런싱

### 탐지 선박 이미지와 속성정보를 jpg와 json으로 추출


# 데이터

# 폴더구조
```
.
├── dataset/
│   ├── test/
│   └── video/
├── result/
│   ├── images/
│   └── json/
└── runs/
```

# 구성파일
