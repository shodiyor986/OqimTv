<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Oqim TV | Video Bozor</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        }

        :root {
            --primary: #8774e1;
            --secondary: #1a1a1a;
            --bg: #0f0f0f;
            --surface: #1a1a1a;
            --border: #2a2a2a;
            --text: #ffffff;
            --text-secondary: #aaaaaa;
            --success: #34d399;
            --danger: #ef4444;
            --warning: #f59e0b;
        }

        body {
            background: var(--bg);
            color: var(--text);
            min-height: 100vh;
            overflow-x: hidden;
        }

        .container {
            max-width: 500px;
            margin: 0 auto;
            padding-bottom: 80px;
            position: relative;
        }

        /* Loader */
        .loader {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--bg);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            transition: opacity 0.3s;
        }

        .loader.hidden {
            opacity: 0;
            pointer-events: none;
        }

        .spinner {
            width: 50px;
            height: 50px;
            border: 3px solid rgba(135, 116, 225, 0.3);
            border-radius: 50%;
            border-top-color: var(--primary);
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        /* Navigation */
        .nav {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: var(--surface);
            display: flex;
            justify-content: space-around;
            padding: 12px 0;
            border-top: 1px solid var(--border);
            z-index: 100;
            max-width: 500px;
            margin: 0 auto;
        }

        .nav-btn {
            background: none;
            border: none;
            color: var(--text-secondary);
            font-size: 24px;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s;
            position: relative;
        }

        .nav-btn.active {
            color: var(--primary);
            background: rgba(135, 116, 225, 0.1);
            transform: translateY(-5px);
        }

        .nav-badge {
            position: absolute;
            top: 0;
            right: 0;
            background: var(--danger);
            color: white;
            font-size: 10px;
            padding: 2px 6px;
            border-radius: 10px;
            min-width: 16px;
            text-align: center;
        }

        /* Pages */
        .page {
            padding: 15px;
            display: none;
            animation: fadeIn 0.3s;
        }

        .page.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        /* Header */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .header h2 {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        /* Buttons */
        .btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 10px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .btn:hover {
            opacity: 0.9;
            transform: translateY(-1px);
        }

        .btn-secondary {
            background: var(--surface);
        }

        .btn-danger {
            background: var(--danger);
        }

        .btn-success {
            background: var(--success);
        }

        /* Story Section */
        .story-list {
            display: flex;
            gap: 15px;
            padding: 10px 0;
            margin-bottom: 20px;
            overflow-x: auto;
            scrollbar-width: none;
        }

        .story-list::-webkit-scrollbar {
            display: none;
        }

        .story {
            width: 70px;
            flex-shrink: 0;
            text-align: center;
            cursor: pointer;
        }

        .story-avatar {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            margin: 0 auto 5px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            color: white;
            background: linear-gradient(45deg, var(--primary), #34d399);
        }

        .story.new .story-avatar {
            background: linear-gradient(45deg, var(--primary), #6c5ce7);
            position: relative;
        }

        .story-avatar img {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            object-fit: cover;
        }

        .story small {
            font-size: 11px;
            color: var(--text-secondary);
            display: block;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        /* Posts */
        .post {
            background: var(--surface);
            border-radius: 15px;
            margin-bottom: 20px;
            overflow: hidden;
            border: 1px solid var(--border);
        }

        .post-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px;
        }

        .post-user {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .post-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: linear-gradient(45deg, var(--primary), #34d399);
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 18px;
        }

        .post-info {
            flex: 1;
        }

        .post-info strong {
            display: block;
            font-size: 14px;
        }

        .post-info small {
            color: var(--text-secondary);
            font-size: 12px;
        }

        .post-menu {
            background: none;
            border: none;
            color: var(--text-secondary);
            font-size: 18px;
            cursor: pointer;
            padding: 5px;
        }

        .video-container {
            position: relative;
            width: 100%;
            height: 300px;
            background: #000;
            overflow: hidden;
        }

        .video-player {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .post-description {
            padding: 12px;
        }

        .post-description p {
            color: var(--text-secondary);
            margin-top: 5px;
            font-size: 14px;
            line-height: 1.4;
        }

        .post-actions {
            display: flex;
            justify-content: space-around;
            padding: 12px;
            border-top: 1px solid var(--border);
            border-bottom: 1px solid var(--border);
        }

        .post-action-btn {
            background: none;
            border: none;
            color: var(--text);
            font-size: 20px;
            cursor: pointer;
            position: relative;
            padding: 5px 10px;
        }

        .post-action-btn.liked {
            color: var(--danger);
        }

        .post-action-btn span {
            position: absolute;
            top: -5px;
            right: -5px;
            background: var(--danger);
            color: white;
            font-size: 10px;
            padding: 2px 5px;
            border-radius: 10px;
            min-width: 15px;
            text-align: center;
        }

        .post-stats {
            padding: 12px;
            font-size: 14px;
            color: var(--text-secondary);
        }

        /* Products */
        .product-filters {
            display: flex;
            gap: 10px;
            padding: 10px 0;
            margin-bottom: 20px;
            overflow-x: auto;
            scrollbar-width: none;
        }

        .product-filters::-webkit-scrollbar {
            display: none;
        }

        .filter-btn {
            padding: 8px 16px;
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 20px;
            color: var(--text);
            white-space: nowrap;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 14px;
        }

        .filter-btn.active {
            background: var(--primary);
            border-color: var(--primary);
        }

        .product-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
        }

        .product-card {
            background: var(--surface);
            border-radius: 15px;
            overflow: hidden;
            border: 1px solid var(--border);
            transition: transform 0.3s;
        }

        .product-card:hover {
            transform: translateY(-5px);
        }

        .product-img {
            width: 100%;
            height: 120px;
            background: var(--border);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 40px;
        }

        .product-info {
            padding: 12px;
        }

        .product-title {
            font-weight: 600;
            margin-bottom: 5px;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }

        .product-desc {
            color: var(--text-secondary);
            font-size: 12px;
            margin-bottom: 10px;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }

        .product-price {
            color: var(--primary);
            font-weight: 700;
            font-size: 16px;
            margin-bottom: 10px;
        }

        /* Forms */
        .form-group {
            margin-bottom: 20px;
        }

        .form-label {
            display: block;
            margin-bottom: 8px;
            color: var(--text-secondary);
            font-size: 14px;
        }

        .form-control {
            width: 100%;
            padding: 12px 15px;
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 10px;
            color: var(--text);
            font-size: 16px;
        }

        .form-control:focus {
            outline: none;
            border-color: var(--primary);
        }

        select.form-control {
            appearance: none;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='white'%3e%3cpath d='M7 10l5 5 5-5z'/%3e%3c/svg%3e");
            background-repeat: no-repeat;
            background-position: right 15px center;
            background-size: 20px;
            padding-right: 40px;
        }

        textarea.form-control {
            min-height: 100px;
            resize: vertical;
        }

        /* Profile */
        .profile-header {
            text-align: center;
            padding: 20px 0;
        }

        .profile-avatar {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background: linear-gradient(45deg, var(--primary), #34d399);
            margin: 0 auto 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 40px;
            position: relative;
        }

        .profile-edit {
            position: absolute;
            bottom: 5px;
            right: 5px;
            background: var(--primary);
            width: 30px;
            height: 30px;
            border-radius: 50%;
            border: none;
            color: white;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .profile-stats {
            display: flex;
            justify-content: space-around;
            padding: 20px;
            background: var(--surface);
            border-radius: 15px;
            margin: 20px 0;
        }

        .stat {
            text-align: center;
            cursor: pointer;
        }

        .stat-value {
            font-size: 20px;
            font-weight: 700;
            margin-bottom: 5px;
        }

        .stat-label {
            font-size: 12px;
            color: var(--text-secondary);
        }

        .profile-actions {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            margin-bottom: 30px;
        }

        .action-btn {
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 10px;
            padding: 15px;
            color: var(--text);
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 8px;
        }

        .action-btn:hover {
            background: var(--border);
        }

        .action-btn i {
            font-size: 24px;
        }

        /* Modals */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            padding: 20px;
        }

        .modal {
            background: var(--surface);
            width: 100%;
            max-width: 400px;
            border-radius: 20px;
            overflow: hidden;
            animation: modalSlide 0.3s;
        }

        @keyframes modalSlide {
            from {
                transform: translateY(50px);
                opacity: 0;
            }
            to {
                transform: translateY(0);
                opacity: 1;
            }
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px;
            border-bottom: 1px solid var(--border);
        }

        .modal-close {
            background: none;
            border: none;
            color: var(--text-secondary);
            font-size: 20px;
            cursor: pointer;
            padding: 5px;
        }

        .modal-body {
            padding: 20px;
            max-height: 60vh;
            overflow-y: auto;
        }

        /* Payment Options */
        .payment-option {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 15px;
            background: var(--surface);
            border: 2px solid var(--border);
            border-radius: 10px;
            margin-bottom: 10px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .payment-option:hover {
            border-color: var(--primary);
        }

        .payment-option.selected {
            border-color: var(--primary);
            background: rgba(135, 116, 225, 0.1);
        }

        .payment-icon {
            font-size: 24px;
            margin-right: 15px;
            color: var(--primary);
        }

        .payment-info {
            flex: 1;
        }

        .payment-title {
            font-weight: 600;
            margin-bottom: 5px;
        }

        .payment-desc {
            font-size: 12px;
            color: var(--text-secondary);
        }

        .payment-amount {
            font-weight: 700;
            font-size: 18px;
            color: var(--primary);
        }

        /* Toast */
        .toast {
            position: fixed;
            bottom: 90px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--primary);
            color: white;
            padding: 12px 24px;
            border-radius: 10px;
            z-index: 1001;
            animation: toastSlide 0.3s;
            max-width: 90%;
            text-align: center;
        }

        .toast.success {
            background: var(--success);
        }

        .toast.error {
            background: var(--danger);
        }

        .toast.warning {
            background: var(--warning);
        }

        @keyframes toastSlide {
            from {
                bottom: -50px;
                opacity: 0;
            }
            to {
                bottom: 90px;
                opacity: 1;
            }
        }

        /* Badge */
        .badge {
            display: inline-block;
            padding: 4px 8px;
            border-radius: 10px;
            font-size: 12px;
            font-weight: 600;
        }

        .badge-primary {
            background: var(--primary);
            color: white;
        }

        .badge-success {
            background: var(--success);
            color: white;
        }

        .badge-warning {
            background: var(--warning);
            color: black;
        }

        /* Activity */
        .activity-list {
            margin-top: 20px;
        }

        .activity-item {
            display: flex;
            align-items: center;
            gap: 15px;
            padding: 12px;
            background: var(--surface);
            border-radius: 10px;
            margin-bottom: 10px;
        }

        .activity-icon {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: var(--border);
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--primary);
        }

        .activity-content {
            flex: 1;
        }

        .activity-content p {
            margin-bottom: 5px;
        }

        .activity-time {
            font-size: 12px;
            color: var(--text-secondary);
        }

        /* Search */
        .search-bar {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .search-input {
            flex: 1;
            padding: 12px 15px;
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 10px;
            color: var(--text);
            font-size: 16px;
        }

        /* Switch */
        .switch {
            position: relative;
            display: inline-block;
            width: 50px;
            height: 24px;
        }

        .switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: var(--border);
            transition: .4s;
            border-radius: 34px;
        }

        .slider:before {
            position: absolute;
            content: "";
            height: 16px;
            width: 16px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }

        input:checked + .slider {
            background-color: var(--primary);
        }

        input:checked + .slider:before {
            transform: translateX(26px);
        }

        /* Utility */
        .hidden {
            display: none !important;
        }

        .text-center {
            text-align: center;
        }

        .mt-10 {
            margin-top: 10px;
        }

        .mt-20 {
            margin-top: 20px;
        }

        .mb-10 {
            margin-bottom: 10px;
        }

        .mb-20 {
            margin-bottom: 20px;
        }

        /* Responsive */
        @media (max-width: 480px) {
            .product-grid {
                grid-template-columns: 1fr;
            }
            
            .profile-actions {
                grid-template-columns: 1fr;
            }
            
            .video-container {
                height: 250px;
            }
        }
    </style>
</head>
<body>
    <div class="loader" id="loader">
        <div class="spinner"></div>
        <p style="margin-top: 20px;">Oqim TV yuklanmoqda...</p>
    </div>

    <div class="container" id="app">
        <!-- Home Page -->
        <div class="page active" id="homePage">
            <div class="header">
                <h2><i class="fas fa-home"></i> Asosiy Oqim</h2>
                <button class="btn btn-secondary" onclick="showSearch()">
                    <i class="fas fa-search"></i>
                </button>
            </div>

            <div class="search-bar hidden" id="searchBar">
                <input type="text" class="search-input" placeholder="Qidirish..." id="searchInput">
                <button class="btn" onclick="performSearch()">
                    <i class="fas fa-search"></i>
                </button>
            </div>

            <div class="story-list">
                <div class="story new" onclick="addStory()">
                    <div class="story-avatar">
                        <i class="fas fa-plus"></i>
                    </div>
                    <small>Qo'shish</small>
                </div>
                <div class="story" onclick="viewStory(1)">
                    <div class="story-avatar">
                        <i class="fas fa-user"></i>
                    </div>
                    <small>Alisher</small>
                </div>
                <div class="story" onclick="viewStory(2)">
                    <div class="story-avatar">
                        <i class="fas fa-store"></i>
                    </div>
                    <small>Biznes</small>
                </div>
            </div>

            <div id="videoFeed">
                <!-- Posts will be loaded here -->
            </div>
        </div>

        <!-- Business Page -->
        <div class="page" id="businessPage">
            <div class="header">
                <h2><i class="fas fa-store"></i> Biznes Bozori</h2>
                <button class="btn" onclick="showPage('uploadPage')">
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
                <!-- Products will be loaded here -->
            </div>
        </div>

        <!-- Upload Page -->
        <div class="page" id="uploadPage">
            <div class="header">
                <h2><i class="fas fa-cloud-upload-alt"></i> Kontent joylash</h2>
                <button class="btn btn-secondary" onclick="showPage('homePage')">
                    <i class="fas fa-arrow-left"></i> Orqaga
                </button>
            </div>

            <div class="form-group">
                <label class="form-label">Kontent turi</label>
                <select class="form-control" id="uploadType" onchange="toggleUploadFields()">
                    <option value="post">Post (Video/Rasm)</option>
                    <option value="product">Tovar sotish</option>
                    <option value="story">Hikoya</option>
                </select>
            </div>

            <div class="form-group">
                <label class="form-label" id="titleLabel">Sarlavha</label>
                <input type="text" class="form-control" id="uploadTitle" placeholder="Sarlavha kiriting...">
            </div>

            <div class="form-group">
                <label class="form-label" id="descLabel">Tavsif</label>
                <textarea class="form-control" id="uploadDescription" placeholder="Tavsif kiriting..." rows="3"></textarea>
            </div>

            <div class="form-group hidden" id="priceField">
                <label class="form-label">Narx (USD)</label>
                <input type="number" class="form-control" id="uploadPrice" placeholder="0.00" step="0.01">
            </div>

            <div class="form-group hidden" id="categoryField">
                <label class="form-label">Kategoriya</label>
                <select class="form-control" id="uploadCategory">
                    <option value="uy">Uy-joy</option>
                    <option value="avto">Avto</option>
                    <option value="elektronika">Elektronika</option>
                    <option value="kiyim">Kiyim</option>
                    <option value="boshqa">Boshqa</option>
                </select>
            </div>

            <div class="form-group">
                <label class="form-label">Media (URL)</label>
                <input type="text" class="form-control" id="uploadMedia" placeholder="https://example.com/image.jpg">
                <small style="color: var(--text-secondary); display: block; margin-top: 5px;">Video yoki rasm linkini kiriting</small>
            </div>

            <button class="btn" onclick="uploadContent()">
                <i class="fas fa-upload"></i> Yuklash
            </button>
        </div>

        <!-- Profile Page -->
        <div class="page" id="profilePage">
            <div class="profile-header">
                <div class="profile-avatar">
                    <i class="fas fa-user"></i>
                    <button class="profile-edit" onclick="editProfile()">
                        <i class="fas fa-pen"></i>
                    </button>
                </div>
                <h2 id="profileName">Foydalanuvchi</h2>
                <div id="profileStatus" class="mt-10">
                    <span class="badge badge-primary">Oddiy obuna</span>
                </div>
                <p class="mt-10" id="profileBalance">Balans: $0.00</p>
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

            <h3 style="margin-top: 30px;">Mening faoliyatim</h3>
            <div class="activity-list" id="myActivity">
                <!-- Activity will be loaded here -->
            </div>
        </div>

        <!-- Navigation -->
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

        <!-- Payment Modal -->
        <div class="modal-overlay hidden" id="paymentModal">
            <div class="modal">
                <div class="modal-header">
                    <h3><i class="fas fa-crown"></i> Premium Obuna</h3>
                    <button class="modal-close" onclick="hidePaymentModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                <div class="modal-body">
                    <p>Premium obuna xususiyatlari:</p>
                    <ul style="margin: 15px 0 20px 20px; color: var(--text-secondary);">
                        <li>Biznes bozoriga kirish</li>
                        <li>Cheksiz tovar joylash</li>
                        <li>Reklama berish imkoniyati</li>
                        <li>Statistika ko'rish</li>
                        <li>Premium belgisi</li>
                    </ul>

                    <div class="payment-options">
                        <div class="payment-option" onclick="selectPayment('ton', 2.5, 'TON')">
                            <div style="display: flex; align-items: center;">
                                <div class="payment-icon">
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
                                <div class="payment-icon">
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
                                <div class="payment-icon">
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

                    <button class="btn mt-20" onclick="processPayment()">
                        <i class="fas fa-lock"></i> Xavfsiz to'lash
                    </button>
                    <button class="btn btn-secondary mt-10" onclick="hidePaymentModal()">
                        Bekor qilish
                    </button>
                </div>
            </div>
        </div>

        <!-- Wallet Modal -->
        <div class="modal-overlay hidden" id="walletModal">
            <div class="modal">
                <div class="modal-header">
                    <h3><i class="fas fa-wallet"></i> Hamjamiyat</h3>
                    <button class="modal-close" onclick="hideWalletModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                <div class="modal-body">
                    <div class="text-center">
                        <div style="font-size: 40px; color: var(--primary);">
                            <i class="fas fa-wallet"></i>
                        </div>
                        <h3 id="walletBalance">0 TON</h3>
                        <p style="color: var(--text-secondary);">Joriy balans</p>
                    </div>

                    <div class="form-group mt-20">
                        <label class="form-label">TON manzili</label>
                        <input type="text" class="form-control" id="walletAddress" value="YOUR_TON_WALLET_ADDRESS" readonly>
                        <small style="color: var(--text-secondary);">Bu manzilga faqat TON yuborishingiz mumkin</small>
                    </div>

                    <div class="form-group">
                        <label class="form-label">USDT manzili (TON tarmog'ida)</label>
                        <input type="text" class="form-control" id="usdtAddress" value="YOUR_USDT_WALLET_ADDRESS" readonly>
                        <small style="color: var(--text-secondary);">Bu manzilga faqat USDT (TON) yuborishingiz mumkin</small>
                    </div>

                    <button class="btn btn-secondary" onclick="copyAddress('walletAddress')">
                        <i class="fas fa-copy"></i> TON manzilini nusxalash
                    </button>
                    <button class="btn btn-secondary mt-10" onclick="copyAddress('usdtAddress')">
                        <i class="fas fa-copy"></i> USDT manzilini nusxalash
                    </button>

                    <div style="margin-top: 20px; padding: 15px; background: var(--border); border-radius: 10px;">
                        <h4>DIQQAT!</h4>
                        <p style="color: var(--text-secondary); font-size: 12px;">
                            Ushbu manzillar faqat demo maqsadlarda ko'rsatilgan. 
                            <strong>Haqiqiy loyihada siz o'zingizning hamjamiyat manzilingizni kiritishingiz kerak.</strong>
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <!-- Settings Modal -->
        <div class="modal-overlay hidden" id="settingsModal">
            <div class="modal">
                <div class="modal-header">
                    <h3><i class="fas fa-cog"></i> Sozlamalar</h3>
                    <button class="modal-close" onclick="hideSettingsModal()">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                <div class="modal-body">
                    <div class="form-group">
                        <label class="form-label">Ism</label>
                        <input type="text" class="form-control" id="userName" placeholder="Ismingizni kiriting">
                    </div>

                    <div class="form-group">
                        <label class="form-label">Til</label>
                        <select class="form-control" id="userLanguage">
                            <option value="uz">O'zbekcha</option>
                            <option value="ru">Русский</option>
                            <option value="en">English</option>
                        </select>
                    </div>

                    <div class="form-group">
                        <label class="form-label">Bildirishnomalar</label>
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
                    <button class="btn btn-danger mt-10" onclick="logout()">
                        <i class="fas fa-sign-out-alt"></i> Chiqish
                    </button>
                </div>
            </div>
        </div>

        <!-- Toast Notification -->
        <div class="toast hidden" id="toast"></div>
    </div>

    <script>
        // ==================== APP STATE ====================
        let appState = {
            currentUser: {
                id: 1,
                name: "Foydalanuvchi",
                isPremium: false,
                balance: 0,
                language: "uz",
                notifications: true
            },
            demoPosts: [
                {
                    id: 1,
                    user: "Alisher",
                    title: "Yangi iPhone 15 Pro Max",
                    description: "128GB, qora rang, yangi, chegaradan olib kelingan",
                    likes: 245,
                    comments: 12,
                    liked: false,
                    media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerJoyrides.mp4",
                    type: "video",
                    userId: 2,
                    timestamp: Date.now() - 3600000
                },
                {
                    id: 2,
                    user: "Biznes",
                    title: "Toshkent markazida 2 xonali uy",
                    description: "Yangi qurilgan, 80 m², mebellangan",
                    likes: 89,
                    comments: 5,
                    liked: true,
                    media: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerMeltdowns.mp4",
                    type: "video",
                    userId: 3,
                    timestamp: Date.now() - 7200000
                }
            ],
            demoProducts: [
                {
                    id: 1,
                    title: "iPhone 15 Pro Max",
                    description: "128GB, qora rang, yangi",
                    price: "999",
                    category: "elektronika",
                    badge: "Yangi",
                    userId: 2,
                    timestamp: Date.now() - 3600000
                },
                {
                    id: 2,
                    title: "2 xonali uy",
                    description: "Toshkent markazi, 80 m²",
                    price: "85000",
                    category: "uy",
                    badge: "Chegirma",
                    userId: 3,
                    timestamp: Date.now() - 7200000
                }
            ],
            userPosts: [],
            userProducts: [],
            selectedPayment: null,
            currentFilter: "all"
        };

        // ==================== TELEGRAM INIT ====================
        let tg = null;
        
        document.addEventListener('DOMContentLoaded', function() {
            // Check if Telegram WebApp is available
            if (typeof Telegram !== 'undefined' && Telegram.WebApp) {
                tg = Telegram.WebApp;
                
                // Initialize Telegram WebApp
                tg.ready();
                tg.expand();
                
                // Set theme colors
                tg.setHeaderColor('#8774e1');
                tg.setBackgroundColor('#0f0f0f');
                
                // Get user data from Telegram
                if (tg.initDataUnsafe && tg.initDataUnsafe.user) {
                    const tgUser = tg.initDataUnsafe.user;
                    appState.currentUser.name = tgUser.first_name || "Foydalanuvchi";
                    if (tgUser.username) {
                        appState.currentUser.name += ` (@${tgUser.username})`;
                    }
                    appState.currentUser.id = tgUser.id;
                }
            } else {
                console.log("Telegram WebApp not detected, running in browser mode");
            }
            
            // Load saved data
            loadUserData();
            
            // Initialize app
            setTimeout(() => {
                document.getElementById('loader').classList.add('hidden');
                document.getElementById('app').style.display = 'block';
                
                loadHomePage();
                updateProfile();
            }, 1000);
        });

        // ==================== PAGE NAVIGATION ====================
        function showPage(pageId) {
            // Hide all pages
            document.querySelectorAll('.page').forEach(page => {
                page.classList.remove('active');
            });
            
            // Update navigation buttons
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            
            // Show selected page
            document.getElementById(pageId).classList.add('active');
            
            // Update active nav button
            const navIndex = {
                'homePage': 0,
                'businessPage': 1,
                'uploadPage': 2,
                'profilePage': 3
            };
            
            if (navIndex[pageId] !== undefined) {
                document.querySelectorAll('.nav-btn')[navIndex[pageId]].classList.add('active');
            }
            
            // Load page content if needed
            switch(pageId) {
                case 'homePage':
                    loadHomePage();
                    break;
                case 'businessPage':
                    loadBusinessPage();
                    break;
                case 'profilePage':
                    updateProfile();
                    break;
            }
        }

        // ==================== HOME PAGE ====================
        function loadHomePage() {
            const feed = document.getElementById('videoFeed');
            const allPosts = [...appState.userPosts, ...appState.demoPosts];
            
            if (allPosts.length === 0) {
                feed.innerHTML = `
                    <div class="text-center" style="padding: 50px 20px;">
                        <div style="font-size: 60px; color: var(--border); margin-bottom: 20px;">
                            <i class="fas fa-video-slash"></i>
                        </div>
                        <h3>Hozircha postlar yo'q</h3>
                        <p style="color: var(--text-secondary); margin: 10px 0 20px;">Birinchi postingizni joylang!</p>
                        <button class="btn" onclick="showPage('uploadPage')">
                            <i class="fas fa-plus"></i> Post joylash
                        </button>
                    </div>
                `;
                return;
            }
            
            feed.innerHTML = allPosts.map(post => createPostHTML(post)).join('');
        }

        function createPostHTML(post) {
            const isUserPost = post.userId === appState.currentUser.id;
            const timeAgo = getTimeAgo(post.timestamp);
            const userLabel = isUserPost ? ' (Siz)' : '';
            
            return `
                <div class="post" id="post-${post.id}">
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
                        <button class="post-menu" onclick="showPostMenu(${post.id})">
                            <i class="fas fa-ellipsis-h"></i>
                        </button>
                    </div>
                    
                    <div class="post-description">
                        <strong>${post.title}</strong>
                        <p>${post.description}</p>
                    </div>
                    
                    ${post.type === 'video' ? `
                        <div class="video-container">
                            <video class="video-player" id="video-${post.id}" controls>
                                <source src="${post.media}" type="video/mp4">
                                Sizning brauzeringiz video elementini qo'llab-quvvatlamaydi.
                            </video>
                        </div>
                    ` : `
                        <div style="width: 100%; height: 300px; background: var(--border); display: flex; align-items: center; justify-content: center; font-size: 60px;">
                            ${post.media.includes('http') ? `<img src="${post.media}" style="width:100%;height:100%;object-fit:cover;">` : post.media}
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
                </div>
            `;
        }

        // ==================== BUSINESS PAGE ====================
        function loadBusinessPage() {
            const grid = document.getElementById('productGrid');
            const allProducts = [...appState.userProducts, ...appState.demoProducts];
            const filteredProducts = appState.currentFilter === 'all' 
                ? allProducts 
                : allProducts.filter(p => p.category === appState.currentFilter);
            
            if (filteredProducts.length === 0) {
                grid.innerHTML = `
                    <div style="grid-column: 1 / -1; text-align: center; padding: 50px 20px;">
                        <div style="font-size: 60px; color: var(--border); margin-bottom: 20px;">
                            <i class="fas fa-box-open"></i>
                        </div>
                        <h3>Hozircha tovarlar yo'q</h3>
                        <p style="color: var(--text-secondary); margin: 10px 0 20px;">Birinchi tovaringizni joylang!</p>
                        <button class="btn" onclick="showPage('uploadPage')">
                            <i class="fas fa-plus"></i> Tovar joylash
                        </button>
                    </div>
                `;
                return;
            }
            
            grid.innerHTML = filteredProducts.map(product => createProductHTML(product)).join('');
        }

        function createProductHTML(product) {
            const isUserProduct = product.userId === appState.currentUser.id;
            const timeAgo = getTimeAgo(product.timestamp);
            
            return `
                <div class="product-card" id="product-${product.id}">
                    ${product.badge ? `<div class="badge badge-success" style="position: absolute; top: 10px; left: 10px; z-index: 1;">${product.badge}</div>` : ''}
                    <div class="product-img">
                        ${product.media || '📦'}
                    </div>
                    <div class="product-info">
                        <div class="product-title">${product.title}${isUserProduct ? ' (Siz)' : ''}</div>
                        <div class="product-desc">${product.description}</div>
                        <div class="product-price">$${parseFloat(product.price).toLocaleString()}</div>
                        <div style="display: flex; justify-content: space-between; align-items: center;">
                            <small style="color: var(--text-secondary);">${timeAgo}</small>
                            <button class="btn" onclick="viewProduct(${product.id}, ${isUserProduct})" style="padding: 8px 12px; font-size: 14px;">
                                <i class="fas fa-shopping-cart"></i>
                            </button>
                        </div>
                    </div>
                </div>
            `;
        }

        function filterProducts(filter) {
            appState.currentFilter = filter;
            
            // Update filter buttons
            document.querySelectorAll('.filter-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');
            
            // Reload products
            loadBusinessPage();
        }

        // ==================== UPLOAD PAGE ====================
        function toggleUploadFields() {
            const type = document.getElementById('uploadType').value;
            const priceField = document.getElementById('priceField');
            const categoryField = document.getElementById('categoryField');
            
            if (type === 'product') {
                priceField.classList.remove('hidden');
                categoryField.classList.remove('hidden');
                document.getElementById('titleLabel').textContent = 'Tovar nomi';
                document.getElementById('descLabel').textContent = 'Tovar tavsifi';
            } else {
                priceField.classList.add('hidden');
                categoryField.classList.add('hidden');
                document.getElementById('titleLabel').textContent = 'Sarlavha';
                document.getElementById('descLabel').textContent = type === 'story' ? 'Hikoya matni' : 'Tavsif';
            }
        }

        function uploadContent() {
            const type = document.getElementById('uploadType').value;
            const title = document.getElementById('uploadTitle').value.trim();
            const description = document.getElementById('uploadDescription').value.trim();
            const media = document.getElementById('uploadMedia').value.trim();
            const price = document.getElementById('uploadPrice').value;
            const category = document.getElementById('uploadCategory').value;
            
            // Validation
            if (!title) {
                showToast('Sarlavha kiriting!', 'error');
                return;
            }
            
            if (!description) {
                showToast('Tavsif kiriting!', 'error');
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
            
            const newId = Date.now();
            
            if (type === 'product') {
                const newProduct = {
                    id: newId,
                    title: title,
                    description: description,
                    price: price,
                    category: category,
                    badge: "Yangi",
                    userId: appState.currentUser.id,
                    timestamp: Date.now()
                };
                
                appState.userProducts.unshift(newProduct);
                saveUserData();
                showToast('Tovar muvaffaqiyatli joylandi!', 'success');
                showPage('businessPage');
                
            } else if (type === 'post') {
                const newPost = {
                    id: newId,
                    user: appState.currentUser.name,
                    title: title,
                    description: description,
                    likes: 0,
                    comments: 0,
                    liked: false,
                    media: media,
                    type: media.includes('.mp4') || media.includes('video') ? 'video' : 'image',
                    userId: appState.currentUser.id,
                    timestamp: Date.now()
                };
                
                appState.userPosts.unshift(newPost);
                saveUserData();
                showToast('Post muvaffaqiyatli joylandi!', 'success');
                showPage('homePage');
                
            } else if (type === 'story') {
                showToast('Hikoya muvaffaqiyatli joylandi!', 'success');
                showPage('homePage');
            }
            
            // Clear form
            document.getElementById('uploadTitle').value = '';
            document.getElementById('uploadDescription').value = '';
            document.getElementById('uploadMedia').value = '';
            document.getElementById('uploadPrice').value = '';
            
            // Update profile
            updateProfile();
        }

        // ==================== PROFILE FUNCTIONS ====================
        function updateProfile() {
            // Update user info
            document.getElementById('profileName').textContent = appState.currentUser.name;
            
            const statusElement = document.getElementById('profileStatus');
            if (appState.currentUser.isPremium) {
                statusElement.innerHTML = '<span class="badge badge-warning">Premium obuna</span>';
            } else {
                statusElement.innerHTML = '<span class="badge badge-primary">Oddiy obuna</span>';
            }
            
            document.getElementById('profileBalance').textContent = `Balans: $${appState.currentUser.balance.toFixed(2)}`;
            
            // Update stats
            document.getElementById('statPosts').textContent = appState.userPosts.length;
            document.getElementById('statProducts').textContent = appState.userProducts.length;
            
            const totalLikes = appState.userPosts.reduce((sum, post) => sum + post.likes, 0);
            document.getElementById('statLikes').textContent = totalLikes;
            
            // Update activity
            const activityContainer = document.getElementById('myActivity');
            const activities = [
                {
                    icon: 'video',
                    text: `${appState.userPosts.length} ta post joyladingiz`,
                    time: 'Bugun'
                },
                {
                    icon: 'shopping-cart',
                    text: `${appState.userProducts.length} ta tovar joyladingiz`,
                    time: 'Bugun'
                },
                {
                    icon: 'heart',
                    text: `${totalLikes} ta like oldingiz`,
                    time: 'Bugun'
                },
                {
                    icon: 'comment',
                    text: `${appState.userPosts.reduce((sum, post) => sum + post.comments, 0)} ta izoh oldingiz`,
                    time: 'Bugun'
                }
            ];
            
            activityContainer.innerHTML = activities.map(activity => `
                <div class="activity-item">
                    <div class="activity-icon">
                        <i class="fas fa-${activity.icon}"></i>
                    </div>
                    <div class="activity-content">
                        <p>${activity.text}</p>
                        <div class="activity-time">${activity.time}</div>
                    </div>
                </div>
            `).join('');
            
            // Update notification badge
            const badge = document.getElementById('notificationBadge');
            const totalNotifications = appState.userPosts.length + appState.userProducts.length;
            if (totalNotifications > 0) {
                badge.classList.remove('hidden');
                badge.textContent = Math.min(totalNotifications, 9);
            } else {
                badge.classList.add('hidden');
            }
        }

        // ==================== PAYMENT SYSTEM ====================
        function selectPayment(type, amount, currency) {
            appState.selectedPayment = { type, amount, currency };
            
            // Update UI
            document.querySelectorAll('.payment-option').forEach(opt => {
                opt.classList.remove('selected');
            });
            event.currentTarget.classList.add('selected');
        }

        function processPayment() {
            if (!appState.selectedPayment) {
                showToast('To\'lov usulini tanlang!', 'error');
                return;
            }
            
            showToast('To\'lov amalga oshirilmoqda...', 'warning');
            
            // Simulate payment processing
            setTimeout(() => {
                appState.currentUser.isPremium = true;
                appState.currentUser.balance += appState.selectedPayment.amount;
                
                showToast('Premium obuna muvaffaqiyatli faollashtirildi!', 'success');
                updateProfile();
                hidePaymentModal();
                saveUserData();
                
                // Reset selected payment
                appState.selectedPayment = null;
            }, 2000);
        }

        function showPaymentModal() {
            if (appState.currentUser.isPremium) {
                showToast('Sizda allaqachon premium obuna faollashtirilgan!', 'info');
            } else {
                document.getElementById('paymentModal').classList.remove('hidden');
            }
        }

        function hidePaymentModal() {
            document.getElementById('paymentModal').classList.add('hidden');
            document.querySelectorAll('.payment-option').forEach(opt => {
                opt.classList.remove('selected');
            });
            appState.selectedPayment = null;
        }

        // ==================== WALLET FUNCTIONS ====================
        function showWallet() {
            document.getElementById('walletBalance').textContent = `${appState.currentUser.balance.toFixed(2)} TON`;
            document.getElementById('walletModal').classList.remove('hidden');
        }

        function hideWalletModal() {
            document.getElementById('walletModal').classList.add('hidden');
        }

        function copyAddress(addressId) {
            const addressElement = document.getElementById(addressId);
            addressElement.select();
            addressElement.setSelectionRange(0, 99999);
            
            try {
                navigator.clipboard.writeText(addressElement.value);
                showToast('Manzil nusxalandi!', 'success');
            } catch (err) {
                console.error('Failed to copy: ', err);
                showToast('Nusxalashda xatolik!', 'error');
            }
        }

        // ==================== INTERACTION FUNCTIONS ====================
        function toggleLike(postId) {
            const allPosts = [...appState.userPosts, ...appState.demoPosts];
            const post = allPosts.find(p => p.id === postId);
            
            if (post) {
                post.liked = !post.liked;
                post.likes += post.liked ? 1 : -1;
                
                // Update UI
                const postElement = document.getElementById(`post-${postId}`);
                if (postElement) {
                    const likeBtn = postElement.querySelector('.post-action-btn');
                    const likeCount = likeBtn.querySelector('span');
                    
                    if (post.liked) {
                        likeBtn.classList.add('liked');
                    } else {
                        likeBtn.classList.remove('liked');
                    }
                    
                    if (likeCount) {
                        likeCount.textContent = post.likes;
                    } else if (post.likes > 0) {
                        likeBtn.innerHTML = `<i class="fas fa-heart"></i><span>${post.likes}</span>`;
                    }
                }
                
                saveUserData();
                updateProfile();
            }
        }

        function commentPost(postId) {
            const comment = prompt("Izohingizni kiriting:");
            if (comment && comment.trim()) {
                const allPosts = [...appState.userPosts, ...appState.demoPosts];
                const post = allPosts.find(p => p.id === postId);
                
                if (post) {
                    post.comments = (post.comments || 0) + 1;
                    
                    // Update UI
                    const postElement = document.getElementById(`post-${postId}`);
                    if (postElement) {
                        const commentBtn = postElement.querySelectorAll('.post-action-btn')[1];
                        const commentCount = commentBtn.querySelector('span');
                        
                        if (commentCount) {
                            commentCount.textContent = post.comments;
                        } else if (post.comments > 0) {
                            commentBtn.innerHTML = `<i class="fas fa-comment"></i><span>${post.comments}</span>`;
                        }
                    }
                    
                    showToast('Izohingiz joylandi!', 'success');
                    saveUserData();
                }
            }
        }

        function sharePost(postId) {
            const allPosts = [...appState.userPosts, ...appState.demoPosts];
            const post = allPosts.find(p => p.id === postId);
            
            if (post) {
                const shareText = `${post.title}\n\n${post.description}\n\nOqim TV orqali ulashildi`;
                
                if (navigator.share) {
                    navigator.share({
                        title: post.title,
                        text: post.description,
                        url: window.location.href
                    }).then(() => {
                        showToast('Post ulashildi!', 'success');
                    }).catch(console.error);
                } else {
                    // Fallback for browsers that don't support Web Share API
                    navigator.clipboard.writeText(shareText).then(() => {
                        showToast('Post matni nusxalandi!', 'success');
                    });
                }
            }
        }

        function savePost(postId) {
            const savedPosts = JSON.parse(localStorage.getItem('savedPosts') || '[]');
            
            if (!savedPosts.includes(postId)) {
                savedPosts.push(postId);
                localStorage.setItem('savedPosts', JSON.stringify(savedPosts));
                showToast('Post saqlandi!', 'success');
            } else {
                showToast('Post allaqachon saqlangan', 'info');
            }
        }

        function viewProduct(productId, isUserProduct) {
            const allProducts = [...appState.userProducts, ...appState.demoProducts];
            const product = allProducts.find(p => p.id === productId);
            
            if (product) {
                if (isUserProduct) {
                    showToast('Bu sizning tovaringiz!', 'info');
                } else {
                    const confirmBuy = confirm(`${product.title}\n\n${product.description}\n\nNarxi: $${product.price}\n\nXarid qilmoqchimisiz?`);
                    if (confirmBuy) {
                        showToast('Xarid amalga oshirildi! Sotuvchi bilan bog\'lanamiz.', 'success');
                    }
                }
            }
        }

        // ==================== SETTINGS FUNCTIONS ====================
        function showSettings() {
            document.getElementById('userName').value = appState.currentUser.name;
            document.getElementById('userLanguage').value = appState.currentUser.language;
            document.getElementById('notifications').checked = appState.currentUser.notifications;
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
                appState.currentUser.name = newName;
                // Update name in user's posts
                appState.userPosts.forEach(post => post.user = newName);
            }
            
            appState.currentUser.language = language;
            appState.currentUser.notifications = notifications;
            
            saveUserData();
            showToast('Sozlamalar saqlandi!', 'success');
            updateProfile();
            hideSettingsModal();
        }

        function logout() {
            if (confirm('Chiqishni istaysizmi?')) {
                localStorage.removeItem('userData');
                showToast('Chiqildi. Sahifa yangilanadi...', 'info');
                setTimeout(() => location.reload(), 1500);
            }
        }

        // ==================== UTILITY FUNCTIONS ====================
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
            toast.className = `toast ${type}`;
            toast.classList.remove('hidden');
            
            setTimeout(() => {
                toast.classList.add('hidden');
            }, 3000);
        }

        function loadUserData() {
            const savedData = localStorage.getItem('userData');
            if (savedData) {
                const parsed = JSON.parse(savedData);
                appState.currentUser = { ...appState.currentUser, ...parsed };
            }
            
            const savedPosts = localStorage.getItem('userPosts');
            if (savedPosts) {
                appState.userPosts = JSON.parse(savedPosts);
            }
            
            const savedProducts = localStorage.getItem('userProducts');
            if (savedProducts) {
                appState.userProducts = JSON.parse(savedProducts);
            }
        }

        function saveUserData() {
            localStorage.setItem('userData', JSON.stringify(appState.currentUser));
            localStorage.setItem('userPosts', JSON.stringify(appState.userPosts));
            localStorage.setItem('userProducts', JSON.stringify(appState.userProducts));
        }

        // ==================== OTHER FUNCTIONS ====================
        function showSearch() {
            const searchBar = document.getElementById('searchBar');
            searchBar.classList.toggle('hidden');
            
            if (!searchBar.classList.contains('hidden')) {
                document.getElementById('searchInput').focus();
            }
        }

        function performSearch() {
            const query = document.getElementById('searchInput').value.toLowerCase().trim();
            
            if (!query) {
                showToast('Qidiruv so\'zini kiriting!', 'error');
                return;
            }
            
            showToast(`"${query}" bo'yicha qidirilmoqda...`, 'info');
            
            // In a real app, you would make an API call here
            setTimeout(() => {
                showToast('Topilmadi', 'warning');
            }, 1000);
        }

        function addStory() {
            showToast('Hikoya qo\'shish funksiyasi tez orada qo\'shiladi!', 'info');
        }

        function viewStory(storyId) {
            showToast(`Hikoya ${storyId} ko'rish funksiyasi tez orada qo'shiladi!`, 'info');
        }

        function showPostMenu(postId) {
            const allPosts = [...appState.userPosts, ...appState.demoPosts];
            const post = allPosts.find(p => p.id === postId);
            const isUserPost = post && post.userId === appState.currentUser.id;
            
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
    </script>
</body>
</html>
