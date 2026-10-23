<!DOCTYPE html>
<html lang="en" class="light">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>any_cook - AI-Powered Global Recipe Generator</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#fffbeb',
                            100: '#fef3c7',
                            500: '#f59e0b',
                            600: '#d97706',
                            700: '#b45309',
                        },
                        emeraldCustom: {
                            500: '#10b981',
                            600: '#059669',
                        },
                        terracotta: {
                            500: '#e05638',
                            600: '#c84327',
                        }
                    }
                }
            }
        }
    </script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
        }
        .glassmorphism {
            background: rgba(255, 255, 255, 0.7);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
        }
        .dark .glassmorphism {
            background: rgba(17, 24, 39, 0.7);
        }
        .gradient-banner {
            background: linear-gradient(135deg, #10b981 0%, #059669 35%, #d97706 100%);
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: transparent;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: rgba(156, 163, 175, 0.5);
            border-radius: 9999px;
        }
        @keyframes spin-wheel {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(1440deg); }
        }
        .spinning {
            animation: spin-wheel 3s cubic-bezier(0.15, 0.8, 0.15, 1) forwards;
        }
    </style>
</head>
<body class="bg-amber-50/40 dark:bg-slate-950 text-slate-800 dark:text-slate-100 min-h-screen flex flex-col transition-colors duration-300">

    <!-- Toast Notification Container -->
    <div id="toast-container" class="fixed top-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <!-- Navigation Bar -->
    <header class="sticky top-0 z-40 w-full glassmorphism border-b border-amber-200/50 dark:border-slate-800 shadow-sm transition-colors">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center gap-3 cursor-pointer" onclick="switchTab('pantry')">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-amber-500 to-emerald-500 flex items-center justify-center text-white shadow-md shadow-amber-500/20">
                    <i class="fa-solid fa-utensils text-xl"></i>
                </div>
                <div>
                    <h1 class="text-xl font-black tracking-tight text-slate-900 dark:text-white flex items-center gap-1">
                        any_<span class="text-amber-500">cook</span>
                    </h1>
                    <p class="text-[10px] text-slate-500 dark:text-slate-400 font-medium hidden sm:block">AI Global Culinary Companion</p>
                </div>
            </div>

            <!-- Navigation Links -->
            <nav class="flex items-center gap-1 sm:gap-2">
                <button onclick="switchTab('pantry')" id="nav-pantry" class="nav-btn px-3 py-2 rounded-lg text-sm font-semibold flex items-center gap-2 bg-amber-500 text-white shadow-sm">
                    <i class="fa-solid fa-kitchen-set"></i>
                    <span class="hidden md:inline">Pantry</span>
                </button>
                <button onclick="switchTab('recipes')" id="nav-recipes" class="nav-btn px-3 py-2 rounded-lg text-sm font-semibold flex items-center gap-2 text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition">
                    <i class="fa-solid fa-book-open"></i>
                    <span class="hidden md:inline">Recipes</span>
                </button>
                <button onclick="switchTab('saved')" id="nav-saved" class="nav-btn px-3 py-2 rounded-lg text-sm font-semibold flex items-center gap-2 text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition relative">
                    <i class="fa-solid fa-bookmark"></i>
                    <span class="hidden md:inline">Saved</span>
                    <span id="saved-badge" class="hidden bg-amber-500 text-white text-[10px] font-bold px-1.5 py-0.5 rounded-full">0</span>
                </button>
                <button onclick="switchTab('shopping')" id="nav-shopping" class="nav-btn px-3 py-2 rounded-lg text-sm font-semibold flex items-center gap-2 text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition relative">
                    <i class="fa-solid fa-cart-shopping"></i>
                    <span class="hidden md:inline">Shopping</span>
                    <span id="shopping-badge" class="hidden bg-emerald-500 text-white text-[10px] font-bold px-1.5 py-0.5 rounded-full">0</span>
                </button>

                <!-- Tools & Settings -->
                <div class="h-6 w-px bg-slate-200 dark:bg-slate-800 mx-1"></div>

                <button onclick="openSettingsModal()" class="p-2 rounded-lg text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition" title="API Settings & Preferences">
                    <i class="fa-solid fa-gear"></i>
                </button>

                <button onclick="toggleDarkMode()" class="p-2 rounded-lg text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition" title="Toggle Dark/Light Mode">
                    <i id="theme-icon" class="fa-solid fa-moon"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">

        <!-- TAB 1: PANTRY & AI GENERATION -->
        <section id="tab-pantry" class="tab-content space-y-6">
            <!-- Hero Banner -->
            <div class="relative rounded-3xl overflow-hidden shadow-xl gradient-banner text-white p-6 sm:p-10">
                <div class="absolute right-0 top-0 opacity-10 pointer-events-none transform translate-x-10 -translate-y-10">
                    <i class="fa-solid fa-earth-americas text-[280px]"></i>
                </div>
                <div class="relative z-10 max-w-2xl">
                    <span class="inline-block px-3 py-1 bg-white/20 backdrop-blur-md rounded-full text-xs font-bold uppercase tracking-wider mb-3">
                        <i class="fa-solid fa-wand-magic-sparkles mr-1"></i> Global AI Culinary Engine
                    </span>
                    <h2 class="text-3xl sm:text-5xl font-extrabold tracking-tight leading-tight">
                        Cook anything with what you have, anywhere.
                    </h2>
                    <p class="mt-3 text-amber-100 text-sm sm:text-base font-medium">
                        Select ingredients in your fridge or pantry, set your diet & equipment, and let AI cook up authentic recipes from around the globe.
                    </p>
                    <div class="mt-6 flex flex-wrap gap-3">
                        <button onclick="generateRecipes()" class="px-6 py-3.5 bg-amber-500 hover:bg-amber-600 text-slate-950 font-bold rounded-2xl shadow-lg hover:shadow-amber-500/20 transition-all flex items-center gap-2 transform active:scale-95">
                            <i class="fa-solid fa-bolt text-lg"></i> Generate AI Recipes
                        </button>
                        <button onclick="openWheelModal()" class="px-5 py-3.5 bg-white/10 hover:bg-white/20 backdrop-blur-md border border-white/20 font-semibold rounded-2xl transition flex items-center gap-2">
                            <i class="fa-solid fa-dharmachakra"></i> Chef's Wheel
                        </button>
                    </div>
                </div>
            </div>

            <!-- Pantry Builder Layout -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Left 2 Cols: Ingredient Selector -->
                <div class="lg:col-span-2 space-y-6">

                    <!-- Custom Search Input -->
                    <div class="bg-white dark:bg-slate-900 rounded-2xl p-4 shadow-sm border border-slate-200/80 dark:border-slate-800">
                        <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-2">Add Custom Ingredient</label>
                        <div class="relative flex items-center">
                            <i class="fa-solid fa-magnifying-glass absolute left-4 text-slate-400"></i>
                            <input type="text" id="custom-ingredient-input" onkeydown="handleIngredientKeyPress(event)" placeholder="e.g. Avocado, Paneer, Chickpeas, Lemongrass..." class="w-full pl-11 pr-24 py-3 bg-slate-50 dark:bg-slate-800/60 rounded-xl border border-slate-200 dark:border-slate-700 text-slate-900 dark:text-white focus:outline-none focus:ring-2 focus:ring-amber-500 text-sm">
                            <button onclick="addCustomIngredient()" class="absolute right-2 px-4 py-1.5 bg-amber-500 hover:bg-amber-600 text-slate-950 font-bold rounded-lg text-xs transition">
                                Add
                            </button>
                        </div>
                    </div>

                    <!-- Visual Categories & Tags -->
                    <div class="bg-white dark:bg-slate-900 rounded-2xl p-5 shadow-sm border border-slate-200/80 dark:border-slate-800 space-y-5">
                        <div class="flex items-center justify-between">
                            <h3 class="font-bold text-slate-900 dark:text-white text-base flex items-center gap-2">
                                <i class="fa-solid fa-layer-group text-amber-500"></i> Global Pantry Quick-Select
                            </h3>
                            <button onclick="clearAllIngredients()" class="text-xs font-semibold text-rose-500 hover:text-rose-600 transition">
                                <i class="fa-solid fa-trash-can mr-1"></i> Clear Selection
                            </button>
                        </div>

                        <!-- Category Filter Tabs -->
                        <div class="flex gap-2 overflow-x-auto pb-2 custom-scrollbar text-xs font-semibold">
                            <button onclick="filterCategory('all')" class="cat-filter-btn active-cat px-3.5 py-2 rounded-xl bg-slate-900 text-white dark:bg-white dark:text-slate-900 whitespace-nowrap">All Items</button>
                            <button onclick="filterCategory('veggies')" class="cat-filter-btn px-3.5 py-2 rounded-xl bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 whitespace-nowrap">🥕 Veggies & Greens</button>
                            <button onclick="filterCategory('proteins')" class="cat-filter-btn px-3.5 py-2 rounded-xl bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 whitespace-nowrap">🥩 Meat & Protein</button>
                            <button onclick="filterCategory('dairy')" class="cat-filter-btn px-3.5 py-2 rounded-xl bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 whitespace-nowrap">🧀 Dairy & Eggs</button>
                            <button onclick="filterCategory('grains')" class="cat-filter-btn px-3.5 py-2 rounded-xl bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 whitespace-nowrap">🌾 Grains & Carbs</button>
                            <button onclick="filterCategory('pantry')" class="cat-filter-btn px-3.5 py-2 rounded-xl bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 whitespace-nowrap">🧄 Spices & Oils</button>
                        </div>

                        <!-- Ingredients Chips Grid -->
                        <div id="pantry-chips-container" class="flex flex-wrap gap-2 max-h-[360px] overflow-y-auto custom-scrollbar pr-1">
                            <!-- Injected dynamically -->
                        </div>
                    </div>
                </div>

                <!-- Right Col: Selected Fridge Basket & Generation Parameters -->
                <div class="space-y-6">
                    <!-- Selected Pantry Drawer -->
                    <div class="bg-white dark:bg-slate-900 rounded-2xl p-5 shadow-sm border border-slate-200/80 dark:border-slate-800">
                        <div class="flex items-center justify-between mb-4">
                            <h3 class="font-bold text-slate-900 dark:text-white text-base flex items-center gap-2">
                                <i class="fa-solid fa-basket-shopping text-emerald-500"></i> My Fridge Basket
                            </h3>
                            <span id="selected-count-badge" class="px-2.5 py-0.5 rounded-full bg-emerald-100 text-emerald-700 dark:bg-emerald-950 dark:text-emerald-300 text-xs font-bold">
                                0 items
                            </span>
                        </div>

                        <div id="selected-ingredients-list" class="flex flex-wrap gap-2 min-h-[100px] max-h-[180px] p-3 bg-slate-50 dark:bg-slate-800/40 rounded-xl border border-dashed border-slate-300 dark:border-slate-700 overflow-y-auto custom-scrollbar align-content-start">
                            <div id="empty-basket-msg" class="w-full h-full flex flex-col items-center justify-center py-6 text-slate-400 text-xs text-center">
                                <i class="fa-solid fa-circle-plus text-2xl mb-1 text-slate-300"></i>
                                Tap or search ingredients above to add to your fridge!
                            </div>
                        </div>
                    </div>

                    <!-- AI Tuning & Household Profile -->
                    <div class="bg-white dark:bg-slate-900 rounded-2xl p-5 shadow-sm border border-slate-200/80 dark:border-slate-800 space-y-4">
                        <h3 class="font-bold text-slate-900 dark:text-white text-base flex items-center gap-2">
                            <i class="fa-solid fa-sliders text-amber-500"></i> Recipe Preferences
                        </h3>

                        <!-- Cuisine Region Selector -->
                        <div>
                            <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-1.5">Global Cuisine Focus</label>
                            <select id="cuisine-select" class="w-full px-3 py-2.5 bg-slate-50 dark:bg-slate-800 rounded-xl border border-slate-200 dark:border-slate-700 text-sm font-semibold text-slate-800 dark:text-slate-200 focus:ring-2 focus:ring-amber-500 outline-none">
                                <option value="Any Cuisine (Global)">🌐 Any Cuisine (Global Explorer)</option>
                                <option value="Italian">🇮🇹 Italian</option>
                                <option value="Indian">🇮🇳 Indian / South Asian</option>
                                <option value="Mexican">🇲🇽 Mexican & Latin</option>
                                <option value="East Asian">🇯🇵 East Asian (Chinese/Japanese/Korean)</option>
                                <option value="Mediterranean">🇬🇷 Mediterranean & Greek</option>
                                <option value="Middle Eastern">🇱🇧 Middle Eastern</option>
                                <option value="American">🇺🇸 American & BBQ</option>
                                <option value="Southeast Asian">🇹🇭 Southeast Asian (Thai/Vietnamese)</option>
                                <option value="Fusion">✨ Global Fusion</option>
                            </select>
                        </div>

                        <!-- Meal Type & Prep Time -->
                        <div class="grid grid-cols-2 gap-3">
                            <div>
                                <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-1.5">Meal Type</label>
                                <select id="mealtype-select" class="w-full px-3 py-2.5 bg-slate-50 dark:bg-slate-800 rounded-xl border border-slate-200 dark:border-slate-700 text-sm font-semibold text-slate-800 dark:text-slate-200 focus:ring-2 focus:ring-amber-500 outline-none">
                                    <option value="Any Meal">🍽️ Any Meal</option>
                                    <option value="Breakfast">🍳 Breakfast</option>
                                    <option value="Lunch">🥪 Lunch</option>
                                    <option value="Dinner">🍲 Dinner</option>
                                    <option value="Snack">🍿 Snack</option>
                                    <option value="Dessert">🍰 Dessert</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-1.5">Max Prep Time</label>
                                <select id="preptime-select" class="w-full px-3 py-2.5 bg-slate-50 dark:bg-slate-800 rounded-xl border border-slate-200 dark:border-slate-700 text-sm font-semibold text-slate-800 dark:text-slate-200 focus:ring-2 focus:ring-amber-500 outline-none">
                                    <option value="15">⏱️ 15 mins (Quick)</option>
                                    <option value="30" selected>⏱️ 30 mins</option>
                                    <option value="60">⏱️ 60 mins</option>
                                    <option value="120">⏱️ Any duration</option>
                                </select>
                            </div>
                        </div>

                        <!-- Strictness Tolerance Slider -->
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label class="text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider">Ingredient Strictness</label>
                                <span id="tolerance-label" class="text-xs font-bold text-amber-600 dark:text-amber-400">Flexible (+Pantry Staples)</span>
                            </div>
                            <input type="range" id="tolerance-slider" min="0" max="2" step="1" value="1" onchange="updateToleranceLabel()" class="w-full accent-amber-500 cursor-pointer">
                            <div class="flex justify-between text-[10px] text-slate-400 font-medium mt-1">
                                <span>Strict (My Items Only)</span>
                                <span>Flexible</span>
                                <span>Creative (Suggest 3+ extra)</span>
                            </div>
                        </div>

                        <!-- Dietary Restrictions & Equipment -->
                        <div class="pt-2 border-t border-slate-100 dark:border-slate-800 space-y-3">
                            <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider">Dietary Preferences</label>
                            <div class="flex flex-wrap gap-1.5" id="dietary-tags-container">
                                <!-- Dynamic check tags -->
                            </div>

                            <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider pt-2">Available Equipment</label>
                            <div class="flex flex-wrap gap-1.5" id="equipment-tags-container">
                                <!-- Dynamic check tags -->
                            </div>
                        </div>

                        <button onclick="generateRecipes()" class="w-full py-3 bg-gradient-to-r from-amber-500 to-emerald-500 hover:from-amber-600 hover:to-emerald-600 text-white font-bold rounded-xl shadow-md transition flex items-center justify-center gap-2 text-sm">
                            <i class="fa-solid fa-sparkles"></i> Generate Custom AI Recipes
                        </button>
                    </div>
                </div>
            </div>
        </section>


        <!-- TAB 2: RECIPE RESULTS & FEED -->
        <section id="tab-recipes" class="tab-content hidden space-y-6">
            <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 bg-white dark:bg-slate-900 p-5 rounded-2xl border border-slate-200/80 dark:border-slate-800 shadow-sm">
                <div>
                    <h2 class="text-2xl font-black text-slate-900 dark:text-white">AI Recipe Suggestions</h2>
                    <p id="recipe-results-subtitle" class="text-xs text-slate-500 dark:text-slate-400 mt-1">Curated culinary options based on your pantry & preferences.</p>
                </div>
                <div class="flex items-center gap-2">
                    <button onclick="generateRecipes()" class="px-4 py-2 bg-amber-500 hover:bg-amber-600 text-slate-950 text-xs font-bold rounded-xl transition flex items-center gap-1.5">
                        <i class="fa-solid fa-rotate-right"></i> Regenerate
                    </button>
                    <button onclick="switchTab('pantry')" class="px-4 py-2 bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-300 text-xs font-semibold rounded-xl transition">
                        Edit Pantry
                    </button>
                </div>
            </div>

            <!-- Loading Spinner State -->
            <div id="recipe-loading-state" class="hidden flex-col items-center justify-center py-20 bg-white dark:bg-slate-900 rounded-3xl border border-slate-200/80 dark:border-slate-800">
                <div class="relative w-20 h-20 mb-4">
                    <div class="absolute inset-0 rounded-full border-4 border-amber-200 dark:border-slate-700"></div>
                    <div class="absolute inset-0 rounded-full border-4 border-amber-500 border-t-transparent animate-spin"></div>
                    <div class="absolute inset-0 flex items-center justify-center text-amber-500 text-xl">
                        <i class="fa-solid fa-kitchen-set animate-pulse"></i>
                    </div>
                </div>
                <h3 class="text-lg font-bold text-slate-900 dark:text-white">Gemini AI is Cooking Up Ideas...</h3>
                <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Matching flavor profiles and calculating pantry deltas.</p>
            </div>

            <!-- Recipe Grid -->
            <div id="recipe-cards-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Recipe Cards will be rendered here -->
            </div>
        </section>


        <!-- TAB 3: SAVED FAVORITES -->
        <section id="tab-saved" class="tab-content hidden space-y-6">
            <div class="bg-white dark:bg-slate-900 p-5 rounded-2xl border border-slate-200/80 dark:border-slate-800 shadow-sm flex justify-between items-center">
                <div>
                    <h2 class="text-2xl font-black text-slate-900 dark:text-white">Saved Cookbook</h2>
                    <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Your bookmarked dishes stored right in your browser.</p>
                </div>
                <button onclick="clearSavedRecipes()" class="text-xs text-rose-500 hover:text-rose-600 font-semibold">
                    <i class="fa-solid fa-trash-can mr-1"></i> Clear Saved
                </button>
            </div>

            <div id="saved-cards-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Saved items rendered here -->
            </div>
        </section>


        <!-- TAB 4: SHOPPING LIST -->
        <section id="tab-shopping" class="tab-content hidden space-y-6">
            <div class="bg-white dark:bg-slate-900 p-5 rounded-2xl border border-slate-200/80 dark:border-slate-800 shadow-sm flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                <div>
                    <h2 class="text-2xl font-black text-slate-900 dark:text-white">Pantry Delta Shopping List</h2>
                    <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Missing ingredients automatically collected from recipe cards.</p>
                </div>
                <div class="flex items-center gap-2">
                    <button onclick="copyShoppingList()" class="px-4 py-2 bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-300 text-xs font-semibold rounded-xl hover:bg-slate-200 transition flex items-center gap-1.5">
                        <i class="fa-solid fa-copy"></i> Copy Text
                    </button>
                    <button onclick="clearShoppingList()" class="px-4 py-2 bg-rose-50 dark:bg-rose-950/40 text-rose-600 dark:text-rose-400 text-xs font-semibold rounded-xl hover:bg-rose-100 transition">
                        Clear All
                    </button>
                </div>
            </div>

            <div class="bg-white dark:bg-slate-900 rounded-2xl p-6 border border-slate-200/80 dark:border-slate-800 shadow-sm max-w-2xl mx-auto space-y-4">
                <!-- Add Custom Item to Shopping List -->
                <div class="flex gap-2">
                    <input type="text" id="shop-custom-input" placeholder="Add extra item to grocery list..." class="flex-1 px-4 py-2.5 bg-slate-50 dark:bg-slate-800 rounded-xl border border-slate-200 dark:border-slate-700 text-sm outline-none focus:ring-2 focus:ring-emerald-500">
                    <button onclick="addCustomShoppingItem()" class="px-5 py-2.5 bg-emerald-500 hover:bg-emerald-600 text-white font-bold rounded-xl text-xs transition">Add</button>
                </div>

                <ul id="shopping-items-list" class="divide-y divide-slate-100 dark:divide-slate-800">
                    <!-- Items rendered dynamically -->
                </ul>
            </div>
        </section>

    </main>


    <!-- MODAL 1: DETAILED RECIPE VIEW & STEP-BY-STEP COOKING -->
    <div id="recipe-modal" class="fixed inset-0 z-50 hidden flex items-center justify-center p-4 bg-slate-950/70 backdrop-blur-md overflow-y-auto">
        <div class="bg-white dark:bg-slate-900 rounded-3xl max-w-3xl w-full max-h-[90vh] overflow-y-auto custom-scrollbar shadow-2xl border border-slate-200/80 dark:border-slate-800 relative">
            <!-- Modal Header Sticky Bar -->
            <div class="sticky top-0 z-20 glassmorphism px-6 py-4 border-b border-slate-200/80 dark:border-slate-800 flex justify-between items-center">
                <div class="flex items-center gap-2">
                    <span id="modal-cuisine-flag" class="text-xl">🌐</span>
                    <span id="modal-cuisine-name" class="text-xs font-extrabold uppercase tracking-widest text-amber-500">Global Cuisine</span>
                </div>
                <div class="flex items-center gap-2">
                    <!-- Unit Switcher -->
                    <button onclick="toggleUnits()" id="unit-toggle-btn" class="px-3 py-1 bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-300 rounded-lg text-xs font-bold hover:bg-slate-200">
                        Units: Metric
                    </button>
                    <!-- Translation Dropdown -->
                    <select id="modal-lang-select" onchange="translateRecipeInModal(this.value)" class="px-2 py-1 bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-300 rounded-lg text-xs font-bold border-none outline-none">
                        <option value="English">🇺🇸 EN</option>
                        <option value="Spanish">🇪🇸 ES</option>
                        <option value="French">🇫🇷 FR</option>
                        <option value="Hindi">🇮🇳 HI</option>
                        <option value="Mandarin">🇨🇳 ZH</option>
                        <option value="Japanese">🇯🇵 JA</option>
                    </select>
                    <button onclick="closeRecipeModal()" class="w-8 h-8 rounded-full bg-slate-100 dark:bg-slate-800 text-slate-500 hover:text-slate-900 dark:hover:text-white flex items-center justify-center">
                        <i class="fa-solid fa-xmark text-lg"></i>
                    </button>
                </div>
            </div>

            <div class="p-6 space-y-6">
                <!-- Title & Summary -->
                <div>
                    <h2 id="modal-title" class="text-2xl sm:text-3xl font-black text-slate-900 dark:text-white leading-tight">Recipe Title</h2>
                    <p id="modal-description" class="text-sm text-slate-600 dark:text-slate-300 mt-2">Tasty description goes here...</p>
                </div>

                <!-- Stats Bar -->
                <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
                    <div class="bg-amber-50 dark:bg-amber-950/30 p-3 rounded-2xl border border-amber-200/50 dark:border-amber-900/50 text-center">
                        <span class="block text-[10px] font-bold text-amber-600 dark:text-amber-400 uppercase">Prep Time</span>
                        <span id="modal-preptime" class="text-sm font-black text-slate-900 dark:text-white">20 mins</span>
                    </div>
                    <div class="bg-emerald-50 dark:bg-emerald-950/30 p-3 rounded-2xl border border-emerald-200/50 dark:border-emerald-900/50 text-center">
                        <span class="block text-[10px] font-bold text-emerald-600 dark:text-emerald-400 uppercase">Difficulty</span>
                        <span id="modal-difficulty" class="text-sm font-black text-slate-900 dark:text-white">Easy</span>
                    </div>
                    <div class="bg-sky-50 dark:bg-sky-950/30 p-3 rounded-2xl border border-sky-200/50 dark:border-sky-900/50 text-center">
                        <span class="block text-[10px] font-bold text-sky-600 dark:text-sky-400 uppercase">Calories</span>
                        <span id="modal-calories" class="text-sm font-black text-slate-900 dark:text-white">450 kcal</span>
                    </div>
                    <div class="bg-purple-50 dark:bg-purple-950/30 p-3 rounded-2xl border border-purple-200/50 dark:border-purple-900/50 text-center">
                        <span class="block text-[10px] font-bold text-purple-600 dark:text-purple-400 uppercase">Pantry Match</span>
                        <span id="modal-match-score" class="text-sm font-black text-slate-900 dark:text-white">90%</span>
                    </div>
                </div>

                <!-- AI Substitution / Chef Tip Box -->
                <div class="p-4 bg-amber-500/10 rounded-2xl border border-amber-500/30 flex gap-3 items-start">
                    <i class="fa-solid fa-lightbulb text-amber-500 text-xl mt-0.5"></i>
                    <div>
                        <h4 class="text-xs font-extrabold text-amber-600 dark:text-amber-400 uppercase tracking-wider">AI Chef Substitution & Tip</h4>
                        <p id="modal-sub-tip" class="text-xs text-slate-700 dark:text-slate-300 mt-0.5">Don't have heavy cream? Swap with equal parts whole milk and melted butter!</p>
                    </div>
                </div>

                <!-- Ingredients Checklist -->
                <div>
                    <div class="flex justify-between items-center mb-3">
                        <h3 class="text-lg font-bold text-slate-900 dark:text-white flex items-center gap-2">
                            <i class="fa-solid fa-list-check text-emerald-500"></i> Ingredients Checklist
                        </h3>
                        <button onclick="addMissingToShoppingList()" class="text-xs font-bold text-emerald-600 hover:text-emerald-700 dark:text-emerald-400 flex items-center gap-1">
                            <i class="fa-solid fa-cart-plus"></i> Add Missing to Cart
                        </button>
                    </div>
                    <div id="modal-ingredients-list" class="grid grid-cols-1 sm:grid-cols-2 gap-2">
                        <!-- Rendered dynamically -->
                    </div>
                </div>

                <!-- Cooking Steps & Timers -->
                <div>
                    <h3 class="text-lg font-bold text-slate-900 dark:text-white mb-3 flex items-center gap-2">
                        <i class="fa-solid fa-fire text-amber-500"></i> Interactive Step-by-Step
                    </h3>
                    <div id="modal-steps-container" class="space-y-3">
                        <!-- Rendered dynamically -->
                    </div>
                </div>

                <!-- Macros Breakdown Bar -->
                <div class="pt-4 border-t border-slate-100 dark:border-slate-800">
                    <h4 class="text-xs font-bold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-2">Nutritional Summary</h4>
                    <div id="modal-macros-bar" class="grid grid-cols-3 gap-2 text-center text-xs font-bold text-slate-700 dark:text-slate-300">
                        <div class="bg-slate-100 dark:bg-slate-800 p-2 rounded-xl">Protein: <span id="macro-p">30g</span></div>
                        <div class="bg-slate-100 dark:bg-slate-800 p-2 rounded-xl">Carbs: <span id="macro-c">45g</span></div>
                        <div class="bg-slate-100 dark:bg-slate-800 p-2 rounded-xl">Fats: <span id="macro-f">15g</span></div>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <!-- MODAL 2: CHEF'S WHEEL (RANDOM SPINNER) -->
    <div id="wheel-modal" class="fixed inset-0 z-50 hidden flex items-center justify-center p-4 bg-slate-950/70 backdrop-blur-md">
        <div class="bg-white dark:bg-slate-900 rounded-3xl max-w-md w-full p-6 text-center shadow-2xl border border-slate-200/80 dark:border-slate-800 relative">
            <button onclick="closeWheelModal()" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-2xl font-black text-slate-900 dark:text-white">Chef's Meal Wheel</h3>
            <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Can't decide? Let the global culinary wheel choose your flavor focus!</p>

            <div class="my-8 relative flex items-center justify-center">
                <!-- Pointer -->
                <div class="absolute -top-4 z-10 text-amber-500 text-2xl">
                    <i class="fa-solid fa-caret-down"></i>
                </div>
                <!-- Wheel graphic -->
                <div id="wheel-disc" class="w-48 h-48 rounded-full border-4 border-amber-500 bg-gradient-to-tr from-amber-400 via-emerald-400 to-amber-600 flex items-center justify-center text-white font-black shadow-inner">
                    <i class="fa-solid fa-utensils text-4xl"></i>
                </div>
            </div>

            <div id="wheel-result" class="text-sm font-bold text-amber-600 dark:text-amber-400 min-h-[24px]">
                Click Spin to Pick a Meal Style!
            </div>

            <button onclick="spinWheel()" class="mt-4 w-full py-3 bg-amber-500 hover:bg-amber-600 text-slate-950 font-bold rounded-2xl shadow-lg transition">
                🎲 Spin the Wheel
            </button>
        </div>
    </div>


    <!-- MODAL 3: API & SETTINGS MODAL -->
    <div id="settings-modal" class="fixed inset-0 z-50 hidden flex items-center justify-center p-4 bg-slate-950/70 backdrop-blur-md">
        <div class="bg-white dark:bg-slate-900 rounded-3xl max-w-md w-full p-6 shadow-2xl border border-slate-200/80 dark:border-slate-800 relative">
            <button onclick="closeSettingsModal()" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-xl font-black text-slate-900 dark:text-white flex items-center gap-2">
                <i class="fa-solid fa-gear text-amber-500"></i> App Preferences & Gemini API
            </h3>
            <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Configure your optional Google Gemini API key or use built-in AI simulation.</p>

            <div class="mt-4 space-y-4">
                <div>
                    <label class="block text-xs font-bold text-slate-500 dark:text-slate-400 uppercase mb-1">Gemini API Key (Optional)</label>
                    <input type="password" id="api-key-input" placeholder="AIzaSy..." class="w-full px-3 py-2 bg-slate-50 dark:bg-slate-800 rounded-xl border border-slate-200 dark:border-slate-700 text-xs font-mono">
                    <p class="text-[10px] text-slate-400 mt-1">Stored securely in your browser's local storage. Leave empty to use default standard AI generator.</p>
                </div>

                <div class="flex items-center justify-between pt-2 border-t border-slate-100 dark:border-slate-800">
                    <span class="text-xs font-bold text-slate-700 dark:text-slate-300">Default Temperature / Creativity</span>
                    <span class="text-xs font-bold text-amber-500">0.7</span>
                </div>
            </div>

            <button onclick="saveSettings()" class="mt-6 w-full py-3 bg-slate-900 dark:bg-white text-white dark:text-slate-900 font-bold rounded-2xl text-xs shadow-md transition">
                Save & Close
            </button>
        </div>
    </div>


    <!-- APP LOGIC & SCRIPT ENGINE -->
    <script>
        /* ==========================================================================
           GLOBAL STATE & CONSTANTS
           ========================================================================== */
        const APP_STATE = {
            selectedIngredients: new Set(['Tomatoes', 'Garlic', 'Eggs', 'Olive Oil']),
            dietary: new Set(),
            equipment: new Set(['Stove', 'Oven']),
            unitSystem: 'metric', // 'metric' | 'imperial'
            currentRecipes: [],
            activeRecipe: null,
            savedRecipes: JSON.parse(localStorage.getItem('anycook_saved') || '[]'),
            shoppingList: JSON.parse(localStorage.getItem('anycook_shopping') || '[]'),
            apiKey: localStorage.getItem('anycook_gemini_key') || '',
            activeLanguage: 'English'
        };

        const PRESET_INGREDIENTS = [
            // Veggies
            { name: 'Tomatoes', emoji: '🍅', cat: 'veggies' },
            { name: 'Garlic', emoji: '🧄', cat: 'veggies' },
            { name: 'Onions', emoji: '🧅', cat: 'veggies' },
            { name: 'Bell Peppers', emoji: '🫑', cat: 'veggies' },
            { name: 'Spinach', emoji: '🥬', cat: 'veggies' },
            { name: 'Potatoes', emoji: '🥔', cat: 'veggies' },
            { name: 'Carrots', emoji: '🥕', cat: 'veggies' },
            { name: 'Broccoli', emoji: '🥦', cat: 'veggies' },
            { name: 'Avocado', emoji: '🥑', cat: 'veggies' },
            { name: 'Mushrooms', emoji: '🍄', cat: 'veggies' },

            // Proteins
            { name: 'Chicken Breast', emoji: '🍗', cat: 'proteins' },
            { name: 'Eggs', emoji: '🥚', cat: 'proteins' },
            { name: 'Ground Beef', emoji: '🥩', cat: 'proteins' },
            { name: 'Tofu', emoji: '🧊', cat: 'proteins' },
            { name: 'Salmon', emoji: '🐟', cat: 'proteins' },
            { name: 'Shrimp', emoji: '🦐', cat: 'proteins' },
            { name: 'Chickpeas', emoji: '🫘', cat: 'proteins' },

            // Dairy
            { name: 'Milk', emoji: '🥛', cat: 'dairy' },
            { name: 'Cheese / Mozzarella', emoji: '🧀', cat: 'dairy' },
            { name: 'Butter', emoji: '🧈', cat: 'dairy' },
            { name: 'Yogurt', emoji: '🥣', cat: 'dairy' },

            // Grains
            { name: 'Rice', emoji: '🍚', cat: 'grains' },
            { name: 'Pasta / Spaghetti', emoji: '🍝', cat: 'grains' },
            { name: 'Bread', emoji: '🍞', cat: 'grains' },
            { name: 'Flour', emoji: '🌾', cat: 'grains' },
            { name: 'Oats', emoji: '🥣', cat: 'grains' },

            // Pantry Staples
            { name: 'Olive Oil', emoji: '🫗', cat: 'pantry' },
            { name: 'Soy Sauce', emoji: '🍾', cat: 'pantry' },
            { name: 'Salt & Pepper', emoji: '🧂', cat: 'pantry' },
            { name: 'Chili Powder', emoji: '🌶️', cat: 'pantry' },
            { name: 'Honey', emoji: '🍯', cat: 'pantry' }
        ];

        const DIETARY_OPTIONS = ['Vegan', 'Vegetarian', 'Keto', 'Halal', 'Kosher', 'Gluten-Free', 'Dairy-Free', 'Low-Carb'];
        const EQUIPMENT_OPTIONS = ['Stove', 'Oven', 'Air Fryer', 'Microwave', 'Blender', 'Instant Pot'];

        const CUISINE_FLAGS = {
            'Italian': '🇮🇹', 'Indian': '🇮🇳', 'Mexican': '🇲🇽', 'East Asian': '🇯🇵',
            'Mediterranean': '🇬🇷', 'Middle Eastern': '🇱🇧', 'American': '🇺🇸',
            'Southeast Asian': '🇹🇭', 'Fusion': '✨', 'Any Cuisine (Global)': '🌐'
        };

        /* ==========================================================================
           INITIALIZATION & DOM RENDERERS
           ========================================================================== */
        window.addEventListener('DOMContentLoaded', () => {
            renderPantryChips('all');
            renderSelectedIngredients();
            renderDietaryTags();
            renderEquipmentTags();
            updateBadges();
            if (APP_STATE.apiKey) {
                document.getElementById('api-key-input').value = APP_STATE.apiKey;
            }
        });

        function renderPantryChips(category = 'all') {
            const container = document.getElementById('pantry-chips-container');
            container.innerHTML = '';

            const filtered = category === 'all' 
                ? PRESET_INGREDIENTS 
                : PRESET_INGREDIENTS.filter(item => item.cat === category);

            filtered.forEach(item => {
                const isSelected = APP_STATE.selectedIngredients.has(item.name);
                const chip = document.createElement('button');
                chip.className = `px-3 py-2 rounded-xl text-xs font-semibold flex items-center gap-1.5 transition-all transform active:scale-95 ${
                    isSelected 
                        ? 'bg-emerald-500 text-white shadow-md shadow-emerald-500/20' 
                        : 'bg-slate-100 text-slate-700 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 dark:hover:bg-slate-700'
                }`;
                chip.onclick = () => toggleIngredient(item.name);
                chip.innerHTML = `<span>${item.emoji}</span> <span>${item.name}</span>`;
                container.appendChild(chip);
            });
        }

        function renderSelectedIngredients() {
            const container = document.getElementById('selected-ingredients-list');
            const badge = document.getElementById('selected-count-badge');
            
            badge.innerText = `${APP_STATE.selectedIngredients.size} items`;
            container.innerHTML = '';

            if (APP_STATE.selectedIngredients.size === 0) {
                container.innerHTML = `
                    <div id="empty-basket-msg" class="w-full h-full flex flex-col items-center justify-center py-6 text-slate-400 text-xs text-center">
                        <i class="fa-solid fa-circle-plus text-2xl mb-1 text-slate-300"></i>
                        Tap or search ingredients above to add to your fridge!
                    </div>`;
                return;
            }

            APP_STATE.selectedIngredients.forEach(item => {
                const chip = document.createElement('span');
                chip.className = "inline-flex items-center gap-1 px-3 py-1.5 rounded-xl bg-amber-500/10 border border-amber-500/30 text-amber-700 dark:text-amber-300 text-xs font-bold animate-fadeIn";
                chip.innerHTML = `
                    <span>${item}</span>
                    <i class="fa-solid fa-circle-xmark cursor-pointer hover:text-rose-500 transition" onclick="toggleIngredient('${item}')"></i>
                `;
                container.appendChild(chip);
            });
        }

        function renderDietaryTags() {
            const container = document.getElementById('dietary-tags-container');
            container.innerHTML = '';
            DIETARY_OPTIONS.forEach(diet => {
                const active = APP_STATE.dietary.has(diet);
                const tag = document.createElement('button');
                tag.className = `px-2.5 py-1 rounded-lg text-[11px] font-bold border transition ${
                    active 
                        ? 'bg-amber-500 text-slate-950 border-amber-500' 
                        : 'bg-slate-50 text-slate-600 border-slate-200 dark:bg-slate-800 dark:text-slate-400 dark:border-slate-700'
                }`;
                tag.onclick = () => {
                    if (active) APP_STATE.dietary.delete(diet);
                    else APP_STATE.dietary.add(diet);
                    renderDietaryTags();
                };
                tag.innerText = diet;
                container.appendChild(tag);
            });
        }

        function renderEquipmentTags() {
            const container = document.getElementById('equipment-tags-container');
            container.innerHTML = '';
            EQUIPMENT_OPTIONS.forEach(eq => {
                const active = APP_STATE.equipment.has(eq);
                const tag = document.createElement('button');
                tag.className = `px-2.5 py-1 rounded-lg text-[11px] font-bold border transition ${
                    active 
                        ? 'bg-emerald-500 text-white border-emerald-500' 
                        : 'bg-slate-50 text-slate-600 border-slate-200 dark:bg-slate-800 dark:text-slate-400 dark:border-slate-700'
                }`;
                tag.onclick = () => {
                    if (active) APP_STATE.equipment.delete(eq);
                    else APP_STATE.equipment.add(eq);
                    renderEquipmentTags();
                };
                tag.innerText = eq;
                container.appendChild(tag);
            });
        }

        /* ==========================================================================
           INTERACTION & PANTRY HANDLERS
           ========================================================================== */
        function toggleIngredient(name) {
            if (APP_STATE.selectedIngredients.has(name)) {
                APP_STATE.selectedIngredients.delete(name);
            } else {
                APP_STATE.selectedIngredients.add(name);
            }
            renderPantryChips();
            renderSelectedIngredients();
        }

        function addCustomIngredient() {
            const input = document.getElementById('custom-ingredient-input');
            const val = input.value.trim();
            if (val) {
                APP_STATE.selectedIngredients.add(val);
                input.value = '';
                renderSelectedIngredients();
                showToast(`Added "${val}" to your fridge!`, 'success');
            }
        }

        function handleIngredientKeyPress(e) {
            if (e.key === 'Enter') addCustomIngredient();
        }

        function clearAllIngredients() {
            APP_STATE.selectedIngredients.clear();
            renderPantryChips();
            renderSelectedIngredients();
            showToast('Cleared all items from fridge.', 'info');
        }

        function filterCategory(cat) {
            document.querySelectorAll('.cat-filter-btn').forEach(btn => {
                btn.className = 'cat-filter-btn px-3.5 py-2 rounded-xl bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300 hover:bg-slate-200 whitespace-nowrap';
            });
            event.target.className = 'cat-filter-btn active-cat px-3.5 py-2 rounded-xl bg-slate-900 text-white dark:bg-white dark:text-slate-900 whitespace-nowrap';
            renderPantryChips(cat);
        }

        function updateToleranceLabel() {
            const val = document.getElementById('tolerance-slider').value;
            const label = document.getElementById('tolerance-label');
            if (val === "0") label.innerText = "Strict (Only My Items)";
            else if (val === "1") label.innerText = "Flexible (+Pantry Staples)";
            else label.innerText = "Creative (Suggest 3+ extra)";
        }

        function switchTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.className = 'nav-btn px-3 py-2 rounded-lg text-sm font-semibold flex items-center gap-2 text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition relative';
            });

            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            const activeNav = document.getElementById(`nav-${tabId}`);
            if (activeNav) {
                activeNav.className = 'nav-btn px-3 py-2 rounded-lg text-sm font-semibold flex items-center gap-2 bg-amber-500 text-white shadow-sm relative';
            }

            if (tabId === 'saved') renderSavedCards();
            if (tabId === 'shopping') renderShoppingList();
        }

        /* ==========================================================================
           AI RECIPE GENERATION ENGINE (GEMINI API + SIMULATOR FALLBACK)
           ========================================================================== */
        async function generateRecipes() {
            if (APP_STATE.selectedIngredients.size === 0) {
                showToast('Please select at least 1 ingredient in your fridge!', 'error');
                return;
            }

            switchTab('recipes');
            const grid = document.getElementById('recipe-cards-grid');
            const loader = document.getElementById('recipe-loading-state');
            grid.innerHTML = '';
            loader.classList.remove('hidden');
            loader.classList.add('flex');

            const ingredientsArray = Array.from(APP_STATE.selectedIngredients);
            const cuisine = document.getElementById('cuisine-select').value;
            const mealType = document.getElementById('mealtype-select').value;
            const prepTime = document.getElementById('preptime-select').value;
            const dietaryArray = Array.from(APP_STATE.dietary);
            const equipmentArray = Array.from(APP_STATE.equipment);

            // Construct Gemini AI Prompt
            const prompt = `Act as a world-class executive chef. Create 3 authentic, appetizing culinary recipes based on these parameters:
            - Available Ingredients: ${ingredientsArray.join(', ')}
            - Cuisine Focus: ${cuisine}
            - Meal Type: ${mealType}
            - Max Prep Time: ${prepTime} minutes
            - Dietary Restrictions: ${dietaryArray.join(', ') || 'None'}
            - Kitchen Equipment: ${equipmentArray.join(', ') || 'Standard Stove'}

            Return ONLY a valid JSON Array with 3 recipe objects matching this schema:
            [
              {
                "id": "unique-string",
                "title": "Recipe Name",
                "cuisine": "Cuisine Origin Name",
                "description": "Short appetizing 2-sentence description",
                "prepTime": "20 mins",
                "difficulty": "Easy/Medium/Hard",
                "calories": 450,
                "matchScore": 95,
                "substitutionTip": "Chef tip for ingredient swaps",
                "macros": {"p": "30g", "c": "40g", "f": "15g"},
                "ingredients": [
                  {"name": "Ingredient Name with metric quantity", "hasIt": true/false}
                ],
                "instructions": [
                  "Detailed step 1 with time indicator if needed",
                  "Detailed step 2"
                ]
              }
            ]`;

            let recipes = null;

            // Attempt Gemini API if Key is present
            if (APP_STATE.apiKey) {
                try {
                    const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${APP_STATE.apiKey}`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({
                            contents: [{ parts: [{ text: prompt }] }],
                            generationConfig: { responseMimeType: "application/json" }
                        })
                    });
                    const data = await response.json();
                    const textResult = data.candidates?.[0]?.content?.parts?.[0]?.text;
                    if (textResult) {
                        recipes = JSON.parse(textResult);
                    }
                } catch (err) {
                    console.warn("Gemini API call failed or throttled, falling back to built-in generator.", err);
                }
            }

            // Built-in Intelligent Fallback Generator if offline/no key
            if (!recipes) {
                await new Promise(r => setTimeout(r, 1200)); // simulate AI delay
                recipes = generateFallbackRecipes(ingredientsArray, cuisine, mealType);
            }

            APP_STATE.currentRecipes = recipes;
            loader.classList.add('hidden');
            loader.classList.remove('flex');
            renderRecipeCards(recipes);
        }

        function generateFallbackRecipes(ingredients, cuisine, mealType) {
            const ingStr = ingredients.slice(0, 3).join(' & ');
            return [
                {
                    id: 'rec-' + Date.now() + '-1',
                    title: `Rustic ${ingStr} Pan-Seared Medley`,
                    cuisine: cuisine === 'Any Cuisine (Global)' ? 'Mediterranean' : cuisine,
                    description: `A fragrant, high-protein skillet meal harmonizing ${ingStr} with rich aromatic seasonings.`,
                    prepTime: '20 mins',
                    difficulty: 'Easy',
                    calories: 420,
                    matchScore: 92,
                    substitutionTip: 'Can swap olive oil with butter or avocado oil for a richer finish.',
                    macros: { p: '28g', c: '35g', f: '14g' },
                    ingredients: ingredients.map(i => ({ name: `150g ${i}`, hasIt: true })).concat([
                        { name: '1 tbsp Olive Oil', hasIt: false },
                        { name: 'Pinch of Sea Salt & Pepper', hasIt: false }
                    ]),
                    instructions: [
                        'Prep and slice all ingredients into uniform bite-sized pieces.',
                        'Heat skillet over medium heat with oil for 2 minutes until shimmering.',
                        'Sauté key ingredients until golden brown and tender.',
                        'Season with salt, pepper, and fresh herbs; serve hot.'
                    ]
                },
                {
                    id: 'rec-' + Date.now() + '-2',
                    title: `Global Fusion ${ingredients[0] || 'Veggie'} Warm Bowl`,
                    cuisine: 'Fusion',
                    description: `An appetizing comforting grain and fridge bowl packed with vibrant colors and nutrients.`,
                    prepTime: '25 mins',
                    difficulty: 'Medium',
                    calories: 510,
                    matchScore: 85,
                    substitutionTip: 'Add toasted sesame seeds or crushed nuts for extra crunch.',
                    macros: { p: '22g', c: '58g', f: '18g' },
                    ingredients: ingredients.map(i => ({ name: `100g ${i}`, hasIt: true })).concat([
                        { name: '1 Cup Cooked Rice or Quinoa', hasIt: false },
                        { name: '2 tbsp Soy Sauce or Lemon Dressing', hasIt: false }
                    ]),
                    instructions: [
                        'Steam or roast ingredients until fork-tender.',
                        'Assemble cooked grains in a bowl as a foundation.',
                        'Arrange roasted ingredients on top and drizzle warm dressing over the dish.'
                    ]
                },
                {
                    id: 'rec-' + Date.now() + '-3',
                    title: `Quick ${ingredients[1] || 'Garden'} Stir-Fry Delight`,
                    cuisine: 'East Asian',
                    description: `Fast-paced 15-minute skillet stir-fry highlighting natural crisp flavors.`,
                    prepTime: '15 mins',
                    difficulty: 'Easy',
                    calories: 380,
                    matchScore: 100,
                    substitutionTip: 'If low on garlic, garlic powder works fantastically!',
                    macros: { p: '19g', c: '29g', f: '12g' },
                    ingredients: ingredients.map(i => ({ name: `80g ${i}`, hasIt: true })),
                    instructions: [
                        'Flash stir-fry ingredients in a hot wok or skillet for 5-7 mins.',
                        'Garnish with available herbs and serve immediately.'
                    ]
                }
            ];
        }

        /* ==========================================================================
           RECIPE CARD & MODAL RENDERERS
           ========================================================================== */
        function renderRecipeCards(recipes) {
            const grid = document.getElementById('recipe-cards-grid');
            grid.innerHTML = '';

            recipes.forEach(recipe => {
                const isSaved = APP_STATE.savedRecipes.some(r => r.id === recipe.id);
                const flag = CUISINE_FLAGS[recipe.cuisine] || '🌐';

                const card = document.createElement('div');
                card.className = "bg-white dark:bg-slate-900 rounded-3xl p-5 border border-slate-200/80 dark:border-slate-800 shadow-sm hover:shadow-xl transition duration-300 flex flex-col justify-between group";
                
                card.innerHTML = `
                    <div>
                        <div class="flex items-center justify-between mb-3">
                            <span class="px-3 py-1 rounded-full bg-amber-500/10 text-amber-600 dark:text-amber-400 text-xs font-bold flex items-center gap-1">
                                <span>${flag}</span> <span>${recipe.cuisine}</span>
                            </span>
                            <button onclick="toggleSaveRecipe('${recipe.id}')" class="text-slate-400 hover:text-amber-500 transition text-lg">
                                <i class="${isSaved ? 'fa-solid fa-bookmark text-amber-500' : 'fa-regular fa-bookmark'}"></i>
                            </button>
                        </div>

                        <h3 class="text-xl font-extrabold text-slate-900 dark:text-white group-hover:text-amber-500 transition line-clamp-1">${recipe.title}</h3>
                        <p class="text-xs text-slate-500 dark:text-slate-400 mt-2 line-clamp-2 leading-relaxed">${recipe.description}</p>

                        <div class="grid grid-cols-3 gap-2 my-4 text-center">
                            <div class="bg-slate-50 dark:bg-slate-800/60 p-2 rounded-xl">
                                <span class="block text-[10px] text-slate-400 font-bold uppercase">Time</span>
                                <span class="text-xs font-bold text-slate-800 dark:text-slate-200">${recipe.prepTime}</span>
                            </div>
                            <div class="bg-slate-50 dark:bg-slate-800/60 p-2 rounded-xl">
                                <span class="block text-[10px] text-slate-400 font-bold uppercase">Match</span>
                                <span class="text-xs font-bold text-emerald-600">${recipe.matchScore}%</span>
                            </div>
                            <div class="bg-slate-50 dark:bg-slate-800/60 p-2 rounded-xl">
                                <span class="block text-[10px] text-slate-400 font-bold uppercase">Calories</span>
                                <span class="text-xs font-bold text-slate-800 dark:text-slate-200">${recipe.calories} kcal</span>
                            </div>
                        </div>
                    </div>

                    <button onclick="openRecipeModal('${recipe.id}')" class="w-full py-3 bg-slate-900 dark:bg-white text-white dark:text-slate-900 font-bold rounded-2xl text-xs shadow hover:bg-amber-500 hover:text-slate-950 dark:hover:bg-amber-500 transition flex items-center justify-center gap-2">
                        <i class="fa-solid fa-utensils"></i> Cook Recipe
                    </button>
                `;
                grid.appendChild(card);
            });
        }

        function openRecipeModal(recipeId) {
            const recipe = APP_STATE.currentRecipes.concat(APP_STATE.savedRecipes).find(r => r.id === recipeId);
            if (!recipe) return;

            APP_STATE.activeRecipe = recipe;
            document.getElementById('modal-title').innerText = recipe.title;
            document.getElementById('modal-description').innerText = recipe.description;
            document.getElementById('modal-cuisine-name').innerText = recipe.cuisine;
            document.getElementById('modal-cuisine-flag').innerText = CUISINE_FLAGS[recipe.cuisine] || '🌐';
            document.getElementById('modal-preptime').innerText = recipe.prepTime;
            document.getElementById('modal-difficulty').innerText = recipe.difficulty;
            document.getElementById('modal-calories').innerText = recipe.calories + ' kcal';
            document.getElementById('modal-match-score').innerText = recipe.matchScore + '%';
            document.getElementById('modal-sub-tip').innerText = recipe.substitutionTip;

            document.getElementById('macro-p').innerText = recipe.macros.p;
            document.getElementById('macro-c').innerText = recipe.macros.c;
            document.getElementById('macro-f').innerText = recipe.macros.f;

            renderModalIngredients(recipe.ingredients);
            renderModalSteps(recipe.instructions);

            document.getElementById('recipe-modal').classList.remove('hidden');
        }

        function closeRecipeModal() {
            document.getElementById('recipe-modal').classList.add('hidden');
        }

        function renderModalIngredients(ingredients) {
            const container = document.getElementById('modal-ingredients-list');
            container.innerHTML = '';

            ingredients.forEach(item => {
                const div = document.createElement('div');
                div.className = `p-2.5 rounded-xl border flex items-center justify-between text-xs font-semibold ${
                    item.hasIt 
                        ? 'bg-emerald-50 dark:bg-emerald-950/30 border-emerald-200/60 text-emerald-900 dark:text-emerald-300' 
                        : 'bg-rose-50 dark:bg-rose-950/30 border-rose-200/60 text-rose-900 dark:text-rose-300'
                }`;
                div.innerHTML = `
                    <span class="flex items-center gap-2">
                        <i class="fa-solid ${item.hasIt ? 'fa-circle-check text-emerald-500' : 'fa-circle-minus text-rose-500'}"></i>
                        <span>${item.name}</span>
                    </span>
                    <span class="text-[10px] font-extrabold uppercase px-1.5 py-0.5 rounded ${item.hasIt ? 'bg-emerald-200 dark:bg-emerald-800' : 'bg-rose-200 dark:bg-rose-800'}">
                        ${item.hasIt ? 'In Fridge' : 'Missing'}
                    </span>
                `;
                container.appendChild(div);
            });
        }

        function renderModalSteps(instructions) {
            const container = document.getElementById('modal-steps-container');
            container.innerHTML = '';

            instructions.forEach((step, idx) => {
                const div = document.createElement('div');
                div.className = "p-3.5 bg-slate-50 dark:bg-slate-800/60 rounded-2xl border border-slate-200 dark:border-slate-700 flex gap-3 items-start";
                div.innerHTML = `
                    <input type="checkbox" class="mt-1 accent-amber-500 w-4 h-4 cursor-pointer">
                    <div class="flex-1">
                        <span class="text-xs font-extrabold text-amber-500 uppercase tracking-wider block">Step ${idx + 1}</span>
                        <p class="text-xs text-slate-800 dark:text-slate-200 mt-0.5 leading-relaxed">${step}</p>
                    </div>
                `;
                container.appendChild(div);
            });
        }

        /* ==========================================================================
           GLOBAL & EXTRAS (SHOPPING, SAVED, TRANSLATE, WHEEL)
           ========================================================================== */
        function toggleSaveRecipe(id) {
            const recipe = APP_STATE.currentRecipes.concat(APP_STATE.savedRecipes).find(r => r.id === id);
            if (!recipe) return;

            const index = APP_STATE.savedRecipes.findIndex(r => r.id === id);
            if (index >= 0) {
                APP_STATE.savedRecipes.splice(index, 1);
                showToast('Removed recipe from Cookbook', 'info');
            } else {
                APP_STATE.savedRecipes.push(recipe);
                showToast('Saved recipe to your Cookbook!', 'success');
            }

            localStorage.setItem('anycook_saved', JSON.stringify(APP_STATE.savedRecipes));
            updateBadges();
            if (APP_STATE.currentRecipes.length > 0) renderRecipeCards(APP_STATE.currentRecipes);
        }

        function renderSavedCards() {
            const grid = document.getElementById('saved-cards-grid');
            if (APP_STATE.savedRecipes.length === 0) {
                grid.innerHTML = `<div class="col-span-full py-12 text-center text-slate-400 text-sm">No saved recipes yet. Bookmark some from the generated recipes tab!</div>`;
                return;
            }
            renderRecipeCards(APP_STATE.savedRecipes);
        }

        function clearSavedRecipes() {
            APP_STATE.savedRecipes = [];
            localStorage.setItem('anycook_saved', '[]');
            updateBadges();
            renderSavedCards();
            showToast('Saved cookbook cleared.', 'info');
        }

        function addMissingToShoppingList() {
            if (!APP_STATE.activeRecipe) return;
            const missing = APP_STATE.activeRecipe.ingredients.filter(i => !i.hasIt);
            if (missing.length === 0) {
                showToast('You already have all ingredients in your fridge!', 'info');
                return;
            }

            missing.forEach(m => {
                if (!APP_STATE.shoppingList.includes(m.name)) {
                    APP_STATE.shoppingList.push(m.name);
                }
            });

            localStorage.setItem('anycook_shopping', JSON.stringify(APP_STATE.shoppingList));
            updateBadges();
            showToast(`Added ${missing.length} missing items to Shopping List!`, 'success');
        }

        function renderShoppingList() {
            const ul = document.getElementById('shopping-items-list');
            ul.innerHTML = '';

            if (APP_STATE.shoppingList.length === 0) {
                ul.innerHTML = `<li class="py-8 text-center text-slate-400 text-xs">Your shopping list is empty.</li>`;
                return;
            }

            APP_STATE.shoppingList.forEach((item, idx) => {
                const li = document.createElement('li');
                li.className = "py-3 flex justify-between items-center text-xs font-semibold text-slate-800 dark:text-slate-200";
                li.innerHTML = `
                    <span class="flex items-center gap-2">
                        <i class="fa-regular fa-square cursor-pointer hover:text-emerald-500" onclick="removeShoppingItem(${idx})"></i>
                        <span>${item}</span>
                    </span>
                    <button onclick="removeShoppingItem(${idx})" class="text-slate-400 hover:text-rose-500">
                        <i class="fa-solid fa-xmark"></i>
                    </button>
                `;
                ul.appendChild(li);
            });
        }

        function addCustomShoppingItem() {
            const input = document.getElementById('shop-custom-input');
            const val = input.value.trim();
            if (val) {
                APP_STATE.shoppingList.push(val);
                input.value = '';
                localStorage.setItem('anycook_shopping', JSON.stringify(APP_STATE.shoppingList));
                updateBadges();
                renderShoppingList();
            }
        }

        function removeShoppingItem(idx) {
            APP_STATE.shoppingList.splice(idx, 1);
            localStorage.setItem('anycook_shopping', JSON.stringify(APP_STATE.shoppingList));
            updateBadges();
            renderShoppingList();
        }

        function clearShoppingList() {
            APP_STATE.shoppingList = [];
            localStorage.setItem('anycook_shopping', '[]');
            updateBadges();
            renderShoppingList();
        }

        function copyShoppingList() {
            if (APP_STATE.shoppingList.length === 0) return;
            const text = "any_cook Grocery List:\n" + APP_STATE.shoppingList.map(i => `- ${i}`).join('\n');
            navigator.clipboard.writeText(text);
            showToast('Copied shopping list to clipboard!', 'success');
        }

        function updateBadges() {
            const savedB = document.getElementById('saved-badge');
            const shopB = document.getElementById('shopping-badge');

            savedB.innerText = APP_STATE.savedRecipes.length;
            savedB.classList.toggle('hidden', APP_STATE.savedRecipes.length === 0);

            shopB.innerText = APP_STATE.shoppingList.length;
            shopB.classList.toggle('hidden', APP_STATE.shoppingList.length === 0);
        }

        /* ==========================================================================
           CHEF'S WHEEL (RANDOMIZER)
           ========================================================================== */
        function openWheelModal() {
            document.getElementById('wheel-modal').classList.remove('hidden');
        }

        function closeWheelModal() {
            document.getElementById('wheel-modal').classList.add('hidden');
        }

        function spinWheel() {
            const disc = document.getElementById('wheel-disc');
            const res = document.getElementById('wheel-result');
            disc.classList.add('spinning');

            const cuisines = ['Italian Pasta Special', 'Indian Spicy Curry', 'Mexican Taco Fiesta', 'Japanese Ramen/Bowl', 'Thai Coconut Curry', 'Mediterranean Wrap'];
            
            setTimeout(() => {
                disc.classList.remove('spinning');
                const pick = cuisines[Math.floor(Math.random() * cuisines.length)];
                res.innerText = `🎉 Chef Recommends: ${pick}!`;
            }, 3000);
        }

        /* ==========================================================================
           SETTINGS & THEME UTILITIES
           ========================================================================== */
        function openSettingsModal() {
            document.getElementById('settings-modal').classList.remove('hidden');
        }

        function closeSettingsModal() {
            document.getElementById('settings-modal').classList.add('hidden');
        }

        function saveSettings() {
            const key = document.getElementById('api-key-input').value.trim();
            APP_STATE.apiKey = key;
            localStorage.setItem('anycook_gemini_key', key);
            closeSettingsModal();
            showToast('Preferences saved!', 'success');
        }

        function toggleDarkMode() {
            const html = document.documentElement;
            const icon = document.getElementById('theme-icon');
            if (html.classList.contains('dark')) {
                html.classList.remove('dark');
                icon.className = 'fa-solid fa-moon';
            } else {
                html.classList.add('dark');
                icon.className = 'fa-solid fa-sun';
            }
        }

        function toggleUnits() {
            APP_STATE.unitSystem = APP_STATE.unitSystem === 'metric' ? 'imperial' : 'metric';
            document.getElementById('unit-toggle-btn').innerText = `Units: ${APP_STATE.unitSystem === 'metric' ? 'Metric' : 'Imperial'}`;
            showToast(`Switched units to ${APP_STATE.unitSystem}`, 'info');
        }

        async function translateRecipeInModal(lang) {
            showToast(`Translating recipe into ${lang}...`, 'info');
        }

        function showToast(message, type = 'info') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            
            const bg = type === 'success' ? 'bg-emerald-600' : type === 'error' ? 'bg-rose-600' : 'bg-slate-900';
            toast.className = `${bg} text-white px-4 py-2.5 rounded-xl shadow-lg text-xs font-bold flex items-center gap-2 pointer-events-auto animate-bounce`;
            toast.innerHTML = `<i class="fa-solid fa-circle-info"></i> <span>${message}</span>`;

            container.appendChild(toast);
            setTimeout(() => toast.remove(), 3000);
        }
    </script>
</body>
</html>
