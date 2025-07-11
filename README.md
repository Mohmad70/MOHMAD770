
<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>منصة إعلانات القفر</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      direction: rtl;
      background-color: #f0f0f0;
      padding: 20px;
    }

    .login-container, .app-container {
      max-width: 600px;
      margin: auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px #ccc;
    }

    input, textarea, select, button {
      width: 100%;
      margin: 10px 0;
      padding: 10px;
      font-size: 16px;
    }

    #adImage {
      border: 1px solid #ccc;
      padding: 10px;
    }

    #adType {
      background-color: #eee;
    }

    .ad-card {
      border: 1px solid #ddd;
      background: #fff;
      margin-bottom: 20px;
      padding: 10px;
      border-radius: 8px;
    }

    .ad-card img {
      max-width: 100%;
      border-radius: 8px;
      margin-top: 10px;
    }

    .comment {
      background: #f9f9f9;
      padding: 5px;
      margin-top: 5px;
      border-radius: 5px;
    }

    #whatsappBtn {
      text-align: center;
      margin-top: 20px;
    }

    #whatsappBtn a {
      background: green;
      color: white;
      padding: 10px 20px;
      border-radius: 8px;
      text-decoration: none;
    }
  </style>
</head>
<body>

  <div class="login-container" id="loginSection">
    <h2>تسجيل الدخول</h2>
    <label>اختر نوع الدخول:</label>
    <select id="loginType">
      <option value="client">عميل</option>
      <option value="admin">مدير</option>
    </select>

    <div id="nameField">
      <input type="text" id="username" placeholder="اكتب اسمك">
    </div>

    <div id="passwordField" style="display: none;">
      <input type="password" id="password" placeholder="كلمة السر">
    </div>

    <button onclick="login()">دخول</button>
  </div>

  <div class="app-container" id="appSection" style="display: none;">
    <h2>منصة الإعلانات</h2>

    <div id="adminControls" style="display: none;">
      <h3>إضافة إعلان جديد</h3>
      <input type="text" id="adTitle" placeholder="عنوان الإعلان">
      <textarea id="adDesc" placeholder="وصف الإعلان"></textarea>
      <select id="adType">
        <option>مستشفى</option>
        <option>عيادة</option>
        <option>بقالة</option>
        <option>بيع سيارة</option>
        <option>بيع حطب</option>
        <option>تهنئة عريس</option>
        <option>دعوة زفاف</option>
        <option>بيع عقارات</option>
      </select>
      <input type="text" id="adPhone" placeholder="رقم التواصل (واتساب)">
      <input type="file" id="adImage" multiple accept="image/*">
      <button onclick="addAd()">نشر الإعلان</button>
    </div>

    <div id="adsContainer"></div>

    <div id="whatsappBtn">
      <a href="https://wa.me/966XXXXXXXXX" target="_blank">📞 تواصل معنا عبر واتساب لطلب إعلانك</a>
    </div>
  </div>

  <script>
    let isAdmin = false;

    document.getElementById('loginType').addEventListener('change', function () {
      const type = this.value;
      document.getElementById('passwordField').style.display = type === 'admin' ? 'block' : 'none';
    });

    function login() {
      const type = document.getElementById('loginType').value;
      const username = document.getElementById('username').value;
      const password = document.getElementById('password').value;

      if (type === 'admin') {
        if (password === '001090Mh') {
          isAdmin = true;
          startApp(username);
        } else {
          alert('كلمة السر غير صحيحة!');
        }
      } else {
        if (username.trim() !== '') {
          isAdmin = false;
          startApp(username);
        } else {
          alert('يرجى إدخال الاسم');
        }
      }
    }

    function startApp(username) {
      document.getElementById('loginSection').style.display = 'none';
      document.getElementById('appSection').style.display = 'block';
      if (isAdmin) {
        document.getElementById('adminControls').style.display = 'block';
        document.getElementById('whatsappBtn').style.display = 'none';
      } else {
        document.getElementById('adminControls').style.display = 'none';
      }
      loadAds();
    }

    let ads = [];

    function addAd() {
      const title = document.getElementById('adTitle').value;
      const desc = document.getElementById('adDesc').value;
      const type = document.getElementById('adType').value;
      const phone = document.getElementById('adPhone').value;
      const files = document.getElementById('adImage').files;

      const images = [];

      if (files.length === 0) {
        alert('يرجى اختيار صورة واحدة على الأقل');
        return;
      }

      let loaded = 0;
      for (let i = 0; i < files.length; i++) {
        const fr = new FileReader();
        fr.onload = function (e) {
          images.push(e.target.result);
          loaded++;
          if (loaded === files.length) {
            const ad = { title, desc, type, phone, images, comments: [], views: 0 };
            ads.push(ad);
            renderAds();
          }
        };
        fr.readAsDataURL(files[i]);
      }
    }

    function renderAds() {
      const container = document.getElementById('adsContainer');
      container.innerHTML = '';
      ads.forEach((ad, index) => {
        ads[index].views++; // زيادة المشاهدات

        const card = document.createElement('div');
        card.className = 'ad-card';
        card.innerHTML = `
          <h3>${ad.title}</h3>
          <p>نوع الإعلان: <strong>${ad.type}</strong></p>
          <p>${ad.desc}</p>
          <p>📞 رقم التواصل: <a href="https://wa.me/${ad.phone}" target="_blank">${ad.phone}</a></p>
          <p>👁️ عدد المشاهدات: ${ad.views}</p>
          ${ad.images.map(img => `<img src="${img}">`).join('')}
          <div><strong>التعليقات:</strong></div>
          <div id="comments-${index}">
            ${ad.comments.map(c => `<div class='comment'>${c}</div>`).join('')}
          </div>
          ${!isAdmin ? `
            <input type='text' placeholder='اكتب تعليقك' id='commentInput-${index}'>
            <button onclick='addComment(${index})'>إرسال</button>
          ` : ''}
        `;
        container.appendChild(card);
      });
    }

    function addComment(index) {
      const input = document.getElementById(`commentInput-${index}`);
      if (input.value.trim() !== '') {
        ads[index].comments.push(input.value.trim());
        renderAds();
      }
    }

    function loadAds() {
      renderAds(); // حالياً بدون تخزين دائم
    }
  </script>

</body>
</html>
