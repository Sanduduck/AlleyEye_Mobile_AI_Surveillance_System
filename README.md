# AlleyEye — 객체 탐지 기반 이동형 CCTV 감시 시스템

> 고정형 CCTV의 사각지대를 이동형 CCTV로 메우는 실시간 감시 프로토타입.
> AI 객체 탐지 · 웹 관제 대시보드 · 자체 암호화 툴을 하나의 시스템으로 통합했습니다.

![HTML](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)
![YOLO](https://img.shields.io/badge/YOLOv5n-00FFFF?style=flat&logo=yolo&logoColor=black)
![Raspberry Pi](https://img.shields.io/badge/Raspberry%20Pi%205-A22846?style=flat&logo=raspberrypi&logoColor=white)

🔗 **데모** : https://sanduduck.github.io/Creative-Problem-Solving-Project/ &nbsp;|&nbsp; 🎬 **발표 영상** : https://youtu.be/EXQxIe49wd0

---

## 한눈에 보기

| | |
| --- | --- |
| **무엇** | 이동형 CCTV가 자율 순찰하며 사람을 탐지·추적하고, 웹/앱에서 실시간 관제하는 감시 시스템 |
| **왜** | 서울은 세계 2위 CCTV 밀도(1제곱마일당 618대)임에도 사각지대 범죄가 지속. 이동형 감시로 공백을 보완 |
| **핵심 기술** | YOLOv5n 객체 탐지(추론은 Colab GPU로 분리) · MJPEG 실시간 스트리밍 · AES-256-GCM 암호화 + 자체 스테가노그래피 |
| **구성** | 라즈베리파이 5 + 카메라 2대 + 모터 (H/W) &nbsp;/&nbsp; Flask + 정적 웹·앱 (S/W) |
| **실증** | RC카 프로토타입 (드론은 동일 구조 확장 대상) |
| **기간 / 형태** | 2025.05 ~ · 4인 팀 프로젝트 (창의문제해결 경진대회 제출작) |

---

## 👤 내 역할 (박동진, 팀장)

프로젝트 기술 설계를 총괄하고, 소프트웨어 전반을 직접 구현했습니다.

- **Colab 추론 연동 / 서버 구조 설계** — 라즈베리파이 단일 처리의 연산 병목을 분석하고, YOLOv5n 추론을 외부 GPU(Google Colab)로 분리하는 구조를 설계·연동했습니다.
- **암호화 툴 개발** — AES-256-GCM + PBKDF2 기반 파일 암·복호화와, 비밀번호를 자연어 문장으로 변환하는 **자체 스테가노그래피 알고리즘**을 직접 설계·구현했습니다.
- **웹/앱 프론트엔드 전반** — 로그인·CAPTCHA·관제 대시보드·암호화 도구까지 전 페이지의 UI와 클라이언트 로직을 담당했습니다.

---

## 🎯 주요 기능

| 페이지 | 기능 |
| --- | --- |
| **로그인 / CAPTCHA** | 해시+솔트 기반 인증, Canvas 자체 CAPTCHA, 관리자/뷰어 권한 분리 |
| **관제 대시보드** | Leaflet 지도에 장치 마커 표시, MJPEG 실시간 스트림 뷰어, 프레임 캡처 & 녹화 |
| **암호화 툴** | 캡처 파일 AES-256-GCM 암·복호화(.venc), 비밀번호 ↔ 문장 스테가 변환 |
| **객체 탐지 (H/W)** | YOLOv5n으로 `person` 탐지 → 인식된 대상 자동 추적 (모터 제어) |

<br>

<div align="center">

**관제 대시보드**
<img width="800" alt="cctv" src="https://github.com/user-attachments/assets/4898d66d-e45d-4a85-a901-04c7c99c92c9" />

**암호화 & 스테가 툴**
<img width="800" alt="en-decode" src="https://github.com/user-attachments/assets/c335d74b-5a61-4cea-a5ba-df00532f7120" />

<sub>더 많은 화면은 아래 <a href="#-전체-스크린샷">전체 스크린샷</a>에서 확인할 수 있습니다.</sub>

</div>

---

## 🛠️ 기술적으로 신경 쓴 점

제한된 하드웨어(라즈베리파이 5) 위에서 실시간성·안정성을 확보하기 위해 아래와 같은 문제를 해결했습니다. *(자세한 배경은 각 항목을 펼쳐서 확인)*

**⚡ 연산 병목 → 추론 오프로딩**
객체 탐지와 모터 제어를 라파에서 동시에 돌리자 서보모터가 멈추는 문제 발생 → **AI 추론을 Google Colab(GPU)으로 분리**하고 라파는 제어·전송에 집중.

**📷 카메라 1대의 한계 → 2대 운용**
한 카메라로 추론 전송과 웹 스트리밍을 겹치니 성능 저하 → **웹캠을 추가해 역할 분리**(추론용 / 스트리밍용).

**🔐 보안 → 표준 암호화 + 자체 알고리즘**
AES-256-GCM(무결성 보장) + PBKDF2로 키 강화, 여기에 **비밀번호를 자연어 문장으로 바꾸는 스테가노그래피를 직접 설계**해 전달성과 공격 저항성을 함께 확보.

<details>
<summary><b>🔍 기술적 의사결정 상세 보기 (문제 → 해결 → 트레이드오프)</b></summary>

<br>

### 1. AI 추론 위치 — 왜 라즈베리파이가 아닌 Colab에서 추론하는가

**문제** — CSI 카메라 영상을 YOLOv5n으로 처리하며 **객체 탐지 + 모터 제어를 라파 5 단일 장치에서 수행**하니 CPU 과부하로 영상이 끊기고, 탐지 연산 중 서보모터 제어가 멈추는 동시성 문제가 발생했습니다.

**해결** — 연산 집약적인 추론 모듈을 **외부 GPU(Google Colab)로 분리**. 라즈베리파이는 캡처·모터 제어·전송에 집중하고, Colab은 영상 스트림을 받아 추론 결과값만 반환하도록 구조화했습니다.

**트레이드오프** — 시스템 안정성·반응성이 향상된 대신, 추론이 네트워크에 의존하게 되었습니다.

---

### 2. 카메라 구성 — 왜 2대인가

**문제** — 추론을 Colab으로 분리한 뒤에도, **한 카메라로 (1) Colab 추론 전송 + (2) 웹 실시간 스트리밍을 동시에** 처리하려니 인코딩·전송 부하가 겹쳐 양쪽 품질이 떨어졌습니다.

**해결** — 동일 방향을 촬영하는 **웹캠을 추가해 2대로 역할 분리**. CSI 카메라는 추론 서버 전송용, 웹캠은 웹 스트리밍용.

**트레이드오프** — 하드웨어 1개를 추가하는 비용으로 소프트웨어 병목을 해소한 실용적 선택.

---

### 3. 영상 전송 방식 — 왜 MJPEG인가

**문제** — 저사양 라파에서 고화질 영상 인코딩과 AI 추론을 동시에 수행하자 프레임 저하·지연이 심했고, 원격 제어까지 얹으면 조작 지연이 컸습니다.

**해결** — 프레임별 JPEG를 HTTP로 연속 전송하는 **MJPEG** 채택. Flask에서 구현이 쉽고 브라우저 부담이 적어, 저사양에서도 안정적 스트리밍과 낮은 지연을 확보. 플러그인 없이 뷰잉·캡처 가능.

**트레이드오프** — 대역폭 효율은 H.264 등보다 낮지만, 구현 단순성과 저지연을 우선.

---

### 4. 객체 탐지 모델 — 왜 YOLOv5n인가

**문제** — 라파 5 기반의 제한된 자원에서, 오탐(낙엽·비·눈 등)을 줄이면서 사람만 정확히 탐지해야 했습니다.

**해결** — 경량 모델 **YOLOv5n**으로 실시간 추론을 확보하고, COCO 모델에서 **`person`(index 0)만 탐지**하도록 한정해 비인간 객체 오탐을 구조적으로 차단.

**트레이드오프** — 정확도를 일부 양보하는 대신 실시간성과 오탐 최소화를 확보. 간담회에서 제기된 "잦은 오탐" 우려에 `person` 한정 설계로 대응.

---

### 5. 암호화 — AES-256-GCM + 자체 스테가노그래피

**문제** — 캡처 파일을 안전하게 저장하고 비밀번호가 평문 노출되지 않으면서, 키를 전달·기억하기 쉽게 다뤄야 했습니다.

**해결 (암호화)** — **AES-256-GCM**(기밀성 + 인증 태그로 무결성 보장) + **PBKDF2-SHA256 310k 반복**으로 짧은 비밀번호도 강한 키로 확장.

**해결 (스테가노그래피, 자체 설계)** — 비밀번호를 **2비트 단위로 다국어 토큰에 매핑해 자연어 문장으로 변환**하는 알고리즘을 직접 구현. SHA-256 시드 PRNG·버킷 교차·반복 회피로 추측 난도를 높이고(표현 모드), 헤더(`"ST"|Len`)와 고정 매핑으로 원본 복원(가역 모드).

> **착안한 선행 연구** — *An improved hybrid image steganography method using AES algorithm* (Scientific Reports, 2025) 등. 해당 연구들은 LSB **이미지** 스테가 기반이며, 본 프로젝트는 그 결론("암호화+스테가 결합이 보안을 강화")에 착안해 **비밀번호↔문장 변환** 방식으로 독자 설계했습니다.

**트레이드오프** — 모든 암·복호화가 브라우저(클라이언트)에서만 수행돼 서버에 평문이 남지 않습니다. 실서비스에선 키 전달 경로·반복수·salt/iv 관리 정책 강화 필요.

---

### 6. 데이터 저장 — 왜 DB 서버 없이 JSON인가

**문제** — 초기엔 사용자·지역·장치 데이터를 JS 코드에 하드코딩 → 데이터·로직이 결합돼 유지보수가 어려웠습니다.

**해결** — 데이터를 **JSON으로 외부화**. 본 시스템은 모든 처리를 클라이언트에서 수행하는 **서버 독립형 아키텍처**를 지향하므로, 별도 DB를 두는 대신 정적 JSON으로 일관성을 유지하고 GitHub Pages 정적 배포까지 가능하게 했습니다.

**트레이드오프** — 데모 규모엔 충분하나, 대규모 동시 쓰기·복잡한 쿼리가 필요한 실서비스에선 RDB/NoSQL 이관 필요.

</details>

---

## 🧩 시스템 구조

```
[이동형 CCTV — 라즈베리파이 5]
   ├─ CSI 카메라 ──▶ Google Colab (YOLOv5n 추론) ──▶ 탐지 결과 반환 ──▶ 모터 제어(자동 추적)
   └─ 웹캠 ────────▶ Flask 웹서버 (MJPEG) ──▶ 실시간 스트림
                                              │
[웹 / 앱 관제]  로그인 → CAPTCHA → 대시보드 ◀──┘
                                   └─ 캡처/녹화 → AES-256-GCM 암호화(.venc) → en-decode 툴에서 복호화
```

---

## 📁 프로젝트 구조

```
repo root
├─ index/       인트로 페이지
├─ main/        허브 / 소개 페이지
├─ login/       회원가입 · 로그인 (해시+솔트)
├─ info/        CAPTCHA 검증 페이지
├─ cctv/        관제 대시보드 (지도 · 스트림 · 캡처/녹화 · 암호화)
├─ en-decode/   암·복호화 & 스테가 도구
├─ screen/      MJPEG 스트림 테스트
├─ images/      스크린샷 · 기능도
└─ docs/        보고서 · 발표 자료
```

---

## 👥 팀 구성

<div align="center">

| 팀장: 박동진 | 이승헌 | 오유진 | 김준영 |
| :---: | :---: | :---: | :---: |
| [<img src="https://avatars.githubusercontent.com/Sanduduck" height=100 width=100><br/>@Sanduduck](https://github.com/Sanduduck) | [<img src="https://avatars.githubusercontent.com/lico0531" height=100 width=100><br/>@lico0531](https://github.com/lico0531) | [<img src="https://avatars.githubusercontent.com/5u0612" height=100 width=100><br/>@5u0612](https://github.com/5u0612) | [<img src="https://avatars.githubusercontent.com/Urban-Potato-717" height=100 width=100><br/>@Urban-Potato-717](https://github.com/Urban-Potato-717) |
| 기술 설계 · S/W 구현 · 발표 | 아이디어 · 기술 조사 · H/W | 기획 · 기술 조사 · 발표자료 | 보고서 · 기술 조사 · 시각자료 |

</div>

---

## 📷 전체 스크린샷

<details>
<summary><b>펼쳐서 전체 화면 보기</b></summary>

<br>

**index (인트로)**
<img width="800" alt="index" src="https://github.com/user-attachments/assets/7a699b59-e770-4217-bf6c-9c5d97dc59d5" />

**main (허브)**
<img width="800" alt="main-1" src="https://github.com/user-attachments/assets/9ba82681-7ebb-449c-89aa-347413ebb13c" />
<img width="800" alt="main-2" src="https://github.com/user-attachments/assets/c1bdb499-3cfe-45b4-a9cf-185a9fe01fe2" />

**login & info (인증 흐름)**
<img width="800" alt="login-1" src="https://github.com/user-attachments/assets/23dffffd-5378-41e1-babe-833243a94c2f" />
<img width="800" alt="info-1" src="https://github.com/user-attachments/assets/ed8988cd-7942-45da-aafa-565b7d262038" />

**cctv (관제 대시보드)**
<img width="800" alt="cctv-2" src="https://github.com/user-attachments/assets/98c0551c-ffd5-4332-9142-0c9eca944cca" />
<img width="800" alt="cctv-3" src="https://github.com/user-attachments/assets/0fcf27e6-67d6-46d5-ba86-e6903e5e3ce1" />

**en-decode (암/복호화 툴)**
<img width="800" alt="en-decode-2" src="https://github.com/user-attachments/assets/075dcd6c-5347-41bf-9bac-57d1d8e33688" />

</details>

<details>
<summary><b>실행 / 트러블슈팅 FAQ</b></summary>

<br>

- **페이지 이동이 안 됨** — `href`·`src`가 상대경로 기준이라 파일 이동 시 경로 수정 필요.
- **이미지가 안 보임** — `images/` 내 파일명·대소문자 일치 확인.
- **WebCrypto 오류** — `crypto.subtle`은 HTTPS 또는 localhost에서 동작. 로컬은 `http://localhost` 권장.
- **스트림/캡처 실패** — 스트림 서버 CORS 헤더 필요. HTTPS 페이지에서 HTTP 스트림은 Mixed Content로 차단됨.

</details>
