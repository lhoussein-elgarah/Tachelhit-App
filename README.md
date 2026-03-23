<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ra nsawal Tachelhit - Apprendre le Berbère</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <style>
        /* Définition des couleurs Sienne/Terracotta et Bleu Majorelle */
        :root { 
            --terracotta: #E2725B; /* Couleur Terre de Sienne/Terracotta */
            --majorelle: #6050DC;   /* Couleur Bleu Majorelle */
            --majorelle-dark: #4A3BB1; 
        }
        
        /* Application du fond Terracotta sur tout le corps */
        body { 
            background-color: var(--terracotta); 
            font-family: 'Inter', sans-serif; 
            -webkit-tap-highlight-color: transparent; 
        }

        /* Classes utilitaires personnalisées */
        .bg-majorelle { background-color: var(--majorelle); }
        .text-majorelle { color: var(--majorelle); }
        .border-majorelle { border-color: var(--majorelle); }
        .hover\:bg-majorelle-dark:hover { background-color: var(--majorelle-dark); }
        
        .bg-terracotta { background-color: var(--terracotta); }
        .text-terracotta { color: var(--terracotta); }

        /* Styles pour les sessions terminées sur le Dashboard */
        .session-done { 
            border: 3px solid var(--majorelle) !important; 
            position: relative; 
            background-color: #EEF2FF; /* Fond bleu très clair pour les cartes finies */
        }
        .session-done::after { 
            content: '🏆'; 
            position: absolute; 
            top: -12px; 
            right: -12px; 
            font-size: 1.5rem; 
        }

        /* Animation de flip pour les Flashcards */
        .perspective-1000 { perspective: 1000px; }
        .card-inner { transition: transform 0.6s; transform-style: preserve-3d; cursor: pointer; }
        .flipped { transform: rotateY(180deg); }
        .card-face { backface-visibility: hidden; position: absolute; width: 100%; height: 100%; border-radius: 1.5rem; }
        .card-back { transform: rotateY(180deg); }
    </style>
</head>
<body class="text-slate-900 pb-12">

    <nav class="bg-majorelle text-white p-5 shadow-xl sticky top-0 z-50">
        <div class="container mx-auto flex justify-between items-center max-w-2xl">
            <h1 class="text-xl font-black italic tracking-tighter uppercase">Ra nsawal Tachelhit</h1>
            <div class="flex gap-4 text-xs font-bold bg-white/20 px-4 py-2 rounded-2xl">
                <span>🏆 <span id="stat-done">0</span>/36</span>
                <span class="border-l border-white/20 pl-4">🔄 <span id="stat-srs">0</span></span>
            </div>
        </div>
    </nav>

    <main class="container mx-auto p-4 max-w-2xl mt-4">
        
        <section id="view-dashboard" class="space-y-6">
            <div class="bg-white p-6 rounded-3xl shadow-xl border border-slate-100">
                <h2 class="text-3xl font-extrabold text-majorelle mb-2">Azul fllawn !</h2>
                <p class="text-slate-600">Bienvenue dans votre programme de 3 mois. Validez chaque session avec un score de 80% au quiz.</p>
            </div>
            
            <div id="session-grid" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                </div>

            <button onclick="if(confirm('Voulez-vous vraiment effacer toute votre progression ?')){localStorage.clear(); location.reload();}" class="w-full py-3 text-xs text-white/70 hover:text-white transition underline mt-8">Réinitialiser toute ma progression</button>
        </section>

        <section id="view-app" class="hidden space-y-6">
            <button onclick="showDashboard()" class="text-white font-bold flex items-center gap-2 hover:opacity-80 transition">
                <i class="fas fa-arrow-left"></i> Retour au Tableau de Bord
            </button>

            <div class="bg-white rounded-[2rem] shadow-2xl border border-slate-100 overflow-hidden">
                
                <div class="flex bg-slate-100 p-2 gap-2">
                    <button id="tab-flash" onclick="switchMode('flash')" class="flex-1 py-3 rounded-xl font-bold transition-all bg-majorelle text-white">Étudier</button>
                    <button id="tab-quiz" onclick="switchMode('quiz')" class="flex-1 py-3 rounded-xl font-bold transition-all text-slate-400">Quiz (25 Q)</button>
                </div>

                <div id="container-flash" class="p-8 text-center space-y-8">
                    <div class="relative h-72 w-full perspective-1000">
                        <div id="fc-inner" class="card-inner h-full w-full relative" onclick="this.classList.toggle('flipped')">
                            <div class="card-face bg-white border border-slate-100 flex flex-col items-center justify-center p-6 shadow-sm">
                                <span class="text-[10px] font-black text-terracotta tracking-widest mb-4 uppercase">Français</span>
                                <p id="fc-fr" class="text-3xl font-extrabold text-slate-800"></p>
                            </div>
                            <div class="card-face card-back bg-majorelle text-white flex flex-col items-center justify-center p-6 shadow-xl">
                                <p id="fc-tif" class="text-5xl font-bold mb-3 tracking-wider"></p>
                                <p id="fc-sh" class="text-xl italic opacity-90 mb-6"></p>
                                <button onclick="event.stopPropagation(); playVoice();" class="w-14 h-14 bg-white/20 rounded-full flex items-center justify-center hover:bg-white/30 transition-colors border-2 border-white/30 shadow-inner">
                                    <i class="fas fa-volume-up text-2xl"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="flex justify-between items-center max-w-sm mx-auto">
                        <button onclick="changeWord(-1)" class="w-14 h-14 rounded-full border-2 border-slate-200 text-slate-400 hover:bg-slate-50 transition"><i class="fas fa-chevron-left text-lg"></i></button>
                        <span id="fc-idx" class="font-black text-slate-300 text-xl"></span>
                        <button onclick="changeWord(1)" class="w-14 h-14 rounded-full bg-majorelle text-white shadow-lg hover:bg-majorelle-dark transition"><i class="fas fa-chevron-right text-lg"></i></button>
                    </div>
                </div>

                <div id="container-quiz" class="hidden p-8 space-y-6">
                    <div class="flex justify-between items-center text-xs font-bold text-majorelle uppercase tracking-tight">
                        <span>Progression du quiz</span>
                        <span id="quiz-q-num" class="bg-majorelle/10 px-3 py-1 rounded-full">1/25</span>
                    </div>
                    <div class="w-full bg-slate-100 h-3 rounded-full overflow-hidden border border-slate-200">
                        <div id="quiz-bar" class="bg-majorelle h-full w-0 transition-all duration-300"></div>
                    </div>
                    <div class="text-center py-6 border-y border-slate-100">
                        <span class="text-[10px] font-black text-terracotta tracking-widest uppercase mb-2 block">Traduire</span>
                        <h3 id="quiz-question" class="text-3xl font-black text-slate-900"></h3>
                    </div>
                    <div id="quiz-options" class="grid grid-cols-1 gap-3 pt-4">
                        </div>
                </div>

                <div id="container-results" class="hidden p-12 text-center space-y-6">
                    <div id="res-icon" class="text-7xl"></div>
                    <h2 id="res-title" class="text-4xl font-black text-slate-900"></h2>
                    <p id="res-score" class="text-6xl font-black text-majorelle bg-majorelle/5 py-4 rounded-3xl"></p>
                    <div id="res-actions" class="pt-8 space-y-3">
                        </div>
                </div>
            </div>
        </section>
    </main>

    <script>
        // --- BASE DE DONNÉES (36 SESSIONS COMPLETES) ---
        const sessions = [
            // NIVEAU A1 (Sessions 1-10)
            { id: 1, title: "Salutations", words: [{fr:"Bonjour", sh:"Azul", tif:"ⴰⵣⵓⵍ"}, {fr:"Comment vas-tu ?", sh:"Manik antgit ?", tif:"ⵎⴰⵏⵉⴽ ⴰⵏⵜⴳⵉⵜ ?"}, {fr:"Je vais bien", sh:"Labas", tif:"ⵍⴰⴱⴰⵙ"}, {fr:"Merci", sh:"Tanmirt", tif:"ⵜⴰⵏⵎⵉⵔⵜ"}, {fr:"Au revoir", sh:"Ar timliti", tif:"ⴰⵔ ⵜⵉⵎⵍⵉⵜⵉ"}] },
            { id: 2, title: "Famille", words: [{fr:"Père", sh:"Baba", tif:"ⴱⴰⴱⴰ"}, {fr:"Mère", sh:"Imma", tif:"ⵉⵎⵎⴰ"}, {fr:"Frère", sh:"Gwma", tif:"ⴳⵯⵎⴰ"}, {fr:"Sœur", sh:"Ultma", tif:"ⵓⵍⵜⵎⴰ"}, {fr:"Fils", sh:"Yiwis", tif:"ⵢⵉⵡⵉⵙ"}] },
            { id: 3, title: "Nombres (1-10)", words: [{fr:"Un", sh:"Yan", tif:"ⵢⴰⵏ"}, {fr:"Deux", sh:"Sin", tif:"ⵙⵉⵏ"}, {fr:"Trois", sh:"Kṛaḍ", tif:"ⴽⵕⴰⴹ"}, {fr:"Cinq", sh:"Smmus", tif:"ⵙⵎⵎⵓⵙ"}, {fr:"Dix", sh:"Mraw", tif:"ⵎⵔⴰⵡ"}] },
            { id: 4, title: "La Maison", words: [{fr:"Maison", sh:"Tigmmi", tif:"ⵜⴰⴳⵎⵎⵉ"}, {fr:"Porte", sh:"Taggurt", tif:"ⵜⴰⴳⴳⵓⵔⵜ"}, {fr:"Thé", sh:"Atay", tif:"ⴰⵜⴰⵢ"}, {fr:"Pain", sh:"Aghrum", tif:"ⴰⵖⵔⵓⵎ"}, {fr:"Eau", sh:"Aman", tif:"ⴰⵎⴰⵏ"}] },
            { id: 5, title: "Verbes d'action 1", words: [{fr:"Manger", sh:"Iccu", tif:"ⵉⵛⵛⵓ"}, {fr:"Boire", sh:"Ssu", tif:"ⵙⵙⵓ"}, {fr:"Dormir", sh:"Ggn", tif:"ⴳⴳⵏ"}, {fr:"Aller", sh:"Ddu", tif:"ⴷⴷⵓ"}, {fr:"Donner", sh:"Fk", tif:"ⴼⴽ"}] },
            { id: 6, title: "Grammaire : Pluriels", words: [{fr:"L'homme", sh:"Argaz", tif:"ⴰⵔⴳⴰⵣ"}, {fr:"Les hommes", sh:"Irgazn", tif:"ⵉⵔⴳⴰⵣⵏ"}, {fr:"La femme", sh:"Tamghart", tif:"ⵜⴰⵎⵖⴰⵔⵜ"}, {fr:"Les femmes", sh:"Timgharin", tif:"ⵜⴰⵎⵖⴰⵔⵉⵏ"}, {fr:"Enfant", sh:"Afrukh", tif:"ⴰⴼⵔⵓⵅ"}] },
            { id: 7, title: "Couleurs", words: [{fr:"Blanc", sh:"Umlil", tif:"ⵓⵎⵍⵉⵍ"}, {fr:"Noir", sh:"Asggan", tif:"ⴰⵙⴳⴳⴰⵏ"}, {fr:"Rouge", sh:"Azggagh", tif:"ⴰⵣⴳⴳⴰⵖ"}, {fr:"Bleu", sh:"Anili", tif:"ⴰⵏⵉⵍⵉ"}, {fr:"Vert", sh:"Azgzaw", tif:"ⴰⵣⴳgzaw"}] },
            { id: 8, title: "Le Souk", words: [{fr:"Argent", sh:"Idrimn", tif:"ⵉⴷⵔⵉⵎⵏ"}, {fr:"Vendre", sh:"Zznz", tif:"ⵣⵣⵏⵣ"}, {fr:"Acheter", sh:"Sgh", tif:"ⵙⵖ"}, {fr:"Huile", sh:"Argan", tif:"ⴰⵔⴳⴰⵏ"}, {fr:"Viande", sh:"Tifyur", tif:"ⵜⵉⴼⵢⵓⵔ"}] },
            { id: 9, title: "Météo", words: [{fr:"Soleil", sh:"Tafukt", tif:"ⵜⴰⴼⵓⴽⵜ"}, {fr:"Pluie", sh:"Anẓaṛ", tif:"ⴰⵏⵥⴰⵕ"}, {fr:"Froid", sh:"Aṣmmiḍ", tif:"ⴰⵚⵎⵎⵉḍ"}, {fr:"Vent", sh:"Aḍu", tif:"ⴰḍⵓ"}, {fr:"Chaud", sh:"Irgha", tif:"ⵉⵔⵖⴰ"}] },
            { id: 10, title: "Sentiments de base", words: [{fr:"Heureux", sh:"Ifrh", tif:"ⵉⴼⵔⵃ"}, {fr:"Triste", sh:"Iqwn", tif:"ⵉⵇⵯⵏ"}, {fr:"Fatigué", sh:"Irmma", tif:"ⵉⵔⵎⵎⴰ"}, {fr:"Soif", sh:"Iffad", tif:"ⵉⴼⴼⴰⴷ"}, {fr:"Faim", sh:"Laẓ", tif:"ⵍⴰⵥ"}] },
            
            // NIVEAU A2 (Sessions 11-20)
            { id: 11, title: "Métiers", words: [{fr:"Berger", sh:"Amksa", tif:"ⴰⵎⴽⵙⴰ"}, {fr:"Maçon", sh:"Abnnay", tif:"ⴰⴱⵏⵏⴰⵢ"}, {fr:"Forgeron", sh:"Amzil", tif:"ⴰⵎⵣⵉⵍ"}, {fr:"Professeur", sh:"Aslmad", tif:"ⴰⵙⵍⵎⴰⴷ"}, {fr:"Boucher", sh:"Agzzar", tif:"ⴰⴳⵣⵣⴰⵔ"}] },
            { id: 12, title: "Le Corps 1", words: [{fr:"Tête", sh:"Agayyu", tif:"ⴰⴳⴰⵢⵢⵓ"}, {fr:"Yeux", sh:"Alln", tif:"ⴰⵍⵍⵏ"}, {fr:"Main", sh:"Afus", tif:"ⴰⴼⵓⵙ"}, {fr:"Pied", sh:"Aḍaṛ", tif:"ⴰḍaṛ"}, {fr:"Cœur", sh:"Ul", tif:"ⵓⵍ"}] },
            { id: 13, title: "Conjugaison : Passé", words: [{fr:"J'ai mangé", sh:"Cciɣ", tif:"ⵛⵛⵉⵖ"}, {fr:"Tu as mangé", sh:"Tccit", tif:"ⵜⵛⵛⵉⵜ"}, {fr:"Il a mangé", sh:"Icca", tif:"ⵉⵛⵛⴰ"}, {fr:"Nous avons mangé", sh:"Ncca", tif:"ⵏⵛⵛⴰ"}, {fr:"Ils ont mangé", sh:"Ccan", tif:"ⵛⵛⴰⵏ"}] },
            { id: 14, title: "Santé", words: [{fr:"Malade", sh:"Imruḍ", tif:"ⵉⵎⵔⵓḍ"}, {fr:"Médecin", sh:"Amjjay", tif:"ⴰⵎⵊⵊⴰⵢ"}, {fr:"Médicament", sh:"Asafaru", tif:"ⴰⵙⴰⴼⴰⵔⵓ"}, {fr:"Hôpital", sh:"Sbitar", tif:"ⵙⴱⵉⵜⴰⵔ"}, {fr:"Douleur", sh:"Attan", tif:"ⴰⵜⵜⴰⵏ"}] },
            { id: 15, title: "Conjugaison : Futur", words: [{fr:"Je vais aller", sh:"Rad ddugh", tif:"ⵔⴰⴷ ⴷⴷⵓⵖ"}, {fr:"Tu vas aller", sh:"Rad tddut", tif:"ⵔⴰⴷ ⵜⴷⴷⵓⵜ"}, {fr:"Demain", sh:"Ipicka", tif:"ⵉⴱⵉⵛⴽⴰ"}, {fr:"Après-demain", sh:"Naf-ipicka", tif:"ⵏⴰⴼ-ⵉⴱⵉⵛⴽⴰ"}, {fr:"Futur", sh:"Rad...", tif:"ⵔⴰⴷ..."}] },
            { id: 16, title: "Se diriger", words: [{fr:"Ici", sh:"Ghid", tif:"ⵖⵉⴷ"}, {fr:"Là-bas", sh:"Ghin", tif:"ⵖⵉⵏ"}, {fr:"Droite", sh:"Afasi", tif:"ⴰⴼⴰⵙⵉ"}, {fr:"Gauche", sh:"Aẓlmaḍ", tif:"ⴰⵥⵍⵎⴰⴹ"}, {fr:"Milieu", sh:"Anammas", tif:"ⴰⵏⴰⵎⵎⴰⵙ"}] },
            { id: 17, title: "Le Corps 2", words: [{fr:"Ventre", sh:"Aḥlig", tif:"ⴰⵃⵍⵉⴳ"}, {fr:"Dos", sh:"Tadawt", tif:"ⵜⴰⴷawt"}, {fr:"Sang", sh:"Idammn", tif:"ⵉⴷⴰⵎⵎⵏ"}, {fr:"Cheveux", sh:"Azar", tif:"ⴰⵣⴰⵔ"}, {fr:"Peau", sh:"Aglim", tif:"ⴰⴳⵍⵉⵎ"}] },
            { id: 18, title: "Pronoms personnels", words: [{fr:"Moi", sh:"Nki", tif:"ⵏⴽⵉ"}, {fr:"Toi", sh:"Kyin", tif:"ⴽⵢⵉⵏ"}, {fr:"Lui", sh:"Ntta", tif:"ⵏⵜⵜⴰ"}, {fr:"Elle", sh:"Nttat", tif:"ⵏⵜⵜⴰⵜ"}, {fr:"Nous", sh:"Nkkni", tif:"ⵏⴽⴽⵏⵉ"}] },
            { id: 19, title: "Animaux de ferme", words: [{fr:"Cheval", sh:"Ayis", tif:"ⴰⵢⵉⵙ"}, {fr:"Âne", sh:"Aɣyul", tif:"ⴰⵖⵢⵓⵍ"}, {fr:"Chien", sh:"Aydi", tif:"ⴰⵢⴷⵉ"}, {fr:"Chat", sh:"Amcic", tif:"ⴰⵎⵛⵉⵛ"}, {fr:"Vache", sh:"Tafunast", tif:"ⵜⴰⴼⵓⵏⴰⵙⵜ"}] },
            { id: 20, title: "Administration", words: [{fr:"Papier", sh:"Lkaɣiḍ", tif:"ⵍⴽⴰɣⵉḍ"}, {fr:"Écrire", sh:"Ara", tif:"ⴰⵔⴰ"}, {fr:"Nom", sh:"Ism", tif:"ⵉⵙⵎ"}, {fr:"Signer", sh:"Sgni", tif:"ⵙⴳⵏⵉ"}, {fr:"Lieu", sh:"Adghar", tif:"ⴰⴷⵖⴰⵔ"}] },
            
            // NIVEAU B1/B2 (Sessions 21-36)
            { id: 21, title: "L'Atlas", words: [{fr:"Montagne", sh:"Adrar", tif:"ⴰⴷⵔⴰⵔ"}, {fr:"Forêt", sh:"Tagant", tif:"ⵜⴰⴳⴰⵏⵜ"}, {fr:"Rivière", sh:"Asif", tif:"ⴰⵙⵉⴼ"}, {fr:"Vallée", sh:"Imlil", tif:"ⵉⵎⵍⵉⵍ"}, {fr:"Chemin", sh:"Agharas", tif:"ⴰⵖⴰⵔⴰⵙ"}] },
            { id: 22, title: "La Nature", words: [{fr:"Ciel", sh:"Ignwan", tif:"ⵉⴳⵏⵡⴰⵏ"}, {fr:"Étoile", sh:"Itri", tif:"ⵉⵜري"}, {fr:"Lune", sh:"Ayur", tif:"ⴰⵢⵓⵔ"}, {fr:"Terre", sh:"Akkal", tif:"ⴰⴽⴽⴰⵍ"}, {fr:"Arbre", sh:"Addag", tif:"ⴰⴷⴷⴰⴳ"}] },
            { id: 23, title: "Animaux Sauvages", words: [{fr:"Lion", sh:"Ayrad", tif:"ⴰⵢⵔⴰⴷ"}, {fr:"Gazelle", sh:"Tamlalt", tif:"ⵜⴰⵎⵍⴰⵍⵜ"}, {fr:"Oiseau", sh:"Agḍiḍ", tif:"ⴰⴳⴹⵉⴹ"}, {fr:"Serpent", sh:"Ifis", tif:"ⵉⴼⵉⵙ"}, {fr:"Aigle", sh:"Amgdul", tif:"ⴰⵎⴳⴷⵓⵍ"}] },
            { id: 24, title: "Célébrations", words: [{fr:"Mariage", sh:"Tamghra", tif:"ⵜⴰⵎⵖⵔⴰ"}, {fr:"Marié", sh:"Isli", tif:"ⵉⵙⵍⵉ"}, {fr:"Mariée", sh:"Tislit", tif:"ⵜⵉⵙⵍⵉⵜ"}, {fr:"Fête", sh:"Lḥif", tif:"ⵍⵃⵉⴼ"}, {fr:"Danse", sh:"Ahwach", tif:"ⴰⵀⵡⴰⵛ"}] },
            { id: 25, title: "Artisanat Souss", words: [{fr:"Tapis", sh:"Tazrbait", tif:"ⵜⴰⵣⵔⴱⴰⵉⵜ"}, {fr:"Poterie", sh:"Afkhn", tif:"ⴰⴼⵅⵅⴰⵔⵏ"}, {fr:"Cuir", sh:"Ilemu", tif:"ⵉⵍⵎⵓ"}, {fr:"Argent", sh:"Anas", tif:"ⴰⵏⴰⵙ"}, {fr:"Bijou", sh:"Tawnza", tif:"ⵜⴰⵡⵏⵣⴰ"}] },
            { id: 26, title: "Cuisine de fête", words: [{fr:"Couscous", sh:"Seksu", tif:"ⵙⴽⵙⵓ"}, {fr:"Miel", sh:"Tammant", tif:"ⵜⴰⵎⵎⴰⵏⵜ"}, {fr:"Beurre", sh:"Udi", tif:"ⵓⴷⵉ"}, {fr:"Amandes", sh:"Luz", tif:"ⵍⵓⵣ"}, {fr:"Safran", sh:"Zafran", tif:"ⵣⴰⴼⵔⴰⵏ"}] },
            { id: 27, title: "Négocier au Souk", words: [{fr:"Cher", sh:"Ighla", tif:"ⵉⵖⵍⴰ"}, {fr:"Pas cher", sh:"Irkhṣ", tif:"ⵉⵔⵅⵚ"}, {fr:"Prix", sh:"Attaman", tif:"ⴰⵜⵜⴰⵎⴰⵏ"}, {fr:"Réduction", sh:"Nuquṣ", tif:"ⵏⵓⵇⵓⵚ"}, {fr:"Marchand", sh:"Attajr", tif:"ⴰⵜⵜⴰⵊⵔ"}] },
            { id: 28, title: "Rendez-vous", words: [{fr:"Attendre", sh:"Qqel", tif:"ⵇⵇⵍ"}, {fr:"Maintenant", sh:"Ghila", tif:"ⵖⵉⵍⴰ"}, {fr:"En retard", sh:"Ittlkhem", tif:"ⵉⵜⵜⵍⵅⵎ"}, {fr:"Vite", sh:"Zrb", tif:"ⵣⵔⴱ"}, {fr:"Prêt", sh:"Iujad", tif:"ⵉⵓⵊⴰⴷ"}] },
            { id: 29, title: "Technologie", words: [{fr:"Téléphone", sh:"Tilifun", tif:"ⵜⵉⵍⵉⴼⵓⵏ"}, {fr:"Écran", sh:"Tawnat", tif:"ⵜⴰⵡⵏⴰⵜ"}, {fr:"Écrire", sh:"Ara", tif:"ⴰⵔⴰ"}, {fr:"Appeler", sh:"Sawal", tif:"ⵙⴰⵡⴰⵍ"}, {fr:"Photo", sh:"Tawlaft", tif:"ⵜⴰⵡⵍⴰⴼⵜ"}] },
            { id: 30, title: "Nuances : Passif", words: [{fr:"C'est fait", sh:"Ittuskka", tif:"ⵉⵜⵜⵓⵙⴽⴽⴰ"}, {fr:"C'est cassé", sh:"Iṛẕa", tif:"ⵉⵕⵥⴰ"}, {fr:"C'est pris", sh:"Ittyamz", tif:"ⵉⵜⵜⵢⴰⵎⵥ"}, {fr:"C'est dit", sh:"Ittynna", tif:"ⵉⵜⵜⵢⵏⵏⴰ"}, {fr:"Subir", sh:"Ttu-", tif:"ⵜⵜⵓ-"}] },
            { id: 31, title: "Politesse avancée", words: [{fr:"S'il vous plaît", sh:"Aflla-k", tif:"ⴰⴼⵍⵍⴰ-ⴽ"}, {fr:"Pardon", sh:"Samḥiyi", tif:"ⵙⴰⵎⵃⵉⵢⵉ"}, {fr:"Bienvenue", sh:"Marḥba", tif:"ⵎⴰⵔⵃⴱⴰ"}, {fr:"Honneur", sh:"Lqr", tif:"ⵍⵇⵔ"}, {fr:"Respect", sh:"Waqqr", tif:"ⵡⴰⵇⵇⵔ"}] },
            { id: 32, title: "Sentiments abstraits", words: [{fr:"Patience", sh:"Ṣṣbr", tif:"ⵚⵚⴱⵔ"}, {fr:"Courage", sh:"Tazmrt", tif:"ⵜⴰⵣⵎⵔⵜ"}, {fr:"Espoir", sh:"Amuddu", tif:"ⴰⵎⵓⴷⴷⵓ"}, {fr:"Peur", sh:"Tikṣḍi", tif:"ⵜⵉⴽⵚⴹⵉ"}, {fr:"Joie", sh:"Tumrt", tif:"ⵜⵓⵎⵔⵜ"}] },
            { id: 33, title: "Pronoms Relatifs", words: [{fr:"Qui", sh:"Wali", tif:"ⵡⴰⵍⵉ"}, {fr:"Celle qui", sh:"Tali", tif:"ⵜⴰⵍⵉ"}, {fr:"Ceux qui", sh:"Willi", tif:"ⵡⵉⵍⵍⵉ"}, {fr:"Lequel", sh:"Manwa", tif:"ⵎⴰⵏⵡⴰ"}, {fr:"Laquelle", sh:"Manta", tif:"ⵎⴰⵏⵜⴰ"}] },
            { id: 34, title: "Proverbes Berbères 1", words: [{fr:"Petit à petit", sh:"Imik s imik", tif:"ⵉⵎⵉⴽ ⵙ ⵉⵎⵉⴽ"}, {fr:"L'oiseau fait son nid", sh:"Ajdiḍ iskar lkc", tif:"ⴰⵊⴷⵉⴹ ⵉⵙⴽⴰⵔ ⵍⴽⵛ"}, {fr:"Patience est clé", sh:"Ṣṣbr ur dars lḥd", tif:"ⵚⵚⴱⵔ ⵓⵔ ⴷⴰⵔⵙ ⵍⵃⴷ"}, {fr:"L'eau coule", sh:"Aman ar tazzaln", tif:"ⴰⵎⴰⵏ ⴰⵔ ⵜⴰⵣⵣⴰⵍⵏ"}, {fr:"Le temps passe", sh:"Lwaqt ar itfki", tif:"ⵍⵓwaqt ar itfki"}] },
            { id: 35, title: "Exprimer une opinion", words: [{fr:"Je pense", sh:"Ar nwiɣ", tif:"ⴰⵔ ⵏⵡⵉⵖ"}, {fr:"À mon avis", sh:"Ghur-i", tif:"ⵖⵓⵔ-ⵉ"}, {fr:"C'est vrai", sh:"Iṣḥa", tif:"ⵉⵚḥⴰ"}, {fr:"C'est faux", sh:"Ur iṣḥa", tif:"ⵓⵔ ⵉⵚḥⴰ"}, {fr:"Peut-être", sh:"Ahat", tif:"ⴰⵀⴰⵜ"}] },
            { id: 36, title: "Maîtrise : Résumé", words: [{fr:"Langue", sh:"Tamslayt", tif:"ⵜⴰⵎⵙⵍⴰⵢⵜ"}, {fr:"Patrie", sh:"Tamazirt", tif:"ⵜⴰⵎⴰⵣⵉⵔⵜ"}, {fr:"Liberté", sh:"Tilelli", tif:"ⵜⵉⵍⵍⴻⵍⵍⵉ"}, {fr:"Honneur", sh:"Istim", tif:"ⵉⵙⵜⵉⵎ"}, {fr:"Fin", sh:"Tagara", tif:"ⵜⴰⴳⴰⵔⴰ"}] }
        ];

        // --- ÉTAT GLOBAL DE L'APPLICATION ---
        let state = {
            curId: null, // ID de la session en cours
            mode: 'flash', // 'flash' ou 'quiz'
            idx: 0, // Index du mot en cours (Flashcards)
            // Chargement de la progression depuis localStorage
            done: JSON.parse(localStorage.getItem('rn_done')) || [], // Sessions terminées
            srs: JSON.parse(localStorage.getItem('rn_srs')) || [],   // Mots à réviser (SRS)
            qQueue: [], // File d'attente des questions du quiz
            qPtr: 0,   // Pointeur vers la question actuelle
            qOk: 0     // Nombre de bonnes réponses
        };

        // --- INITIALISATION ---
        function init() {
            renderDash();
            updateStats();
        }

        // --- DASHBOARD : GÉNÉRATION DE LA GRILLE ---
        function renderDash() {
            const grid = document.getElementById('session-grid');
            grid.innerHTML = sessions.map(s => `
                <div onclick="startSession(${s.id})" class="p-5 bg-white rounded-3xl shadow-sm border-2 cursor-pointer transition-all hover:border-majorelle hover:shadow-lg ${state.done.includes(s.id) ? 'session-done' : 'border-transparent'}">
                    <p class="text-[10px] font-bold text-majorelle mb-1 uppercase tracking-widest">Session ${s.id}</p>
                    <h3 class="text-lg font-black text-slate-800">${s.title}</h3>
                </div>
            `).join('');
        }

        // --- LOGIQUE DE NAVIGATION ---
        function startSession(id) {
            state.curId = id;
            state.idx = 0;
            document.getElementById('view-dashboard').classList.add('hidden');
            document.getElementById('view-app').classList.remove('hidden');
            document.body.classList.remove('bg-terracotta');
            document.body.style.backgroundColor = '#EEF2FF'; // Fond bleu très clair en mode étude
            switchMode('flash');
        }

        function showDashboard() {
            document.getElementById('view-dashboard').classList.remove('hidden');
            document.getElementById('view-app').classList.add('hidden');
            document.body.style.backgroundColor = ''; // Reset inline style
            document.body.classList.add('bg-terracotta');
            renderDash();
            updateStats();
        }

        function switchMode(m) {
            state.mode = m;
            document.getElementById('container-flash').classList.toggle('hidden', m !== 'flash');
            document.getElementById('container-quiz').classList.toggle('hidden', m !== 'quiz');
            document.getElementById('container-results').classList.add('hidden');
            
            const btnF = document.getElementById('tab-flash');
            const btnQ = document.getElementById('tab-quiz');
            // Styles dynamiques des onglets (Bleu Majorelle)
            btnF.className = m === 'flash' ? "flex-1 py-3 rounded-xl font-bold bg-majorelle text-white shadow-md" : "flex-1 py-3 rounded-xl font-bold text-slate-400 hover:text-majorelle transition";
            btnQ.className = m === 'quiz' ? "flex-1 py-3 rounded-xl font-bold bg-majorelle text-white shadow-md" : "flex-1 py-3 rounded-xl font-bold text-slate-400 hover:text-majorelle transition";

            if (m === 'flash') updateFlash();
            if (m === 'quiz') initQuiz();
        }

        // --- MOTEUR FLASHCARDS ---
        function updateFlash() {
            const s = sessions.find(x => x.id === state.curId);
            const w = s.words[state.idx];
            document.getElementById('fc-fr').innerText = w.fr;
            document.getElementById('fc-tif').innerText = w.tif;
            document.getElementById('fc-sh').innerText = w.sh;
            document.getElementById('fc-idx').innerText = `${state.idx + 1} / ${s.words.length}`;
            document.getElementById('fc-inner').classList.remove('flipped'); // Reset flip state
        }

        function changeWord(dir) {
            const s = sessions.find(x => x.id === state.curId);
            // Bouclage infini des mots
            state.idx = (state.idx + dir + s.words.length) % s.words.length;
            updateFlash();
        }

        // --- MOTEUR QUIZ DYNAMIQUE ---
        function initQuiz() {
            const s = sessions.find(x => x.id === state.curId);
            let pool = [...s.words]; // Mots de la session actuelle
            
            // Injection intelligente des erreurs SRS passées
            state.srs.forEach(w => { 
                if(!pool.some(p => p.fr === w.fr)) pool.push(w); 
            });

            // Création d'une file d'attente de 25 questions tirées du pool
            state.qQueue = [];
            for(let i=0; i<25; i++) {
                state.qQueue.push(pool[i % pool.length]);
            }
            state.qQueue.sort(() => Math.random() - 0.5); // Mélange aléatoire
            
            state.qPtr = 0;
            state.qOk = 0;
            renderQ();
        }

        function renderQ() {
            const q = state.qQueue[state.qPtr];
            
            // Mise à jour de l'UI
            document.getElementById('quiz-q-num').innerText = `${state.qPtr + 1}/25`;
            document.getElementById('quiz-bar').style.width = `${(state.qPtr / 25) * 100}%`;
            document.getElementById('quiz-question').innerText = q.fr;

            // Génération des options (1 bonne, 3 mauvaises)
            let opts = [q.sh];
            const allSh = sessions.flatMap(s => s.words.map(w => w.sh)); // Pool de toutes les réponses Tachelhit possibles
            
            while(opts.length < 4) {
                let r = allSh[Math.floor(Math.random() * allSh.length)];
                if(!opts.includes(r)) opts.push(r);
            }
            opts.sort(() => Math.random() - 0.5); // Mélange des options

            // Rendu des boutons d'option
            document.getElementById('quiz-options').innerHTML = opts.map(o => `
                <button onclick="check('${o.replace(/'/g, "\\'")}', '${q.sh.replace(/'/g, "\\'")}')" class="w-full py-5 px-6 bg-slate-50 border-2 border-slate-100 rounded-2xl font-bold text-left text-slate-800 hover:border-majorelle hover:bg-indigo-50 transition-all flex justify-between items-center group">
                    ${o}
                    <i class="fas fa-arrow-right text-majorelle opacity-0 group-hover:opacity-100 transition"></i>
                </button>
            `).join('');
        }

        function check(sel, cor) {
            const q = state.qQueue[state.qPtr];
            
            if (sel === cor) {
                // Bonne réponse
                state.qOk++;
                // Retirer du SRS si présent
                state.srs = state.srs.filter(x => x.fr !== q.fr);
            } else {
                // Mauvaise réponse -> Ajouter/Maintenir dans SRS
                if(!state.srs.some(x => x.fr === q.fr)) state.srs.push(q);
            }
            
            // Sauvegarde immédiate du SRS
            localStorage.setItem('rn_srs', JSON.stringify(state.srs));
            
            // Passage à la question suivante
            state.qPtr++;
            if (state.qPtr < 25) renderQ(); else showRes();
        }

        // --- SON DE VICTOIRE (SYNTHÉTISÉ) ---
        function playVictorySound() {
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                // Suite de notes harmonieuses (C5, E5, G5, C6)
                const notes = [523.25, 659.25, 783.99, 1046.50]; 
                
                notes.forEach((freq, i) => {
                    const osc = ctx.createOscillator();
                    const gain = ctx.createGain();
                    osc.type = 'triangle'; // Son doux
                    osc.frequency.setValueAtTime(freq, ctx.currentTime + i * 0.12);
                    
                    gain.gain.setValueAtTime(0.1, ctx.currentTime + i * 0.12);
                    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + i * 0.12 + 0.4);
                    
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    
                    osc.start(ctx.currentTime + i * 0.12);
                    osc.stop(ctx.currentTime + i * 0.12 + 0.5);
                });
            } catch(e) { console.log("Audio non supporté"); }
        }

        // --- ÉCRAN RÉSULTATS ---
        function showRes() {
            document.getElementById('container-quiz').classList.add('hidden');
            const res = document.getElementById('container-results');
            res.classList.remove('hidden');
            
            // Critère de succès : 80% (20 bonnes réponses sur 25)
            const scorePercent = (state.qOk / 25) * 100;
            const win = scorePercent >= 80;
            
            document.getElementById('res-icon').innerText = win ? "🏆" : "📚";
            document.getElementById('res-title').innerText = win ? "BRAVO ! Session Validée" : "ENCORE UN EFFORT...";
            document.getElementById('res-score').innerText = `${state.qOk}/25`;
            
            if (win && !state.done.includes(state.curId)) {
                // Marquage de la session comme terminée
                state.done.push(state.curId);
                localStorage.setItem('rn_done', JSON.stringify(state.done));
                playVictorySound();
            }

            // Boutons d'action conditionnels
            document.getElementById('res-actions').innerHTML = win ? 
                `<button onclick="showDashboard()" class="w-full py-4 bg-majorelle text-white rounded-2xl font-bold text-lg hover:bg-majorelle-dark shadow-lg transition">Continuer vers le Dashboard</button>` :
                `<button onclick="switchMode('flash')" class="w-full py-4 bg-terracotta text-white rounded-2xl font-bold text-lg hover:opacity-90 shadow-lg transition mb-3">Réviser les Flashcards</button>
                 <button onclick="initQuiz()" class="w-full py-4 border-2 border-slate-200 rounded-2xl font-bold text-slate-600 hover:bg-slate-50 transition">Réessayer le Quiz (25 Q)</button>`;
            updateStats();
        }

        // --- MISE À JOUR STATS NAV BAR ---
        function updateStats() {
            document.getElementById('stat-done').innerText = state.done.length;
            document.getElementById('stat-srs').innerText = state.srs.length;
        }

        // --- SYNTHÈSE VOCALE (TEXT-TO-SPEECH) ---
        function playVoice() {
            const s = sessions.find(x => x.id === state.curId);
            const text = s.words[state.idx].sh;
            // Note : Le Tachelhit n'est pas supporté nativement. 
            // 'fr-FR' est utilisé comme fallback pour la lecture phonétique.
            const msg = new SpeechSynthesisUtterance(text);
            msg.lang = 'fr-FR'; 
            msg.rate = 0.8;
            window.speechSynthesis.speak(msg);
        }

        // --- DÉMARRAGE ---
        init();

    </script>
</body>
</html>
# lhoussein.github.io
