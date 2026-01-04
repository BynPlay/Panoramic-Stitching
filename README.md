# 🌄 Panorama Image Stitching

**OpenCV 없이 순수 Python으로 구현한 파노라마 이미지 합성 파이프라인**

컴퓨터 비전의 핵심 알고리즘들을 직접 구현하여, 여러 장의 이미지를 하나의 연속적인 파노라마로 합성합니다.

---

## ✨ Features

| 단계 | 구현 내용 |
|:---:|:---|
| **Preprocessing** | Gaussian Smoothing을 통한 노이즈 제거 |
| **Corner Detection** | Harris Corner Detector로 특징점 추출 |
| **Feature Matching** | Normalized Correlation + Lowe's Ratio Test |
| **Outlier Rejection** | RANSAC 알고리즘으로 이상치 제거 |
| **Transformation** | Homography 계산 (DLT + SVD) |
| **Stitching** | Image Warping & Blending |
| **Post-processing** | Group Adjustment + Reinhard Tone Mapping |

---

## 🔧 Pipeline Overview

```
Input Images
     │
     ▼
┌─────────────────┐
│  Gaussian Blur  │  ← 고주파 노이즈 제거, σ=2.0
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Harris Corner   │  ← Sobel 필터 + M 행렬 고유값 분석
│    Detection    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Descriptor      │  ← 패치 기반 + L2 정규화
│   Matching      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    RANSAC       │  ← 4-point sampling, threshold 기반
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Homography     │  ← Direct Linear Transform
│   Estimation    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Stitching     │  ← Warping + Average Blending
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Tone Mapping    │  ← Reinhard Global Operator
└────────┬────────┘
         │
         ▼
   Panorama Output
```

---

## 📸 Results

### Corner Detection
코너 포인트는 모든 방향에서 밝기 변화가 큰 지점으로, 이미지 매칭의 핵심 기준점입니다.

### Feature Matching
추출된 코너 간 디스크립터 유사도를 비교하여 대응점을 탐색합니다.

### RANSAC Filtering
무작위 샘플링으로 이상치를 제거하고, 가장 많은 인라이어를 포함하는 Homography를 선택합니다.

### Final Panorama
여러 시점의 이미지가 하나의 연속적인 장면으로 합성됩니다.

---

## 🛠 Tech Stack

- **Language**: Python
- **Core Libraries**: NumPy (행렬 연산), OpenCV (이미지 I/O만 사용)
- **Environment**: PyCharm, Jupyter Notebook

---

## 📂 Project Structure

```
panorama-stitching/
├── src/
│   ├── gaussian_filter.py    # Gaussian Kernel & Smoothing
│   ├── harris_corner.py      # Harris Corner Detection
│   ├── descriptor.py         # Feature Descriptor Generation
│   ├── matching.py           # Point Matching & Ratio Test
│   ├── ransac.py             # RANSAC for Homography
│   ├── homography.py         # DLT + SVD Computation
│   ├── stitching.py          # Image Warping & Blending
│   └── tone_mapping.py       # Reinhard Tone Mapping
├── images/                   # Input images
├── output/                   # Generated panoramas
└── README.md
```

---

## 🧮 Key Algorithms

### Gaussian Smoothing
2D Gaussian 분포 기반 커널로 고주파 노이즈를 제거합니다.

```
G(x,y) = (1/2πσ²) × exp(-(x² + y²)/2σ²)
```

### Harris Corner Response
M 행렬의 고유값을 분석하여 코너 가능성을 평가합니다.

```
R = det(M) - k × trace(M)²
```
- 두 고유값 모두 큼 → **Corner**
- 하나만 큼 → Edge
- 둘 다 작음 → Flat region

### Homography Estimation
DLT(Direct Linear Transform)와 SVD를 활용하여 3×3 투영 변환 행렬을 계산합니다.

---

## 💡 Design Decisions

| 결정 | 이유 |
|:---|:---|
| Kernel size = 3 | 특징점이 많은 데이터셋에 적합한 작은 크기 |
| σ = 2.0 | 노이즈 제거와 디테일 보존의 균형점 |
| Lowe's Ratio Test | 잘못된 매칭을 효과적으로 필터링 |
| Average Blending | 경계부 자연스러운 전환 |
| Reinhard TMO | 밝기-색상 비율 유지하며 대비 조정 |

---

## 🚀 Getting Started

```bash
# Clone repository
git clone https://github.com/username/panorama-stitching.git
cd panorama-stitching

# Install dependencies
pip install numpy opencv-python

# Run panorama generation
python main.py --input ./images --output ./output
```

---

## 📚 References

- Harris, C., & Stephens, M. (1988). *A combined corner and edge detector*
- Lowe, D. G. (2004). *Distinctive image features from scale-invariant keypoints*
- Fischler, M. A., & Bolles, R. C. (1981). *Random sample consensus*
- Reinhard, E. et al. (2002). *Photographic tone reproduction for digital images*

---

## 📝 License

This project is for educational purposes as part of Computer Vision coursework.
