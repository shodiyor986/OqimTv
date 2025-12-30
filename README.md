<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Oqim TV | Video Bozor</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* 1. ASOSIY USLUBLAR */
        :root {
            --tg-theme-bg-color: #0f0f0f;
            --tg-theme-text-color: #ffffff;
            --tg-theme-hint-color: #aaaaaa;
            --tg-theme-button-color: #8774e1;
            --primary: #8774e1;
            --success: #34d399;
            --danger: #ef4444;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; }
        body { background: var(--tg-theme-bg-color); color: var(--tg-theme-text-color); min-height: 100vh; }
        .container { max-width: 500px; margin: 0 auto; padding-bottom: 70px; }
        .hidden { display: none !important; }

        /* 2. YUKLASH ANIMATSIYASI */
        .loader { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #0f0f0f; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 1000; }
        .spinner { width: 50px; height: 50px; border: 3px solid rgba(135, 116, 225, 0.3); border-radius: 50%; border-top-color: var(--primary); animation: spin 1s linear infinite; }
        @keyframes spin { to { transform: rotate(360deg); } }

        /* 3. NAVIGATSIYA */
        .nav { display: flex; justify-content: space-around; background: #1a1a1a; position: fixed; bottom: 0; width: 100%; max-width: 500px; padding: 12px 0; border-top: 1px solid #333; z-index: 100; }
        .nav-btn { background: none; border: none; color: #666; font-size: 24px; position: relative; }
        .nav-btn.active { color: var(--primary); }
        .nav-badge { position: absolute; top: -5px; right: -8px; background: var(--danger); color: white; font-size: 10px; padding: 2px 6px; border-radius: 10px; }

        /* 4. SAHIFALAR */
        .page { padding: 15px; display: none; animation: fadeIn 0.3s; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* 5. HOME (Instagram/TikTok uslubi) */
        .story-list { display: flex; gap: 15px; padding: 15px 0; overflow-x: auto; }
        .story { width: 70px; flex-shrink: 0; text-align: center; }
        .story-avatar { width: 60px; height: 60px; border-radius: 50%; background: linear-gradient(45deg, gold, red); padding: 3px; }
        .story-avatar img { width: 100%; height: 100%; border-radius: 50%; border: 2px solid #0f0f0f; }
        .feed { margin-top: 10px; }
        .post { background: #1a1a1a; border-radius: 15px; margin-bottom: 20px; overflow: hidden; }
        .post-header { display: flex; justify-content: space-between; align-items: center; padding: 12px; }
        .post-user { display: flex; align-items: center; gap: 10px; }
        .post-avatar { width: 40px; height: 40px; border-radius: 50%; background: #333; }
        .post-actions { display: flex; justify-content: space-around; padding: 12px; font-size: 24px; }
        .post-action-btn { background: none; border: none; color: white; }
        .liked { color: var(--danger) !important; }
        .post-stats { padding: 0 12px 12px; font-size: 14px; color: #aaa; }
        .video-container { position: relative; width: 100%; height: 500px; background: #000; overflow: hidden; }
        .video-player { width: 100%; height: 100%; object-fit: cover; }

        /* 6. BIZNES (Tovarlar bozori) */
        .product-filters { display: flex; gap: 10px; padding: 15px 0; overflow-x: auto; }
        .filter-btn { padding: 8px 16px; background: #2a2a2a; border: none; border-radius: 20px; color: white; white-space: nowrap; }
        .filter-btn.active { background: var(--primary); }
        .product-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; margin-top: 15px; }
        .product-card { background: #1a1a1a; border-radius: 15px; overflow: hidden; position: relative; }
        .product-badge { position: absolute; top: 10px; left: 10px; background: gold; color: black; padding: 3px 8px; border-radius: 10px; font-size: 12px; font-weight: bold; }
        .product-img { width: 100%; height: 150px; background: #333; display: flex; align-items: center; justify-content: center; font-size: 40px; }
        .product-info { padding: 12px; }
        .product-price { color: var(--primary); font-size: 18px; font-weight: bold; margin: 5px 0; }
        .sell-btn { background: var(--primary); color: white; border: none; padding: 10px; border-radius: 10px; width: 100%; margin-top: 10px; font-weight: bold; }

        /* 7. PROFIL */
        .profile-header { text-align: center; padding: 20px; }
        .profile-avatar { width: 100px; height: 100px; border-radius: 50%; background: linear-gradient(45deg, var(--primary), #34d399); margin: 0 auto 15px; position: relative; }
        .profile-stats { display: flex; justify-content: space-around; padding: 20px; background: #1a1a1a; border-radius: 15px; margin: 20px 0; }
        .stat { text-align: center; }
        .stat-value { font-size: 20px; font-weight: bold; }
        .profile-actions { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        .action-btn { padding: 15px; background: #2a2a2a; border: none; border-radius: 10px; color: white; text-align: center; }

        /* 8. TO'LOV MODALI */
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); display: flex; justify-content: center; align-items: center; z-index: 1000; }
        .modal { background: #1a1a1a; width: 90%; max-width: 400px; border-radius: 20px; padding: 25px; }
        .payment-options { margin: 20px 0; }
        .payment-option { display: flex; align-items: center; justify-content: space-between; padding: 15px; background: #2a2a2a; margin: 10px 0; border-radius: 10px; }
    </style>
</head>
<body>
    <!-- 1. YUKLASH EKRANI -->
    <div class="loader" id="loader">
        <div class="spinner"></div>
        <p style="margin-top: 20px;">Oqim TV yuklanmoqda...</p>
    </div>

    <div class="container" id="app" style="display: none;">
        <!-- 2. ASOSIY KONTENT -->
        <div class="page active" id="homePage">
            <h3><i class="fas fa-home"></i> Asosiy Oqim</h3>
            <div class="story-list">
                <div class="story"><div class="story-avatar"><i class="fas fa-user" style="font-size: 24px; line-height: 54px; color: white;"></i></div><small>Siz</small></div>
                <div class="story"><div class="story-avatar" style="background: linear-gradient(45deg, red, orange);"></div><small>Alisher</small></div>
                <div class="story"><div class="story-avatar" style="background: linear-gradient(45deg, purple, blue);"></div><small>Biznes</small></div>
            </div>

            <div class="feed" id="videoFeed">
                <!-- Videolar bu yerda yuklanadi -->
            </div>
        </div>

        <div class="page" id="businessPage">
            <h3><i class="fas fa-store"></i> Biznes Bozori</h3>
            <div class="product-filters">
                <button class="filter-btn active" data-filter="all">Hammasi</button>
                <button class="filter-btn" data-filter="uy">Uy-joy</button>
                <button class="filter-btn" data-filter="avto">Avto</button>
                <button class="filter-btn" data-filter="elektronika">Elektronika</button>
            </div>
            <button class="sell-btn" onclick="showUploadForm()"><i class="fas fa-plus"></i> Tovar joylash</button>
            <div class="product-grid" id="productGrid">
                <!-- Tovarlar bu yerda yuklanadi -->
            </div>
        </div>

        <div class="page" id="uploadPage">
            <h3><i class="fas fa-cloud-upload-alt"></i> Kontent joylash</h3>
            <div style="background: #1a1a1a; padding: 20px; border-radius: 15px; margin-top: 20px;">
                <div style="margin-bottom: 15px;">
                    <label>Tur</label>
                    <select id="uploadType" style="width: 100%; padding: 10px; background: #2a2a2a; border: none; border-radius: 10px; color: white; margin-top: 5px;">
                        <option value="post">Post (Video/Rasm)</option>
                        <option value="product">Tovar</option>
                    </select>
                </div>
                <div style="margin-bottom: 15px;">
                    <label>Sarlavha</label>
                    <input type="text" id="uploadTitle" placeholder="Sarlavha..." style="width: 100%; padding: 10px; background: #2a2a2a; border: none; border-radius: 10px; color: white; margin-top: 5px;">
                </div>
                <div style="margin-bottom: 15px;">
                    <label>Media (video/rasm URL)</label>
                    <input type="text" id="uploadMedia" placeholder="https://..." style="width: 100%; padding: 10px; background: #2a2a2a; border: none; border-radius: 10px; color: white; margin-top: 5px;">
                </div>
                <div style="margin-bottom: 15px;" id="priceField">
                    <label>Narx ($)</label>
                    <input type="number" id="uploadPrice" placeholder="0.00" style="width: 100%; padding: 10px; background: #2a2a2a; border: none; border-radius: 10px; color: white; margin-top: 5px;">
                </div>
                <button class="sell-btn" onclick="uploadContent()"><i class="fas fa-upload"></i> Yuklash</button>
            </div>
        </div>

        <div class="page" id="profilePage">
            <div class="profile-header">
                <div class="profile-avatar">
                    <i class="fas fa-user" style="font-size: 40px; line-height: 100px;"></i>
                </div>
                <h3 id="profileName">Foydalanuvchi</h3>
                <p id="profileStatus"><span class="product-badge" style="background: gray; color: white;">Oddiy obuna</span></p>
            </div>

            <div class="profile-stats">
                <div class="stat"><div class="stat-value" id="statPosts">0</div><small>Post</small></div>
                <div class="stat"><div class="stat-value" id="statProducts">0</div><small>Tovar</small></div>
                <div class="stat"><div class="stat-value" id="statLikes">0</div><small>Like</small></div>
            </div>

            <div class="profile-actions">
                <button class="action-btn" onclick="showPage('uploadPage')"><i class="fas fa-upload"></i> Joylash</button>
                <button class="action-btn" onclick="showSettings()"><i class="fas fa-cog"></i> Sozlamalar</button>
                <button class="action-btn" onclick="showPaymentModal()"><i class="fas fa-crown"></i> Premium</button>
                <button class="action-btn" onclick="logout()"><i class="fas fa-sign-out-alt"></i> Chiqish</button>
            </div>

            <div style="margin-top: 30px;">
                <h4>Mening faoliyatim</h4>
                <div id="myActivity">
                    <!-- Faoliyat bu yerda -->
                </div>
            </div>
        </div>

        <!-- 3. TO'LOV MODALI -->
        <div class="modal-overlay hidden" id="paymentModal">
            <div class="modal">
                <h3><i class="fas fa-crown"></i> Premium obuna</h3>
                <p>Biznes bozoriga kirish, tovar joylash va barcha imkoniyatlar</p>
                
                <div class="payment-options">
                    <div class="payment-option" onclick="selectPayment('stars')">
                        <div><i class="fas fa-star" style="color: gold;"></i> Telegram Stars</div>
                        <div><b>$0.5</b> (1 yil)</div>
                    </div>
                    <div class="payment-option" onclick="selectPayment('ton')">
                        <div><i class="fas fa-coins" style="color: #0098ea;"></i> TON</div>
                        <div><b>2.5 TON</b></div>
                    </div>
                    <div class="payment-option" onclick="selectPayment('card')">
                        <div><i class="fas fa-credit-card"></i> Bank karta (UZS)</div>
                        <div><b>6,250 UZS</b></div>
                    </div>
                </div>

                <button class="sell-btn" onclick="processPayment()" style="margin-top: 20px;">
                    <i class="fas fa-lock"></i> Xavfsiz to'lash
                </button>
                <button class="sell-btn" onclick="hidePaymentModal()" style="background: #666; margin-top: 10px;">
                    Bekor qilish
                </button>
            </div>
        </div>

        <!-- 4. NAVIGATSIYA -->
        <div class="nav">
            <button class="nav-btn active" onclick="showPage('homePage')"><i class="fas fa-home"></i></button>
            <button class="nav-btn" onclick="showPage('businessPage')"><i class="fas fa-store"></i></button>
            <button class="nav-btn" onclick="showPage('uploadPage')"><i class="fas fa-plus-square"></i></button>
            <button class="nav-btn" onclick="showPage('profilePage')"><span class="nav-badge" id="notificationBadge">3</span><i class="fas fa-user"></i></button>
        </div>
    </div>

    <script>
        // ========== 1. DO'MIY MA'LUMOTLAR ==========
        let currentUser = {
            id: 1,
            name: "Foydalanuvchi",
            isPremium: false,
            posts: [],
            products: [],
            balance: 0
        };

        let demoPosts = [
            {id: 1, user: "Alisher", text: "Yangi iPhone 15 Pro", likes: 245, liked: false, media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerJoyrides.mp4", type: "video"},
            {id: 2, user: "Biznes", text: "Toshkent markazida uy", likes: 89, liked: true, media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerMeltdowns.mp4", type: "video"},
            {id: 3, user: "Sardor", text: "Mening yangi mashinam", likes: 412, liked: false, media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerEscapes.mp4", type: "video"}
        ];

        let demoProducts = [
            {id: 1, title: "iPhone 15 Pro", price: "$999", type: "elektronika", media: "📱", badge: "Yangi"},
            {id: 2, title: "2 xonali uy", price: "$85,000", type: "uy", media: "🏠", badge: "Chegirma"},
            {id: 3, title: "Tesla Model 3", price: "$42,500", type: "avto", media: "🚗", badge: null},
            {id: 4, title: "Samsung TV", price: "$899", type: "elektronika", media: "📺", badge: "Sotuvda"}
        ];

        // ========== 2. SAYT ISHGA TUSHGANDA ==========
        document.addEventListener('DOMContentLoaded', function() {
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                document.getElementById('app').style.display = 'block';
                loadHomePage();
                loadBusinessPage();
                updateProfile();
            }, 2000);
        });

        // ========== 3. SAHIFALARNI YUKLASH ==========
        function showPage(pageId) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
            
            document.getElementById(pageId).classList.add('active');
            document.querySelector(`[onclick*="${pageId}"]`).classList.add('active');
        }

        function loadHomePage() {
            const feed = document.getElementById('videoFeed');
            feed.innerHTML = '';
            
            demoPosts.forEach(post => {
                const postEl = document.createElement('div');
                postEl.className = 'post';
                postEl.innerHTML = `
                    <div class="post-header">
                        <div class="post-user">
                            <div class="post-avatar"></div>
                            <div>
                                <strong>${post.user}</strong><br>
                                <small>${post.text}</small>
                            </div>
                        </div>
                        <button><i class="fas fa-ellipsis-h"></i></button>
                    </div>
                    <div class="video-container">
                        <video class="video-player" controls>
                            <source src="${post.media}" type="video/mp4">
                        </video>
                    </div>
                    <div class="post-actions">
                        <button class="post-action-btn ${post.liked ? 'liked' : ''}" onclick="toggleLike(${post.id})">
                            <i class="fas fa-heart"></i>
                        </button>
                        <button class="post-action-btn"><i class="fas fa-comment"></i></button>
                        <button class="post-action-btn"><i class="fas fa-share"></i></button>
                        <button class="post-action-btn"><i class="fas fa-bookmark"></i></button>
                    </div>
                    <div class="post-stats">
                        <b>${post.likes}</b> like • <span>12 soat oldin</span>
                    </div>
                `;
                feed.appendChild(postEl);
            });
        }

        function loadBusinessPage() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = '';
            
            demoProducts.forEach(product => {
                const productEl = document.createElement('div');
                productEl.className = 'product-card';
                productEl.innerHTML = `
                    ${product.badge ? `<div class="product-badge">${product.badge}</div>` : ''}
                    <div class="product-img">${product.media}</div>
                    <div class="product-info">
                        <strong>${product.title}</strong>
                        <div class="product-price">${product.price}</div>
                        <button class="sell-btn" onclick="viewProduct(${product.id})">Xarid qilish</button>
                    </div>
                `;
                grid.appendChild(productEl);
            });
        }

        // ========== 4. PROFILNI YANGILASH ==========
        function updateProfile() {
            document.getElementById('profileName').textContent = currentUser.name;
            document.getElementById('profileStatus').innerHTML = currentUser.isPremium 
                ? '<span class="product-badge" style="background: gold; color: black;">Premium obuna</span>'
                : '<span class="product-badge" style="background: gray; color: white;">Oddiy obuna</span>';
            
            document.getElementById('statPosts').textContent = demoPosts.length;
            document.getElementById('statProducts').textContent = demoProducts.length;
            document.getElementById('statLikes').textContent = demoPosts.reduce((sum, p) => sum + p.likes, 0);
            
            const activity = document.getElementById('myActivity');
            activity.innerHTML = `
                <div style="background: #1a1a1a; padding: 15px; border-radius: 10px; margin-top: 10px;">
                    <p><i class="fas fa-video"></i> Siz 3 ta video joyladingiz</p>
                    <p><i class="fas fa-shopping-cart"></i> 2 ta xarid amalga oshirdingiz</p>
                    <p><i class="fas fa-coins"></i> Balans: $${currentUser.balance}</p>
                </div>
            `;
        }

        // ========== 5. INTERAKTIV FUNKSIYALAR ==========
        function toggleLike(postId) {
            const post = demoPosts.find(p => p.id === postId);
            if (post) {
                post.liked = !post.liked;
                post.likes += post.liked ? 1 : -1;
                loadHomePage();
            }
        }

        function showUploadForm() {
            showPage('uploadPage');
            document.getElementById('uploadType').addEventListener('change', function() {
                document.getElementById('priceField').style.display = this.value === 'product' ? 'block' : 'none';
            });
        }

        function uploadContent() {
            const type = document.getElementById('uploadType').value;
            const title = document.getElementById('uploadTitle').value;
            const media = document.getElementById('uploadMedia').value;
            
            if (!title) {
                alert('Sarlavha kiriting!');
                return;
            }
            
            alert(`${type === 'product' ? 'Tovar' : 'Post'} muvaffaqiyatli yuklandi! (DEMO)`);
            document.getElementById('uploadTitle').value = '';
            document.getElementById('uploadMedia').value = '';
            showPage('homePage');
        }

        function viewProduct(id) {
            const product = demoProducts.find(p => p.id === id);
            if (product) {
                alert(`Xarid qilish: ${product.title} - ${product.price}\n\nBu demo versiya. Haqiqiy to'lov tizimi ulangan bo'lsa, bu yerda to'lov oynasi ochilardi.`);
            }
        }

        // ========== 6. TO'LOV TIZIMI (DEMO) ==========
        let selectedPayment = null;

        function showPaymentModal() {
            document.getElementById('paymentModal').classList.remove('hidden');
        }

        function hidePaymentModal() {
            document.getElementById('paymentModal').classList.add('hidden');
        }

        function selectPayment(type) {
            selectedPayment = type;
            document.querySelectorAll('.payment-option').forEach(el => {
                el.style.background = '#2a2a2a';
            });
            event.currentTarget.style.background = '#3a3a3a';
        }

        function processPayment() {
            if (!selectedPayment) {
                alert('To\'lov usulini tanlang!');
                return;
            }
            
            alert(`To'lov ${selectedPayment.toUpperCase()} orqali amalga oshirilmoqda...\n\nDEMO: To'lov muvaffaqiyatli!`);
            currentUser.isPremium = true;
            updateProfile();
            hidePaymentModal();
        }

        // ========== 7. BOSHQA FUNKSIYALAR ==========
        function showSettings() {
            alert('Sozlamalar sahifasi (DEMO)');
        }

        function logout() {
            if (confirm('Chiqishni istaysizmi?')) {
                alert('Chiqildi (DEMO)');
            }
        }

        // ========== 8. TELEGRAM INTEGRATSIYASI (DEMO) ==========
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.MainButton.hide();
        
        if (tg.initDataUnsafe.user) {
            currentUser.name = tg.initDataUnsafe.user.first_name || "Foydalanuvchi";
            updateProfile();
        }
    </script>
</body>
</html>
