<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>bmax - TikTok-Style Social Feed</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome CDN for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <!-- Supabase JS Client -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
            overflow-x: hidden;
        }
        /* Hide scrollbar for Chrome, Safari and Opera */
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        /* Hide scrollbar for IE, Edge and Firefox */
        .no-scrollbar {
            -ms-overflow-style: none;  /* IE and Edge */
            scrollbar-width: none;  /* Firefox */
        }
        .snap-container {
            scroll-snap-type: y mandatory;
        }
        .snap-item {
            scroll-snap-align: start;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col justify-between selection:bg-pink-500 selection:text-white">

    <!-- Top Header Navigation -->
    <header class="fixed top-0 left-0 right-0 z-40 bg-gradient-to-b from-slate-950/80 to-transparent backdrop-blur-md px-4 py-3 flex items-center justify-between max-w-md mx-auto">
        <div class="flex items-center space-x-1 cursor-pointer" onclick="switchTab('home')">
            <span class="text-xl font-black tracking-tighter text-pink-500">bmax</span>
            <div class="w-1.5 h-1.5 bg-cyan-400 rounded-full animate-pulse"></div>
        </div>
        <div class="flex space-x-6 text-sm font-semibold text-slate-300">
            <button onclick="switchFeedTab('following')" id="header-following-btn" class="transition hover:text-white">Following</button>
            <button onclick="switchFeedTab('foryou')" id="header-foryou-btn" class="text-white border-b-2 border-pink-500 pb-0.5">For You</button>
        </div>
        <button onclick="openSearchModal()" class="text-slate-300 hover:text-white text-lg">
            <i class="fa-solid fa-magnifying-glass"></i>
        </button>
    </header>

    <!-- Main Content Area -->
    <main class="flex-1 w-full max-w-md mx-auto relative pt-14 pb-20 overflow-y-auto no-scrollbar" id="main-container">
        
        <!-- HOME FEED VIEW -->
        <section id="view-home" class="view-section w-full h-[calc(100vh-7rem)] snap-container overflow-y-scroll no-scrollbar flex flex-col">
            <!-- Dynamic Feed items injected here -->
        </section>

        <!-- DISCOVER VIEW -->
        <section id="view-discover" class="view-section hidden p-4 space-y-4">
            <h2 class="text-2xl font-bold tracking-tight text-white mb-2">Discover Trends</h2>
            <div class="relative">
                <i class="fa-solid fa-search absolute left-3 top-3.5 text-slate-400"></i>
                <input type="text" placeholder="Search creators, hashtags, sounds..." class="w-full bg-slate-900 border border-slate-800 rounded-xl py-3 pl-10 pr-4 text-sm text-white focus:outline-none focus:border-pink-500 transition">
            </div>
            <div class="grid grid-cols-2 gap-3 mt-4">
                <div class="bg-slate-900 rounded-2xl overflow-hidden aspect-[3/4] relative group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1518709268805-4e9042af9f23?w=400" class="w-full h-full object-cover group-hover:scale-105 transition duration-300">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent p-3 flex flex-col justify-end">
                        <span class="text-xs font-bold text-cyan-400">#neonart</span>
                        <p class="text-xs text-slate-200">Digital dreams</p>
                    </div>
                </div>
                <div class="bg-slate-900 rounded-2xl overflow-hidden aspect-[3/4] relative group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=400" class="w-full h-full object-cover group-hover:scale-105 transition duration-300">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent p-3 flex flex-col justify-end">
                        <span class="text-xs font-bold text-pink-400">#travel</span>
                        <p class="text-xs text-slate-200">Ocean breeze</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- INBOX VIEW -->
        <section id="view-inbox" class="view-section hidden p-4 space-y-4">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-2xl font-bold text-white">Inbox</h2>
                <div class="flex space-x-2 bg-slate-900 p-1 rounded-xl border border-slate-800">
                    <button onclick="switchInboxTab('activity')" id="inbox-tab-activity" class="px-3 py-1 rounded-lg text-xs font-semibold bg-pink-500 text-white transition">Activity</button>
                    <button onclick="switchInboxTab('messages')" id="inbox-tab-messages" class="px-3 py-1 rounded-lg text-xs font-semibold text-slate-400 transition">Messages</button>
                </div>
            </div>
            <!-- Notification list -->
            <div id="inbox-content-list" class="space-y-3">
                <div class="flex items-center space-x-3 p-3 bg-slate-900/60 rounded-2xl border border-slate-800/60">
                    <div class="w-10 h-10 rounded-full bg-pink-500/20 text-pink-500 flex items-center justify-center font-bold">
                        <i class="fa-solid fa-heart"></i>
                    </div>
                    <div class="flex-1">
                        <p class="text-xs text-slate-200"><span class="font-bold text-white">@sarah_art</span> liked your picture (+10 Rep)</p>
                        <span class="text-[10px] text-slate-500">2 hours ago</span>
                    </div>
                    <div class="w-12 h-12 rounded-xl overflow-hidden bg-slate-800">
                        <img src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=100" class="w-full h-full object-cover">
                    </div>
                </div>
            </div>
        </section>

        <!-- PROFILE VIEW -->
        <section id="view-profile" class="view-section hidden p-4 space-y-6">
            <!-- Profile Header -->
            <div class="flex flex-col items-center text-center space-y-3 pt-2">
                <div class="relative">
                    <img id="profile-avatar" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=200" class="w-24 h-24 rounded-full object-cover border-2 border-pink-500 shadow-xl">
                    <span class="absolute bottom-0 right-0 bg-cyan-400 text-slate-950 text-[10px] font-bold px-2 py-0.5 rounded-full shadow">bmax Pro</span>
                </div>
                <div>
                    <h2 id="profile-display-name" class="text-xl font-bold text-white">Alex Morgan</h2>
                    <p id="profile-username" class="text-xs text-slate-400">@alex_creator</p>
                </div>
                <p id="profile-bio" class="text-xs text-slate-300 max-w-xs leading-relaxed">Creating digital worlds and sharing vibrant moments one capture at a time. Welcome to my bmax profile! 🚀</p>
                <div id="profile-website-container" class="hidden items-center space-x-1 text-xs text-pink-400 font-medium">
                    <i class="fa-solid fa-link"></i>
                    <a id="profile-website-link" href="#" target="_blank" class="hover:underline"></a>
                </div>

                <!-- Stats Bar -->
                <div class="flex justify-around w-full py-4 bg-slate-900/80 rounded-2xl border border-slate-800/80 my-2">
                    <div class="flex flex-col">
                        <span id="stat-following" class="font-bold text-white text-base">142</span>
                        <span class="text-[11px] text-slate-400">Following</span>
                    </div>
                    <div class="w-[1px] bg-slate-800"></div>
                    <div class="flex flex-col">
                        <span id="stat-followers" class="font-bold text-white text-base">3.8K</span>
                        <span class="text-[11px] text-slate-400">Followers</span>
                    </div>
                    <div class="w-[1px] bg-slate-800"></div>
                    <div class="flex flex-col">
                        <span id="stat-favorites" class="font-bold text-white text-base">524</span>
                        <span class="text-[11px] text-slate-400">Favorites</span>
                    </div>
                </div>

                <!-- Reputation Gauge Card -->
                <div class="w-full bg-gradient-to-r from-slate-900 to-slate-900/90 border border-slate-800 rounded-2xl p-4 text-left space-y-2 relative overflow-hidden">
                    <div class="absolute -right-4 -bottom-4 w-24 h-24 bg-pink-500/10 rounded-full blur-xl pointer-events-none"></div>
                    <div class="flex justify-between items-center">
                        <div class="flex items-center space-x-2">
                            <i class="fa-solid fa-gauge-high text-pink-500"></i>
                            <span class="text-xs font-bold text-white">Reputation Gauge</span>
                        </div>
                        <span id="reputation-score-badge" class="text-xs font-bold text-cyan-400 bg-cyan-950/60 px-2.5 py-1 rounded-full border border-cyan-800/50">48 Points</span>
                    </div>
                    <div class="w-full bg-slate-800 h-2 rounded-full overflow-hidden">
                        <div id="reputation-gauge-bar" class="bg-gradient-to-r from-pink-500 to-cyan-400 h-full w-[48%] transition-all duration-500"></div>
                    </div>
                    <p class="text-[11px] text-slate-400">Upload images to boost your reputation gauge (+2 points per upload).</p>
                </div>

                <!-- Action Buttons: Edit Profile & Settings -->
                <div class="flex space-x-3 w-full">
                    <button onclick="openEditProfileModal()" class="flex-1 bg-slate-800 hover:bg-slate-700 text-white font-semibold py-2.5 rounded-xl text-xs transition border border-slate-700 shadow">
                        <i class="fa-solid fa-pen mr-1.5"></i> Edit Profile
                    </button>
                    <button onclick="openSettingsModal()" class="px-4 bg-slate-800 hover:bg-slate-700 text-white font-semibold py-2.5 rounded-xl text-xs transition border border-slate-700 shadow">
                        <i class="fa-solid fa-gear"></i>
                    </button>
                </div>
            </div>

            <!-- Profile Content Tabs (Posts / Saved) -->
            <div class="border-t border-slate-800 pt-4">
                <div class="flex justify-center space-x-12 pb-3 border-b border-slate-800/60 text-sm font-semibold">
                    <button onclick="switchProfileTab('posts')" id="profile-tab-posts" class="text-pink-500 border-b-2 border-pink-500 pb-2">Posts</button>
                    <button onclick="switchProfileTab('saved')" id="profile-tab-saved" class="text-slate-400 hover:text-white pb-2">Saved</button>
                </div>
                <!-- Grid items -->
                <div id="profile-posts-grid" class="grid grid-cols-3 gap-2 mt-3">
                    <!-- Dynamic user posts grid -->
                </div>
            </div>
        </section>

    </main>

    <!-- Bottom Navigation Bar -->
    <nav class="fixed bottom-0 left-0 right-0 z-40 bg-slate-950/90 backdrop-blur-xl border-t border-slate-900 px-4 py-2 flex items-center justify-between max-w-md mx-auto">
        <button onclick="switchTab('home')" id="nav-btn-home" class="flex flex-col items-center text-pink-500 space-y-1 transition">
            <i class="fa-solid fa-house text-lg"></i>
            <span class="text-[10px] font-semibold">Home</span>
        </button>
        <button onclick="switchTab('discover')" id="nav-btn-discover" class="flex flex-col items-center text-slate-400 hover:text-white space-y-1 transition">
            <i class="fa-solid fa-compass text-lg"></i>
            <span class="text-[10px] font-semibold">Discover</span>
        </button>
        <!-- Upload (+) Button -->
        <button onclick="openUploadModal()" class="w-12 h-10 bg-gradient-to-r from-pink-500 to-cyan-400 rounded-xl flex items-center justify-center text-slate-950 font-bold text-lg shadow-lg hover:scale-105 transition">
            <i class="fa-solid fa-plus"></i>
        </button>
        <button onclick="switchTab('inbox')" id="nav-btn-inbox" class="flex flex-col items-center text-slate-400 hover:text-white space-y-1 transition relative">
            <i class="fa-solid fa-message text-lg"></i>
            <span class="absolute -top-1 right-2 w-2 h-2 bg-pink-500 rounded-full"></span>
            <span class="text-[10px] font-semibold">Inbox</span>
        </button>
        <button onclick="switchTab('profile')" id="nav-btn-profile" class="flex flex-col items-center text-slate-400 hover:text-white space-y-1 transition">
            <i class="fa-solid fa-user text-lg"></i>
            <span class="text-[10px] font-semibold">Profile</span>
        </button>
    </nav>

    <!-- UPLOAD MODAL -->
    <div id="upload-modal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-md hidden flex items-center justify-center p-4">
        <div class="bg-slate-900 border border-slate-800 w-full max-w-sm rounded-3xl p-6 space-y-4 shadow-
