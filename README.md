# SmartKitchenAI

แอปผู้ช่วยจัดการวัตถุดิบและวางแผนมื้ออาหารสำหรับครัวเรือน โดยเชื่อมต่อข้อมูลผ่าน Firebase และใช้ Google Gemini เพื่อวิเคราะห์และแนะนำเมนูที่เหมาะสม ช่วยลดของเสีย จัดการสต็อก และทำงานร่วมกันในครอบครัวได้อย่างลื่นไหล

## ไฮไลต์ของระบบ
- **แดชบอร์ดสต็อกแบบเรียลไทม์** ภาพรวมวัตถุดิบ, รายการใกล้หมดอายุ และอินไซต์ AI จากข้อมูล Firebase (`lib/dashboard/dashboard.dart`).
- **คำแนะนำเมนูอัจฉริยะ** วิเคราะห์วัตถุดิบ ปรับรสชาติ/ภาษา และกรองแพ้อาหารด้วยบริการ Gemini (`lib/foodreccom/services/enhanced_ai_recommendation_service.dart`).
- **จัดการวัตถุดิบครบวงจร** เพิ่ม/แก้ไข/แบ่งหมวดหมู่ พร้อมฐานข้อมูลอายุการเก็บและตัวสแกนบาร์โค้ด (`lib/rawmaterial`).
- **ลิสต์ซื้อของและโหมดใช้งานเร็ว** เลือกรายการที่ต้องซื้อ จัดกลุ่ม และบันทึกการใช้แบบรวดเร็ว (`lib/rawmaterial/widgets`).
- **ครอบครัวและโปรไฟล์สุขภาพ** แชร์สต็อกครอบครัว เชิญด้วย QR/รหัส และเก็บข้อมูลสุขภาพ (`lib/profile`).
- **การแจ้งเตือนและบันทึกกิจกรรม** รวมการแจ้งเตือนหมดอายุและประวัติการทำอาหาร (`lib/notifications`).
- **Cloud Functions สำหรับงานเบื้องหลัง** ใช้ประมวลผลและส่งแจ้งเตือนฝั่งเซิร์ฟเวอร์ (`functions/index.js`).

## เทคโนโลยีหลัก
- Flutter 3.8.1 (Dart 3.8) + Material Design
- Firebase: Authentication, Cloud Firestore, Storage, Messaging, Dynamic Links, App Check
- Google Gemini ผ่านแพ็กเกจ `google_generative_ai` และ ML Kit Barcode Scanning
- spoonacular-recipe-food-nutrition api
- open food fact api
- Provider + Shared Preferences สำหรับสถานะและแคชในเครื่อง
- Firebase Cloud Functions (Node.js 18) สำหรับงาน background

## โครงสร้างโค้ดสำคัญ
```text
lib/
  dashboard/             หน้าแดชบอร์ดและวิดเจ็ตสรุปสต็อก
  foodreccom/            คำแนะนำเมนู, AI services, และวิดเจ็ตสูตรอาหาร
  rawmaterial/           จัดการวัตถุดิบ, รายการซื้อ, และตัวสแกนบาร์โค้ด
  profile/               โปรไฟล์ผู้ใช้ ข้อมูลสุขภาพ และศูนย์ครอบครัว
  notifications/         หน้าศูนย์แจ้งเตือนและการซิงก์
  welcomeapp/            หน้าล็อกอิน ลงทะเบียน และ onboarding
functions/               Cloud Functions ฝั่งเซิร์ฟเวอร์
assets/images/           ทรัพยากรภาพสำหรับ UI
```

## เตรียมสภาพแวดล้อม
1. ติดตั้ง Flutter SDK >= 3.8.1 และเครื่องมือแพลตฟอร์ม (Android Studio/Xcode); ตรวจสอบด้วย `flutter doctor`.
2. ติดตั้ง Firebase CLI (`npm install -g firebase-tools`) และ FlutterFire CLI (`dart pub global activate flutterfire_cli`).
3. ติดตั้ง Node.js 18 LTS สำหรับพัฒนา Cloud Functions.
4. ตั้งค่าเครื่องมือ build ที่จำเป็น เช่น Java 17 (Android) และ Xcode (iOS).

## ตั้งค่า Firebase
1. `firebase login` แล้วสร้างโปรเจกต์ใหม่หรือเลือกโปรเจกต์ที่มีอยู่.
2. ใช้ `flutterfire configure --project <project-id>` เพื่อสร้าง/อัปเดต `lib/firebase_options.dart`.
3. เปิดใช้บริการที่ต้องใช้: Email/Password Auth, Cloud Firestore, Cloud Storage, Cloud Messaging, Dynamic Links (สำหรับการเชิญครอบครัว), App Check และ (ถ้าต้องการ) Functions.
4. ตั้งค่า App Check ให้รองรับอุปกรณ์พัฒนา (Debug provider) ตามที่ระบุใน `lib/main.dart`.
5. สำหรับ Cloud Functions ให้แก้ไขไฟล์ `functions/.env` หรือค่าอื่นที่จำเป็น แล้วรัน `firebase deploy --only functions` หรือ `firebase emulators:start` ระหว่างพัฒนา.

## ตั้งค่าไฟล์ .env
สร้างหรืออัปเดตไฟล์ `.env` ที่รากโปรเจกต์ด้วยค่า เช่น:
```dotenv
GEMINI_API_KEYS=ai-xxxx,ai-yyyy
GEMINI_PRIMARY_MODEL=gemini-2.5-flash
GEMINI_FALLBACK_MODEL=gemini-2.5-flash
AI_MAX_INGREDIENTS=12
STRICT_MAX_INGREDIENTS=12
AI_RATE_LIMIT=30000
RAPIDAPI_KEY=your-rapidapi-key
GEMINI_USE_SDK=true
```
- `GEMINI_API_KEYS`: คีย์ของ Google AI Studio/Gemini สามารถเว้นคอมมาเพื่อหมุนใช้หลายคีย์.
- `RAPIDAPI_KEY`: ใช้กับบริการภายนอก (ถ้าต้องการ); เว้นว่างได้ถ้าไม่ใช้.
- ค่าควบคุมอื่น ๆ (`AI_MAX_INGREDIENTS`, `AI_RATE_LIMIT`, ฯลฯ) สามารถปรับตามโควตาและพฤติกรรมที่ต้องการ.

## ติดตั้งและรันแอป
1. ติดตั้งแพ็กเกจด้วย `flutter pub get`.
2. ถ้าสร้างไฟล์ native ใหม่ให้รัน `flutterfire configure` อีกครั้งเพื่อซิงก์ค่าบนทุกแพลตฟอร์ม.
3. รันแอปด้วย `flutter run -t lib/main.dart` เลือกอุปกรณ์ที่ต้องการ (Android/iOS/Web/Desktop).
4. สำหรับเว็บให้เพิ่ม `--web-renderer canvaskit` หากต้องการประสิทธิภาพด้านกราฟิก.

## การทดสอบและคุณภาพ
- `flutter analyze` เพื่อตรวจสอบโค้ดสไตล์และปัญหาทั่วไป.
- `flutter test` เพื่อรัน unit/widget tests (`test/widget_test.dart`).
- ใช้ Firebase Emulator Suite สำหรับทดสอบการเชื่อมต่อ Auth/Firestore โดยไม่แตะข้อมูลจริง.

## เครื่องมือและสคริปต์เพิ่มเติม
- `analyze_tests.py`: สคริปต์ช่วยวิเคราะห์สถิติการทดสอบและสร้างรายงาน (.csv, .png).
- โฟลเดอร์ `build/` และ `.dart_tool/` สร้างอัตโนมัติ ไม่ควรคอมมิต.
- ใช้ `lib/foodreccom/utils` สำหรับตัวช่วยเกี่ยวกับวัตถุดิบ, การแปล และการจัดการ allergy.

## แนวปฏิบัติแนะนำ
- เก็บคีย์จริงไว้ในตัวแปรสภาพแวดล้อมหรือ Secret Manager เมื่อขึ้น production.
- สร้าง branch ย่อยสำหรับแต่ละฟีเจอร์และเปิด Pull Request พร้อมคำอธิบายการทดสอบ.
- ตรวจสอบการอนุญาตกล้องและการแจ้งเตือนบนอุปกรณ์จริงก่อนปล่อยใช้งาน.
- ใช้ภาษาไทยใน UI ให้สอดคล้องกับทีมและผู้ใช้ เปลี่ยนค่าฟอนต์ใน `ThemeData` ได้ตามต้องการ.

