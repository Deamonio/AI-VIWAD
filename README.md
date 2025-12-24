<div align="center">

# 🚦 AI-VIWAD

### Artificial Intelligence - Visual Impairment Walking Assistant Device

*AI 기반 시각장애인 보행 보조 장치*

![Python](https://img.shields.io/badge/Python-95.1%25-3776AB?style=flat&logo=python&logoColor=white)
![HTML](https://img.shields.io/badge/HTML-1.2%25-E34F26?style=flat&logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS-1.1%25-1572B6?style=flat&logo=css3&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-C++-00979D?style=flat&logo=arduino&logoColor=white)

![GitHub Stars](https://img.shields.io/github/stars/Deamonio/AI-VIWAD?style=social)
![GitHub Forks](https://img.shields.io/github/forks/Deamonio/AI-VIWAD?style=social)

**2023 CodeFair 출품작**

---

### 🎬 작동 영상

[![AI-VIWAD Demo](https://img.youtube.com/vi/JBitoZGlmSs/maxresdefault.jpg)](https://www.youtube.com/watch?v=JBitoZGlmSs)

**▶️ 클릭하여 시스템 작동 영상 보기**

*점자 블록 인식 + 신호등 AI 분석으로 안전한 보행을 돕는 AI-VIWAD*

---

### 📸 System Preview

![AI-VIWAD](README%20asses/profile_image.png)

*AI-VIWAD 시스템 - 맞춤 제작 신발 + 웹 인터페이스*

</div>

---

## 📋 목차

- [프로젝트 소개](#-프로젝트-소개)
- [시스템 아키텍처](#-시스템-아키텍처)
- [주요 기능](#-주요-기능)
- [하드웨어 구성](#-하드웨어-구성)
- [설치 방법](#-설치-방법)
- [사용 방법](#-사용-방법)
- [코드 분석](#-코드-분석)

---

## 🎯 프로젝트 소개

**AI-VIWAD (Visual Impairment Walking Assistant Device)**는 시각장애인이 **횡단보도를 안전하게 건널 수 있도록 돕는 AI 기반 보행 보조 장치**입니다. 

### 💡 개발 동기

> 시각 장애인들의 보행 중 사고 위험에 대한 인식을 높이고, **실제 인터뷰를 통해 문제의 심각성을 확인**한 후, 이를 해결하고자 AI 기반 보행 보조 장치와 웹 서비스를 개발하게 되었습니다. 

특히, 기존의 보행 보조 도구들이 제공하지 못하는 **실시간 정보 제공 및 사용자 맞춤형 지원**에 대한 필요성을 느껴 개발에 착수했습니다.

### 🌟 특징

- 👟 **점자 블록 인식**: 컬러 센서로 노란색 점자 블록 감지 → 진동 알림
- 🚦 **신호등 인식**: YOLOv5 AI 모델로 빨간불/파란불 실시간 분석
- 📱 **웹 인터페이스**: Flask + ESP32-CAM 실시간 영상 스트리밍
- 🔊 **음성 안내**: TTS로 신호등 상태 음성 알림
- 📡 **소켓 통신**: 아두이노(신발) ↔ 웹 서버 실시간 데이터 전송

### 👥 팀 구성

**팀 Makers**
- **조윤혁**: 팀장 & 전자 회로
- **송승현**: 전자 회로 & 소프트웨어
- **양예성**: 소프트웨어
- **김강현**: 웹 개발 (Assistant)

### 📅 제작 기간

**2023.04 ~ 2023.10** (6개월)

---

## 🏗️ 시스템 아키텍처

### 전체 구조

```
맞춤 제작 신발 (Arduino)
- 컬러 센서 (TCS34725)
- 진동 모터
- 소켓 통신 모듈
         |
         | Socket 통신
         ↓
Flask 웹 서버 (Python)
- YOLOv5 신호등 인식
- ESP32-CAM 영상 스트리밍
- TTS 음성 안내
         |
         | HTTP/Video Stream
         ↓
웹 인터페이스 (HTML/CSS/JS)
- 실시간 카메라 화면
- 신호등 상태 시각화
- 사용자 제어 버튼
```

---

## ✨ 주요 기능

### 1. 🟡 점자 블록 인식 (Arduino + 컬러 센서)

**컬러 센서 기반 점자 블록 감지**

**하드웨어:**
- **TCS34725 컬러 센서**: 노란색(점자 블록) 감지
- **진동 모터**: 촉각 피드백
- **Arduino Nano**: 메인 컨트롤러

**작동 원리:**

```
1. 컬러 센서가 바닥 색상 스캔
   ↓
2. RGB 값 분석
   ↓
3. 노란색 범위 감지 시 (점자 블록)
   ↓
4. 진동 모터 작동 (0.5초)
   ↓
5. 소켓으로 웹 서버에 알림 전송
```

**신발 맞춤 제작:**
- 신발 내부에 Arduino 보드 탑재 공간 가공
- 컬러 센서를 신발 밑창에 부착
- 방수 처리 및 배선 정리

![신발 내 장착](md%20-%20신발%20내%20장착.png)

---

### 2. 🚦 신호등 인식 (YOLOv5)

**AI 객체 감지 기반 신호등 상태 분석**

**YOLOv5 모델:**

```python
# app.py - YOLOv5 모델 로드
model = torch. hub.load('ultralytics/yolov5', 'custom', 
                        path='best.pt', force_reload=True)
model.conf = 0.4  # Confidence threshold
model.iou = 0.45  # IoU threshold
```

**신호등 감지 프로세스:**

```python
def generate_frames():
    cap = cv2.VideoCapture(URL + ":81/stream")  # ESP32-CAM
    
    while cap.isOpened():
        ret, img = cap.read()
        
        # YOLOv5 추론
        img_input = letterbox(img, img_size, stride=stride)[0]
        img_input = preprocess(img_input)
        pred = model(img_input, augment=False, visualize=False)[0]
        pred = non_max_suppression(pred, conf_thres, iou_thres)
        
        # 결과 처리
        if len(pred):
            for det in pred:
                label = int(det[5])  # 0: 빨간불, 1: 파란불
                if label == 0:
                    speak("빨간불입니다")
                elif label == 1:
                    speak("파란불입니다")
```

**학습 데이터:**
- **클래스**:  빨간불, 파란불
- **데이터셋**: 다양한 환경(낮/밤, 날씨)에서 촬영한 신호등 이미지
- **성능**:  Confidence 40%, IoU 45%

![신호등 이미지](md%20-%20욜로%20로고. jpg)

---

### 3. 🌐 웹 인터페이스 (Flask + ESP32-CAM)

**실시간 영상 스트리밍 웹 서비스**

**Flask 서버:**

```python
# app.py
from flask import Flask, render_template, Response

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/select_model', methods=['POST'])
def select_model():
    global select_mode
    select_mode = 1  # 신발 + AI 모드
    return render_template('shoe_model.html')

@app.route('/video_feed')
def video_feed():
    return Response(generate_frames(),
                    mimetype='multipart/x-mixed-replace; boundary=frame')
```

**ESP32-CAM 연동:**

```python
# ESP32-CAM IP 주소
URL = "http://192.168.0.100"  # 실제 IP로 변경 필요

# 해상도 설정
def set_resolution(url, index=10):
    try:
        requests.get(url + "/control? var=framesize&val=" + str(index))
    except:
        pass
```

**웹 UI:**

- **메인 페이지 (`index.html`)**: 시작 버튼 (안전실발 + AI)
- **카메라 페이지 (`shoe_model.html`)**: 실시간 영상 + 신호등 시각화

![웹 홈 화면](md%20-%20웹%20홈화면.png)
![웹 캠 화면](md%20-%20웹%20캠화면.png)

---

### 4. 🔊 음성 안내 (TTS)

**Text-to-Speech 음성 출력**

```python
import pyttsx3

engine = pyttsx3.init()

def speak(text):
    engine.say(text)
    engine.runAndWait()

# 사용 예시
speak("빨간불입니다.  기다려주세요")
speak("파란불입니다.  건너세요")
```

**음성 안내 시나리오:**
- 빨간불 감지 → "빨간불입니다"
- 파란불 감지 → "파란불입니다"
- 점자 블록 감지 → (진동만, 음성 X)

**개선 필요 사항:**
- 음성의 자연스러움 향상
- 배경 소음 환경에서의 명확성

---

### 5. 🔌 소켓 통신 (Arduino ↔ Flask)

**실시간 데이터 전송**

**통신 프로토콜:**

```
Arduino (신발)
   ↓ Socket 통신
   ↓ "1" (점자 블록 감지)
   ↓ "0" (감지 안 됨)
Flask 서버
   ↓ 데이터 수신
   ↓ YOLOv5 작동 여부 결정
   ↓ 웹 UI 업데이트
```

**Flask 소켓 서버:**

```python
import socket

server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('0.0.0.0', 8888))
server_socket.listen(1)

arduino_data = "0"

def handle_arduino():
    global arduino_data
    conn, addr = server_socket.accept()
    while True:
        data = conn. recv(1024).decode()
        if data: 
            arduino_data = data
            print(f"Arduino Data:   {arduino_data}")
```

---

## 🛠️ 하드웨어 구성

### BOM (Bill of Materials)

| 부품 | 수량 | 용도 | 비고 |
|------|------|------|------|
| Arduino Nano | 1 | 메인 컨트롤러 | 신발 내 장착 |
| TCS34725 | 1 | 컬러 센서 | 점자 블록 감지 |
| 진동 모터 | 1 | 촉각 피드백 | 0.5초 진동 |
| ESP32-CAM | 1 | 카메라 스트리밍 | 무선 IP 통신 |
| 맞춤 제작 신발 | 1 | 하드웨어 케이스 | 내부 가공 |
| 점퍼 와이어 | 다수 | 연결 | - |
| 배터리 | 1 | 전원 공급 | - |

---

### 🔌 회로 구성

**Arduino Nano 핀 연결:**

```
Arduino Nano
├── Digital Pin 2 → 진동 모터
├── SDA (A4) → TCS34725 SDA
├── SCL (A5) → TCS34725 SCL
├── TX → Socket 통신 모듈 RX
├── RX → Socket 통신 모듈 TX
├── 5V → 센서 전원
└── GND → 공통 접지
```

---

## 📦 설치 방법

### 1. 저장소 클론

```bash
git clone https://github.com/Deamonio/AI-VIWAD.git
cd AI-VIWAD
```

---

### 2. Python 의존성 설치

```bash
cd AI_Main

# 가상환경 생성 (선택)
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 패키지 설치
pip install -r requirements.txt
```

**requirements.txt:**
```txt
Flask>=2.2.0
torch>=1.10.0
torchvision>=0.11.0
opencv-python>=4.5.0
numpy>=1.21.0
pyttsx3>=2.90
requests>=2.26.0
```

---

### 3. YOLOv5 모델 준비

```bash
# YOLOv5 저장소 클론
git clone https://github.com/ultralytics/yolov5.git

# 학습된 모델 파일 (best.pt)을 AI_Main/ 에 배치
# (학습 방법은 YOLOv5 공식 문서 참조)
```

---

### 4. ESP32-CAM 설정

**Arduino IDE:**

```cpp
#include "esp_camera.h"
#include <WiFi.h>

const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";

void startCameraServer();

void setup() {
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
    }
    
    startCameraServer();
    Serial.print("Camera Stream:  http://");
    Serial.println(WiFi.localIP());
}
```

**스트리밍 URL:**
```
http://192.168.0.100:81/stream
```

---

### 5. Arduino 코드 업로드

```bash
# Arduino IDE에서 Arduino/ 폴더의 . ino 파일 열기
# 보드:  Arduino Nano
# 포트: COM3 (또는 /dev/ttyUSB0)
# 업로드
```

---

## 🚀 사용 방법

### 1️⃣ Flask 서버 실행

```bash
cd AI_Main
python app.py
```

**출력:**
```
 * Running on http://0.0.0.0:5000
Socket server listening on port 8888... 
```

---

### 2️⃣ 웹 브라우저 접속

```
http://localhost:5000/
```

**메인 페이지:**
- "안전실발 + AI" 버튼 클릭
- 카메라 페이지로 이동

---

### 3️⃣ 시스템 작동

**시나리오:**

1. **신발 착용**
   ```
   Arduino 전원 ON
   컬러 센서 활성화
   소켓 서버 연결
   ```

2. **점자 블록 감지**
   ```
   노란색 감지
   → 진동 모터 0.5초 작동
   → 소켓으로 "1" 전송
   → Flask 서버가 YOLOv5 활성화
   ```

3. **신호등 인식**
   ```
   ESP32-CAM 영상 수신
   → YOLOv5 추론
   → 빨간불/파란불 감지
   → TTS 음성 안내
   → 웹 UI에 결과 표시
   ```

---

## 💻 코드 분석

### Flask 서버 (app.  py)

#### 핵심 라우트

```python
# 메인 페이지
@app.route('/')
def index():
    return render_template('index.html')

# 모델 선택
@app.route('/select_model', methods=['POST'])
def select_model():
    global select_mode
    model_type = request.form.get('select_model')
    
    if model_type == 'is_shoe':
        select_mode = 1  # 신발 + AI 모드
    
    return render_template('shoe_model.html')

# 비디오 스트리밍
@app. route('/video_feed')
def video_feed():
    return Response(generate_frames(),
                    mimetype='multipart/x-mixed-replace; boundary=frame')
```

---

#### YOLOv5 추론

```python
def generate_frames():
    global arduino_data, select_mode
    
    cap = cv2.VideoCapture(URL + ":81/stream")
    
    # 신발 모드 & 점자 블록 감지 시 작동
    if (select_mode == 1 and arduino_data == "1") or (select_mode == 2):
        set_resolution(URL, index=10)
        
        while cap.isOpened():
            ret, img = cap.read()
            if not ret:
                break
            
            # 전처리
            img_input = letterbox(img, img_size, stride=stride)[0]
            img_input = img_input.transpose((2, 0, 1))[::-1]
            img_input = np.ascontiguousarray(img_input)
            img_input = torch.from_numpy(img_input).to(device)
            img_input = img_input.float() / 255.0
            img_input = img_input.unsqueeze(0)
            
            # YOLOv5 추론
            pred = model(img_input, augment=False, visualize=False)[0]
            pred = non_max_suppression(pred, conf_thres, iou_thres, 
                                        classes, agnostic_nms, max_det=max_det)[0]
            
            # 결과 처리
            pred = pred. cpu().numpy()
            
            # Bounding Box 그리기 & 음성 안내
            for det in pred:
                label = int(det[5])
                if label == 0:
                    speak("빨간불")
                elif label == 1:
                    speak("파란불")
            
            # 프레임 인코딩
            ret, buffer = cv2.imencode('.jpg', img)
            frame = buffer.tobytes()
            yield (b'--frame\r\n'
                   b'Content-Type: image/jpeg\r\n\r\n' + frame + b'\r\n')
```

---

### 웹 UI (HTML/CSS)

#### index.html (메인 페이지)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>AI-VIWAD</title>
    <link rel="stylesheet" href="static/index.css">
</head>
<body>
    <form id="is_shoe_form" method="POST" action="/select_model">
        <input type="hidden" name="select_model" value="is_shoe">
    </form>
    
    <div id="is_shoe_button">
        <div class="center_div">
            <img src="static/iphone.png" width="160rem" height="160rem">
            <p class="div_title"><strong>안전실발 + AI</strong></p>
        </div>
    </div>
    
    <script>
        document.getElementById('is_shoe_button').addEventListener('click', function() {
            document.getElementById('is_shoe_form').submit();
        });
    </script>
</body>
</html>
```

---

#### shoe_model. html (카메라 페이지)

```html
<!DOCTYPE html>
<html>
<head>
    <title>AI와드</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='shoe_model.css') }}">
</head>
<body>
    <div id="title">AI와드</div>
    <div id="content">
        <div id="traffic-lights-container">
            <!-- 왼쪽 신호등 -->
            <div class="traffic-light">
                <div class="light light-red"></div>
                <div class="light light-yellow"></div>
                <div class="light light-green"></div>
            </div>
            
            <!-- 카메라 화면 -->
            <div id="video-container">
                <img id="video" src="{{ url_for('video_feed') }}" 
                     width="1200" height="600">
            </div>
            
            <!-- 오른쪽 신호등 -->
            <div class="traffic-light">
                <div class="light light-red"></div>
                <div class="light light-yellow"></div>
                <div class="light light-green"></div>
            </div>
        </div>
    </div>
</body>
</html>
```

---

## 📊 성능 지표

### 시스템 성능

| 항목 | 성능 |
|------|------|
| 점자 블록 감지 속도 | 실시간 (50ms 이내) |
| YOLOv5 추론 속도 | 약 100ms/프레임 |
| 신호등 인식 정확도 | Confidence 40% 이상 |
| 영상 스트리밍 지연 | 약 200ms |
| 음성 안내 지연 | 약 500ms |

---

## 🎯 활용 사례

### 1. 횡단보도 보행

```
점자 블록 감지
→ 진동 알림
→ 신호등 확인 (YOLOv5)
→ 음성 안내 ("파란불입니다")
→ 안전하게 횡단보도 건너기
```

### 2. 지하철역/버스 정류장

```
점자 블록 따라 이동
→ 실시간 피드백
→ 안전한 승하차 지점 도달
```

---

## 🚀 향후 개선 사항

- [ ] GPS 연동 (현재 위치 음성 안내)
- [ ] 장애물 감지 센서 추가 (초음파/라이다)
- [ ] 음성 인식 (사용자 음성 명령)
- [ ] 배터리 최적화 (저전력 모드)
- [ ] 모바일 앱 개발 (웹 대체)
- [ ] 다국어 TTS 지원

---

## 🤝 기여하기

기여는 언제나 환영합니다! 🎉

### 기여 방법

1. Fork 이 저장소
2. Feature 브랜치 생성:  `git checkout -b feature/AmazingFeature`
3. 변경사항 커밋: `git commit -m 'Add some AmazingFeature'`
4. 브랜치에 Push: `git push origin feature/AmazingFeature`
5. Pull Request 생성

---

## 📜 라이선스

이 프로젝트는 MIT License 하에 배포됩니다.

---

## 📞 연락처

<div align="center">

### 프로젝트 관리자:  Deamonio

[![Email](https://img.shields.io/badge/Email-hyun0810d@gmail.com-EA4335?style=for-the-badge&logo=gmail&logoColor=white)](mailto:hyun0810d@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-Deamonio-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Deamonio)

**프로젝트 링크**:  [https://github.com/Deamonio/AI-VIWAD](https://github.com/Deamonio/AI-VIWAD)

**작품 설명서**: [Google Docs](https://docs.google.com/document/d/1tyV2fjZkA8JoP6vy9Sr6bMmbAfNee75N/edit?usp=sharing&ouid=107164361597916380257&rtpof=true&sd=true)

</div>

---

## 🙏 감사의 말

| Flask | YOLOv5 | Arduino | ESP32 |
|-------|--------|---------|-------|
| 웹 프레임워크 | AI 모델 | 하드웨어 | 카메라 |

**특별 감사:**
- 🦾 **CodeFair 2023** - 발표 기회 제공
- 👥 **시각장애인 협회** - 인터뷰 및 피드백
- 🎓 **멘토님들** - 기술 자문
- 💡 **팀 Makers** - 6개월간의 열정

---

<div align="center">

## ⭐ 이 프로젝트가 마음에 드셨다면 Star를 눌러주세요!

[![Star History Chart](https://api.star-history.com/svg?repos=Deamonio/AI-VIWAD&type=Date)](https://star-history.com/#Deamonio/AI-VIWAD&Date)

---

**Made with 🦾 by Team Makers**

*"AI와 함께하는 안전한 보행"*

---

**© 2023 Deamonio. All rights reserved.**

[⬆ 맨 위로 돌아가기](#-ai-viwad)

</div>
