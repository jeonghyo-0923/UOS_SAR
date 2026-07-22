# UOS_SAR

탐색구조(SAR, Search and Rescue) 임무 지원을 위한 항공영상 기반 객체 탐지 및 지오레퍼런싱 시스템입니다. 유인기(Aircraft)와 드론(Drone)으로 촬영한 영상에서 객체(선박, 사람)를 자동으로 탐지하고, 각 객체의 실제 경위도 좌표를 계산하여 객체 단위의 크롭 이미지와 JSON 형식 속성정보를 생성합니다.

촬영 플랫폼(유인기/드론)에 따라 두 개의 서브 프로젝트로 구성되어 있습니다.

## 폴더 구조

```
UOS_SAR/
├── Aircraft/    # 유인기 기반 객체 탐지 및 지오레퍼런싱 시스템
├── Drone/       # 드론 기반 선박 탐지 및 지오레퍼런싱 시스템
├── .gitignore
└── README.md
```

## 서브 프로젝트

### ✈️ Aircraft — 유인기 기반 객체 탐지 및 지오레퍼런싱 시스템
- **입력**: 유인기로 촬영한 동영상(mp4)
- **주요 처리**
  1. 동영상 → 프레임 단위 이미지 변환
  2. OCR 기반 메타데이터(경위도, 촬영시각 등) 추출
  3. 사전 학습된 모델을 이용한 선박 탐지
  4. 탐지 객체 중심 픽셀 좌표 → 실제 경위도 좌표 변환(EPSG:5185 기반)
  5. 객체 단위 크롭 이미지 및 JSON 속성정보 생성
- **탐지 클래스**: 선박(0), 사람(1, 추후 추가 예정)
- 상세 내용: [Aircraft/README.md](./Aircraft/README.md)

### 🚁 Drone — 드론 기반 선박 탐지 및 지오레퍼런싱 시스템
- **드론 기종**: DJI M30T (추후 타 기종 확장 예정)
- **탑재 센서**: 광학 센서 (IR 센서 추후 확장 예정)
- **입력**: 드론으로 촬영한 이미지(.JPG, XMP 메타데이터 포함)
- **주요 처리**
  1. 이미지 XMP 메타데이터(촬영시점, GPS, 카메라 파라미터) 추출
  2. 사전 학습된 모델을 이용한 선박 탐지
  3. 탐지 객체 중심 픽셀 좌표 → 실제 경위도 좌표 변환
  4. 객체 단위 크롭 이미지 및 JSON 속성정보 생성
- **탐지 클래스**: 선박(0), 사람(1, 추후 추가 예정)
- 상세 내용: [Drone/README.md](./Drone/README.md)

## 공통 기능
1. 항공/드론 영상에서 객체(현재 선박, 추후 사람 클래스 추가 예정) 자동 탐지
2. 탐지된 객체의 픽셀 좌표를 실제 경위도 좌표로 변환(지오레퍼런싱)
3. 객체 단위 크롭 이미지 + JSON 형식 속성정보 출력
4. Docker 기반 실행 환경 제공

## 최종 가시화
각 서브시스템은 촬영 플랫폼(유인기/드론)의 위치 기반 수색 경로와, 탐지된 객체(선박)의 클래스·좌표·크롭 이미지·탐지 시각 정보를 지도 위에 함께 시각화합니다. 촬영 시간 정보를 기반으로 한 객체(선박) 위치 추적 기능도 지원합니다.

| 구분 | Aircraft | Drone |
|---|---|---|
| 촬영 플랫폼 | 유인기 | 드론(DJI M30T) |
| 입력 | 동영상(mp4) | 이미지(.JPG) |
| 메타데이터 추출 | OCR 기반 | XMP 기반 |
| 좌표계 | EPSG:5185 → 경위도 변환 | GPS 경위도 직접 산출 |
| 탐지 클래스 | 선박(사람 추후 예정) | 선박(사람 추후 예정) |

## 실행 방법
각 서브 프로젝트는 Docker 기반으로 독립 실행됩니다. 자세한 빌드·실행 명령은 각 서브 프로젝트의 README를 참고하세요.

```bash
cd ./Aircraft   # 또는 ./Drone
docker build -t my-pytorch-app .
docker run --rm \
  -v $(pwd)/input:/workspace/<project>/input \
  -v $(pwd)/runs:/workspace/<project>/runs \
  -v $(pwd)/result:/workspace/<project>/result \
  my-pytorch-app
```

## Requirements
각 서브 폴더 내 `requirements.txt` 참고
- [Aircraft/requirements.txt](./Aircraft/requirements.txt)
- [Drone/requirements.txt](./Drone/requirements.txt)

## License
[라이선스 정보 필요]
