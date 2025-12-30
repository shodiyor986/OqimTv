<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>🎬 KinoTV - Telegram Mini App</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        :root {
            --primary: #6c5ce7;
            --secondary: #a29bfe;
            --dark: #2d3436;
            --light: #f9f9f9;
            --success: #00b894;
            --warning: #fdcb6e;
            --danger: #d63031;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
            background: var(--dark);
            color: var(--light);
            line-height: 1.6;
            min-height: 100vh;
            padding-bottom: 80px;
            overflow-x: hidden;
        }
        
        /* Header */
        .header {
            background: linear-gradient(135deg, var(--primary), #5f27cd);
            padding: 15px 20px;
            position: sticky;
            top: 0;
            z-index: 1000;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .logo {
            font-size: 24px;
            font-weight: 800;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .user-section {
            display: flex;
            align-items: center;
            gap: 15px;
        }
        
        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: var(--secondary);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            cursor: pointer;
        }
        
        /* Stories */
        .stories-section {
            padding: 20px;
            overflow-x: auto;
            white-space: nowrap;
            -webkit-overflow-scrolling: touch;
        }
        
        .section-title {
            font-size: 18px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .stories-container {
            display: flex;
            gap: 20px;
            padding: 10px 0;
        }
        
        .story-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            min-width: 80px;
        }
        
        .story-circle {
            width: 70px;
            height: 70px;
            border-radius: 50%;
            position: relative;
            cursor: pointer;
            margin-bottom: 8px;
            transition: transform 0.3s;
        }
        
        .story-circle:hover {
            transform: scale(1.05);
        }
        
        .story-ring {
            position: absolute;
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background: conic-gradient(var(--primary) 0%, var(--secondary) 100%);
            animation: rotate 2s linear infinite;
        }
        
        @keyframes rotate {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        .story-image {
            position: absolute;
            width: 90%;
            height: 90%;
            background: var(--dark);
            border-radius: 50%;
            top: 5%;
            left: 5%;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }
        
        .story-image img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .story-title {
            font-size: 12px;
            text-align: center;
            white-space: normal;
            max-width: 80px;
        }
        
        .new-badge {
            background: var(--success);
            color: white;
            font-size: 10px;
            padding: 2px 6px;
            border-radius: 10px;
            position: absolute;
            top: -5px;
            right: -5px;
            z-index: 2;
        }
        
        /* Movies Grid */
        .movies-section {
            padding: 0 20px 20px;
        }
        
        .movies-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
            gap: 15px;
        }
        
        .movie-card {
            background: rgba(255,255,255,0.05);
            border-radius: 12px;
            overflow: hidden;
            transition: all 0.3s;
            cursor: pointer;
            border: 1px solid rgba(255,255,255,0.1);
        }
        
        .movie-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(108, 92, 231, 0.2);
            border-color: var(--primary);
        }
        
        .movie-poster {
            width: 100%;
            height: 230px;
            object-fit: cover;
        }
        
        .movie-info {
            padding: 12px;
        }
        
        .movie-title {
            font-size: 14px;
            font-weight: 600;
            margin-bottom: 5px;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }
        
        .movie-year {
            font-size: 12px;
            color: var(--secondary);
        }
        
        /* Buttons */
        .btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 25px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            font-size: 14px;
        }
        
        .btn:hover {
            background: #5f27cd;
            transform: translateY(-2px);
        }
        
        .btn-subscribe {
            background: linear-gradient(135deg, var(--warning), #e17055);
        }
        
        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.9);
            z-index: 2000;
            align-items: center;
            justify-content: center;
            animation: fadeIn 0.3s;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        .modal-content {
            background: var(--dark);
            border-radius: 20px;
            padding: 25px;
            width: 90%;
            max-width: 400px;
            border: 2px solid var(--primary);
            position: relative;
        }
        
        .close-modal {
            position: absolute;
            top: 15px;
            right: 20px;
            font-size: 24px;
            cursor: pointer;
            color: var(--secondary);
        }
        
        /* Forms */
        .form-group {
            margin-bottom: 20px;
        }
        
        .form-control {
            width: 100%;
            padding: 12px 15px;
            background: rgba(255,255,255,0.1);
            border: 1px solid rgba(255,255,255,0.2);
            border-radius: 10px;
            color: white;
            font-size: 16px;
            transition: all 0.3s;
        }
        
        .form-control:focus {
            outline: none;
            border-color: var(--primary);
            background: rgba(108, 92, 231, 0.1);
        }
        
        /* Subscription Plans */
        .plans-grid {
            display: grid;
            gap: 15px;
            margin: 20px 0;
        }
        
        .plan-card {
            background: rgba(255,255,255,0.05);
            padding: 20px;
            border-radius: 15px;
            border: 2px solid transparent;
            transition: all 0.3s;
            cursor: pointer;
        }
        
        .plan-card:hover, .plan-card.selected {
            border-color: var(--warning);
            background: rgba(253, 203, 110, 0.1);
        }
        
        .plan-price {
            font-size: 24px;
            font-weight: 800;
            color: var(--warning);
        }
        
        .plan-stars {
            font-size: 14px;
            color: var(--secondary);
        }
        
        /* Loading */
        .loading {
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 50px;
        }
        
        .spinner {
            width: 40px;
            height: 40px;
            border: 4px solid rgba(255,255,255,0.1);
            border-top-color: var(--primary);
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }
        
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        
        /* Responsive */
        @media (min-width: 768px) {
            .movies-grid {
                grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
                gap: 20px;
            }
            
            .movie-poster {
                height: 280px;
            }
            
            .story-circle {
                width: 85px;
                height: 85px;
            }
        }
        
        @media (min-width: 1024px) {
            body {
                max-width: 1200px;
                margin: 0 auto;
                border-left: 1px solid rgba(255,255,255,0.1);
                border-right: 1px solid rgba(255,255,255,0.1);
            }
        }
        
        /* Utility */
        .hidden {
            display: none !important;
        }
        
        .text-center {
            text-align: center;
        }
        
        .mt-3 { margin-top: 15px; }
        .mb-3 { margin-bottom: 15px; }
        .p-3 { padding: 15px; }
    </style>
</head>
<body>
    <!-- Header -->
    <div class="header">
        <div class="logo">
            <i class="fas fa-film"></i> KinoTV
        </div>
        <div class="user-section">
            <div class="btn btn-subscribe" id="subscribeBtn">
                <i class="fas fa-crown"></i> Obuna
            </div>
            <div class="user-avatar" id="userAvatar" onclick="showProfile()">
                <i class="fas fa-user"></i>
            </div>
        </div>
    </div>
    
    <!-- Stories Section -->
    <div class="stories-section" id="storiesSection">
        <h3 class="section-title">
            <i class="fas fa-circle-play"></i> Yangi Istoriyalar
        </h3>
        <div class="stories-container" id="storiesContainer">
            <!-- Stories loaded dynamically -->
        </div>
    </div>
    
    <!-- Movies Section -->
    <div class="movies-section">
        <h3 class="section-title">
            <i class="fas fa-popcorn"></i> Filmlar
        </h3>
        <div class="movies-grid" id="moviesGrid">
            <!-- Movies loaded dynamically -->
        </div>
    </div>
    
    <!-- Loading Indicator -->
    <div class="loading hidden" id="loading">
        <div class="spinner"></div>
    </div>
    
    <!-- Login Modal -->
    <div class="modal" id="loginModal">
        <div class="modal-content">
            <div class="close-modal" onclick="closeModal('loginModal')">×</div>
            <h3 class="text-center mb-3">
                <i class="fas fa-sign-in-alt"></i> Kirish
            </h3>
            <div class="form-group">
                <input type="text" class="form-control" id="loginUsername" 
                       placeholder="Telegram username" autocomplete="username">
            </div>
            <div class="form-group">
                <input type="password" class="form-control" id="loginPassword" 
                       placeholder="Parol" autocomplete="current-password">
            </div>
            <button class="btn" onclick="login()" style="width: 100%;">
                <i class="fas fa-sign-in-alt"></i> Kirish
            </button>
            <p class="text-center mt-3">
                Hisobingiz yo'qmi? 
                <a href="#" onclick="showRegister()" style="color: var(--secondary);">
                    Ro'yxatdan o'ting
                </a>
            </p>
        </div>
    </div>
    
    <!-- Register Modal -->
    <div class="modal" id="registerModal">
        <div class="modal-content">
            <div class="close-modal" onclick="closeModal('registerModal')">×</div>
            <h3 class="text-center mb-3">
                <i class="fas fa-user-plus"></i> Ro'yxatdan o'tish
            </h3>
            <div class="form-group">
                <input type="text" class="form-control" id="registerUsername" 
                       placeholder="Telegram username">
            </div>
            <div class="form-group">
                <input type="password" class="form-control" id="registerPassword" 
                       placeholder="Parol">
            </div>
            <div class="form-group">
                <input type="password" class="form-control" id="registerConfirm" 
                       placeholder="Parolni tasdiqlash">
            </div>
            <button class="btn" onclick="register()" style="width: 100%;">
                <i class="fas fa-check"></i> Ro'yxatdan o'tish
            </button>
        </div>
    </div>
    
    <!-- Subscription Modal -->
    <div class="modal" id="subscriptionModal">
        <div class="modal-content">
            <div class="close-modal" onclick="closeModal('subscriptionModal')">×</div>
            <h3 class="text-center mb-3">
                <i class="fas fa-crown"></i> Obuna Sotib Olish
            </h3>
            <div class="plans-grid" id="plansGrid">
                <!-- Plans loaded dynamically -->
            </div>
            <button class="btn btn-subscribe" onclick="processPayment()" 
                    style="width: 100%;" id="payButton">
                <i class="fas fa-shopping-cart"></i> Telegram Stars orqali to'lash
            </button>
        </div>
    </div>
    
    <!-- Admin Modal -->
    <div class="modal" id="adminModal">
        <div class="modal-content">
            <div class="close-modal" onclick="closeModal('adminModal')">×</div>
            <h3 class="text-center mb-3">
                <i class="fas fa-user-shield"></i> Admin Panel
            </h3>
            <div class="form-group">
                <input type="text" class="form-control" id="movieTitle" 
                       placeholder="Film nomi">
            </div>
            <div class="form-group">
                <textarea class="form-control" id="movieDesc" 
                          placeholder="Film tavsifi" rows="3"></textarea>
            </div>
            <div class="form-group">
                <input type="text" class="form-control" id="moviePoster" 
                       placeholder="Poster URL">
            </div>
            <div class="form-group">
                <input type="text" class="form-control" id="movieVideo" 
                       placeholder="Video URL">
            </div>
            <button class="btn" onclick="addMovie()" style="width: 100%;">
                <i class="fas fa-plus"></i> Film qo'shish
            </button>
        </div>
    </div>
    
    <!-- Video Player Modal -->
    <div class="modal" id="videoModal">
        <div class="modal-content">
            <div class="close-modal" onclick="closeModal('videoModal')">×</div>
            <h3 id="videoTitle" class="text-center mb-3"></h3>
            <div style="width: 100%; height: 250px; background: #000; border-radius: 10px; 
                        display: flex; align-items: center; justify-content: center;" 
                 id="videoPlayer">
                <i class="fas fa-play-circle" style="font-size: 50px; color: white;"></i>
            </div>
            <p id="videoDesc" class="mt-3"></p>
        </div>
    </div>
    
    <script>
        // Telegram WebApp
        const tg = window.Telegram?.WebApp;
        
        // App State
        let currentUser = null;
        let selectedPlan = null;
        let currentMovie = null;
        
        // Initialize app
        document.addEventListener('DOMContentLoaded', async () => {
            if (tg) {
                tg.expand();
                tg.ready();
                
                // Set theme
                tg.setHeaderColor('#6c5ce7');
                tg.setBackgroundColor('#2d3436');
                
                // Check for initData
                if (tg.initDataUnsafe?.user) {
                    const user = tg.initDataUnsafe.user;
                    await autoLogin(user.username);
                }
            }
            
            // Load content
            await loadStories();
            await loadMovies();
            await loadPlans();
            
            // Check authentication
            checkAuth();
        });
        
        // Auto login with Telegram username
        async function autoLogin(username) {
            try {
                const response = await fetch('/api/login', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        username: username,
                        password: 'telegram_auth'
                    })
                });
                
                if (!response.ok) {
                    await registerUser(username);
                } else {
                    const data = await response.json();
                    if (data.success) {
                        currentUser = data.user;
                        updateUI();
                    }
                }
            } catch (error) {
                console.error('Auto login error:', error);
            }
        }
        
        // Register user
        async function registerUser(username) {
            try {
                const response = await fetch('/api/register', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        username: username,
                        password: 'telegram_auth'
                    })
                });
                
                if (response.ok) {
                    await autoLogin(username);
                }
            } catch (error) {
                console.error('Register error:', error);
            }
        }
        
        // Manual login
        async function login() {
            const username = document.getElementById('loginUsername').value;
            const password = document.getElementById('loginPassword').value;
            
            if (!username || !password) {
                showMessage('Iltimos, barcha maydonlarni to\'ldiring', 'error');
                return;
            }
            
            showLoading(true);
            
            try {
                const response = await fetch('/api/login', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ username, password })
                });
                
                const data = await response.json();
                
                if (data.success) {
                    currentUser = data.user;
                    closeModal('loginModal');
                    updateUI();
                    showMessage('Xush kelibsiz!', 'success');
                } else {
                    showMessage(data.error || 'Login muvaffaqiyatsiz', 'error');
                }
            } catch (error) {
                showMessage('Server xatosi', 'error');
            } finally {
                showLoading(false);
            }
        }
        
        // Manual register
        async function register() {
            const username = document.getElementById('registerUsername').value;
            const password = document.getElementById('registerPassword').value;
            const confirm = document.getElementById('registerConfirm').value;
            
            if (!username || !password) {
                showMessage('Iltimos, barcha maydonlarni to\'ldiring', 'error');
                return;
            }
            
            if (password !== confirm) {
                showMessage('Parollar mos kelmadi', 'error');
                return;
            }
            
            showLoading(true);
            
            try {
                const response = await fetch('/api/register', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ username, password })
                });
                
                const data = await response.json();
                
                if (data.success) {
                    closeModal('registerModal');
                    showMessage('Ro\'yxatdan o\'tdingiz! Endi kirishingiz mumkin', 'success');
                    document.getElementById('loginUsername').value = username;
                    document.getElementById('loginPassword').value = password;
                    showModal('loginModal');
                } else {
                    showMessage(data.error || 'Ro\'yxatdan o\'tish muvaffaqiyatsiz', 'error');
                }
            } catch (error) {
                showMessage('Server xatosi', 'error');
            } finally {
                showLoading(false);
            }
        }
        
        // Load stories
        async function loadStories() {
            try {
                const response = await fetch('/api/stories');
                const data = await response.json();
                
                if (data.success) {
                    renderStories(data.stories);
                }
            } catch (error) {
                console.error('Load stories error:', error);
            }
        }
        
        // Load movies
        async function loadMovies() {
            try {
                const response = await fetch('/api/movies');
                const data = await response.json();
                
                if (data.success) {
                    renderMovies(data.movies);
                }
            } catch (error) {
                console.error('Load movies error:', error);
            }
        }
        
        // Load subscription plans
        async function loadPlans() {
            try {
                const response = await fetch('/api/subscription/plans');
                const data = await response.json();
                
                if (data.success) {
                    renderPlans(data.plans);
                }
            } catch (error) {
                console.error('Load plans error:', error);
            }
        }
        
        // Render stories
        function renderStories(stories) {
            const container = document.getElementById('storiesContainer');
            
            if (!stories || stories.length === 0) {
                container.innerHTML = '<p class="text-center">Hozircha istoriyalar yo\'q</p>';
                return;
            }
            
            container.innerHTML = stories.map(story => `
                <div class="story-item" onclick="playStory(${story.id})">
                    <div class="story-circle">
                        ${story.is_new ? '<div class="new-badge">Yangi</div>' : ''}
                        <div class="story-ring"></div>
                        <div class="story-image">
                            ${story.preview_url ? 
                                `<img src="${story.preview_url}" alt="${story.movie_title}">` : 
                                `<i class="fas fa-film"></i>`}
                        </div>
                    </div>
                    <div class="story-title">${story.movie_title}</div>
                </div>
            `).join('');
        }
        
        // Render movies
        function renderMovies(movies) {
            const container = document.getElementById('moviesGrid');
            
            if (!movies || movies.length === 0) {
                container.innerHTML = '<p class="text-center">Hozircha filmlar yo\'q</p>';
                return;
            }
            
            container.innerHTML = movies.map(movie => `
                <div class="movie-card" onclick="showMovie(${movie.id})">
                    <img src="${movie.poster_url}" alt="${movie.title}" class="movie-poster" 
                         onerror="this.src='https://via.placeholder.com/300x450/6c5ce7/ffffff?text=Kino'">
                    <div class="movie-info">
                        <div class="movie-title">${movie.title}</div>
                        <div class="movie-year">${movie.year}</div>
                    </div>
                </div>
            `).join('');
        }
        
        // Render subscription plans
        function renderPlans(plans) {
            const container = document.getElementById('plansGrid');
            
            container.innerHTML = plans.map(plan => `
                <div class="plan-card ${selectedPlan?.id === plan.id ? 'selected' : ''}" 
                     onclick="selectPlan(${plan.id})">
                    <h4>${plan.name}</h4>
                    <div class="plan-price">${plan.price} 💎</div>
                    <div class="plan-stars">${plan.stars} Stars</div>
                    <small>${plan.days} kun</small>
                </div>
            `).join('');
        }
        
        // Show movie details
        async function showMovie(movieId) {
            try {
                const response = await fetch('/api/movies');
                const data = await response.json();
                
                if (data.success) {
                    const movie = data.movies.find(m => m.id === movieId);
                    if (movie) {
                        currentMovie = movie;
                        
                        document.getElementById('videoTitle').textContent = movie.title;
                        document.getElementById('videoDesc').textContent = movie.description || 'Tavsif mavjud emas';
                        
                        const videoPlayer = document.getElementById('videoPlayer');
                        videoPlayer.innerHTML = movie.video_url ? `
                            <video controls style="width:100%; height:100%; border-radius:10px;">
                                <source src="${movie.video_url}" type="video/mp4">
                            </video>
                        ` : `<p class="text-center">Video mavjud emas</p>`;
                        
                        showModal('videoModal');
                    }
                }
            } catch (error) {
                console.error('Show movie error:', error);
            }
        }
        
        // Play story
        function playStory(storyId) {
            showMessage('Istoriya ko\'rsatilmoqda...', 'info');
            // In real app, play the story video
        }
        
        // Select subscription plan
        function selectPlan(planId) {
            const plans = document.querySelectorAll('.plan-card');
            plans.forEach(plan => plan.classList.remove('selected'));
            
            event.currentTarget.classList.add('selected');
            
            // Find selected plan
            const planCards = Array.from(plans);
            const selectedIndex = planCards.findIndex(card => card.classList.contains('selected'));
            
            if (selectedIndex !== -1) {
                selectedPlan = { id: planId };
            }
        }
        
        // Process payment
        async function processPayment() {
            if (!selectedPlan) {
                showMessage('Iltimos, obuna rejasini tanlang', 'error');
                return;
            }
            
            if (!currentUser) {
                showMessage('Avval tizimga kiring', 'error');
                showModal('loginModal');
                return;
            }
            
            if (tg?.openInvoice) {
                // Telegram Stars payment
                tg.openInvoice(
                    'invoice_link_from_your_bot', // Replace with actual invoice link
                    function(status) {
                        if (status === 'paid') {
                            completeSubscription();
                        } else {
                            showMessage('To\'lov bekor qilindi', 'error');
                        }
                    }
                );
            } else {
                // Simulate payment for demo
                if (confirm(`${selectedPlan.id === 3 ? '2999' : '299'} Stars uchun to\'lov qilishni tasdiqlaysizmi?`)) {
                    await completeSubscription();
                }
            }
        }
        
        // Complete subscription
        async function completeSubscription() {
            try {
                const response = await fetch('/api/subscription/buy', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ plan_id: selectedPlan.id })
                });
                
                const data = await response.json();
                
                if (data.success) {
                    closeModal('subscriptionModal');
                    showMessage('Obuna muvaffaqiyatli sotib olindi!', 'success');
                    
                    // Update user subscription status
                    if (currentUser) {
                        currentUser.has_subscription = true;
                        updateUI();
                    }
                } else {
                    showMessage(data.error || 'Xatolik yuz berdi', 'error');
                }
            } catch (error) {
                showMessage('Server xatosi', 'error');
            }
        }
        
        // Add movie (admin)
        async function addMovie() {
            if (!currentUser?.is_admin) return;
            
            const title = document.getElementById('movieTitle').value;
            const description = document.getElementById('movieDesc').value;
            const poster_url = document.getElementById('moviePoster').value;
            const video_url = document.getElementById('movieVideo').value;
            
            if (!title) {
                showMessage('Film nomini kiriting', 'error');
                return;
            }
            
            try {
                const response = await fetch('/api/admin/movies/add', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        title,
                        description,
                        poster_url,
                        video_url
                    })
                });
                
                const data = await response.json();
                
                if (data.success) {
                    showMessage('Film va istorya qo\'shildi!', 'success');
                    closeModal('adminModal');
                    
                    // Clear form
                    document.getElementById('movieTitle').value = '';
                    document.getElementById('movieDesc').value = '';
                    document.getElementById('moviePoster').value = '';
                    document.getElementById('movieVideo').value = '';
                    
                    // Reload content
                    await loadStories();
                    await loadMovies();
                } else {
                    showMessage(data.error || 'Xatolik yuz berdi', 'error');
                }
            } catch (error) {
                showMessage('Server xatosi', 'error');
            }
        }
        
        // Check authentication
        async function checkAuth() {
            try {
                const response = await fetch('/api/user/profile');
                if (response.ok) {
                    const data = await response.json();
                    if (data.success) {
                        currentUser = data.user;
                        updateUI();
                    }
                }
            } catch (error) {
                // Not logged in, show login modal
                if (!currentUser) {
                    setTimeout(() => showModal('loginModal'), 1000);
                }
            }
        }
        
        // Update UI based on user state
        function updateUI() {
            const userAvatar = document.getElementById('userAvatar');
            const subscribeBtn = document.getElementById('subscribeBtn');
            
            if (currentUser) {
                // Update avatar
                userAvatar.innerHTML = currentUser.username.charAt(0).toUpperCase();
                userAvatar.style.background = `linear-gradient(135deg, ${currentUser.is_admin ? '#e17055' : '#6c5ce7'}, ${currentUser.is_admin ? '#d63031' : '#5f27cd'})`;
                
                // Update subscription button
                if (currentUser.has_subscription) {
                    subscribeBtn.innerHTML = `<i class="fas fa-crown"></i> Obuna aktiv`;
                    subscribeBtn.style.background = 'var(--success)';
                    subscribeBtn.onclick = () => showMessage('Sizda obuna aktiv!', 'info');
                } else {
                    subscribeBtn.innerHTML = `<i class="fas fa-crown"></i> Obuna sotib olish`;
                    subscribeBtn.style.background = '';
                    subscribeBtn.onclick = () => showModal('subscriptionModal');
                }
                
                // Show admin button if admin
                if (currentUser.is_admin) {
                    const adminBtn = document.createElement('button');
                    adminBtn.className = 'btn';
                    adminBtn.innerHTML = '<i class="fas fa-user-shield"></i> Admin';
                    adminBtn.onclick = () => showModal('adminModal');
                    adminBtn.style.background = '#d63031';
                    
                    document.querySelector('.user-section').prepend(adminBtn);
                }
            }
        }
        
        // Show profile
        function showProfile() {
            if (!currentUser) {
                showModal('loginModal');
                return;
            }
            
            const message = currentUser.is_admin ? 
                `Admin: ${currentUser.username}` :
                `Foydalanuvchi: ${currentUser.username}\n` +
                `Obuna: ${currentUser.has_subscription ? 'Aktiv' : 'Yo\'q'}`;
            
            showMessage(message, 'info');
        }
        
        // Modal controls
        function showModal(modalId) {
            document.getElementById(modalId).style.display = 'flex';
        }
        
        function closeModal(modalId) {
            document.getElementById(modalId).style.display = 'none';
        }
        
        function showRegister() {
            closeModal('loginModal');
            showModal('registerModal');
        }
        
        // Show loading
        function showLoading(show) {
            document.getElementById('loading').classList.toggle('hidden', !show);
        }
        
        // Show message
        function showMessage(text, type = 'info') {
            if (tg?.showPopup) {
                tg.showPopup({
                    title: type === 'error' ? 'Xatolik' : 
                           type === 'success' ? 'Muvaffaqiyat' : 'Xabar',
                    message: text,
                    buttons: [{ type: 'close' }]
                });
            } else {
                alert(text);
            }
        }
        
        // Event listeners
        document.getElementById('subscribeBtn').onclick = () => {
            if (!currentUser) {
                showModal('loginModal');
            } else if (!currentUser.has_subscription) {
                showModal('subscriptionModal');
            }
        };
        
        // Close modals on outside click
        document.querySelectorAll('.modal').forEach(modal => {
            modal.addEventListener('click', (e) => {
                if (e.target === modal) {
                    modal.style.display = 'none';
                }
            });
        });
    </script>
</body>
</html>
