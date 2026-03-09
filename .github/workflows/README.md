# 🏭 Nhà Máy Build Trung Tâm của Yuta (Yuta's Central Build Factory)

Đây là **động cơ CI/CD trung tâm** dùng để biên dịch mã nguồn Web thành tệp ứng dụng Android (`.apk`).

Kho lưu trữ này cung cấp một **Reusable GitHub Actions Workflow** (quy trình có thể tái sử dụng).
Các dự án ứng dụng khác chỉ cần kết nối tới "nhà máy" này để tự động build APK mà **không cần viết lại logic CI/CD**.

---

# 🧠 Kiến trúc hệ thống

```
Repo Ứng Dụng
(mobile-app-template)
        │
        │ Push code
        ▼
GitHub Actions
        │
        │ Gọi workflow dùng chung
        ▼
build-factory
        │
        │ Build APK Android
        ▼
Telegram Bot
        │
        ▼
Người dùng nhận file APK
```

Kiến trúc này cho phép **một hệ thống build duy nhất** phục vụ cho nhiều ứng dụng khác nhau.

---

# ⚙️ Quy trình xử lý chính (Core Pipeline)

Workflow chính nằm tại:

```
.github/workflows/reusable-build.yml
```

Khi được kích hoạt, hệ thống sẽ tự động thực hiện các bước sau.

---

## 1️⃣ Thiết lập môi trường

Máy ảo CI sẽ cài đặt các công cụ cần thiết:

* Node.js 22
* Java 21
* Android SDK
* Gradle

Điều này đảm bảo môi trường build **sạch và ổn định**.

---

## 2️⃣ Build mã nguồn Web

Hệ thống sẽ cài thư viện và đóng gói dự án web:

```
npm install
npm run build
```

Kết quả:

```
dist/
```

Thư mục này chứa **toàn bộ mã nguồn web đã tối ưu để chạy production**.

---

## 3️⃣ Khởi tạo Capacitor

Workflow sẽ tự động chuẩn bị lớp Android native.

Các bước thực hiện:

* Đồng bộ dự án Capacitor
* Xóa cache Android cũ
* Tạo icon Android từ `icon.png`

Bước này giúp **kết nối ứng dụng Web với Android Native**.

---

## 4️⃣ Biên dịch Android

Gradle sẽ build ứng dụng Android.

```
./gradlew assembleDebug
```

File đầu ra:

```
app-debug.apk
```

Vị trí file:

```
android/app/build/outputs/apk/debug/
```

---

## 5️⃣ Gửi file qua Telegram

Sau khi build xong, workflow sẽ sử dụng **Telegram Bot API** để gửi file APK trực tiếp tới điện thoại của bạn.

Kết quả:

📦 File APK được gửi tự động qua Telegram.

---

# 🔌 Cách tích hợp với repo ứng dụng

Để sử dụng nhà máy build này, repo ứng dụng cần tạo file:

```
.github/workflows/main.yml
```

Ví dụ:

```yaml
name: Build App

on:
  push:
    branches:
      - main

jobs:
  build:
    uses: TontonYuta/build-factory/.github/workflows/reusable-build.yml@main
    with:
      app_name: ${{ github.event.repository.name }}
    secrets: inherit
```

Sau khi push code, pipeline sẽ **tự động chạy**.

---

# 🔐 Biến bảo mật cần thiết

Cần cấu hình các secrets trong GitHub:

```
Settings → Secrets and variables → Actions
```

Các biến bắt buộc:

| Secret           | Mô tả                            |
| ---------------- | -------------------------------- |
| TELEGRAM_TOKEN   | Token API của Telegram Bot       |
| TELEGRAM_CHAT_ID | ID của cuộc trò chuyện nhận file |

Những biến này cho phép hệ thống **gửi APK về đúng thiết bị của bạn**.

---

# 📦 Cấu trúc dự án mong đợi

Các repo ứng dụng sử dụng hệ thống này nên có cấu trúc:

```
project-root
│
├ index.html
├ icon.png
├ capacitor.config.json
├ package.json
└ dist/
```

Lưu ý:

* `index.html` phải tồn tại
* `icon.png` dùng để tạo icon Android
* `capacitor.config.json` định danh ứng dụng

---

# 🚀 Quy trình sử dụng thực tế

Ví dụ bạn tạo một repo mới:

```
math-learning-app
```

Các bước:

1. Tạo mã nguồn web bằng AI Studio
2. Copy các file vào repo
3. Push code lên GitHub

```
git add .
git commit -m "Deploy ứng dụng mới"
git push origin main
```

Sau đó hệ thống sẽ tự động:

* Cài thư viện
* Build web
* Biên dịch Android
* Gửi APK qua Telegram

⏱ Thời gian build: **3 – 5 phút**

---

# 🧩 Triết lý thiết kế

Dự án này sử dụng kiến trúc **CI/CD tập trung**.

Ưu điểm:

* Chỉ cần bảo trì logic build tại một nơi
* Repo ứng dụng gọn nhẹ
* Dễ mở rộng cho nhiều app
* Nâng cấp hệ thống nhanh hơn

Khi cập nhật **build-factory**, mọi ứng dụng kết nối đều được hưởng lợi ngay lập tức.

---

# 👨‍💻 Tác giả

Phát triển và duy trì bởi **TontonYuta**

2026
