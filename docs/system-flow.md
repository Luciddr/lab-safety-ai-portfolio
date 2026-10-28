# System Flow

```text
Camera / Test Video
        |
        v
AI Detection: fire / smoke
        |
        v
Event Server and Database
        |
        +--> Dashboard
        +--> Snapshot and incident clip
        +--> Notification channel
```

ระบบแยกการรับภาพ การประมวลผล การบันทึกข้อมูล และการแสดงผลออกจากกัน เพื่อให้สามารถเปลี่ยนกล้องหรือย้าย Server ได้ในอนาคต
