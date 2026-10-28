# Code Examples

ตัวอย่างต่อไปนี้เป็นส่วนย่อของแนวทางที่ใช้ในโครงงาน จัดทำให้ปลอดภัยสำหรับการเผยแพร่และไม่มีข้อมูลเชื่อมต่อจริง

## 1. AI Detection Loop

```python
import cv2
from ultralytics import YOLO

model = YOLO("models/fire_smoke_model.pt")
camera = cv2.VideoCapture("test_video.mp4")

while camera.isOpened():
    ok, frame = camera.read()
    if not ok:
        break

    results = model.predict(frame, conf=0.25, verbose=False)
    detections = results[0].boxes

    for box in detections:
        label = results[0].names[int(box.cls[0])]
        confidence = float(box.conf[0])
        if label in {"fire", "smoke"}:
            print(f"{label}: {confidence:.2f}")

camera.release()
```

## 2. สร้างข้อมูลเหตุการณ์

```python
event = {
    "event_type": "fire_detected",
    "risk_level": "high",
    "message": "ตรวจพบเปลวไฟในห้องปฏิบัติการ",
    "camera": "lab_camera_1",
    "confidence": 0.91,
    "image_path": "snapshots/fire_detected.jpg",
    "video_path": "clips/fire_detected.webm",
}
```

## 3. รับเหตุการณ์ด้วย Flask API

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.post("/api/event")
def create_event():
    event = request.get_json(force=True)
    save_event_to_database(event)
    return jsonify({"ok": True, "event": event}), 201
```

## 4. หลักการแจ้งเตือน

ระบบจะส่งการแจ้งเตือนเมื่อเหตุการณ์มีระดับความเสี่ยงสูง และแนบภาพหลักฐานที่บันทึกไว้ โดย URL ของ Webhook จริงจะเก็บไว้ในตัวแปรแวดล้อม ไม่เขียนไว้ในโค้ด

```python
import os

webhook_url = os.getenv("DISCORD_WEBHOOK_URL")
if webhook_url and event["risk_level"] == "high":
    send_notification(webhook_url, event)
```

ตัวอย่างเหล่านี้แสดงแนวคิดการเชื่อมต่อระหว่าง AI, Server, ฐานข้อมูล และระบบแจ้งเตือน โดยไม่เปิดเผยรหัสผ่านหรือข้อมูลของสถานที่จริง
