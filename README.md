# Daily — Habit Journal (نسخة جاهزة للهاتف / PWA)

هذا المجلد يحتوي على تطبيقك جاهزًا كـ **PWA** (تطبيق ويب تقدّمي):
يعمل بدون إنترنت، له أيقونة، ويمكن تحويله إلى **APK أندرويد حقيقي** بخطوات بسيطة بدون كتابة كود.

## محتويات المجلد
```
index.html        ← التطبيق نفسه (مع كل تحسيناتك)
manifest.json      ← بيانات التطبيق (الاسم، الأيقونة، الألوان)
sw.js               ← يجعل التطبيق يعمل أوفلاين
icons/              ← أيقونات التطبيق (192px, 512px, maskable)
```

البيانات (العادات، اليوميات، الملاحظات) تُحفظ محليًا على جهاز المستخدم (localStorage)،
وتبقى محفوظة حتى بدون إنترنت، وحتى بعد تحويله لتطبيق أندرويد.

---

## الخطوة 1: رفع الملفات على GitHub

1. أنشئ حسابًا على [github.com](https://github.com) إن لم يكن لديك واحد.
2. اضغط **New repository** وأعطه اسمًا مثل `daily-habit-app` واجعله **Public**.
3. ارفع كل الملفات الموجودة في هذا المجلد (بنفس البنية: `index.html`, `manifest.json`, `sw.js`, ومجلد `icons`) — إمّا بالسحب والإفلات من صفحة الريبو مباشرة (Add file → Upload files)، أو عبر Git من جهازك.
4. بعد الرفع، اذهب إلى **Settings → Pages**.
5. تحت "Build and deployment" اختر **Deploy from a branch**، ثم الفرع `main` والمجلد `/ (root)`، واضغط **Save**.
6. انتظر دقيقة أو اثنتين، سيظهر لك رابط مثل:
   ```
   https://USERNAME.github.io/daily-habit-app/
   ```
   افتحه للتأكد أن التطبيق يعمل بشكل صحيح.

---

## الخطوة 2: تحويله إلى APK أندرويد

### الطريقة الموصى بها (مجانية، رسمية، بدون كود) — PWABuilder
1. اذهب إلى [www.pwabuilder.com](https://www.pwabuilder.com)
2. الصق رابط GitHub Pages الذي حصلت عليه، واضغط **Start**.
3. سيفحص الموقع تطبيقك (سيجد الـ manifest والـ service worker جاهزين تلقائيًا).
4. اضغط **Package for stores** ثم اختر **Android**.
5. اترك الإعدادات الافتراضية (أو غيّر اسم الحزمة Package ID إن أردت)، ثم اضغط **Generate**.
6. سيُنزَّل لك ملف مضغوط يحتوي مشروع أندرويد (Trusted Web Activity) جاهز.
7. لتحويله إلى APK فعلي تحتاج أحد الخيارين:
   - **الأسهل:** ثبّت [Android Studio](https://developer.android.com/studio) وافتح المشروع المُنزَّل، ثم من القائمة `Build → Build Bundle(s) / APK(s) → Build APK(s)`.
   - **بدون Android Studio:** ثبّت أداة [Bubblewrap CLI](https://github.com/GoogleChromeLabs/bubblewrap) (تحتاج Node.js + Java JDK فقط)، ثم نفّذ:
     ```
     npm i -g @bubblewrap/cli
     bubblewrap init --manifest=https://USERNAME.github.io/daily-habit-app/manifest.json
     bubblewrap build
     ```
     سينتج لك ملف `app-release-signed.apk` جاهز للتثبيت مباشرة.

### الطريقة الأسرع (تجريبية / بدون Android Studio)
مواقع تحوّل أي رابط ويب إلى APK مباشرة (تكفي رابط GitHub Pages من الخطوة 1):
- [websitetoapk.com](https://websitetoapk.com)
- [appsgeyser.com](https://appsgeyser.com)
- [median.co](https://median.co) (فيه خطة مدفوعة لكن مناسب لتطبيقات احترافية)

فقط ضع رابط GitHub Pages، اختر اسم وأيقونة التطبيق، واضغط توليد — ستحصل على APK جاهز للتنزيل والتثبيت مباشرة على هاتفك.

---

## ملاحظات
- التطبيق يحفظ البيانات محليًا في الهاتف، فإن حذفت التطبيق أو مسحت بياناته تُفقد البيانات (يوجد زر تصدير/استيراد JSON داخل صفحة Setup للنسخ الاحتياطي).
- لتثبيت APK على أندرويد يجب تفعيل "تثبيت من مصادر غير معروفة" (Install unknown apps) من إعدادات الهاتف.
- إن أردت لاحقًا نشره على متجر Google Play، فخيار PWABuilder + Bubblewrap هو الأنسب لأنه ينتج ملف AAB متوافق مع المتجر.

---

## حل مشكلة "Fix the links to your icons" / "Fix the icon types" في PWABuilder

هذان الخطآن يعنيان عمليًا نفس الشيء: **PWABuilder لا يستطيع فتح ملفات الأيقونات على الرابط المكتوب في manifest.json**
(فيحصل على صفحة 404 بدل صورة PNG، ولهذا يظهر خطأ بالرابط وخطأ بالنوع معًا).

### السبب الأكثر شيوعًا
عند رفع مجلد `icons` عبر واجهة GitHub بالسحب والإفلات (Upload files)، أحيانًا لا يحافظ المتصفح
على بنية المجلد، فتُرفع الصور مباشرة في جذر المستودع بدل داخل مجلد `icons/`.
النتيجة: الرابط `.../icons/icon-192.png` يعطي 404 لأن الصورة موجودة فعليًا في `.../icon-192.png`.

### كيف تتأكد
افتح هذا الرابط مباشرة في المتصفح (غيّر اسم المستخدم/المستودع حسب رابطك):
```
https://erenyeager55556667-ops.github.io/Traker02/icons/icon-192.png
```
- إذا ظهرت الصورة → المشكلة شيء آخر (تأكد من عدم وجود خطأ إملائي في manifest.json).
- إذا ظهر 404 → المجلد `icons` غير موجود في المكان الصحيح، اتبع الحل التالي.

### الحل (بدون Git، من متصفح GitHub مباشرة)
1. افتح مستودعك على GitHub، وتأكد أولًا: هل يوجد مجلد باسم `icons` في نفس مستوى `index.html` و `manifest.json`؟
   - إن كانت الصور موجودة في الجذر (بجانب index.html مباشرة) بدل داخل مجلد icons، احذفها من هناك أولًا.
2. اضغط **Add file → Upload files**.
3. اسحب ملفات الأيقونات (icon-48.png … icon-512-maskable.png) إلى صندوق الرفع.
4. **قبل الضغط على Commit**: ستظهر كل صورة في قائمة بأسمائها، اضغط على اسم كل ملف وعدّله ليصبح:
   ```
   icons/icon-48.png
   icons/icon-72.png
   icons/icon-96.png
   icons/icon-128.png
   icons/icon-144.png
   icons/icon-152.png
   icons/icon-192.png
   icons/icon-384.png
   icons/icon-512.png
   icons/icon-512-maskable.png
   ```
   (بمجرد كتابة `icons/` قبل اسم الملف، ينشئ GitHub المجلد تلقائيًا عند الحفظ.)
5. اضغط **Commit changes**.
6. تأكد أن `manifest.json` في مستودعك يطابق النسخة الجديدة المرفقة هنا (تحتوي الآن على مجموعة أيقونات أكبر: 48 حتى 512 بكسل، إضافة إلى نسخة maskable) — إن كانت مختلفة، افتحه على GitHub واضغط تعديل (القلم ✎)، واستبدل محتواه بمحتوى ملف `manifest.json` المرفق هنا.
7. ارجع إلى PWABuilder واضغط **Refresh** أو أعد فحص الرابط من جديد.

### بخصوص التحذير الأصفر "Fix the icon sizes"
كان بسبب نقص عدد المقاسات المتوفرة. النسخة الجديدة من manifest.json تحتوي الآن على 9 مقاسات مختلفة
(48 حتى 512) بالإضافة لنسخة maskable، وهذا كافٍ لإزالة هذا التحذير.

