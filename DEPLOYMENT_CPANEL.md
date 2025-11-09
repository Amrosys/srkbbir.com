# دليل نشر سرك ببير على cPanel

هذا الدليل يشرح كيفية نشر تطبيق سرك ببير على استضافة cPanel.

## متطلبات النشر

تطبيق سرك ببير يتكون من جزئين:
1. **Frontend (الواجهة الأمامية)**: ملفات React الثابتة
2. **Backend (الخادم الخلفي)**: خادم Go + قاعدة بيانات (Redis/Memcached)

## الطريقة الأولى: نشر Frontend على cPanel + Backend على خادم منفصل

### الخطوة 1: بناء Frontend

تم بناء الملفات بنجاح في مجلد `website/dist/`

### الخطوة 2: رفع الملفات على cPanel

#### الطريقة الأولى: باستخدام File Manager في cPanel

1. **تسجيل الدخول إلى cPanel**
   - افتح المتصفح واذهب إلى `srkbbir.com/cpanel`
   - أدخل اسم المستخدم وكلمة المرور

2. **فتح File Manager**
   - ابحث عن "File Manager" في cPanel
   - انقر عليه

3. **الذهاب إلى public_html**
   - في القائمة اليسرى، انقر على `public_html`
   - هذا هو المجلد الذي سيتم عرض محتواه على srkbbir.com

4. **رفع الملفات**
   - انقر على زر "Upload" في الأعلى
   - اختر جميع الملفات من مجلد `website/dist/` على جهازك
   - أو قم بضغط مجلد `dist` كملف zip أولاً ثم ارفعه وفك الضغط

5. **نسخ الملفات من dist إلى public_html**
   - بعد الرفع، انتقل إلى محتويات مجلد `dist`
   - حدد جميع الملفات
   - انقر على "Move" أو "Copy"
   - انقلها إلى `public_html`

#### الطريقة الثانية: باستخدام FTP

1. **استخدم FileZilla أو أي عميل FTP**
   - Host: `ftp.srkbbir.com` أو `srkbbir.com`
   - Username: اسم مستخدم cPanel
   - Password: كلمة مرور cPanel
   - Port: 21

2. **ارفع محتويات مجلد dist**
   - في الجانب المحلي، انتقل إلى `website/dist/`
   - في الجانب البعيد، انتقل إلى `/public_html/`
   - اسحب جميع الملفات من `dist` إلى `public_html`

#### الطريقة الثالثة: باستخدام SSH/Terminal (إذا كان متاحاً)

```bash
# ضغط ملفات dist
cd website
zip -r dist.zip dist/*

# رفع الملف باستخدام scp
scp dist.zip username@srkbbir.com:~/public_html/

# تسجيل الدخول عبر SSH
ssh username@srkbbir.com

# فك الضغط
cd public_html
unzip dist.zip
mv dist/* .
rm -rf dist dist.zip
```

### الخطوة 3: إعداد .htaccess لـ React Router

نظراً لأن التطبيق يستخدم React Router، تحتاج إلى إنشاء ملف `.htaccess` في `public_html`:

```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-l
  RewriteRule . /index.html [L]
</IfModule>

# Security Headers
<IfModule mod_headers.c>
  Header set X-Content-Type-Options "nosniff"
  Header set X-Frame-Options "DENY"
  Header set X-XSS-Protection "1; mode=block"
  Header set Referrer-Policy "strict-origin-when-cross-origin"
</IfModule>

# MIME Types
AddType image/svg+xml .svg
AddType application/javascript .js
AddType text/css .css
```

### الخطوة 4: إعداد Backend Server

**المشكلة**: cPanel العادي لا يدعم تشغيل تطبيقات Go مباشرة.

#### الحلول المتاحة:

**الخيار 1: استخدام VPS أو Cloud Server للـ Backend**

1. **استخدم خادم منفصل (DigitalOcean, AWS, Linode)**
   - نشّط Backend على خادم منفصل
   - مثال: `api.srkbbir.com`
   - اتبع تعليمات Docker الموجودة في المشروع

2. **قم بتحديث متغيرات البيئة في Frontend**
   - قبل البناء، قم بتعديل ملف `.env` في مجلد `website`:
   ```bash
   VITE_API_URL=https://api.srkbbir.com
   ```
   - أعد بناء Frontend:
   ```bash
   npm run build
   ```
   - ارفع الملفات الجديدة إلى cPanel

**الخيار 2: استخدام Docker (إذا كان cPanel يدعمه)**

بعض مقدمي cPanel يدعمون Docker. تحقق من ذلك:

```bash
# نشر باستخدام Docker Compose
cd deploy/docker-compose/insecure
docker-compose up -d
```

**الخيار 3: استخدام خدمات مُدارة**

- **Frontend**: على cPanel (كما هو موضح أعلاه)
- **Backend**: على Railway.app أو Render.com أو Fly.io (مجاني للبدء)
- **Database**: Redis Cloud (مجاني للبدء)

## إعداد SSL Certificate (HTTPS)

1. **في cPanel، ابحث عن "SSL/TLS Status"**
2. انقر على "Run AutoSSL" للحصول على شهادة مجانية
3. أو استخدم "Let's Encrypt" إذا كان متاحاً

## إعداد DNS

تأكد من أن DNS يشير إلى الخادم الصحيح:

1. **في cPanel، اذهب إلى "Zone Editor"**
2. تأكد من وجود السجلات التالية:
   - `A Record`: `srkbbir.com` → عنوان IP للخادم
   - `CNAME`: `www` → `srkbbir.com`
   - (اختياري) `CNAME`: `api` → عنوان خادم Backend

## الاختبار

1. افتح المتصفح واذهب إلى `https://srkbbir.com`
2. يجب أن تظهر الواجهة العربية
3. جرّب إنشاء سر جديد للتأكد من عمل كل شيء

## الأخطاء الشائعة وحلولها

### 1. صفحة بيضاء فارغة
**الحل**: تحقق من:
- أن جميع الملفات تم رفعها بشكل صحيح
- ملف `.htaccess` موجود ويحتوي على قواعد إعادة التوجيه
- افتح Console في المتصفح (F12) لرؤية الأخطاء

### 2. الصور والأيقونات لا تظهر
**الحل**: تأكد من رفع مجلد `assets` بالكامل

### 3. خطأ CORS عند الاتصال بـ Backend
**الحل**: تأكد من إعداد CORS في Backend Server:
```bash
--cors-allow-origin=https://srkbbir.com
```

### 4. خطأ 404 عند التنقل بين الصفحات
**الحل**: تأكد من وجود ملف `.htaccess` بقواعد إعادة التوجيه الصحيحة

## التوصية النهائية

**للحصول على أفضل أداء وسهولة في الإدارة:**

1. **Frontend**: على cPanel (كما هو موضح)
2. **Backend + Database**: على خادم VPS بسيط (DigitalOcean $5/شهر)
3. استخدم Docker Compose للنشر السريع للـ Backend

أو استخدم منصات مثل:
- **Vercel/Netlify**: للـ Frontend (مجاني)
- **Railway/Render**: للـ Backend (مجاني للبدء)

---

إذا كنت تحتاج مساعدة في أي خطوة، لا تتردد في السؤال!
