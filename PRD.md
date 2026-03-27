# [Master PRD] REFIK ANADOL 'UNSUPERVISED' - WebGL 3D 클론 랜딩 페이지

## 1. 프로젝트 개요 (Overview)
- **목표**: AI가 생성한 데이터의 잠재 공간(Latent Space)을 다루는 레픽 아나돌의 영상(UNSUPERVISED)을 브라우저 기반의 실시간 3D WebGL 공간으로 완벽히 복제.
- **코어 기술**: Three.js (또는 React Three Fiber), WebGL 커스텀 셰이더(GLSL), GPGPU 시뮬레이션.

## 2. 렌더링 파이프라인 & 아키텍처 (Rendering Architecture)
- **GPGPU 파티클 시스템**: 수백만 개의 파티클 위치와 속도를 연산해야 하므로 CPU가 아닌 GPU 연산(Compute Shader / FBO)을 활용한 렌더링 파이프라인 구축 필수.
- **동적 해상도 스케일링**: 사용자의 디바이스/GPU 성능을 감지하여 렌더링되는 파티클 개수를 동적으로 조절 (High: 200만, Mid: 100만, Low: 50만). 저사양 기기는 비디오 루프로 Fallback.

## 3. 3D 시각화 및 오브젝트 스펙 (3D Visual Specifications)
영상의 압도적인 미학을 수학적, 그래픽적으로 구현하기 위한 필수 조건입니다.

### A. 중앙 AI Data Sculpture (유체/파티클 덩어리)
- **형태 및 알고리즘**: 단순한 점들의 모임이 아님. Vector Field와 Curl Noise 알고리즘을 결합하여, 실처럼 가늘고 날카로운 줄기(Tendrils)들이 얽히고설키는 유기적인 해류/난류(Turbulence) 시뮬레이션 구현.
- **컬러 및 블렌딩**: 네온 마젠타, 시안, 에메랄드 그린 색상 베이스. 파티클이 겹칠수록 빛이 강해지는 가산 혼합(Additive Blending) 모드 적용.
- **포스트 프로세싱 (Bloom)**: 밀도가 높아지는 중앙부는 입자들이 뭉치면서 완전히 순백색(White blown-out)으로 타오르는 강력한 블룸(Bloom) 효과 필수.
- **애니메이션 리듬**: 20초 사이클(루프) 동안 팽창과 수축을 반복. 이때 단순히 일정한 속도가 아니라, 비선형 가감속(Non-linear Easing) 함수를 적용해 생물처럼 호흡하는 듯한 속도 변화(Velocity variation) 구현.

### B. 배경 (Background Data Universe)
- **심연 배경**: #000000 (Solid Black).
- **구형 와이어프레임 (Spherical Grid)**: 중앙 오브젝트를 감싸는 거대한 3D 구체. 지구본의 위도/경도(Latitude/Longitude) 선처럼 교차하는 얇은 점선 형태.
- **페이드 아웃 효과**: 카메라 뷰포트 가장자리(Edge)로 갈수록 선명도가 떨어지도록 Radial Gradient Mask (알파 채널 페이드 아웃)를 적용하여 심연으로 사라지는 우주적 공간감 연출.

## 4. HUD 인터페이스 및 레이아웃 (UI/UX Layer)
웹 UI가 3D 공간과 이질감 없이, 공상과학 영화의 전술 인터페이스처럼 융합되어야 합니다.

### 타이포그래피 룰:
- **폰트**: Roboto Mono 또는 Space Mono (Regular/Light). 폰트 크기 10px~12px 고정.
- **컬러**: 완전한 흰색이 아닌 눈부심을 방지하는 오프화이트 (#F0F0F0).
- **시각적 분리**: 데이터 라벨(Light/Opacity 0.7)과 데이터 값(Regular/Opacity 1.0)의 굵기/투명도 차이 두기.

### 동적 지시선 (Dynamic Callout Lines) - [핵심 인터랙션]:
- 화면 외곽에 고정된 텍스트 박스(MEDIUM, RESOLUTION 등)에서 중앙 3D 오브젝트를 향해 1px 두께의 얇은 지시선 연결.
- **3D 좌표 트래킹**: 이 선의 끝점은 중앙 3D 모델 표면의 특정 노드(Vector3 좌표)에 매핑되어야 함. Raycasting 및 World-to-Screen 변환을 사용하여 파티클이 요동치거나 뷰가 바뀔 때 선이 실시간으로 따라가야 함.
- **앵커 닷 (Anchor Dot)**: 지시선이 오브젝트와 닿는 끝부분에 아주 작은 원형 점을 렌더링하여 타겟팅 느낌 강화.

### 정적 레이아웃 배치:
- **Top Left**: DATA PAINTINGS & SCULPTURES (세로 쓰기, vertical-rl 적용).
- **Top Right**: UNSUPERVISED (가장 크게), 그 아래 DATA UNIVERSE — MoMA.
- **Bottom Left**: REFIK ANADOL STUDIO 텍스트와 좌측에 스튜디오 공식 SVG 심볼 아이콘 정렬.
- **Bottom Right**: PLATFORM, EDITION 등의 스펙 데이터 블록. 줄 간격(line-height)을 1.2로 좁게 설정하여 기계적인 전산 출력물 느낌 강조.

## 5. 유저 인터랙션 (User Interaction)
- **마우스 패럴랙스 (Mouse Parallax)**: 마우스 커서의 X, Y 움직임에 따라 3D 카메라가 미세하게 반대 방향으로 패닝(Panning)되어 화면의 깊이감(Depth)을 극대화. 유저가 화면을 드래그해 임의로 줌인/아웃 하는 것은 차단.
- **Call to Action (CTA)**: 원본엔 없으나 랜딩 페이지 목적 달성을 위해 화면 중앙 하단 또는 우측 하단에 아웃라인 스타일(투명 배경, 얇은 선)의 버튼(VIEW ON FERALFILE) 배치.
