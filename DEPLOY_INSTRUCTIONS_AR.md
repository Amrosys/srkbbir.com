# تعليمات سريعة لنشر سرك ببير على cPanel

## ملفات جاهزة للنشر ✅

تم بناء التطبيق بنجاح! الملفات الجاهزة موجودة في:
- المجلد: `website/dist/`
- ملف مضغوط جاهز: `sirkbbir-frontend.zip`

## الخطوات السريعة 🚀

### 1. تحميل ملف الـ ZIP
قم بتحميل ملف `sirkbbir-frontend.zip` من المشروع إلى جهازك

### 2. تسجيل الدخول إلى cPanel
- اذهب إلى: `srkbbir.com/cpanel` أو `srkbbir.com:2083`
- أدخل اسم المستخدم وكلمة المرور

### 3. رفع الملفات
#### الطريقة الأولى (الأسهل):
1. افتح **File Manager** في cPanel
2. اذهب إلى مجلد `public_html`
3. انقر على **Upload**
4. ارفع ملف `sirkbbir-frontend.zip`
5. بعد الرفع، انقر بزر الماوس الأيمن على الملف
6. اختر **Extract**
7. بعد فك الضغط، ادخل إلى مجلد `website/dist/`
8. حدد **جميع الملفات** داخل `dist`
9. انقر **Move** → اختر `/public_html/`
10. احذف مجلد `website` الفارغ وملف `sirkbbir-frontend.zip`

#### الطريقة الثانية (FTP):
1. استخدم FileZilla أو WinSCP
2. الاتصال:
   - Host: `ftp.srkbbir.com`
   - Username: اسم مستخدم cPanel
   - Password: كلمة المرور
3. انتقل إلى `public_html` على الخادم
4. ارفع محتويات مجلد `website/dist/` (ليس المجلد نفسه)

### 4. التحقق
- افتح المتصفح
- اذهب إلى `https://srkbbir.com` أو `http://srkbbir.com`
- يجب أن تظهر الواجهة العربية! 🎉

## ملاحظات مهمة ⚠️

### Backend Server (الخادم الخلفي)
**حالياً**: التطبيق سيعمل للواجهة فقط، لكن لن تستطيع إنشاء الأسرار بدون Backend

**الحلول**:

#### الخيار 1: استخدام خدمة مجانية للـ Backend
1. اذهب إلى [Railway.app](https://railway.app)
2. سجل بحساب GitHub
3. انقر "New Project" → "Deploy from GitHub repo"
4. اختر مشروع srkbbir.com
5. سيعطيك رابط مثل: `https://sirkbbir-backend.railway.app`
6. عدّل ملف `.env` في الواجهة:
   ```
   VITE_API_URL=https://sirkbbir-backend.railway.app
   ```
7. أعد بناء Frontend ورفعه مرة أخرى

#### الخيار 2: VPS رخيص
- DigitalOcean: $5/شهر
- Vultr: $5/شهر
- Linode: $5/شهر

#### الخيار 3: Docker على cPanel (إذا كان مدعوماً)
اسأل مزود الاستضافة إذا كان يدعم Docker

## الملفات في المشروع

```
sirkbbir.com/
├── website/dist/              ← الملفات المبنية جاهزة للنشر
│   ├── index.html            ← الصفحة الرئيسية
│   ├── .htaccess             ← إعدادات Apache (مهم!)
│   ├── sirkbbir.svg          ← الشعار العربي
│   └── assets/               ← ملفات JavaScript و CSS
├── sirkbbir-frontend.zip     ← ملف مضغوط جاهز للرفع
└── DEPLOYMENT_CPANEL.md      ← دليل مفصل (بالعربية)
```

## دعم HTTPS (SSL)

في cPanel:
1. ابحث عن "SSL/TLS Status"
2. انقر "Run AutoSSL"
3. انتظر دقائق قليلة
4. سيصبح موقعك متاحاً على `https://srkbbir.com` 🔒

## المساعدة

إذا واجهت أي مشكلة:
1. تحقق من ملف `.htaccess` موجود في `public_html`
2. افتح المتصفح واضغط F12 لرؤية الأخطاء
3. تأكد من رفع جميع الملفات من داخل مجلد `dist`
4. راجع الدليل المفصل في `DEPLOYMENT_CPANEL.md`

---

**تم بناء المشروع بنجاح! ملفاتك جاهزة للنشر.** ✨
