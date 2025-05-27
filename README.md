<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kuis Pengetahuan Arduino Dasar</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }

        .quiz-container {
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
            width: 100%;
            max-width: 600px;
            text-align: center;
        }

        h1 {
            color: #333;
            margin-bottom: 10px;
        }

        #completion-message {
            color: #28a745;
            font-size: 1.2em;
            font-weight: bold;
            margin-top: 5px;
            margin-bottom: 20px;
        }

        .question-counter-text {
            font-size: 0.9em;
            color: #666;
            margin-bottom: 20px;
        }

        #question-container {
            margin-bottom: 20px;
        }

        #question {
            font-size: 1.5em;
            font-weight: bold;
            margin-bottom: 25px;
            color: #444;
        }

        .btn-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }

        .btn {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 12px 15px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            transition: background-color 0.2s ease, box-shadow 0.2s ease;
            word-wrap: break-word;
            min-height: 50px;
            display: flex;
            align-items: center;
            justify-content: center;
            outline: none;
            font-weight: bold;
        }

        .btn:not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q) { background-color: #007bff; }
        .btn:not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q):focus {
            background-color: #007bff;
            box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.5);
        }
        .btn:not([disabled]):not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q):hover {}
        .btn:not([disabled]):not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q):focus:hover {
            background-color: #007bff;
            box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.5);
        }

        .btn.correct { background-color: #28a745 !important; box-shadow: none; }
        .btn.correct:hover { background-color: #218838 !important; }
        .btn.correct:focus {
            background-color: #28a745 !important;
            box-shadow: 0 0 0 3px rgba(40, 167, 69, 0.6) !important;
        }

        .btn.wrong { background-color: #dc3545 !important; box-shadow: none; }
        .btn.wrong:hover { background-color: #c82333 !important; }
        .btn.wrong:focus {
            background-color: #dc3545 !important;
            box-shadow: 0 0 0 3px rgba(220, 53, 69, 0.6) !important;
        }

        .btn:disabled {
            cursor: not-allowed;
            opacity: 0.65;
        }
        /* Adjusted to not conflict with new button's disabled state if it's not a skip-btn or answer btn */
        .btn:disabled:not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q) {
            background-color: #6c757d !important;
            color: #ccc !important;
        }


        .controls {
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        #skip-navigation-controls {
            justify-content: space-between; /* Adjusted to space-around or similar if needed for 3 buttons */
            margin-top: 40px;
            margin-bottom: 10px;
        }

        .skip-btn { /* This style is for prev-50 and next-50 */
            background-color: #28a745; /* Green */
            color: white;
            padding: 8px 12px;
            font-size: 0.9em;
            min-width: 80px; /* Ensures same width for all skip-type buttons */
        }
        .skip-btn:hover {
            background-color: #218838; /* Darker Green */
            color: white;
        }
        .skip-btn:disabled { /* Default disabled for green skip buttons */
            background-color: #a3d8b0 !important;
            color: #e9f5ec !important;
            /* cursor: not-allowed; is inherited from .btn:disabled */
            /* opacity: 0.65; is inherited from .btn:disabled */
        }

        /* New button style for "Previous Question" */
        .btn-prev-q {
            background-color: #5F9EA0; /* CadetBlue - "biru terang" */
            color: white; /* Text color */
            padding: 8px 12px; /* Same padding as skip-btn */
            font-size: 0.9em; /* Same font size as skip-btn */
            min-width: 80px; /* Same min-width as skip-btn */
        }
        .btn-prev-q:hover:not([disabled]) {
            background-color: #4682B4; /* SteelBlue - darker for hover */
            color: white;
        }
        .btn-prev-q:disabled {
            background-color: #B0C4DE !important; /* LightSteelBlue - for disabled state */
            color: #666666 !important; /* Darker text for readability on light blue */
            /* opacity will be applied by .btn:disabled */
        }


        .hide { display: none !important; }
    </style>
</head>
<body>
    <div class="quiz-container">
        <h1>Pengetahuan Arduino Dasar</h1>
        <p id="completion-message" class="hide">Selamat Kuis Sudah Selesai 🎉</p>
        <div id="initial-controls" class="controls">
            <button id="start-btn" class="btn">Mulai</button>
            <button id="continue-btn" class="btn hide">Lanjutkan</button>
        </div>
        <div id="question-counter" class="question-counter-text hide">0/0</div>
        <div id="question-container" class="hide">
            <div id="question">Kata Bahasa Inggris</div>
            <div id="answer-buttons" class="btn-grid">
            </div>
            <div id="skip-navigation-controls" class="controls hide">
                <button id="prev-50-btn" class="btn skip-btn">&laquo; 50</button>
                <button id="prev-question-btn" class="btn btn-prev-q">&lt;</button> <button id="next-50-btn" class="btn skip-btn">50 &raquo;</button>
            </div>
        </div>
    </div>

    <script>
        const startButton = document.getElementById('start-btn');
        const continueButton = document.getElementById('continue-btn');
        const initialControls = document.getElementById('initial-controls');
        const completionMessageElement = document.getElementById('completion-message');
        const questionContainerElement = document.getElementById('question-container');
        const questionElement = document.getElementById('question');
        const answerButtonsElement = document.getElementById('answer-buttons');
        const questionCounterElement = document.getElementById('question-counter');

        const skipNavigationControls = document.getElementById('skip-navigation-controls');
        const prev50Button = document.getElementById('prev-50-btn');
        const prevQuestionButton = document.getElementById('prev-question-btn'); // Referensi untuk tombol baru
        const next50Button = document.getElementById('next-50-btn');
        const JUMP_AMOUNT = 50;

        let orderedQuestions, currentQuestionIndex;
        let score = 0;
        let questionTimeout;

        // Daftar kata mentah dari PDF (Inggris: Indonesia) - Total 1580 kata
        const rawVocabularyList = [
            // Kosakata 1-308 (dari PDF pertama)disini yahhhhh
        // Set 1: 100 Pertanyaan Elektronika Dasar

            { en: 'abandon', id: 'meninggalkan' }, { en: 'abate', id: 'mereda' },
            { en: 'abbreviation', id: 'singkatan' }, { en: 'abduct', id: 'menculik' },
            { en: 'abhor', id: 'jijik' }, { en: 'abject', id: 'hina' },
            { en: 'able', id: 'sanggup' }, { en: 'abolish', id: 'menghapuskan' },
            { en: 'abominate', id: 'merasa jijik' }, { en: 'abort', id: 'menggugurkan' },
            { en: 'abrupt', id: 'tiba-tiba' }, { en: 'absolve', id: 'membebaskan' },
            { en: 'abstain', id: 'menjauhkan diri' }, { en: 'abstruse', id: 'muskil' },
            { en: 'absurd', id: 'konyol' }, { en: 'abundant', id: 'berlimpah-limpah' },
            { en: 'abyss', id: 'neraka' }, { en: 'accessible', id: 'diakses' },
            { en: 'acclaim', id: 'pujian' }, { en: 'accomplice', id: 'pembantu' },
            { en: 'accurate', id: 'tepat' }, { en: 'accuse', id: 'menuduh' },
            { en: 'acid', id: 'asam' }, { en: 'acquaint', id: 'memperkenalkan' },
            { en: 'acquiesce', id: 'menyetujui' }, { en: 'acrid', id: 'tajam' },
            { en: 'actuate', id: 'menjalankan' }, { en: 'acute', id: 'akut' },
            { en: 'adamant', id: 'kukuh' }, { en: 'adapt', id: 'menyesuaikan' },
            { en: 'adept', id: 'mahir' }, { en: 'adequate', id: 'memadai' },
            { en: 'adhere', id: 'mengikuti' }, { en: 'adjacent', id: 'berdekatan' },
            { en: 'adjourn', id: 'mempertangguhkan' }, { en: 'admonish', id: 'menegur' },
            { en: 'adorn', id: 'menghiasi' }, { en: 'advent', id: 'kedatangan' },
            { en: 'adverse', id: 'merugikan' }, { en: 'advocate', id: 'penganjur' },
            { en: 'affable', id: 'ramah' }, { en: 'affect', id: 'mempengaruhi' },
            { en: 'affiliate', id: 'afiliasi' }, { en: 'affinity', id: 'afinitas' },
            { en: 'affliction', id: 'penderitaan' }, { en: 'affluent', id: 'makmur' },
            { en: 'aggravate', id: 'memperburuk' }, { en: 'aggregate', id: 'agregat' },
            { en: 'agile', id: 'tangkas' }, { en: 'agitate', id: 'mengagitasi' },
            { en: 'agrarian', id: 'agraris' }, { en: 'ailment', id: 'penyakit' },
            { en: 'akin', id: 'sama' }, { en: 'allegiance', id: 'kepatuhan' },
            { en: 'alleviate', id: 'meringankan' }, { en: 'allocate', id: 'membagikan' },
            { en: 'allot', id: 'membagikan' }, { en: 'allude', id: 'menyinggung' },
            { en: 'aloft', id: 'tinggi' }, { en: 'aloof', id: 'jauh' },
            { en: 'alternative', id: 'alternatif' }, { en: 'amateurish', id: 'kurang baik' },
            { en: 'amazing', id: 'menakjubkan' }, { en: 'ambient', id: 'ambien' },
            { en: 'ambiguous', id: 'dwimakna' }, { en: 'ambivalence', id: 'ambivalensi' },
            { en: 'ambivalent', id: 'ambivalen' }, { en: 'amelioration', id: 'perbaikan' },
            { en: 'amenable', id: 'menerima' }, { en: 'amiable', id: 'ramah' },
            { en: 'amicable', id: 'ramah tamah' }, { en: 'amnesia', id: 'amnesia' },
            { en: 'amnesty', id: 'amnesti' }, { en: 'amphitheater', id: 'ampiteater' },
            { en: 'analogous', id: 'sejalan' }, { en: 'anecdote', id: 'anekdot' },
            { en: 'annex', id: 'mencaplok' }, { en: 'annual', id: 'tahunan' },
            { en: 'anonymous', id: 'anonim' }, { en: 'antagonism', id: 'antagonisme' },
            { en: 'antagonist', id: 'antagonis' }, { en: 'antebellum', id: 'sebelum perang' },
            { en: 'antedate', id: 'mendahului' }, { en: 'anticipate', id: 'mengharapkan' },
            { en: 'antique', id: 'antik' }, { en: 'anxious', id: 'cemas' },
            { en: 'apathy', id: 'apati' }, { en: 'apollonian', id: 'Apolonia' },
            { en: 'apparent', id: 'nyata' }, { en: 'appealing', id: 'menarik' },
            { en: 'appease', id: 'menenangkan' }, { en: 'applaud', id: 'salut' },
            { en: 'appraise', id: 'menilai' }, { en: 'apprise', id: 'memberitahukan' },
            { en: 'approbation', id: 'persetujuan' }, { en: 'apt', id: 'tepat' },
            { en: 'aqueous', id: 'encer' }, { en: 'arable', id: 'garapan' },
            { en: 'arduous', id: 'sulit' }, { en: 'arid', id: 'gersang' },
            { en: 'armistice', id: 'gencatan senjata' }, { en: 'aroma', id: 'aroma' },
            { en: 'arrogant', id: 'sombong' }, { en: 'articulate', id: 'pandai berbicara' },
            { en: 'artificial', id: 'buatan' }, { en: 'ascertain', id: 'memastikan' },
            { en: 'assail', id: 'menyerang' }, { en: 'assault', id: 'serangan' },
            { en: 'assert as', id: 'menegaskan' }, { en: 'assiduous', id: 'tekun' },
            { en: 'assimilate', id: 'mengasimilasi' }, { en: 'astonishing', id: 'mengherankan' },
            { en: 'astute', id: 'cerdik' }, { en: 'atone', id: 'menebus' },
            { en: 'atrocious', id: 'mengerikan' }, { en: 'atrocity', id: 'kekejaman' },
            { en: 'attain', id: 'mencapai' }, { en: 'attribute', id: 'atribut' },
            { en: 'audacious', id: 'berani' }, { en: 'auditory', id: 'pendengaran' },
            { en: 'augment', id: 'menambah' }, { en: 'august', id: 'mulia' },
            { en: 'austere', id: 'keras' }, { en: 'authentic', id: 'asli' },
            { en: 'authorize', id: 'mengizinkan' }, { en: 'autocracy', id: 'otokrasi' },
            { en: 'automaton', id: 'otomat' }, { en: 'autonomy', id: 'otonomi' },
            { en: 'avalanche', id: 'longsor' }, { en: 'avarice', id: 'ketamakan' },
            { en: 'aver', id: 'menegaskan' }, { en: 'aversion', id: 'keengganan' },
            { en: 'avert', id: 'mencegah' }, { en: 'aviator', id: 'penerbang' },
            { en: 'avid', id: 'keranjingan' }, { en: 'avoid', id: 'menghindari' },
            { en: 'avouch', id: 'menanggung' }, { en: 'awkward', id: 'canggung' },
            { en: 'bacchanalian', id: 'pora' }, { en: 'bachelor', id: 'bujangan' },
            { en: 'backbreaking', id: 'melelahkan' }, { en: 'baffle', id: 'membingungkan' },
            { en: 'bald', id: 'botak' }, { en: 'balmy', id: 'gila' },
            { en: 'ban', id: 'melarang' }, { en: 'bankrupt', id: 'palit' },
            { en: 'bar', id: 'bar' }, { en: 'bare', id: 'telanjang' },
            { en: 'barren', id: 'mandul' }, { en: 'barter', id: 'barter' },
            { en: 'bashful', id: 'malu' }, { en: 'bead', id: 'titis' },
            { en: 'beam', id: 'balok' }, { en: 'bear', id: 'menanggung' },
            { en: 'beckon', id: 'mengisyaratkan' }, { en: 'becoming', id: 'menjadi' },
            { en: 'befall', id: 'menimpa' }, { en: 'bellicose', id: 'yg suka berperang' },
            { en: 'belligerence', id: 'agresif' }, { en: 'belligerent', id: 'yg suka berperang' },
            { en: 'beneficial', id: 'bermanfaat' }, { en: 'beneficiary', id: 'penerima' },
            { en: 'benefit', id: 'manfaat' }, { en: 'benevolent', id: 'penuh kebajikan' },
            { en: 'bequeath', id: 'mewariskan' }, { en: 'besiege', id: 'mengepung' },
            { en: 'bestow', id: 'melimpahkan' }, { en: 'betray', id: 'mengkhianati' },
            { en: 'beverage', id: 'minuman' }, { en: 'bias', id: 'prasangka' },
            { en: 'bicker', id: 'cekcok' }, { en: 'bilateral', id: 'bilateral' },
            { en: 'biography', id: 'biografi' }, { en: 'bland', id: 'lunak' },
            { en: 'blatant', id: 'menyolok' }, { en: 'blend', id: 'campuran' },
            { en: 'blizzard', id: 'badai salju' }, { en: 'bloom', id: 'berkembang' },
            { en: 'bluff', id: 'menggertak' }, { en: 'blunder', id: 'keliru' },
            { en: 'blunt', id: 'menumpulkan' }, { en: 'blurry', id: 'kabur' },
            { en: 'bold', id: 'berani' }, { en: 'bolster', id: 'mendukung' },
            { en: 'bond', id: 'obligasi' }, { en: 'boom', id: 'ledakan' },
            { en: 'bothersome', id: 'menyusahkan' }, { en: 'bough', id: 'dahan' },
            { en: 'brace', id: 'penjepit' }, { en: 'breakthrough', id: 'penerobosan' },
            { en: 'breathtaking', id: 'hati' }, { en: 'breed', id: 'berkembang biak' },
            { en: 'breeze', id: 'angin sepoi-sepoi' }, { en: 'brilliant', id: 'cemerlang' },
            { en: 'brisk', id: 'cepat' }, { en: 'brittle', id: 'rapuh' },
            { en: 'broach', id: 'bros' }, { en: 'brochure', id: 'brosur' },
            { en: 'brutal', id: 'brutal' }, { en: 'bulky', id: 'tebal' },
            { en: 'burrow', id: 'liang' }, { en: 'buttress', id: 'menopang' },
            { en: 'cajole', id: 'membujuk' }, { en: 'calamity', id: 'bencana' },
            { en: 'calisthenics', id: 'senam' }, { en: 'calm', id: 'menenangkan' },
            { en: 'camouflage', id: 'kamuflase' }, { en: 'canvass', id: 'kampas' },
            { en: 'capable', id: 'mampu' }, { en: 'captivate', id: 'memikat hati' },
            { en: 'captor', id: 'penawan' }, { en: 'carefree', id: 'riang' },
            { en: 'caricature', id: 'karikatur' }, { en: 'carnage', id: 'pembunuhan besar-besaran' },
            { en: 'carve', id: 'mengukir' }, { en: 'cast', id: 'melemparkan' },
            { en: 'casual', id: 'lepas' }, { en: 'cataclysm', id: 'bencana alam' },
            { en: 'catastrophe', id: 'malapetaka' }, { en: 'caustic', id: 'pedas' },
            { en: 'cautious', id: 'berhati-hati' }, { en: 'cavity', id: 'rongga' },
            { en: 'celebrated', id: 'kenamaan' }, { en: 'censor', id: 'sensor' },
            { en: 'censure', id: 'kecaman' }, { en: 'census', id: 'sensus' },
            { en: 'centennial', id: 'seratus tahun' }, { en: 'chaffer', id: 'tawar-menawar' },
            { en: 'chaos', id: 'kekacauan' }, { en: 'charming', id: 'menawan' },
            { en: 'chasm', id: 'jurang' }, { en: 'cherish', id: 'menghargai' },
            { en: 'chicanery', id: 'ketidakjujuran' }, { en: 'chide', id: 'mencaci' },
            { en: 'chilly', id: 'dingin' }, { en: 'choice', id: 'pilihan' },
            { en: 'chop', id: 'memotong' }, { en: 'chubby', id: 'gemuk' },
            { en: 'cicerone', id: 'penunjuk jalan' }, { en: 'cite', id: 'mengutip' },
            { en: 'clandestine', id: 'rahasia' }, { en: 'clash', id: 'bentrokan' },
            { en: 'classify', id: 'menggolongkan' }, { en: 'clever', id: 'pintar' },
            { en: 'cliche', id: 'kata klise' }, { en: 'cling', id: 'melekat' },
            { en: 'clumsy', id: 'kikuk' }, { en: 'coalescence', id: 'peleburan' },
            { en: 'coalition', id: 'koalisi' }, { en: 'coax', id: 'membujuk' },
            { en: 'coherent', id: 'koheren' }, { en: 'coin', id: 'koin' },
            { en: 'collaborate', id: 'berkolaborasi' }, { en: 'colossal', id: 'kolosal' },
            { en: 'commence', id: 'memulai' }, { en: 'commerce', id: 'perdagangan' },
            { en: 'commitment', id: 'komitmen' }, { en: 'commodity', id: 'komoditi' },
            { en: 'compact', id: 'padat' }, { en: 'compel', id: 'memaksa' }



        ];

        let questions = [];

        rawVocabularyList.sort((a, b) => {
            const enA = a.en.toLowerCase();
            const enB = b.en.toLowerCase();
            if (enA < enB) return -1;
            if (enA > enB) return 1;
            return 0;
        });

        function generateQuestions() {
            const allIndonesianTranslations = rawVocabularyList.map(item => item.id);
            questions = [];
            rawVocabularyList.forEach(vocabItem => {
                const correctAnswer = vocabItem.id;
                const distractors = [];
                let attempts = 0;
                while (distractors.length < 3 && attempts < allIndonesianTranslations.length * 2) {
                    const randomIndex = Math.floor(Math.random() * allIndonesianTranslations.length);
                    const potentialDistractor = allIndonesianTranslations[randomIndex];
                    if (potentialDistractor !== correctAnswer && !distractors.includes(potentialDistractor)) {
                        distractors.push(potentialDistractor);
                    }
                    attempts++;
                }
                while (distractors.length < 3) {
                    const fallbackOptions = ["opsi lain A", "opsi lain B", "opsi lain C", "opsi lain D", "opsi lain E", "opsi lain F"];
                    let fallbackIndex = 0;
                    let safetyNet = 0;
                    while(distractors.length < 3 && safetyNet < fallbackOptions.length * 3) {
                        const fbOption = fallbackOptions[fallbackIndex % fallbackOptions.length] + `_${distractors.length}${Math.floor(Math.random()*100)}`;
                        if (fbOption !== correctAnswer && !distractors.includes(fbOption)) {
                             distractors.push(fbOption);
                        }
                        fallbackIndex++;
                        safetyNet++;
                    }
                     if(distractors.length < 3) {
                        for(let i=0; i < (3-distractors.length); i++){
                            distractors.push("pilihan default " + (i+1+distractors.length) + Math.random().toString(36).substring(7));
                        }
                     }
                }
                const answerOptions = [
                    { text: correctAnswer, correct: true },
                    { text: distractors[0], correct: false },
                    { text: distractors[1], correct: false },
                    { text: distractors[2], correct: false }
                ];
                questions.push({
                    question: vocabItem.en,
                    answers: answerOptions
                });
            });
        }

        generateQuestions();

        function saveProgress() {
            if (!questionContainerElement.classList.contains('hide') && orderedQuestions && currentQuestionIndex < orderedQuestions.length) {
                 const progress = {
                    currentQuestionIndex: currentQuestionIndex,
                    score: score,
                    orderedQuestions: orderedQuestions
                };
                localStorage.setItem('quizProgress', JSON.stringify(progress));
            }
        }

        function loadProgress() {
            const savedProgress = localStorage.getItem('quizProgress');
            if (savedProgress) {
                try {
                    const progressData = JSON.parse(savedProgress);
                    if (progressData && typeof progressData.currentQuestionIndex === 'number' &&
                        typeof progressData.score === 'number' && Array.isArray(progressData.orderedQuestions) &&
                        progressData.orderedQuestions.length > 0 &&
                        progressData.currentQuestionIndex < progressData.orderedQuestions.length &&
                        progressData.orderedQuestions.length === questions.length) { // Validasi tambahan: jumlah soal harus sama
                        return progressData;
                    } else {
                        clearProgress();
                        return null;
                    }
                } catch (e) {
                    console.error("Error parsing saved progress:", e);
                    clearProgress();
                    return null;
                }
            }
            return null;
        }

        function clearProgress() {
            localStorage.removeItem('quizProgress');
        }

        prev50Button.addEventListener('click', () => navigateQuestions(-JUMP_AMOUNT));
        prevQuestionButton.addEventListener('click', () => navigateQuestions(-1)); // Event listener untuk tombol baru
        next50Button.addEventListener('click', () => navigateQuestions(JUMP_AMOUNT));

        function navigateQuestions(amount) {
            clearTimeout(questionTimeout);
            if (!orderedQuestions || orderedQuestions.length === 0) return;

            let newIndex = currentQuestionIndex + amount;
            if (newIndex < 0) newIndex = 0;
            else if (newIndex >= orderedQuestions.length) newIndex = orderedQuestions.length - 1;

            if (newIndex !== currentQuestionIndex) {
                currentQuestionIndex = newIndex;
                setNextQuestion();
            } else {
                updateSkipButtonStates();
            }
        }

        function updateSkipButtonStates() {
            if (!orderedQuestions || orderedQuestions.length === 0 || questionContainerElement.classList.contains('hide')) {
                skipNavigationControls.classList.add('hide');
                if(prev50Button) prev50Button.disabled = true;
                if(prevQuestionButton) prevQuestionButton.disabled = true; // Nonaktifkan tombol baru
                if(next50Button) next50Button.disabled = true;
                return;
            }
            skipNavigationControls.classList.remove('hide');
            const isFirstQuestion = currentQuestionIndex === 0;
            const isLastQuestion = currentQuestionIndex === (orderedQuestions.length - 1);

            if(prev50Button) prev50Button.disabled = isFirstQuestion;
            if(prevQuestionButton) prevQuestionButton.disabled = isFirstQuestion; // Atur status disabled tombol baru
            if(next50Button) next50Button.disabled = isLastQuestion;

            if (orderedQuestions.length <= 1) {
                if(prev50Button) prev50Button.disabled = true;
                if(prevQuestionButton) prevQuestionButton.disabled = true; // Atur status disabled tombol baru
                if(next50Button) next50Button.disabled = true;
            }
        }


        window.addEventListener('load', () => {
            const savedData = loadProgress();
            startButton.innerText = 'Mulai';
            completionMessageElement.classList.add('hide');
            if (savedData) {
                continueButton.classList.remove('hide');
            } else {
                continueButton.classList.add('hide');
            }
            if (questionContainerElement.classList.contains('hide')) {
                initialControls.classList.remove('hide');
                skipNavigationControls.classList.add('hide');
            } else {
                 initialControls.classList.add('hide');
                 // Mungkin juga perlu updateSkipButtonStates() di sini jika kuis dilanjutkan
                 // dan langsung menampilkan soal.
            }
        });

        startButton.addEventListener('click', () => startGame(false));
        continueButton.addEventListener('click', () => startGame(true));

        function startGame(isContinuing = false) {
            clearTimeout(questionTimeout);
            completionMessageElement.classList.add('hide');
            if (!isContinuing) {
                startButton.innerText = 'Mulai';
            }
            initialControls.classList.add('hide');
            questionContainerElement.classList.remove('hide');
            questionCounterElement.classList.remove('hide');

            const savedData = loadProgress();
            if (isContinuing && savedData && savedData.orderedQuestions && savedData.orderedQuestions.length === questions.length) {
                orderedQuestions = savedData.orderedQuestions;
                currentQuestionIndex = savedData.currentQuestionIndex;
                score = savedData.score;
            } else {
                clearProgress();
                orderedQuestions = [...questions];
                currentQuestionIndex = 0;
                score = 0;
            }

            if (!orderedQuestions || orderedQuestions.length === 0) {
                showResults();
                completionMessageElement.innerText = "Tidak ada soal untuk ditampilkan.";
                completionMessageElement.style.color = "#dc3545";
                completionMessageElement.classList.remove('hide');
                startButton.innerText = 'Mulai';
                return;
            }
            setNextQuestion();
        }

        function setNextQuestion() {
            resetState();
            if (orderedQuestions && currentQuestionIndex < orderedQuestions.length) {
                questionCounterElement.innerText = `${currentQuestionIndex + 1} / ${orderedQuestions.length}`;
                showQuestion(orderedQuestions[currentQuestionIndex]);
                saveProgress();
                if (document.activeElement && typeof document.activeElement.blur === 'function') {
                    document.activeElement.blur();
                }
            } else {
                showResults();
            }
            updateSkipButtonStates(); // Panggil di sini untuk memastikan state tombol selalu update
        }

        function showQuestion(questionData) {
            questionElement.innerText = questionData.question;
            answerButtonsElement.innerHTML = '';
            const shuffledAnswers = [...questionData.answers].sort(() => Math.random() - 0.5);
            shuffledAnswers.forEach(answer => {
                const button = document.createElement('button');
                button.innerText = answer.text;
                button.classList.add('btn');
                if (answer.correct) {
                    button.dataset.correct = answer.correct;
                }
                button.addEventListener('click', selectAnswer);
                answerButtonsElement.appendChild(button);
            });
        }

        function resetState() {
            clearTimeout(questionTimeout);
            while (answerButtonsElement.firstChild) {
                answerButtonsElement.removeChild(answerButtonsElement.firstChild);
            }
        }

        function selectAnswer(e) {
            const selectedButton = e.target;
            const correct = selectedButton.dataset.correct === 'true';
            if (correct) { score++; }
            Array.from(answerButtonsElement.children).forEach(button => {
                setStatusClass(button, button.dataset.correct === 'true');
                button.disabled = true;
            });
            saveProgress();
            questionTimeout = setTimeout(() => {
                if (orderedQuestions && currentQuestionIndex < orderedQuestions.length -1) {
                    currentQuestionIndex++;
                    setNextQuestion();
                } else if (orderedQuestions && currentQuestionIndex === orderedQuestions.length -1) {
                    showResults();
                }
            }, 9000);
        }

        function setStatusClass(element, correct) {
            clearStatusClass(element);
            if (correct) { element.classList.add('correct'); }
            else { element.classList.add('wrong'); }
        }

        function clearStatusClass(element) {
            element.classList.remove('correct');
            element.classList.remove('wrong');
        }

        function showResults() {
            clearTimeout(questionTimeout);
            questionContainerElement.classList.add('hide');
            questionCounterElement.classList.add('hide');
            skipNavigationControls.classList.add('hide');
            clearProgress();
            completionMessageElement.innerText = "Selamat Kuis Sudah Selesai 🎉";
            completionMessageElement.style.color = "#28a745";
            completionMessageElement.classList.remove('hide');
            startButton.innerText = 'Ulangi Kuis';
            initialControls.classList.remove('hide');
            continueButton.classList.add('hide');
        }
    </script>
</body>
</html>
