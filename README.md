<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Oqim TV | Video Bozor</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --tg-theme-bg-color: #0f0f0f;
            --tg-theme-text-color: #ffffff;
            --tg-theme-hint-color: #aaaaaa;
            --tg-theme-button-color: #8774e1;
            --primary: #8774e1;
            --success: #34d399;
            --danger: #ef4444;
            --warning: #f59e0b;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; }
        body { background: var(--tg-theme-bg-color); color: var(--tg-theme-text-color); min-height: 100vh; }
        .container { max-width: 500px; margin: 0 auto; padding-bottom: 80px; }
        .hidden { display: none !important; }
        
        .loader { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #0f0f0f; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 1000; }
        .spinner { width: 50px; height: 50px; border: 3px solid rgba(135, 116, 225, 0.3); border-radius: 50%; border-top-color: var(--primary); animation: spin 1s linear infinite; }
        @keyframes spin { to { transform: rotate(360deg); } }
        
        .nav { display: flex; justify-content: space-around; background: #1a1a1a; position: fixed; bottom: 0; width: 100%; max-width: 500px; padding: 12px 0; border-top: 1px solid #333; z-index: 100; }
        .nav-btn { background: none; border: none; color: #666; font-size: 24px; position: relative; transition: all 0.3s; }
        .nav-btn.active { color: var(--primary); transform: translateY(-5px); }
        .nav-badge { position: absolute; top: -5px; right: -8px; background: var(--danger); color: white; font-size: 10px; padding: 2px 6px; border-radius: 10px; }
        
        .page { padding: 15px; display: none; animation: fadeIn 0.3s; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .header h3 { display: flex; align-items: center; gap: 10px; }
        
        .story-list { display: flex; gap: 15px; padding: 15px 0; overflow-x: auto; }
        .story { width: 70px; flex-shrink: 0; text-align: center; cursor: pointer; }
        .story-avatar { width: 60px; height: 60px; border-radius: 50%; background: linear-gradient(45deg, gold, red); padding: 3px; margin: 0 auto 5px; }
        .story-avatar img { width: 100%; height: 100%; border-radius: 50%; border: 2px solid #0f0f0f; }
        .story.new .story-avatar { background: linear-gradient(45deg, var(--primary), #34d399); position: relative; }
        .story.new .story-avatar:after { content: '+'; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); font-size: 24px; font-weight: bold; }
        
        .feed { margin-top: 10px; }
        .post { background: #1a1a1a; border-radius: 15px; margin-bottom: 20px; overflow: hidden; border: 1px solid #2a2a2a; }
        .post-header { display: flex; justify-content: space-between; align-items: center; padding: 12px; }
        .post-user { display: flex; align-items: center; gap: 10px; }
        .post-avatar { width: 40px; height: 40px; border-radius: 50%; background: #333; display: flex; align-items: center; justify-content: center; }
        .post-info { flex: 1; }
        .post-info strong { display: block; }
        .post-info small { color: #aaa; font-size: 12px; }
        .post-menu { background: none; border: none; color: #aaa; font-size: 18px; }
        
        .video-container { position: relative; width: 100%; height: 400px; background: #000; overflow: hidden; }
        .video-player { width: 100%; height: 100%; object-fit: cover; }
        .video-controls { position: absolute; bottom: 10px; left: 0; right: 0; display: flex; justify-content: space-between; align-items: center; padding: 0 15px; opacity: 0; transition: opacity 0.3s; }
        .video-container:hover .video-controls { opacity: 1; }
        .play-btn, .volume-btn { background: rgba(0,0,0,0.7); border: none; color: white; width: 40px; height: 40px; border-radius: 50%; }
        
        .post-actions { display: flex; justify-content: space-around; padding: 12px; font-size: 22px; border-bottom: 1px solid #2a2a2a; }
        .post-action-btn { background: none; border: none; color: white; position: relative; }
        .post-action-btn.liked { color: var(--danger); }
        .post-action-btn span { position: absolute; top: -8px; right: -8px; background: var(--danger); color: white; font-size: 10px; padding: 2px 5px; border-radius: 10px; }
        
        .post-stats { padding: 12px; font-size: 14px; color: #aaa; }
        .post-description { padding: 0 12px 12px; }
        
        .product-filters { display: flex; gap: 10px; padding: 15px 0; overflow-x: auto; }
        .filter-btn { padding: 8px 16px; background: #2a2a2a; border: none; border-radius: 20px; color: white; white-space: nowrap; transition: all 0.3s; }
        .filter-btn.active { background: var(--primary); }
        
        .product-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; margin-top: 15px; }
        .product-card { background: #1a1a1a; border-radius: 15px; overflow: hidden; position: relative; border: 1px solid #2a2a2a; transition: transform 0.3s; }
        .product-card:hover { transform: translateY(-5px); }
        .product-badge { position: absolute; top: 10px; left: 10px; background: gold; color: black; padding: 3px 8px; border-radius: 10px; font-size: 12px; font-weight: bold; z-index: 1; }
        .product-img { width: 100%; height: 150px; background: #333; display: flex; align-items: center; justify-content: center; font-size: 40px; position: relative; overflow: hidden; }
        .product-img img { width: 100%; height: 100%; object-fit: cover; }
        .product-info { padding: 12px; }
        .product-title { font-weight: bold; margin-bottom: 5px; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; }
        .product-price { color: var(--primary); font-size: 18px; font-weight: bold; margin: 5px 0; }
        .product-meta { display: flex; justify-content: space-between; font-size: 12px; color: #aaa; }
        
        .btn { background: var(--primary); color: white; border: none; padding: 12px; border-radius: 10px; width: 100%; margin-top: 10px; font-weight: bold; cursor: pointer; transition: all 0.3s; }
        .btn:hover { opacity: 0.9; }
        .btn-secondary { background: #2a2a2a; }
        .btn-success { background: var(--success); }
        .btn-danger { background: var(--danger); }
        
        .form-group { margin-bottom: 15px; }
        .form-group label { display: block; margin-bottom: 5px; color: #aaa; }
        .form-control { width: 100%; padding: 12px; background: #2a2a2a; border: 1px solid #333; border-radius: 10px; color: white; font-size: 16px; }
        select.form-control { appearance: none; background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='white'%3e%3cpath d='M7 10l5 5 5-5z'/%3e%3c/svg%3e"); background-repeat: no-repeat; background-position: right 10px center; background-size: 20px; }
        
        .profile-header { text-align: center; padding: 20px; }
        .profile-avatar { width: 100px; height: 100px; border-radius: 50%; background: linear-gradient(45deg, var(--primary), #34d399); margin: 0 auto 15px; position: relative; display: flex; align-items: center; justify-content: center; font-size: 40px; }
        .profile-edit { position: absolute; bottom: 0; right: 0; background: var(--primary); width: 30px; height: 30px; border-radius: 50%; border: none; color: white; }
        
        .profile-stats { display: flex; justify-content: space-around; padding: 20px; background: #1a1a1a; border-radius: 15px; margin: 20px 0; }
        .stat { text-align: center; cursor: pointer; }
        .stat-value { font-size: 20px; font-weight: bold; }
        .stat-label { font-size: 12px; color: #aaa; }
        
        .profile-actions { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        .action-btn { padding: 15px; background: #2a2a2a; border: none; border-radius: 10px; color: white; text-align: center; display: flex; flex-direction: column; align-items: center; gap: 5px; cursor: pointer; transition: all 0.3s; }
        .action-btn:hover { background: #333; }
        
        .activity-list { margin-top: 10px; }
        .activity-item { display: flex; align-items: center; gap: 10px; padding: 12px; background: #1a1a1a; border-radius: 10px; margin-bottom: 10px; }
        .activity-icon { width: 40px; height: 40px; border-radius: 50%; background: #2a2a2a; display: flex; align-items: center; justify-content: center; }
        
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); display: flex; justify-content: center; align-items: center; z-index: 1000; padding: 20px; }
        .modal { background: #1a1a1a; width: 100%; max-width: 400px; border-radius: 20px; padding: 25px; max-height: 90vh; overflow-y: auto; }
        .modal-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .modal-close { background: none; border: none; color: #aaa; font-size: 24px; }
        
        .payment-options { margin: 20px 0; }
        .payment-option { display: flex; align-items: center; justify-content: space-between; padding: 15px; background: #2a2a2a; margin: 10px 0; border-radius: 10px; cursor: pointer; transition: all 0.3s; border: 2px solid transparent; }
        .payment-option:hover { background: #333; }
        .payment-option.selected { border-color: var(--primary); background: rgba(135, 116, 225, 0.1); }
        .payment-icon { font-size: 24px; margin-right: 10px; }
        .payment-info { flex: 1; }
        .payment-title { font-weight: bold; }
        .payment-desc { font-size: 12px; color: #aaa; }
        .payment-amount { font-weight: bold; }
        
        .toast { position: fixed; bottom: 80px; left: 50%; transform: translateX(-50%); background: var(--primary); color: white; padding: 12px 20px; border-radius: 10px; z-index: 1001; animation: slideUp 0.3s; }
        @keyframes slideUp { from { bottom: -50px; } to { bottom: 80px; } }
        
        .search-bar { display: flex; gap: 10px; margin-bottom: 20px; }
        .search-input { flex: 1; padding: 12px; background: #2a2a2a; border: none; border-radius: 10px; color: white; }
        .search-btn { background: var(--primary); border: none; color: white; width: 50px; border-radius: 10px; }
        
        @media (max-width: 480px) {
            .product-grid { grid-template-columns: 1fr; }
            .video-container { height: 300px; }
            .profile-actions { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>
    <div class="loader" id="loader">
        <div class="spinner"></div>
        <p style="margin-top: 20px;">Oqim TV yuklanmoqda...</p>
    </div>

    <div class="container" id="app" style="display: none;">
        <!-- HOME PAGE -->
        <div class="page active" id="homePage">
            <div class="header">
                <h3><i class="fas fa-home"></i> Asosiy Oqim</h3>
                <button class="search-btn" onclick="showSearch()"><i class="fas fa-search"></i></button>
            </div>
            
            <div class="story-list">
                <div class="story new" onclick="addStory()">
                    <div class="story-avatar"></div>
                    <small>Qo'shish</small>
                </div>
                <div class="story">
                    <div class="story-avatar" style="background: linear-gradient(45deg, red, orange);">
                        <i class="fas fa-user" style="font-size: 24px; line-height: 54px; color: white;"></i>
                    </div>
                    <small>Alisher</small>
                </div>
                <div class="story">
                    <div class="story-avatar" style="background: linear-gradient(45deg, purple, blue);">
                        <i class="fas fa-store" style="font-size: 24px; line-height: 54px; color: white;"></i>
                    </div>
                    <small>Biznes</small>
                </div>
            </div>

            <div class="search-bar hidden" id="searchBar">
                <input type="text" class="search-input" placeholder="Qidirish..." id="searchInput">
                <button class="search-btn" onclick="performSearch()"><i class="fas fa-search"></i></button>
            </div>

            <div class="feed" id="videoFeed">
                <!-- Videolar bu yerda yuklanadi -->
            </div>
        </div>

        <!-- BUSINESS PAGE -->
        <div class="page" id="businessPage">
            <div class="header">
                <h3><i class="fas fa-store"></i> Biznes Bozori</h3>
                <button class="btn" onclick="showPage('uploadPage')" style="width: auto; padding: 8px 16px;">
                    <i class="fas fa-plus"></i> Joylash
                </button>
            </div>
            
            <div class="product-filters">
                <button class="filter-btn active" onclick="filterProducts('all')">Hammasi</button>
                <button class="filter-btn" onclick="filterProducts('uy')">Uy-joy</button>
                <button class="filter-btn" onclick="filterProducts('avto')">Avto</button>
                <button class="filter-btn" onclick="filterProducts('elektronika')">Elektronika</button>
                <button class="filter-btn" onclick="filterProducts('kiyim')">Kiyim</button>
                <button class="filter-btn" onclick="filterProducts('boshqa')">Boshqa</button>
            </div>

            <div class="product-grid" id="productGrid">
                <!-- Tovarlar bu yerda yuklanadi -->
            </div>
        </div>

        <!-- UPLOAD PAGE -->
        <div class="page" id="uploadPage">
            <div class="header">
                <h3><i class="fas fa-cloud-upload-alt"></i> Kontent joylash</h3>
                <button class="btn-secondary" onclick="showPage('homePage')" style="width: auto; padding: 8px 16px;">
                    Orqaga
                </button>
            </div>
            
            <div class="form-group">
                <label>Kontent turi</label>
                <select class="form-control" id="uploadType" onchange="togglePriceField()">
                    <option value="post">Post (Video/Rasm)</option>
                    <option value="product">Tovar sotish</option>
                    <option value="story">Hikoya</option>
                </select>
            </div>
            
            <div class="form-group">
                <label id="titleLabel">Sarlavha</label>
                <input type="text" class="form-control" id="uploadTitle" placeholder="Sarlavha kiriting...">
            </div>
            
            <div class="form-group">
                <label id="descLabel">Tavsif</label>
                <textarea class="form-control" id="uploadDescription" placeholder="Tavsif kiriting..." rows="3"></textarea>
            </div>
            
            <div class="form-group" id="priceField" style="display: none;">
                <label>Narx (USD)</label>
                <input type="number" class="form-control" id="uploadPrice" placeholder="0.00" step="0.01">
            </div>
            
            <div class="form-group" id="categoryField" style="display: none;">
                <label>Kategoriya</label>
                <select class="form-control" id="uploadCategory">
                    <option value="uy">Uy-joy</option>
                    <option value="avto">Avto</option>
                    <option value="elektronika">Elektronika</option>
                    <option value="kiyim">Kiyim</option>
                    <option value="boshqa">Boshqa</option>
                </select>
            </div>
            
            <div class="form-group">
                <label>Media (URL)</label>
                <input type="text" class="form-control" id="uploadMedia" placeholder="https://example.com/image.jpg">
                <small style="color: #aaa;">Video yoki rasm linkini kiriting</small>
            </div>
            
            <button class="btn" onclick="uploadContent()">
                <i class="fas fa-upload"></i> Yuklash
            </button>
        </div>

        <!-- PROFILE PAGE -->
        <div class="page" id="profilePage">
            <div class="profile-header">
                <div class="profile-avatar">
                    <i class="fas fa-user"></i>
                    <button class="profile-edit" onclick="editProfile()">
                        <i class="fas fa-pen"></i>
                    </button>
                </div>
                <h3 id="profileName">Foydalanuvchi</h3>
                <p id="profileStatus">
                    <span class="product-badge" style="background: gray; color: white; display: inline-block;">Oddiy obuna</span>
                </p>
                <p style="color: #aaa; margin-top: 10px;" id="profileBalance">Balans: $0.00</p>
            </div>

            <div class="profile-stats">
                <div class="stat" onclick="showUserPosts()">
                    <div class="stat-value" id="statPosts">0</div>
                    <div class="stat-label">Postlar</div>
                </div>
                <div class="stat" onclick="showUserProducts()">
                    <div class="stat-value" id="statProducts">0</div>
                    <div class="stat-label">Tovarlar</div>
                </div>
                <div class="stat" onclick="showUserLikes()">
                    <div class="stat-value" id="statLikes">0</div>
                    <div class="stat-label">Likelar</div>
                </div>
            </div>

            <div class="profile-actions">
                <button class="action-btn" onclick="showPage('uploadPage')">
                    <i class="fas fa-upload"></i>
                    <span>Joylash</span>
                </button>
                <button class="action-btn" onclick="showSettings()">
                    <i class="fas fa-cog"></i>
                    <span>Sozlamalar</span>
                </button>
                <button class="action-btn" onclick="showPaymentModal()">
                    <i class="fas fa-crown"></i>
                    <span>Premium</span>
                </button>
                <button class="action-btn" onclick="showWallet()">
                    <i class="fas fa-wallet"></i>
                    <span>Hamjamiyat</span>
                </button>
            </div>

            <h4 style="margin-top: 30px;">Mening faoliyatim</h4>
            <div class="activity-list" id="myActivity">
                <!-- Faoliyat bu yerda -->
            </div>
        </div>

        <!-- PAYMENT MODAL -->
        <div class="modal-overlay hidden" id="paymentModal">
            <div class="modal">
                <div class="modal-header">
                    <h3><i class="fas fa-crown"></i> Premium Obuna</h3>
                    <button class="modal-close" onclick="hidePaymentModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                <p>Premium obuna xususiyatlari:</p>
                <ul style="margin: 10px 0 20px 20px; color: #aaa;">
                    <li>Biznes bozoriga kirish</li>
                    <li>Cheksiz tovar joylash</li>
                    <li>Reklama berish imkoniyati</li>
                    <li>Statistika ko'rish</li>
                    <li>Premium belgisi</li>
                </ul>
                
                <div class="payment-options">
                    <div class="payment-option" onclick="selectPayment('ton', 2.5, 'TON')">
                        <div style="display: flex; align-items: center;">
                            <div class="payment-icon" style="color: #0098ea;">
                                <i class="fas fa-coins"></i>
                            </div>
                            <div class="payment-info">
                                <div class="payment-title">TON</div>
                                <div class="payment-desc">Telegraph Open Network</div>
                            </div>
                        </div>
                        <div class="payment-amount">2.5 TON</div>
                    </div>
                    
                    <div class="payment-option" onclick="selectPayment('usdt', 5, 'USD')">
                        <div style="display: flex; align-items: center;">
                            <div class="payment-icon" style="color: #26a17b;">
                                <i class="fas fa-coins"></i>
                            </div>
                            <div class="payment-info">
                                <div class="payment-title">USDT (TON)</div>
                                <div class="payment-desc">Tether on TON network</div>
                            </div>
                        </div>
                        <div class="payment-amount">5.0 USDT</div>
                    </div>
                    
                    <div class="payment-option" onclick="selectPayment('stars', 500, 'Stars')">
                        <div style="display: flex; align-items: center;">
                            <div class="payment-icon" style="color: gold;">
                                <i class="fas fa-star"></i>
                            </div>
                            <div class="payment-info">
                                <div class="payment-title">Telegram Stars</div>
                                <div class="payment-desc">Telegram ichki valyutasi</div>
                            </div>
                        </div>
                        <div class="payment-amount">500 Stars</div>
                    </div>
                </div>

                <button class="btn" onclick="processPayment()" style="margin-top: 20px;">
                    <i class="fas fa-lock"></i> Xavfsiz to'lash
                </button>
                <button class="btn btn-secondary" onclick="hidePaymentModal()" style="margin-top: 10px;">
                    Bekor qilish
                </button>
            </div>
        </div>

        <!-- WALLET MODAL -->
        <div class="modal-overlay hidden" id="walletModal">
            <div class="modal">
                <div class="modal-header">
                    <h3><i class="fas fa-wallet"></i> Hamjamiyat</h3>
                    <button class="modal-close" onclick="hideWalletModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                
                <div style="text-align: center; padding: 20px 0;">
                    <div style="font-size: 40px; color: var(--primary);">
                        <i class="fas fa-wallet"></i>
                    </div>
                    <h3 id="walletBalance">0 TON</h3>
                    <p style="color: #aaa;">Joriy balans</p>
                </div>
                
                <div class="form-group">
                    <label>Hamjamiyat manzili (faqat TON qabul qiladi)</label>
                    <input type="text" class="form-control" id="walletAddress" value="UQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA" readonly>
                    <small style="color: #aaa;">Bu manzilga faqat TON yuborishingiz mumkin</small>
                </div>
                
                <div class="form-group">
                    <label>USDT manzili (TON tarmog'ida)</label>
                    <input type="text" class="form-control" id="usdtAddress" value="UQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA" readonly>
                    <small style="color: #aaa;">Bu manzilga faqat USDT (TON) yuborishingiz mumkin</small>
                </div>
                
                <button class="btn btn-secondary" onclick="copyAddress('walletAddress')">
                    <i class="fas fa-copy"></i> TON manzilini nusxalash
                </button>
                <button class="btn btn-secondary" onclick="copyAddress('usdtAddress')" style="margin-top: 10px;">
                    <i class="fas fa-copy"></i> USDT manzilini nusxalash
                </button>
                
                <div style="margin-top: 20px; padding: 15px; background: #2a2a2a; border-radius: 10px;">
                    <h4>DIQQAT!</h4>
                    <p style="color: #aaa; font-size: 12px;">
                        Ushbu manzillar faqat demo maqsadlarda ko'rsatilgan. 
                        <strong>Haqiqiy loyihada siz o'zingizning hamjamiyat manzilingizni kiritishingiz kerak.</strong>
                        Manzilni `YOUR_WALLET_ADDRESS` o'rniga qo'ying.
                    </p>
                </div>
            </div>
        </div>

        <!-- SETTINGS MODAL -->
        <div class="modal-overlay hidden" id="settingsModal">
            <div class="modal">
                <div class="modal-header">
                    <h3><i class="fas fa-cog"></i> Sozlamalar</h3>
                    <button class="modal-close" onclick="hideSettingsModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                
                <div class="form-group">
                    <label>Ism</label>
                    <input type="text" class="form-control" id="userName" value="Foydalanuvchi">
                </div>
                
                <div class="form-group">
                    <label>Til</label>
                    <select class="form-control" id="userLanguage">
                        <option value="uz">O'zbekcha</option>
                        <option value="ru">Русский</option>
                        <option value="en">English</option>
                    </select>
                </div>
                
                <div class="form-group">
                    <label>Bildirishnomalar</label>
                    <div style="display: flex; justify-content: space-between; align-items: center;">
                        <span>Push-bildirishnomalar</span>
                        <label class="switch">
                            <input type="checkbox" id="notifications" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                </div>
                
                <button class="btn" onclick="saveSettings()">
                    <i class="fas fa-save"></i> Saqlash
                </button>
                <button class="btn btn-danger" onclick="logout()" style="margin-top: 10px;">
                    <i class="fas fa-sign-out-alt"></i> Chiqish
                </button>
            </div>
        </div>

        <!-- NAVIGATION -->
        <div class="nav">
            <button class="nav-btn active" onclick="showPage('homePage')">
                <i class="fas fa-home"></i>
            </button>
            <button class="nav-btn" onclick="showPage('businessPage')">
                <i class="fas fa-store"></i>
            </button>
            <button class="nav-btn" onclick="showPage('uploadPage')">
                <i class="fas fa-plus-square"></i>
            </button>
            <button class="nav-btn" onclick="showPage('profilePage')">
                <i class="fas fa-user"></i>
                <span class="nav-badge hidden" id="notificationBadge">3</span>
            </button>
        </div>
        
        <!-- TOAST NOTIFICATION -->
        <div class="toast hidden" id="toast"></div>
    </div>

    <script>
        // ========== 1. ASOSIY O'ZGARUVCHILAR ==========
        let currentUser = {
            id: 1,
            name: "Foydalanuvchi",
            isPremium: false,
            balance: 0,
            walletAddress: "YOUR_WALLET_ADDRESS", // BU YERNI O'ZGARTIRING!
            language: "uz",
            notifications: true
        };

        let demoPosts = [
            {id: 1, user: "Alisher", text: "Yangi iPhone 15 Pro Max sotiladi", desc: "128GB, qora rang, yangi, chegaradan olib kelingan", likes: 245, comments: 12, liked: false, media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerJoyrides.mp4", type: "video", userId: 2, timestamp: Date.now() - 3600000},
            {id: 2, user: "Biznes", text: "Toshkent markazida 2 xonali uy", desc: "Yangi qurilgan, 80 m², mebellangan", likes: 89, comments: 5, liked: true, media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerMeltdowns.mp4", type: "video", userId: 3, timestamp: Date.now() - 7200000},
            {id: 3, user: "Sardor", text: "Mening yangi Tesla Model 3", desc: "2023 yil, qizil rang, 10,000 km", likes: 412, comments: 45, liked: false, media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerEscapes.mp4", type: "video", userId: 4, timestamp: Date.now() - 10800000}
        ];

        let demoProducts = [
            {id: 1, title: "iPhone 15 Pro Max", desc: "128GB, qora rang, yangi", price: "999", type: "elektronika", media: "📱", badge: "Yangi", userId: 2, timestamp: Date.now() - 3600000},
            {id: 2, title: "2 xonali uy", desc: "Toshkent markazi, 80 m²", price: "85000", type: "uy", media: "🏠", badge: "Chegirma", userId: 3, timestamp: Date.now() - 7200000},
            {id: 3, title: "Tesla Model 3", desc: "2023 yil, qizil rang", price: "42500", type: "avto", media: "🚗", badge: null, userId: 4, timestamp: Date.now() - 10800000},
            {id: 4, title: "Samsung 4K TV 65\"", desc: "Smart TV, yangi", price: "899", type: "elektronika", media: "📺", badge: "Sotuvda", userId: 2, timestamp: Date.now() - 14400000}
        ];

        let userPosts = [];
        let userProducts = [];
        let selectedPayment = null;
        let currentFilter = "all";

        // ========== 2. SAYT ISHGA TUSHGANDA ==========
        document.addEventListener('DOMContentLoaded', function() {
            // Telegram WebApp init
            const tg = window.Telegram.WebApp;
            tg.expand();
            tg.MainButton.hide();
            
            // Foydalanuvchi ma'lumotlarini olish
            if (tg.initDataUnsafe.user) {
                currentUser.id = tg.initDataUnsafe.user.id;
                currentUser.name = tg.initDataUnsafe.user.first_name || "Foydalanuvchi";
                if (tg.initDataUnsafe.user.username) {
                    currentUser.name += ` (@${tg.initDataUnsafe.user.username})`;
                }
            }
            
            // Demo ma'lumotlarni o'qish
            loadUserData();
            
            // Sahifalarni yuklash
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                document.getElementById('app').style.display = 'block';
                loadHomePage();
                loadBusinessPage();
                updateProfile();
                checkNotifications();
            }, 1500);
        });

        // ========== 3. SAHIFA NAVIGATSIYASI ==========
        function showPage(pageId) {
            // Sahifalarni yashirish
            document.querySelectorAll('.page').forEach(p => {
                p.classList.remove('active');
                if (p.id === 'searchBar') p.classList.add('hidden');
            });
            
            // Navigation tugmalarini yangilash
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
            
            // Yangi sahifani ko'rsatish
            document.getElementById(pageId).classList.add('active');
            
            // Navigation tugmasini faollashtirish
            if (pageId === 'homePage') {
                document.querySelector('.nav-btn:nth-child(1)').classList.add('active');
            } else if (pageId === 'businessPage') {
                document.querySelector('.nav-btn:nth-child(2)').classList.add('active');
            } else if (pageId === 'uploadPage') {
                document.querySelector('.nav-btn:nth-child(3)').classList.add('active');
            } else if (pageId === 'profilePage') {
                document.querySelector('.nav-btn:nth-child(4)').classList.add('active');
            }
            
            // Maxsus sahifa yuklash
            if (pageId === 'homePage') loadHomePage();
            if (pageId === 'businessPage') loadBusinessPage();
            if (pageId === 'profilePage') updateProfile();
        }

        // ========== 4. HOME PAGE (VIDEO FEED) ==========
        function loadHomePage() {
            const feed = document.getElementById('videoFeed');
            feed.innerHTML = '';
            
            // Foydalanuvchining o'z postlarini boshiga qo'shish
            if (userPosts.length > 0) {
                userPosts.forEach(post => {
                    feed.appendChild(createPostElement(post, true));
                });
            }
            
            // Demo postlar
            demoPosts.forEach(post => {
                feed.appendChild(createPostElement(post, false));
            });
            
            // Agar post bo'lmasa
            if (demoPosts.length === 0 && userPosts.length === 0) {
                feed.innerHTML = `
                    <div style="text-align: center; padding: 50px 20px;">
                        <div style="font-size: 60px; color: #333; margin-bottom: 20px;">
                            <i class="fas fa-video-slash"></i>
                        </div>
                        <h3>Hozircha postlar yo'q</h3>
                        <p style="color: #aaa; margin: 10px 0 20px;">Birinchi postingizni joylang!</p>
                        <button class="btn" onclick="showPage('uploadPage')">
                            <i class="fas fa-plus"></i> Post joylash
                        </button>
                    </div>
                `;
            }
        }

        function createPostElement(post, isUserPost) {
            const postEl = document.createElement('div');
            postEl.className = 'post';
            postEl.id = `post-${post.id}`;
            
            const timeAgo = getTimeAgo(post.timestamp);
            const userLabel = isUserPost ? ' (Siz)' : '';
            
            postEl.innerHTML = `
                <div class="post-header">
                    <div class="post-user">
                        <div class="post-avatar">
                            <i class="fas fa-user"></i>
                        </div>
                        <div class="post-info">
                            <strong>${post.user}${userLabel}</strong>
                            <small>${timeAgo}</small>
                        </div>
                    </div>
                    <button class="post-menu" onclick="showPostMenu(${post.id}, ${isUserPost})">
                        <i class="fas fa-ellipsis-h"></i>
                    </button>
                </div>
                
                <div class="post-description">
                    <strong>${post.text}</strong>
                    <p style="color: #aaa; margin-top: 5px;">${post.desc || ''}</p>
                </div>
                
                ${post.type === 'video' ? `
                    <div class="video-container">
                        <video class="video-player" id="video-${post.id}" controls>
                            <source src="${post.media}" type="video/mp4">
                        </video>
                        <div class="video-controls">
                            <button class="play-btn" onclick="togglePlay(${post.id})">
                                <i class="fas fa-play" id="play-icon-${post.id}"></i>
                            </button>
                            <button class="volume-btn" onclick="toggleVolume(${post.id})">
                                <i class="fas fa-volume-up" id="volume-icon-${post.id}"></i>
                            </button>
                        </div>
                    </div>
                ` : `
                    <div style="width: 100%; height: 300px; background: #333; display: flex; align-items: center; justify-content: center; font-size: 60px;">
                        ${post.media || '📷'}
                    </div>
                `}
                
                <div class="post-actions">
                    <button class="post-action-btn ${post.liked ? 'liked' : ''}" onclick="toggleLike(${post.id})">
                        <i class="fas fa-heart"></i>
                        ${post.likes > 0 ? `<span>${post.likes}</span>` : ''}
                    </button>
                    <button class="post-action-btn" onclick="commentPost(${post.id})">
                        <i class="fas fa-comment"></i>
                        ${post.comments > 0 ? `<span>${post.comments}</span>` : ''}
                    </button>
                    <button class="post-action-btn" onclick="sharePost(${post.id})">
                        <i class="fas fa-share"></i>
                    </button>
                    <button class="post-action-btn" onclick="savePost(${post.id})">
                        <i class="fas fa-bookmark"></i>
                    </button>
                </div>
                
                <div class="post-stats">
                    <b>${post.likes}</b> like • <b>${post.comments}</b> comment • <span>${timeAgo}</span>
                </div>
            `;
            
            return postEl;
        }

        // ========== 5. BUSINESS PAGE (PRODUCTS) ==========
        function loadBusinessPage() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = '';
            
            // Foydalanuvchining o'z tovarlarini boshiga qo'shish
            if (userProducts.length > 0) {
                userProducts.forEach(product => {
                    if (currentFilter === 'all' || product.type === currentFilter) {
                        grid.appendChild(createProductElement(product, true));
                    }
                });
            }
            
            // Demo tovarlar
            demoProducts.forEach(product => {
                if (currentFilter === 'all' || product.type === currentFilter) {
                    grid.appendChild(createProductElement(product, false));
                }
            });
            
            // Agar tovar bo'lmasa
            if (grid.children.length === 0) {
                grid.innerHTML = `
                    <div style="grid-column: 1 / -1; text-align: center; padding: 50px 20px;">
                        <div style="font-size: 60px; color: #333; margin-bottom: 20px;">
                            <i class="fas fa-box-open"></i>
                        </div>
                        <h3>Hozircha tovarlar yo'q</h3>
                        <p style="color: #aaa; margin: 10px 0 20px;">Birinchi tovaringizni joylang!</p>
                        <button class="btn" onclick="showPage('uploadPage')">
                            <i class="fas fa-plus"></i> Tovar joylash
                        </button>
                    </div>
                `;
            }
        }

        function createProductElement(product, isUserProduct) {
            const productEl = document.createElement('div');
            productEl.className = 'product-card';
            productEl.id = `product-${product.id}`;
            
            const userLabel = isUserProduct ? ' (Siz)' : '';
            
            productEl.innerHTML = `
                ${product.badge ? `<div class="product-badge">${product.badge}</div>` : ''}
                <div class="product-img">
                    ${product.media}
                </div>
                <div class="product-info">
                    <div class="product-title">${product.title}${userLabel}</div>
                    <p style="color: #aaa; font-size: 12px; margin: 5px 0;">${product.desc}</p>
                    <div class="product-price">$${parseFloat(product.price).toLocaleString()}</div>
                    <div class="product-meta">
                        <span>${getTimeAgo(product.timestamp)}</span>
                        <span>${isUserProduct ? 'Sotuvda' : product.userId === 2 ? 'Alisher' : product.userId === 3 ? 'Biznes' : 'Sardor'}</span>
                    </div>
                    <button class="btn" onclick="viewProduct(${product.id}, ${isUserProduct})">
                        <i class="fas fa-shopping-cart"></i> Xarid qilish
                    </button>
                </div>
            `;
            
            return productEl;
        }

        function filterProducts(filter) {
            currentFilter = filter;
            
            // Filter tugmalarini yangilash
            document.querySelectorAll('.filter-btn').forEach(btn => btn.classList.remove('active'));
            event.target.classList.add('active');
            
            // Tovarlarni qayta yuklash
            loadBusinessPage();
        }

        // ========== 6. UPLOAD PAGE ==========
        function togglePriceField() {
            const type = document.getElementById('uploadType').value;
            const priceField = document.getElementById('priceField');
            const categoryField = document.getElementById('categoryField');
            const titleLabel = document.getElementById('titleLabel');
            const descLabel = document.getElementById('descLabel');
            
            if (type === 'product') {
                priceField.style.display = 'block';
                categoryField.style.display = 'block';
                titleLabel.textContent = 'Tovar nomi';
                descLabel.textContent = 'Tovar tavsifi';
            } else if (type === 'story') {
                priceField.style.display = 'none';
                categoryField.style.display = 'none';
                titleLabel.textContent = 'Hikoya sarlavhasi';
                descLabel.textContent = 'Hikoya matni';
            } else {
                priceField.style.display = 'none';
                categoryField.style.display = 'none';
                titleLabel.textContent = 'Post sarlavhasi';
                descLabel.textContent = 'Post tavsifi';
            }
        }

        function uploadContent() {
            const type = document.getElementById('uploadType').value;
            const title = document.getElementById('uploadTitle').value.trim();
            const desc = document.getElementById('uploadDescription').value.trim();
            const media = document.getElementById('uploadMedia').value.trim();
            const price = document.getElementById('uploadPrice').value;
            const category = document.getElementById('uploadCategory').value;
            
            // Validatsiya
            if (!title) {
                showToast('Sarlavha kiriting!', 'error');
                return;
            }
            
            if (!media) {
                showToast('Media URL kiriting!', 'error');
                return;
            }
            
            if (type === 'product' && (!price || parseFloat(price) <= 0)) {
                showToast('To\'g\'ri narx kiriting!', 'error');
                return;
            }
            
            // Yangi kontent yaratish
            const newId = Date.now();
            
            if (type === 'product') {
                const newProduct = {
                    id: newId,
                    title: title,
                    desc: desc,
                    price: price,
                    type: category,
                    media: "📦",
                    badge: "Yangi",
                    userId: currentUser.id,
                    timestamp: Date.now()
                };
                
                userProducts.unshift(newProduct);
                demoProducts.unshift(newProduct);
                
                showToast('Tovar muvaffaqiyatli joylandi!', 'success');
                showPage('businessPage');
                
            } else {
                const newPost = {
                    id: newId,
                    user: currentUser.name,
                    text: title,
                    desc: desc,
                    likes: 0,
                    comments: 0,
                    liked: false,
                    media: media,
                    type: type === 'story' ? 'image' : media.includes('.mp4') ? 'video' : 'image',
                    userId: currentUser.id,
                    timestamp: Date.now()
                };
                
                userPosts.unshift(newPost);
                demoPosts.unshift(newPost);
                
                showToast('Post muvaffaqiyatli joylandi!', 'success');
                showPage('homePage');
            }
            
            // Formani tozalash
            document.getElementById('uploadTitle').value = '';
            document.getElementById('uploadDescription').value = '';
            document.getElementById('uploadMedia').value = '';
            document.getElementById('uploadPrice').value = '';
            
            // Profilni yangilash
            updateProfile();
        }

        // ========== 7. PROFILE FUNCTIONS ==========
        function updateProfile() {
            // Foydalanuvchi ma'lumotlari
            document.getElementById('profileName').textContent = currentUser.name;
            document.getElementById('profileStatus').innerHTML = currentUser.isPremium 
                ? '<span class="product-badge" style="background: gold; color: black;">Premium obuna</span>'
                : '<span class="product-badge" style="background: gray; color: white;">Oddiy obuna</span>';
            
            document.getElementById('profileBalance').textContent = `Balans: $${currentUser.balance.toFixed(2)}`;
            
            // Statistikalar
            document.getElementById('statPosts').textContent = userPosts.length;
            document.getElementById('statProducts').textContent = userProducts.length;
            document.getElementById('statLikes').textContent = userPosts.reduce((sum, p) => sum + p.likes, 0) + 
                                                             demoPosts.filter(p => p.userId === currentUser.id).reduce((sum, p) => sum + p.likes, 0);
            
            // Faoliyat tarixi
            const activity = document.getElementById('myActivity');
            activity.innerHTML = '';
            
            const activities = [
                {icon: 'video', text: `${userPosts.length} ta post joyladingiz`, time: 'Bugun'},
                {icon: 'shopping-cart', text: `${userProducts.length} ta tovar joyladingiz`, time: 'Bugun'},
                {icon: 'heart', text: '15 ta postga like bosdingiz', time: '1 kun oldin'},
                {icon: 'comment', text: '3 ta izoh qoldirdingiz', time: '2 kun oldin'}
            ];
            
            activities.forEach(act => {
                const actEl = document.createElement('div');
                actEl.className = 'activity-item';
                actEl.innerHTML = `
                    <div class="activity-icon">
                        <i class="fas fa-${act.icon}"></i>
                    </div>
                    <div style="flex: 1;">
                        <div>${act.text}</div>
                        <small style="color: #aaa;">${act.time}</small>
                    </div>
                `;
                activity.appendChild(actEl);
            });
            
            // Notification badge
            const badge = document.getElementById('notificationBadge');
            if (userPosts.length > 0 || userProducts.length > 0) {
                badge.classList.remove('hidden');
                badge.textContent = Math.min(userPosts.length + userProducts.length, 9);
            } else {
                badge.classList.add('hidden');
            }
        }

        // ========== 8. PAYMENT SYSTEM (TON/USDT) ==========
        function selectPayment(type, amount, currency) {
            selectedPayment = { type, amount, currency };
            
            // UI yangilash
            document.querySelectorAll('.payment-option').forEach(opt => {
                opt.classList.remove('selected');
            });
            event.currentTarget.classList.add('selected');
        }

        function processPayment() {
            if (!selectedPayment) {
                showToast('To\'lov usulini tanlang!', 'error');
                return;
            }
            
            if (selectedPayment.type === 'ton' || selectedPayment.type === 'usdt') {
                // Telegram'ning to'lov fakturasini yaratish
                const invoiceParams = {
                    title: "Oqim TV Premium Obuna (1 yil)",
                    description: "Premium obuna xususiyatlari:\n• Biznes bozoriga kirish\n• Cheksiz tovar joylash\n• Reklama berish imkoniyati\n• Statistika ko'rish\n• Premium belgisi",
                    currency: selectedPayment.currency === 'USD' ? 'USD' : 'TON',
                    prices: [
                        { 
                            label: "Premium Obuna", 
                            amount: (selectedPayment.amount * 1000000000).toString() 
                            // Miqdor nanotonda (1 TON = 1,000,000,000 nanotons)
                        }
                    ],
                    payload: `premium_${selectedPayment.type}_${Date.now()}`,
                    // provider_token: "YOUR_PROVIDER_TOKEN" // Agar biznes akkauntingiz bo'lsa
                };

                // Telegram'ning to'lov oynasini ochish
                Telegram.WebApp.openInvoice(invoiceParams, (status) => {
                    if (status === 'paid') {
                        showToast('To\'lov muvaffaqiyatli! Premium obuna faollashtirildi.', 'success');
                        currentUser.isPremium = true;
                        updateProfile();
                        hidePaymentModal();
                        
                        // To'lov tarixiga qo'shish
                        const paymentHistory = JSON.parse(localStorage.getItem('paymentHistory') || '[]');
                        paymentHistory.push({
                            type: selectedPayment.type,
                            amount: selectedPayment.amount,
                            currency: selectedPayment.currency,
                            date: new Date().toISOString(),
                            status: 'completed'
                        });
                        localStorage.setItem('paymentHistory', JSON.stringify(paymentHistory));
                        
                    } else if (status === 'cancelled') {
                        showToast('To\'lov bekor qilindi.', 'warning');
                    } else if (status === 'failed') {
                        showToast('To\'lov amalga oshirilmadi. Iltimos, qayta urinib ko\'ring.', 'error');
                    }
                });
                
            } else if (selectedPayment.type === 'stars') {
                // Telegram Stars uchun (kelajakda qo'shish mumkin)
                showToast('Hozircha faqat TON/USDT to\'lovlari qo\'llab-quvvatlanadi.', 'info');
            }
        }

        function showPaymentModal() {
            if (!currentUser.isPremium) {
                document.getElementById('paymentModal').classList.remove('hidden');
            } else {
                showToast('Sizda allaqachon premium obuna faollashtirilgan!', 'info');
            }
        }

        function hidePaymentModal() {
            document.getElementById('paymentModal').classList.add('hidden');
            selectedPayment = null;
            document.querySelectorAll('.payment-option').forEach(opt => {
                opt.classList.remove('selected');
            });
        }

        // ========== 9. WALLET FUNCTIONS ==========
        function showWallet() {
            // Demo balans (haqiqiy loyihada API orqali olinadi)
            const demoBalance = 12.5;
            document.getElementById('walletBalance').textContent = `${demoBalance} TON`;
            
            // Hamjamiyat manzillarini ko'rsatish
            // DIQQAT: Haqiqiy loyihada currentUser.walletAddress o'z manzilingiz bilan almashtiring!
            document.getElementById('walletAddress').value = currentUser.walletAddress;
            document.getElementById('usdtAddress').value = currentUser.walletAddress;
            
            document.getElementById('walletModal').classList.remove('hidden');
        }

        function hideWalletModal() {
            document.getElementById('walletModal').classList.add('hidden');
        }

        function copyAddress(addressId) {
            const address = document.getElementById(addressId);
            address.select();
            document.execCommand('copy');
            
            showToast('Manzil nusxalandi!', 'success');
        }

        // ========== 10. INTERACTION FUNCTIONS ==========
        function toggleLike(postId) {
            const post = [...demoPosts, ...userPosts].find(p => p.id === postId);
            if (post) {
                post.liked = !post.liked;
                post.likes += post.liked ? 1 : -1;
                
                // UI yangilash
                const likeBtn = document.querySelector(`#post-${postId} .post-action-btn:nth-child(1)`);
                const likeCount = likeBtn.querySelector('span');
                
                if (post.liked) {
                    likeBtn.classList.add('liked');
                    likeBtn.innerHTML = '<i class="fas fa-heart"></i>';
                    if (likeCount) likeCount.textContent = post.likes;
                    else likeBtn.innerHTML += `<span>${post.likes}</span>`;
                } else {
                    likeBtn.classList.remove('liked');
                    likeBtn.innerHTML = '<i class="fas fa-heart"></i>';
                    if (likeCount) likeCount.textContent = post.likes;
                    else if (post.likes > 0) likeBtn.innerHTML += `<span>${post.likes}</span>`;
                }
                
                // Like'ni saqlash
                saveLike(postId, post.liked);
                updateProfile();
            }
        }

        function commentPost(postId) {
            const comment = prompt("Izohingizni kiriting:");
            if (comment && comment.trim()) {
                const post = [...demoPosts, ...userPosts].find(p => p.id === postId);
                if (post) {
                    post.comments = (post.comments || 0) + 1;
                    
                    // UI yangilash
                    const commentBtn = document.querySelector(`#post-${postId} .post-action-btn:nth-child(2)`);
                    const commentCount = commentBtn.querySelector('span');
                    
                    commentBtn.innerHTML = '<i class="fas fa-comment"></i>';
                    if (commentCount) commentCount.textContent = post.comments;
                    else commentBtn.innerHTML += `<span>${post.comments}</span>`;
                    
                    showToast('Izohingiz joylandi!', 'success');
                }
            }
        }

        function sharePost(postId) {
            const post = [...demoPosts, ...userPosts].find(p => p.id === postId);
            if (post) {
                const shareUrl = `https://t.me/share/url?url=${encodeURIComponent(window.location.href)}&text=${encodeURIComponent(post.text)}`;
                window.open(shareUrl, '_blank');
                showToast('Post ulashildi!', 'success');
            }
        }

        function savePost(postId) {
            const saved = JSON.parse(localStorage.getItem('savedPosts') || '[]');
            if (!saved.includes(postId)) {
                saved.push(postId);
                localStorage.setItem('savedPosts', JSON.stringify(saved));
                showToast('Post saqlandi!', 'success');
            } else {
                showToast('Post allaqachon saqlangan', 'info');
            }
        }

        function viewProduct(productId, isUserProduct) {
            const product = [...demoProducts, ...userProducts].find(p => p.id === productId);
            if (product) {
                if (isUserProduct) {
                    showToast('Bu sizning tovaringiz!', 'info');
                } else {
                    const confirmBuy = confirm(`${product.title}\nNarxi: $${product.price}\n\nXarid qilmoqchimisiz?`);
                    if (confirmBuy) {
                        showToast('Xarid amalga oshirildi! Sotuvchi bilan bog\'lanamiz.', 'success');
                    }
                }
            }
        }

        // ========== 11. SETTINGS FUNCTIONS ==========
        function showSettings() {
            document.getElementById('userName').value = currentUser.name.replace(/ \(@.*\)/, '');
            document.getElementById('userLanguage').value = currentUser.language;
            document.getElementById('notifications').checked = currentUser.notifications;
            document.getElementById('settingsModal').classList.remove('hidden');
        }

        function hideSettingsModal() {
            document.getElementById('settingsModal').classList.add('hidden');
        }

        function saveSettings() {
            const newName = document.getElementById('userName').value.trim();
            const language = document.getElementById('userLanguage').value;
            const notifications = document.getElementById('notifications').checked;
            
            if (newName) {
                currentUser.name = newName;
                // Foydalanuvchi nomini barcha postlarida yangilash
                userPosts.forEach(post => post.user = newName);
                demoPosts.forEach(post => {
                    if (post.userId === currentUser.id) post.user = newName;
                });
            }
            
            currentUser.language = language;
            currentUser.notifications = notifications;
            
            // Saqlash
            localStorage.setItem('userData', JSON.stringify(currentUser));
            
            showToast('Sozlamalar saqlandi!', 'success');
            updateProfile();
            hideSettingsModal();
        }

        // ========== 12. UTILITY FUNCTIONS ==========
        function getTimeAgo(timestamp) {
            const now = Date.now();
            const diff = now - timestamp;
            
            const minute = 60 * 1000;
            const hour = 60 * minute;
            const day = 24 * hour;
            const week = 7 * day;
            const month = 30 * day;
            const year = 365 * day;
            
            if (diff < minute) return 'hozirgina';
            if (diff < hour) return `${Math.floor(diff / minute)} daqiqa oldin`;
            if (diff < day) return `${Math.floor(diff / hour)} soat oldin`;
            if (diff < week) return `${Math.floor(diff / day)} kun oldin`;
            if (diff < month) return `${Math.floor(diff / week)} hafta oldin`;
            if (diff < year) return `${Math.floor(diff / month)} oy oldin`;
            return `${Math.floor(diff / year)} yil oldin`;
        }

        function showToast(message, type = 'info') {
            const toast = document.getElementById('toast');
            toast.textContent = message;
            toast.className = 'toast';
            
            // Rangni belgilash
            if (type === 'success') toast.style.background = 'var(--success)';
            else if (type === 'error') toast.style.background = 'var(--danger)';
            else if (type === 'warning') toast.style.background = 'var(--warning)';
            else toast.style.background = 'var(--primary)';
            
            toast.classList.remove('hidden');
            
            // 3 soniyadan keyin yashirish
            setTimeout(() => {
                toast.classList.add('hidden');
            }, 3000);
        }

        function loadUserData() {
            const savedData = localStorage.getItem('userData');
            if (savedData) {
                const parsed = JSON.parse(savedData);
                currentUser = { ...currentUser, ...parsed };
            }
            
            const savedPosts = localStorage.getItem('userPosts');
            if (savedPosts) userPosts = JSON.parse(savedPosts);
            
            const savedProducts = localStorage.getItem('userProducts');
            if (savedProducts) userProducts = JSON.parse(savedProducts);
        }

        function saveLike(postId, liked) {
            const likes = JSON.parse(localStorage.getItem('postLikes') || '{}');
            likes[postId] = liked;
            localStorage.setItem('postLikes', JSON.stringify(likes));
        }

        function checkNotifications() {
            // Demo notification
            setTimeout(() => {
                if (Math.random() > 0.5) {
                    showToast('Yangi postlar mavjud!', 'info');
                }
            }, 5000);
        }

        function showSearch() {
            const searchBar = document.getElementById('searchBar');
            searchBar.classList.toggle('hidden');
            if (!searchBar.classList.contains('hidden')) {
                document.getElementById('searchInput').focus();
            }
        }

        function performSearch() {
            const query = document.getElementById('searchInput').value.toLowerCase();
            if (!query) return;
            
            showToast(`"${query}" bo'yicha qidirilmoqda...`, 'info');
            // Haqiqiy loyihada bu yerda API so'rovi bo'ladi
        }

        function addStory() {
            showToast('Hikoya qo\'shish funksiyasi tez orada qo\'shiladi!', 'info');
        }

        function showPostMenu(postId, isUserPost) {
            const options = isUserPost 
                ? ['Tahrirlash', 'O\'chirish', 'Statistika']
                : ['Shikoyat qilish', 'Bloklash', 'Ulashish'];
            
            const choice = prompt(`Post uchun:\n${options.map((opt, i) => `${i+1}. ${opt}`).join('\n')}\n\nRaqam kiriting:`);
            
            if (choice) {
                const index = parseInt(choice) - 1;
                if (index >= 0 && index < options.length) {
                    showToast(`${options[index]} amalga oshirildi!`, 'info');
                }
            }
        }

        function editProfile() {
            showSettings();
        }

        function showUserPosts() {
            showPage('homePage');
            showToast('Sizning postlaringiz', 'info');
        }

        function showUserProducts() {
            showPage('businessPage');
            showToast('Sizning tovarlaringiz', 'info');
        }

        function showUserLikes() {
            showToast('Siz like bosgan postlar', 'info');
        }

        function logout() {
            if (confirm('Chiqishni istaysizmi?')) {
                localStorage.removeItem('userData');
                localStorage.removeItem('userPosts');
                localStorage.removeItem('userProducts');
                showToast('Chiqildi. Sahifa yangilanadi...', 'info');
                setTimeout(() => location.reload(), 2000);
            }
        }

        function togglePlay(videoId) {
            const video = document.getElementById(`video-${videoId}`);
            const icon = document.getElementById(`play-icon-${videoId}`);
            
            if (video.paused) {
                video.play();
                icon.className = 'fas fa-pause';
            } else {
                video.pause();
                icon.className = 'fas fa-play';
            }
        }

        function toggleVolume(videoId) {
            const video = document.getElementById(`video-${videoId}`);
            const icon = document.getElementById(`volume-icon-${videoId}`);
            
            if (video.muted) {
                video.muted = false;
                icon.className = 'fas fa-volume-up';
            } else {
                video.muted = true;
                icon.className = 'fas fa-volume-mute';
            }
        }
    </script>
</body>
</html>
