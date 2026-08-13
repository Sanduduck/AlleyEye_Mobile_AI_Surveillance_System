# **AlleyEye**

### 객체 탐지 기반 드론 감시 시스템 — 대회 제출용 (팀 코드톡톡)

**배포 사이트** : https://sanduduck.github.io/Creative-Problem-Solving-Project/
**발표 영상** : https://youtu.be/EXQxIe49wd0?si=oCB2ey8aT9CxMtu7

---

## 📌 프로젝트 소개

* 고정형 CCTV의 사각지대를 보완하는 **이동형 CCTV 기반 실시간 감시 프로토타입**입니다. (실증 플랫폼: RC카 → 드론 확장 대상)
* **배경:** 서울은 1제곱마일당 618대의 CCTV로 세계 2위 밀도임에도 사각지대를 노린 범죄가 지속되고 있으며, 창원시 도심 공원의 약 55%가 CCTV 사각지대로 방치되어 있습니다. 이동형 감시로 이 공백을 메우는 것이 목표입니다.
* 실시간 스트림 관제(지도 + 이미지 스트림), 캡처·녹화, 클라이언트 측 암호화(.venc), 스테가노그래피 기반 키 표현 기능을 포함합니다.
* 창의문제해결 경진대회(심사용) 제출용으로 **간결·직관적 시연**에 초점을 맞춘 데모 저장소입니다.
* 로컬 정적 서버(HTTP)로 실행하여 심사요청 흐름을 즉시 시연할 것을 권장합니다.

---

## 📖 목차

1. [팀원 구성](#팀원-구성)
2. [개발 기간 & 담당](#-개발-기간)
3. [사용 기술](#️-사용-기술)
4. [주요 기능](#-주요-기능)
5. [🧠 기술적 의사결정](#-기술적-의사결정-technical-decisions) ← *왜 이 기술을 선택했는가 (6건)*
6. [프로젝트 구조](#-프로젝트-구조-권장)
7. [결과 스크린샷](#-결과-시연용-스크린샷--기능도)
8. [스테가노그래피 & 암호화 상세](#-스테가노그래피자체-제작-알고리즘--암호화--기술-상세)
9. [파일별 역할](#-파일별-역할-간단-정리)
10. [FAQ](#-자주-발생하는-문제-faq)

---

## 팀원 구성

<div align="center">

|                                                           **팀장: 박동진**                                                          |                                                           **이승헌**                                                           |                                                        **오유진**                                                        |                                                                **김준영**                                                               |
| :----------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------: |
| [<img src="https://avatars.githubusercontent.com/Sanduduck" height=120 width=120> <br/> @Sanduduck](https://github.com/Sanduduck) | [<img src="https://avatars.githubusercontent.com/lico0531" height=120 width=120> <br/> @lico053](https://github.com/lico0531) | [<img src="https://avatars.githubusercontent.com/5u0612" height=120 width=120> <br/> @5u0612](https://github.com/5u0612) | [<img src="https://avatars.githubusercontent.com/Urban-Potato-717" height=120 width=120> <br/> @Urban-Potato-717](https://github.com/Urban-Potato-717) |

</div>

---

## 📅 개발 기간

* 25/05/25 \~ 진행중 (대회 제출용 브랜치 기준)

### 👨‍💻 개발 담당 (요약)

* **박동진 (팀장)** — BackEnd/FrontEnd 통합(웹앱 개발)
* **이승헌** — H/W·IoT
* **오유진** — FrontEnd 보조
* **김준영** — H/W 보조

---

* **H/W:** Raspberry Pi 5, CSI 카메라 · 웹캠, 서보/DC 모터 + 모터 드라이버, 초음파 센서
* **AI/영상 처리:** YOLOv5n (COCO 모델, `person` 탐지) + OpenCV, 추론은 Google Colab(GPU)로 분리
* **Backend:** Python (Flask)
* **Frontend:** HTML / CSS / JavaScript (웹·앱 공통), 앱은 Expo
* **Data:** JSON 기반 정적 데이터 (사용자·지역·장치 정보), 비밀번호는 해시+솔트 처리
* **실증 플랫폼:** RC카 (프로토타입) → 드론은 동일 구조 확장 대상

---

## 🎯 주요 기능

* **회원가입 / 로그인 / 권한 분기 (관리자/뷰어)** — 클라이언트 localStorage 기반 데모
* **CAPTCHA (info.html)** — Canvas 기반 사용자 검증 흐름
* **관제 대시보드 (cctv.html)**
  * Leaflet 지도에 드론(장치) 마커 표시
  * 지역별 드론 추가/삭제(관리자 전용)
  * MJPEG 이미지 스트림 뷰어, 프레임 캡처, 클라이언트 녹화(MediaRecorder)
  * 캡처/녹화 파일 → AES-GCM 암호화(.venc)로 저장
* **암호화 & 스테가툴 (en-decode.html)**
  * AES-GCM(256) + PBKDF2-SHA256(기본 310k iter) 기반 암/복호화
  * 스테가노그래피: 비밀번호 ↔ 사람이 읽기 쉬운 문장(가역/비가역 모드) 변환
* **스트림 테스트 (screen.html)** — 로컬/내부 네트워크 MJPEG 스트림 테스트 페이지

---

## 🧠 기술적 의사결정 (Technical Decisions)

단순히 "무엇을 썼는가"보다 **왜 그것을 선택했는가**를 기록합니다. 각 항목은 하드웨어·성능 제약과 트레이드오프를 근거로 결정되었습니다.

<details>
<summary><b>1. AI 추론 위치 — 왜 라즈베리파이가 아닌 Colab에서 추론하는가</b></summary>

<br>

**문제**
CSI 카메라로 받은 실시간 영상을 YOLOv5n으로 처리했는데, **모든 연산(객체 탐지 + 모터 제어)을 라즈베리파이 5 단일 장치에서 수행**하니 CPU 성능 한계로 과부하가 걸렸습니다. 그 결과 영상이 끊기고, **객체 탐지 연산 중에는 서보모터 제어가 멈추거나 버벅이는** 동시성 문제가 발생했습니다.

**분석**
병목의 원인은 *연산 집약적인 AI 추론*과 *실시간 하드웨어 제어*가 한 장치의 CPU를 두고 경쟁하는 구조 자체였습니다.

**선택한 구조 — 추론 오프로딩**
연산 집약적인 객체 탐지 모듈을 **외부 GPU 자원(Google Colab)으로 분리**했습니다.
* **라즈베리파이(Edge)** — 영상 캡처, 모터 제어, 웹 전송에 집중
* **Colab(추론 서버)** — 영상 스트림을 받아 YOLOv5n 추론 후 **결과값만 반환**

**결과 / 트레이드오프**
라즈베리파이가 제어·전송에만 집중하게 되어 **시스템 안정성과 반응성**이 향상되었습니다. 대신 추론이 네트워크에 의존하게 되는 트레이드오프가 생겼습니다.

</details>

<details>
<summary><b>2. 카메라 구성 — 왜 카메라를 2대 썼는가</b></summary>

<br>

**문제**
추론을 Colab으로 분리한 뒤에도, **하나의 카메라 모듈로 (1) Colab 추론 서버 전송과 (2) 웹사이트 실시간 스트리밍을 동시에** 처리하려니 다시 성능 문제가 발생했습니다.

**검토**
단일 카메라의 프레임을 두 경로로 분기하는 방식은 인코딩·전송 부하가 겹쳐 양쪽 모두 품질이 떨어졌습니다.

**해결**
동일 방향을 촬영하는 **웹캠을 추가해 카메라 2대를 운용**하고, 역할을 물리적으로 분리했습니다.
* **CSI 카메라** → 추론 서버(Colab) 전송용
* **웹캠** → 웹서버 스트리밍용

**결과**
두 기능이 서로 간섭하지 않게 되어 **추론과 스트리밍을 동시에 안정적으로** 수행할 수 있었습니다. 하드웨어를 한 개 더 쓰는 비용으로 소프트웨어 병목을 해소한 실용적 선택이었습니다.

</details>

<details>
<summary><b>3. 영상 전송 방식 — 왜 MJPEG인가</b></summary>

<br>

**문제**
라즈베리파이의 성능 한계로 **고화질 영상 실시간 인코딩과 AI 추론을 동시에** 수행하자 심각한 프레임 저하·지연이 발생했습니다. 원격 제어까지 얹으면 지연(Latency)이 조작감을 해쳤습니다.

**검토한 대안**

| 방식 | 특징 | 제약 |
| --- | --- | --- |
| 전체 영상 인코딩(H.264 등) | 대역폭 효율 우위 | 저사양 기기에서 인코딩 부하 큼 |
| **MJPEG (프레임별 JPEG)** | 구현 단순, 클라이언트 부담 적음 | 대역폭 효율은 낮음 |

**선택 이유**
각 프레임을 JPEG로 압축해 HTTP 응답으로 연속 전송하는 **MJPEG**을 채택했습니다. Flask 등 웹 프레임워크에서 **구현이 쉽고**, 브라우저 측 부담이 적어 **저사양 하드웨어에서도 안정적인 스트리밍과 낮은 반응 지연**을 확보할 수 있었습니다. 별도 플러그인 없이 뷰잉·캡처가 가능한 점도 이점이었습니다.

</details>

<details>
<summary><b>4. 객체 탐지 모델 — 왜 YOLOv5n(nano)인가</b></summary>

<br>

**문제**
이동형 CCTV의 연산 자원은 **Raspberry Pi 5** 기반으로 제한적이었고, 오탐(낙엽·비·눈 등)을 줄이면서 **사람만 정확히** 탐지해야 했습니다.

**선택**
* **경량 모델(nano)** — 제한된 엣지 환경에서 실시간 추론이 가능하도록 YOLOv5n을 채택했습니다.
* **탐지 대상 한정** — COCO 모델에서 `person`(index 0)만 탐지하도록 설계해, 비인간 객체로 인한 오탐을 구조적으로 차단했습니다.

**결과 / 트레이드오프**
정확도를 일부 양보하는 대신 **실시간성**과 **오탐 최소화**를 확보했습니다. 중간 간담회에서 제기된 "군 시설 CCTV의 잦은 오탐" 우려에 대해, `person` 한정 설계로 대응할 수 있음을 근거로 제시했습니다.

</details>

<details>
<summary><b>6. 데이터 저장 — 왜 별도 DB 서버 없이 JSON인가</b></summary>

<br>

**문제**
초기에는 사용자·지역·장치 데이터를 JavaScript 코드 안에 **하드코딩**해 관리했습니다. 데이터와 로직이 뒤섞여 값 하나만 바꿔도 코드를 직접 수정해야 했고, 확장이 번거로웠습니다.

**검토한 대안**

| 선택지 | 특징 | 제약 |
| --- | --- | --- |
| 코드 내 하드코딩 | 초기 구현 빠름 | 데이터·로직 결합, 유지보수 어려움 |
| RDB / MongoDB 서버 | 대규모·동시성에 유리 | 별도 서버 구동·운영 필요, 심사 시연 환경에 과함 |
| **JSON 외부화** | 정적 서버만으로 동작, 구조 단순 | 대규모 동시 쓰기엔 부적합 |

**선택 이유**
* 데이터를 코드에서 분리해 **JSON으로 외부화**함으로써 로직과 데이터를 나눠 **유지보수성**을 확보했습니다.
* 본 시스템은 **모든 처리를 클라이언트(브라우저)에서 수행하는 서버 독립형 아키텍처**를 지향합니다. 별도 DB 서버를 두면 이 원칙과 어긋나고, 심사·시연 환경에선 오히려 구동 부담만 커집니다.
* 데이터 규모가 데모 수준(장치·지역 목록 등)이라 **정적 JSON만으로 충분**했고, GitHub Pages 같은 정적 호스팅에 그대로 배포할 수 있었습니다.

**결과 / 트레이드오프**
DB 서버 없이 **정적 배포·즉시 시연**이 가능해졌습니다. 대신 대규모 동시 쓰기나 복잡한 쿼리가 필요한 실서비스 단계에서는 RDB/NoSQL로의 이관이 필요합니다.

</details>

<details>
<summary><b>5. 암호화 — AES-256-GCM + 자체 스테가노그래피 병행</b></summary>

<br>

**문제**
캡처·녹화 파일을 안전하게 저장하고, 비밀번호가 서버에 평문으로 노출되지 않아야 했습니다. 동시에 키(비밀번호)를 **전달·기억하기 쉬운 형태**로 다룰 필요가 있었습니다.

**선택 — 암호화**
* **AES-256-GCM** — 기밀성과 **무결성(인증 태그)** 을 함께 제공하는 AEAD 방식으로, 전송·저장 중 변조를 즉시 탐지할 수 있습니다.
* **키 유도: PBKDF2-SHA256 (310,000 반복)** — 짧은 비밀번호도 강한 키로 확장하여 **무차별 대입·사전 공격에 대한 저항성**을 높였습니다.

**선택 — 스테가노그래피 (자체 설계)**
암호화와 스테가노그래피를 **함께 적용하면 보안성이 강화된다는 선행 연구**에 착안해, 비밀번호를 **2비트 단위로 다국어 토큰에 매핑하여 자연어 문장으로 변환**하는 알고리즘을 직접 설계·구현했습니다.
* **표현 모드** — SHA-256 시드 기반 PRNG, 버킷 교차, 반복 회피, 구두점 장식으로 추측 난도를 높인 문장형 출력 생성
* **가역 모드** — 헤더(`"ST"|Len`)와 고정 매핑으로 원본 비밀번호를 정확히 복원
* 실제 암호 강도를 저하시키지 않으면서 **전달·기억·사회공학 공격 저항성**을 강화하는 보조 계층

> **착안한 선행 연구**
> * *An improved hybrid image steganography method using AES algorithm*, **Scientific Reports (Nature)**, 2025.
> * *Cryptosystem for Secure Data Transmission using AES and Steganography*, 2019.
>
> ※ 위 연구들은 주로 LSB **이미지** 스테가노그래피 기반입니다. 본 프로젝트는 "암호화 + 스테가 결합이 단일 기법 대비 보안을 강화한다"는 결론에 착안하되, 이미지가 아닌 **비밀번호↔문장 변환** 방식으로 팀이 독자 설계했습니다.

**결과 / 트레이드오프**
모든 암·복호화가 **브라우저(클라이언트) 내에서만** 수행되어 민감 데이터가 서버에 평문으로 남지 않습니다. 실사용 시에는 키 전달 경로, 반복수, salt/iv 관리 정책을 추가로 강화해야 합니다.

</details>

---

## 📂 프로젝트 구조 (권장)

```
/ 📂(repo root)
├─ README.md
├─ 📂index                 # 인트로 페이지
│   └─ index.html, index.css, index.js
├─ 📂main                  # 허브 / 프로젝트 소개 페이지
│   └─ main.html, main.css, main.js
├─ 📂login                 # 회원가입 / 로그인 (localStorage + PBKDF2)
│   └─ login.html, login.css, login.js
├─ 📂info                  # CAPTCHA 및 심사용 안내 페이지
│   └─ info.html, info.css, info.js
├─ 📂cctv                  # 관제 대시보드 (지도, 스트림, 캡처/녹화, 암호화)
│   └─ cctv.html, cctv.css, cctv.js
├─ 📂en-decode             # 암복호화 & 스테가 도구
│   └─ en-decode.html, en-decode.css, en-decode.js
├─ 📂screen                # 스트림 테스트 (MJPEG)
│   └─ screen.html, screen.css, screen.js
├─ 📂images/               # 모든 이미지(logo, 기능도, 스크린샷, etc)
└─ 📂docs/                 # 제출 파일
   └─ 최종 보고서.docx
   └─ 최종 PPT.docx
   └─ 최종 영상 제출.docx
```

> **주의사항** — 모든 이미지는 `./images/`에 넣고 HTML에서 `./images/filename.png`로 참조하세요. (대소문자 구분)

---

## 📷 결과 (시연용 스크린샷 / 기능도)

### **index (인트로)**
<img width="1920" height="1080" alt="index" src="https://github.com/user-attachments/assets/7a699b59-e770-4217-bf6c-9c5d97dc59d5" />

### **main (허브)**
<img width="1920" height="1080" alt="main-1" src="https://github.com/user-attachments/assets/9ba82681-7ebb-449c-89aa-347413ebb13c" />
<img width="1920" height="1080" alt="main-2" src="https://github.com/user-attachments/assets/c1bdb499-3cfe-45b4-a9cf-185a9fe01fe2" />
<img width="1920" height="1080" alt="main-3" src="https://github.com/user-attachments/assets/c173e6db-c673-460b-bb90-9995b0b7c19b" />

### **login (회원가입 / 로그인 흐름) & info (보안 페이지)**
<img width="1920" height="1080" alt="login-1" src="https://github.com/user-attachments/assets/23dffffd-5378-41e1-babe-833243a94c2f" />
<img width="1918" height="1078" alt="login-2" src="https://github.com/user-attachments/assets/5d322584-6af2-4212-9c8e-e1f6d0ff5aff" />
<img width="1920" height="1080" alt="info-1" src="https://github.com/user-attachments/assets/ed8988cd-7942-45da-aafa-565b7d262038" />
<img width="1920" height="1080" alt="info-2" src="https://github.com/user-attachments/assets/2ca545e2-4781-46ec-91b6-c0e31fb35359" />

### **cctv (관제 대시보드)**
<img width="1920" height="1080" alt="cctv-1" src="https://github.com/user-attachments/assets/4898d66d-e45d-4a85-a901-04c7c99c92c9" />
<img width="1920" height="1080" alt="cctv-2" src="https://github.com/user-attachments/assets/98c0551c-ffd5-4332-9142-0c9eca944cca" />
<img width="1920" height="1080" alt="cctv-3" src="https://github.com/user-attachments/assets/0fcf27e6-67d6-46d5-ba86-e6903e5e3ce1" />

### **en-decode (암/복호화 툴)**
<img width="1920" height="1080" alt="en-decode-1" src="https://github.com/user-attachments/assets/c335d74b-5a61-4cea-a5ba-df00532f7120" />
<img width="1920" height="1080" alt="en-decode-2" src="https://github.com/user-attachments/assets/075dcd6c-5347-41bf-9bac-57d1d8e33688" />

---

## 🧾 스테가노그래피(자체 제작 알고리즘) & 암호화 — 기술 상세

### 암호화 요약

* **대칭:** AES-GCM (256-bit)
* **키 유도:** PBKDF2-SHA256 (기본 반복수: 310,000 — 데모/심사용)
* **파일 포맷(.venc):** 고유 매직(`VIDENC01`) + 파라미터(alg id, iter, salt len, iv len, name/mime len) + salt + iv + 파일명/메타 + 암호문
* **복호화:** 동일 비밀번호 필요, en-decode 도구에서 복호화 가능

### 스테가노그래피 (팀 제작)

* **목적:** 암호(또는 키)를 사람이 읽기 쉬운 문장(또는 단어열)로 변환해 전달성을 높임
* **동작 모드:**
  * **가역 (Reversible)**: 문장 ↔ 원래 비밀번호 상호 변환 가능 (심사/운영용)
  * **비가역 (Irreversible)**: 문장에서 키만 파생, 원본 비밀번호는 복원 불가 (보안 지향)
* **활용 예시:** 캡처 파일 암호화를 위해 운영자가 심사자에게 "가독 문장"으로 비밀번호 전달 → 심사자는 en-decode에서 동일한 문장으로 복호화 / 가역 모드 사용 권장
* **보안 주의:** 실사용 시 키 관리(전달 경로), 반복수, salt/iv 관리 정책을 강화해야 함.

---

## 🔧 파일별 역할 (간단 정리)

* `main.*` — 심사용 허브(소개 + 이동 버튼)
* `login.*` — 로컬 회원가입/로그인 (PBKDF2 localStorage 데모)
* `info.*` — CAPTCHA(사람 검증) / 심사용 플로우 연결
* `cctv.*` — 관제 대시보드(지도, 스트림, 캡처/녹화, 암호화 저장)
* `en-decode.*` — 암복호화 툴 + 스테가 전환 툴
* `screen.*` — 스트림(MJPEG) 연결 테스트 페이지
* `images/` — UI 리소스, 기능도, 스크린샷 등
* `docs/` — 상세 보고서/발표 자료

---

## ❗ 자주 발생하는 문제 (FAQ)

<details>
<summary><b>Q. 페이지를 폴더로 옮겼더니 이동이 안 됩니다.</b></summary>

HTML 내부의 `href`, `window.location.href`, 이미지 `src`는 **상대경로 기준**입니다. 파일 이동 시 경로(예: `../main.html`)를 수정하세요.
</details>

<details>
<summary><b>Q. 이미지가 보이지 않습니다.</b></summary>

`images/`에 파일이 있는지, 파일명·대소문자가 일치하는지 확인하세요.
</details>

<details>
<summary><b>Q. WebCrypto/crypto.subtle 관련 오류가 발생합니다.</b></summary>

`crypto.subtle.importKey` 등 WebCrypto 기능은 **HTTPS 환경(또는 localhost)** 에서 동작 권장합니다. 로컬 테스트는 `http://localhost`로 실행하거나 최신 브라우저 사용을 권장합니다.
</details>

<details>
<summary><b>Q. 스트림(CORS) 또는 캡처가 실패합니다.</b></summary>

스트림 서버에 `Access-Control-Allow-Origin` 헤더가 필요할 수 있습니다. 또는 HTTPS 페이지에서 HTTP 스트림을 불러오면 Mixed Content로 차단됩니다(HTTPS 페이지는 HTTP 스트림 차단).
</details>
