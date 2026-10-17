# Bolo Chat — APK بنانے کے لیے مکمل ہینڈ اوور (تازہ ترین)

یہ پیکیج **Bolo Chat** ایپ کا تازہ ترین مکمل سورس کوڈ + تمام ہدایات ہیں۔
اس فولڈر کی ہر فائل ڈویلپر کو دیں۔

## پیکیج میں کیا ہے

| فائل | مقصد |
|---|---|
| `Bolo Chat Source.zip` | ایپ کا مکمل سورس کوڈ (React + TypeScript) |
| `vendor/space-sdk.tgz` | ایک لازمی library — نیچے "GitHub Actions" والا حصہ پڑھیں |
| `Offline P2P Chat - Developer Spec.md` | آف لائن بلوٹوتھ چیٹ فیچر کی مکمل تفصیل |
| یہ گائیڈ | APK بنانے کے تمام راستے اور ہدایات |

## ایپ کا تعارف

- نام (فی الحال): **Bolo Chat** — چین کے لیے نام ابھی طے ہونا ہے، تجویز: **KuaiLiao** (快聊 — "تیز چیٹ"، چیک شدہ، صاف نام)
- لوگو: `client/src/assets/bolo-logo.jpg` (منظور شدہ)
- ٹیکنالوجی: React + TypeScript، Bun
- بیک اینڈ: Firebase Firestore (رئیل ٹائم)

## موجودہ فیچرز

- ایک فون سے دوسرے فون تک اصل پیغام رسانی (text، image، voice)
- بھیجا/پہنچا/پڑھا کے نشان، تیز رفتار (optimistic send)
- واٹس ایپ جیسا اسٹیٹس (تصویر + 8 رنگین تحریری پس منظر، 24 گھنٹے)
- گروپس: بنانا، سیٹنگ، وال پیپر، ایڈمن
- ایپ لاک اور چیٹ لاک (پن/پاسورڈ/فنگر پرنٹ)
- آرکائیو چیٹس، بلاک، غائب ہونے والے پیغامات
- پروفائل تصویر، یوزرنیم، موبائل نمبر، Gmail
- یوزرنیم سے تلاش (ٹیلیگرام کی طرح)
- 50 وال پیپر، 25 زبانیں (چینی زبان شامل)، 12 وائس انداز
- کال اسکرینیں (اصل کال کے لیے WebRTC درکار)

## Firebase پروجیکٹ (پہلے سے تیار)

- پروجیکٹ: Rabta، ID: `rabta-24267`
- پلان: Spark (مفت)، Firestore: `asia-south1`، test mode
- Console: https://console.firebase.google.com/project/rabta-24267/overview
- `firebaseConfig` پہلے سے `client/src/firebase.ts` میں لگی ہوئی ہے

**اہم:** Firestore test mode کے rules تقریباً **15 نومبر 2026** کو ختم ہوں گے —
اس سے پہلے مناسب security rules لگانا ضروری ہے۔

## GitHub Actions پر build کا مسئلہ اور حل (ضروری)

GitHub پر build اس لیے fail ہوتی ہے کہ `package.json` میں یہ لائن ایک ایسی فائل
مانگتی ہے جو صرف اصل ڈویلپمنٹ کمپیوٹر میں تھی:

```json
"@hatch/space-sdk": "file:/opt/hatch/skills/spaces/ts-runtime/dist/space-sdk.tgz"
```

**حل:**
1. اس پیکیج کی `vendor/space-sdk.tgz` فائل repo میں `vendor/` فولڈر میں رکھیں
2. `package.json` میں لائن یوں بدلیں:
   ```json
   "@hatch/space-sdk": "file:./vendor/space-sdk.tgz"
   ```
3. repo سے `bun.lock` ڈیلیٹ کر دیں (اس ZIP میں شامل نہیں — GitHub والے bun ورژن سے mismatch تھا)
4. Workflow دوبارہ چلائیں

## APK بنانے کے راستے

### راستہ 1 — تیز (Capacitor)
1. `bun install` پھر `bun run build:client` — `client/dist/` تیار ہوگی
2. Capacitor project میں wrap کریں
3. Android Studio میں APK/AAB بنائیں

### راستہ 2 — مکمل native (Flutter / React Native)
Firestore کو بیک اینڈ رکھتے ہوئے نئے سرے سے native ایپ لکھیں۔

## چین / Huawei AppGallery منصوبہ (اہم)

- **Firebase چین میں نہیں چلتا** (گوگل وہاں بند ہے) — چین والی build کے لیے
  بیک اینڈ **Huawei AppGallery Connect / HMS** یا چین میں ہوسٹڈ سروس پر بنائیں۔
- Offline P2P چیٹ کو کسی بیک اینڈ کی ضرورت نہیں — وہ ہر جگہ چلے گی۔
- چین میں اصل مقابلہ WeChat سے ہوگا۔
- چین میں پبلش کے لیے local compliance (real-name verification، ICP filing)
  ڈویلپر/پبلشر کو سنبھالنا ہوگی۔
- **Offline P2P چیٹ** فیچر کی مکمل spec اس پیکیج میں الگ فائل میں ہے:
  `Offline P2P Chat - Developer Spec.md` (HMS Nearby Service استعمال کریں،
  گوگل کی Nearby API نہیں)۔

## اہم فائلیں (سورس میں)

- `client/src/App.tsx` — تمام اسکرینیں
- `client/src/firebase.ts` — Firebase config اور messaging
- `client/src/assets/bolo-logo.jpg` — منظور شدہ لوگو
- `client/src/theme.css` — ڈیزائن
- `server/src/` — بیک اینڈ actions اور schema
- `package.json` — dependencies اور build scripts
