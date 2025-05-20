<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kuis Pengetahuan Kosa-kata Inggris</title>
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

        .btn:not(.correct):not(.wrong):not(.skip-btn) { background-color: #007bff; }
        .btn:not(.correct):not(.wrong):not(.skip-btn):focus {
            background-color: #007bff;
            box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.5);
        }
        .btn:not([disabled]):not(.correct):not(.wrong):not(.skip-btn):hover {}
        .btn:not([disabled]):not(.correct):not(.wrong):not(.skip-btn):focus:hover {
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
        .btn:disabled:not(.correct):not(.wrong):not(.skip-btn) {
            background-color: #6c757d !important;
            color: #ccc !important;
        }

        .controls {
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        #skip-navigation-controls {
            justify-content: space-between;
            margin-top: 25px;
            margin-bottom: 10px;
        }

        .skip-btn {
            background-color: #28a745;
            color: white;
            padding: 8px 12px;
            font-size: 0.9em;
            min-width: 80px;
        }
        .skip-btn:hover {
            background-color: #218838;
            color: white;
        }
        .skip-btn:disabled {
            background-color: #a3d8b0 !important;
            color: #e9f5ec !important;
            cursor: not-allowed;
        }

        .hide { display: none !important; }
    </style>
</head>
<body>
    <div class="quiz-container">
        <h1>Pengetahuan Kosa-kata Inggris</h1>
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
                <button id="next-50-btn" class="btn skip-btn">50 &raquo;</button>
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
        const next50Button = document.getElementById('next-50-btn');
        const JUMP_AMOUNT = 50;

        let orderedQuestions, currentQuestionIndex;
        let score = 0;
        let questionTimeout;

        // Daftar kata mentah dari PDF (Inggris: Indonesia) - Total 1580 kata
        const rawVocabularyList = [
            // Kosakata 1-308 (dari PDF pertama)
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
            { en: 'compact', id: 'padat' }, { en: 'compel', id: 'memaksa' },
            { en: 'competent', id: 'kompeten' }, { en: 'compile', id: 'menyusun' },
            { en: 'complement', id: 'melengkapi' }, { en: 'compliment', id: 'pujian' },
            { en: 'comply', id: 'memenuhi' }, { en: 'component', id: 'komponen' },
            { en: 'comprehensible', id: 'terpahamkan' }, { en: 'comprehensive', id: 'komprehensif' },
            { en: 'comprise', id: 'meliputi' }, { en: 'compulsory', id: 'wajib' },
            { en: 'concede', id: 'mengakui' }, { en: 'concerted', id: 'terpadu' },
            { en: 'concise', id: 'singkat' }, { en: 'concoct', id: 'mereka-reka' },
            { en: 'concord', id: 'kerukunan' }, { en: 'concrete', id: 'beton' },
            { en: 'concurrent', id: 'yg berbarengan' }, { en: 'condemn', id: 'mengutuk' },
            { en: 'condone', id: 'memaafkan' }, { en: 'conducive', id: 'kondusif' },
            { en: 'confer', id: 'menganugerahkan' }, { en: 'confidential', id: 'rahasia' },
            { en: 'configuration', id: 'konfigurasi' }, { en: 'confiscate', id: 'menyita' },
            { en: 'conflict', id: 'konflik' }, { en: 'conform', id: 'sesuai' },
            { en: 'congenial', id: 'serasi' }, { en: 'congestion', id: 'kemacetan' },
            { en: 'conglomerate', id: 'konglomerat' }, { en: 'congregate', id: 'berkumpul' },
            { en: 'conjecture', id: 'dugaan' }, { en: 'consecutive', id: 'berturut-turut' },
            { en: 'consequential', id: 'konsekuensial' }, { en: 'consistent', id: 'konsisten' },
            { en: 'conspicuous', id: 'menyolok' }, { en: 'construe', id: 'menafsirkan' },
            { en: 'contaminate', id: 'mencemari' }, { en: 'contemplate', id: 'merenungkan' },
            { en: 'contempt', id: 'penghinaan' }, { en: 'contention', id: 'anggapan' },
            { en: 'contrive', id: 'merancang' }, { en: 'controversial', id: 'kontroversial' },
            { en: 'controversy', id: 'kontroversi' }, { en: 'convenient', id: 'mudah' },
            { en: 'convey', id: 'menyampaikan' }, { en: 'conviction', id: 'keyakinan' },
            { en: 'conviviality', id: 'keramahan' }, { en: 'copewith', id: 'copewith' },
            { en: 'copious', id: 'banyak sekali' }, { en: 'cordial', id: 'ramah' },
            { en: 'corporeal', id: 'jasmani' }, { en: 'corpulent', id: 'banyak' },
            { en: 'corroborate', id: 'menguatkan' }, { en: 'courteous', id: 'sopan' },
            { en: 'cov', id: 'tersembunyi' },
            { en: 'cozy', id: 'nyaman' },
            { en: 'crave', id: 'mendambakan' }, { en: 'crease', id: 'lipatan' },
            { en: 'criminology', id: 'kriminologi' }, { en: 'crooked', id: 'bengkok' },
            { en: 'crouch', id: 'mendekam' }, { en: 'crucial', id: 'sangat penting' },
            { en: 'crude', id: 'mentah' }, { en: 'cruel', id: 'kejam' },
            { en: 'cryptic', id: 'samar' }, { en: 'cultivate', id: 'mengolah' },
            { en: 'cumbersome', id: 'berat/merepotkan' },
            { en: 'cumulative', id: 'kumulatif' }, { en: 'curb', id: 'mengekang' },
            { en: 'curious', id: 'ingin tahu' }, { en: 'curt', id: 'singkat' },
            { en: 'curtail', id: 'membatasi' }, { en: 'damp', id: 'lembab' },
            { en: 'dangle', id: 'menjuntai' }, { en: 'daring', id: 'berani' },
            { en: 'daunt', id: 'mengecilkan hati' }, { en: 'dazzling', id: 'yg mempesonakan' },
            { en: 'decadent', id: 'dekaden' }, { en: 'decay', id: 'kerusakan' },
            { en: 'deceit', id: 'penipuan' }, { en: 'deception', id: 'penipuan' },
            { en: 'decipher', id: 'menguraikan' }, { en: 'declare', id: 'menyatakan' },
            { en: 'declivity', id: 'landaian' }, { en: 'decompose', id: 'membusuk' },
            { en: 'decorate', id: 'menghias' }, { en: 'decorous', id: 'sopan' },
            { en: 'decrement', id: 'pengurangan' }, { en: 'decriminalize', id: 'dekriminalisasi' },
            { en: 'dedicate', id: 'membaktikan' }, { en: 'deduction', id: 'deduksi' },
            { en: 'deem', id: 'menganggap' }, { en: 'defacto', id: 'defacto' },
            { en: 'defame', id: 'memfitnah' }, { en: 'defect', id: 'cacat' },
            { en: 'defective', id: 'cacat' }, { en: 'defer', id: 'menunda' },
            { en: 'defiant', id: 'menantang' }, { en: 'deficient', id: 'kurang' },
            { en: 'definitive', id: 'definitif' }, { en: 'defraud', id: 'menipu' },
            { en: 'degenerate', id: 'merosot' }, { en: 'dehydrate', id: 'mendehidrasi' },
            { en: 'deity', id: 'dewa' }, { en: 'dejected', id: 'sedih' },
            { en: 'deliberate', id: 'disengaja' }, { en: 'delicate', id: 'halus' },
            { en: 'delightful', id: 'menyenangkan' }, { en: 'delphic', id: 'Delphi' },
            { en: 'deluge', id: 'banjir' }, { en: 'delusion', id: 'khayalan' },
            { en: 'demise', id: 'kematian' }, { en: 'demolish', id: 'menghancurkan' },
            { en: 'denote', id: 'menunjukkan' }, { en: 'denounce', id: 'mencela' },
            { en: 'dense', id: 'padat' }, { en: 'deprave', id: 'merusak akhlak' },
            { en: 'depress', id: 'menekan' }, { en: 'deprive', id: 'menghilangkan' },
            { en: 'derelict', id: 'lalai' }, { en: 'deride', id: 'mengejek' },
            { en: 'descry', id: 'menampak' }, { en: 'desecrate', id: 'menodai' },
            { en: 'deserted', id: 'sepi' }, { en: 'desiccate', id: 'mengering' },
            { en: 'desist', id: 'berhenti' }, { en: 'desolate', id: 'sepi' },
            { en: 'desultory', id: 'acak-acakan' }, { en: 'detect', id: 'menemukan' },
            { en: 'deteriorate', id: 'memburuk' }, { en: 'detract', id: 'mengurangi' },
            { en: 'detrimental', id: 'merugikan' }, { en: 'deviate', id: 'menyimpang' },
            { en: 'device', id: 'alat' }, { en: 'devise', id: 'merancang' },
            { en: 'devoid', id: 'tanpa' }, { en: 'dexterous', id: 'tangkas' },
            { en: 'didactic', id: 'bersifat mendidik' }, { en: 'digress', id: 'ngelantur' },
            { en: 'diligence', id: 'ketekunan' }, { en: 'diligent', id: 'rajin' },
            { en: 'dilute', id: 'mencairkan' }, { en: 'dim', id: 'redup' },
            { en: 'dimension', id: 'dimensi' }, { en: 'dingy', id: 'kotor' },
            { en: 'Dionysian', id: 'Dionisia' }, { en: 'dip', id: 'mencelupkan' },
            { en: 'dire', id: 'mengerikan' }, { en: 'directory', id: 'direktori' },
            { en: 'discard', id: 'membuang' }, { en: 'discern', id: 'melihat' },
            { en: 'discipline', id: 'disiplin' }, { en: 'disclose', id: 'menyingkapkan' },
            { en: 'discord', id: 'perselisihan' }, { en: 'discrepancy', id: 'perbedaan' },
            { en: 'disdain', id: 'penghinaan' }, { en: 'dismal', id: 'suram' },
            { en: 'dismay', id: 'kecemasan' }, { en: 'dismissive', id: 'meremehkan' },
            { en: 'disparity', id: 'disparitas' }, { en: 'disperse', id: 'membubarkan' },
            { en: 'disposition', id: 'watak' },
            { en: 'disprove', id: 'membantah' }, { en: 'dispute', id: 'perselisihan' },
            { en: 'disrobe', id: 'menanggalkan pakaian' }, { en: 'dissect', id: 'membedah' },
            { en: 'disseminate', id: 'menyebarluaskan' }, { en: 'dissolve', id: 'membubarkan' },
            { en: 'distinct', id: 'berbeda' }, { en: 'distinguished', id: 'terhormat' },
            { en: 'distract', id: 'mengalihkan' }, { en: 'diverge', id: 'menyimpang' },
            { en: 'divulge', id: 'membocorkan' }, { en: 'docile', id: 'jinak' },
            { en: 'dodge', id: 'menghindari' }, { en: 'dogged', id: 'mantap' },
            { en: 'doleful', id: 'sedih' }, { en: 'dominate', id: 'mendominasi' },
            { en: 'donate', id: 'menyumbangkan' }, { en: 'dot', id: 'dot' },
            { en: 'downfall', id: 'kejatuhan' }, { en: 'doze', id: 'tertidur' },
            { en: 'drain', id: 'menguras' }, { en: 'drastic', id: 'drastis' },
            { en: 'drawback', id: 'kekurangan' }, { en: 'dreary', id: 'suram' },
            { en: 'drench', id: 'membuat basah kuyup' }, { en: 'drip', id: 'menitik' },
            { en: 'drought', id: 'kekeringan' }, { en: 'drowsy', id: 'mengantuk' },
            { en: 'dubious', id: 'ragu-ragu' }, { en: 'dull', id: 'membosankan' },
            { en: 'dumbfound', id: 'mengherankan' }, { en: 'dunce', id: 'orang bodoh' },
            { en: 'dungeon', id: 'penjara gelap bawah tanah' }, { en: 'durable', id: 'tahan lama' },
            { en: 'dwell', id: 'tinggal' }, { en: 'dwelling', id: 'tempat tinggal' },
            { en: 'dwindle', id: 'berkurang' }, { en: 'dynamic', id: 'dinamis' },
            { en: 'eclipse', id: 'gerhana' }, { en: 'ecology', id: 'ekologi' },
            { en: 'ecumenical', id: 'ekumenis' }, { en: 'edible', id: 'dimakan' },
            { en: 'edifice', id: 'gedung' }, { en: 'eerie', id: 'ngeri' },
            { en: 'efface', id: 'menghapus' },
            { en: 'effuse', id: 'yg mengeluarkan' }, { en: 'effusion', id: 'pengaliran cairan' },
            { en: 'elaborate', id: 'menguraikan' }, { en: 'elasticity', id: 'elastisitas' },
            { en: 'elderly', id: 'tua' }, { en: 'elegant', id: 'anggun' },
            { en: 'elevate', id: 'mengangkat' }, { en: 'elicit', id: 'memperoleh' },
            { en: 'eligible', id: 'memenuhi syarat' }, { en: 'elucidate', id: 'menjelaskan' },
            { en: 'elude', id: 'menghindari' }, { en: 'emanate', id: 'muncul' },
            { en: 'emancipate', id: 'membebaskan' }, { en: 'embed', id: 'menanamkan' },
            { en: 'embitter', id: 'menyakitkan hati' }, { en: 'emblem', id: 'lambang' },
            { en: 'emboss', id: 'menatah' }, { en: 'eminent', id: 'terkenal' },
            { en: 'emit', id: 'memancarkan' }, { en: 'emulate', id: 'meniru' },
            { en: 'enamored', id: 'terpikat' }, { en: 'enchant', id: 'mempesona' },
            { en: 'enchanting', id: 'mempesona' }, { en: 'encomium', id: 'pujian agung' },
            { en: 'encounter', id: 'menghadapi' }, { en: 'endeavor', id: 'berusaha keras' },
            { en: 'endorse', id: 'mengesahkan' }, { en: 'engender', id: 'menimbulkan' },
            { en: 'enhance', id: 'mempertinggi' }, { en: 'enigma', id: 'teka-teki' },
            { en: 'enlist', id: 'memperoleh' }, { en: 'enmity', id: 'permusuhan' },
            { en: 'ennui', id: 'perasaan bosan' }, { en: 'ensue', id: 'terjadi' },
            { en: 'enthrall', id: 'memikat' }, { en: 'entice', id: 'menarik' },
            { en: 'enumerate', id: 'menghitung' }, { en: 'enunciate', id: 'melafalkan' },
            { en: 'envelope', id: 'amplop' }, { en: 'environ', id: 'lingkungan' },
            { en: 'envisage', id: 'membayangkan' }, { en: 'ephemeral', id: 'tidak kekal' },
            { en: 'epiphyte', id: 'benalu' }, { en: 'epitaph', id: 'tulisan di batu nisan' },
            { en: 'epithet', id: 'julukan' }, { en: 'equitable', id: 'adil' },
            { en: 'equivocal', id: 'samar-samar' }, { en: 'era', id: 'era' },
            { en: 'eradicate', id: 'membasmi' }, { en: 'erect', id: 'tegak' },
            { en: 'escalate', id: 'meningkatkan' }, { en: 'eschew', id: 'menjauhkan diri' },
            { en: 'espouse', id: 'mendukung' }, { en: 'essay', id: 'karangan' },
            { en: 'essential', id: 'penting' }, { en: 'estate', id: 'perkebunan' },
            { en: 'esteem', id: 'penghargaan' }, { en: 'estrange', id: 'menjauhkan' },
            { en: 'eulogy', id: 'sanjungan' }, { en: 'evacuate', id: 'mengungsi' },
            { en: 'evade', id: 'menghindari' }, { en: 'evanescent', id: 'cepat berlalu dr ingatan' },
            { en: 'even', id: 'bahkan' }, { en: 'evenhanded', id: 'adil dan tidak memihak' },
            { en: 'evolve', id: 'berkembang' }, { en: 'exacerbate', id: 'memperuncing' },
            { en: 'exacting', id: 'rewel' }, { en: 'exalt', id: 'mengagungkan' },
            { en: 'excavate', id: 'menggali' }, { en: 'exceed', id: 'melebihi' },
            { en: 'excerpt', id: 'kutipan' }, { en: 'excrete', id: 'mengeluarkan' },
            { en: 'excursion', id: 'tamasya' }, { en: 'execute', id: 'melaksanakan' },
            { en: 'exhaustive', id: 'lengkap' }, { en: 'exhilarating', id: 'menggembirakan' },
            { en: 'exhortation', id: 'nasihat' }, { en: 'expendable', id: 'dapat dikorbankan' },
            { en: 'expire', id: 'berakhir' }, { en: 'explicit', id: 'eksplisit' },
            { en: 'exploit', id: 'mengeksploitasi' }, { en: 'explore', id: 'menjelajah' },
            { en: 'expose', id: 'menelanjangi' }, { en: 'expunge', id: 'menghapus' },
            { en: 'extensive', id: 'luas' }, { en: 'extol', id: 'mendewakan' },
            { en: 'extract', id: 'ekstrak' }, { en: 'extraneous', id: 'asing' },
            { en: 'extravagant', id: 'boros' }, { en: 'fable', id: 'fabel' },
            { en: 'fabled', id: 'dibuat-buat' }, { en: 'facet', id: 'segi' },
            { en: 'facile', id: 'lancar' }, { en: 'facilitate', id: 'memudahkan' },
            { en: 'facility', id: 'fasilitas' }, { en: 'fade', id: 'luntur' },
            { en: 'faint', id: 'lemah' }, { en: 'fake', id: 'gadungan' },
            { en: 'fallout', id: 'kejatuhan' }, { en: 'falter', id: 'bimbang' },
            { en: 'famine', id: 'kelaparan' }, { en: 'fancy', id: 'indah' },
            { en: 'fare', id: 'tarif' }, { en: 'fasten', id: 'mengancingkan' },
            { en: 'fatal', id: 'fatal' }, { en: 'fatigue', id: 'kelelahan' },
            { en: 'faulty', id: 'salah' }, { en: 'feasible', id: 'layak' },
            { en: 'feat', id: 'prestasi' }, { en: 'fee', id: 'biaya' },
            { en: 'feeble', id: 'lemah' }, { en: 'feign', id: 'berpura-pura' },
            { en: 'felicity', id: 'kebahagiaan besar' },
            { en: 'ferocious', id: 'ganas' }, { en: 'fertile', id: 'subur' },
            { en: 'fervent', id: 'sungguh-sungguh' }, { en: 'fickle', id: 'plin-plan' },
            { en: 'fidelity', id: 'kesetiaan' }, { en: 'fiery', id: 'berapi-api' },
            { en: 'finance', id: 'keuangan' }, { en: 'finite', id: 'terbatas' },
            { en: 'fire', id: 'api' }, { en: 'fitting', id: 'sesuai' },
            { en: 'flagrant', id: 'menyolok' }, { en: 'flatten', id: 'meratakan' },
            { en: 'flaw', id: 'cacat' }, { en: 'flee', id: 'melarikan diri' },
            { en: 'flexible', id: 'fleksibel' }, { en: 'flimsy', id: 'tipis' },
            { en: 'flip', id: 'membalik' }, { en: 'florid', id: 'kemerah-merahan' },
            { en: 'fluctuate', id: 'berubah-ubah' }, { en: 'foggy', id: 'berkabut' },
            { en: 'fold', id: 'melipat' }, { en: 'fool', id: 'menipu' },
            { en: 'forego', id: 'melepaskan' },
            { en: 'foremost', id: 'terutama' }, { en: 'forestall', id: 'mencegah' },
            { en: 'forlorn', id: 'sedih' }, { en: 'formative', id: 'formatif' },
            { en: 'foul', id: 'busuk' }, { en: 'fragile', id: 'rapuh' },
            { en: 'fragment', id: 'fragmen' }, { en: 'fragrant', id: 'harum' },
            { en: 'frail', id: 'lemah' }, { en: 'framework', id: 'kerangka' },
            { en: 'fraud', id: 'penipuan' }, { en: 'fraudulent', id: 'curang' },
            { en: 'frenetic', id: 'hiruk pikuk' },
            { en: 'frustrate', id: 'menggagalkan' }, { en: 'fugitive', id: 'buronan' },
            { en: 'fundamental', id: 'mendasar' }, { en: 'fuse', id: 'sekering' },
            { en: 'fusion', id: 'fusi' }, { en: 'futile', id: 'sia-sia' },
            { en: 'gain', id: 'mendapatkan' }, { en: 'gainsay', id: 'membantah' },
            { en: 'gala', id: 'gala' }, { en: 'gale', id: 'badai' },
            { en: 'gap', id: 'celah' }, { en: 'garrulous', id: 'cerewet' },
            { en: 'gaudy', id: 'terlalu menyolok' }, { en: 'genial', id: 'ramah' },
            { en: 'gentle', id: 'lemah lembut' }, { en: 'genuine', id: 'asli' },
            { en: 'germane', id: 'relevan' },
            { en: 'germinate', id: 'berkecambah' }, { en: 'ghastly', id: 'mengerikan' },
            { en: 'glisten', id: 'berkilau' }, { en: 'glitter', id: 'berkilau' },
            { en: 'glorify', id: 'memuliakan' }, { en: 'glory', id: 'kejayaan' },
            { en: 'glossy', id: 'berkilau' }, { en: 'goad', id: 'mendorong' },
            { en: 'gorgeous', id: 'cantik' }, { en: 'grab', id: 'mengambil' },
            { en: 'grace', id: 'rahmat' }, { en: 'graphic', id: 'grafis' },
            { en: 'grasp', id: 'memahami' }, { en: 'grassroots', id: 'rakyat' },
            { en: 'gratuitous', id: 'cuma-cuma' },
            { en: 'grave', id: 'kuburan' }, { en: 'gravid', id: 'hamil' },
            { en: 'gravitas', id: 'kewibawaan' },
            { en: 'gravitate', id: 'condong' }, { en: 'gravity', id: 'gaya berat' },
            { en: 'graze', id: 'menggembalakan' }, { en: 'gregarious', id: 'yg suka berteman' },
            { en: 'grim', id: 'suram' }, { en: 'grip', id: 'pegangan' },
            { en: 'gross', id: 'bruto' }, { en: 'grueling', id: 'sangat meletihkan' },
            { en: 'grumble', id: 'ngomel' }, { en: 'guile', id: 'tipu daya' },
            { en: 'gullible', id: 'mudah tertipu' }, { en: 'gusto', id: 'semangat' },
            { en: 'haggle', id: 'tawar-menawar' }, { en: 'hamper', id: 'menghambat' },
            { en: 'handpick', id: 'memilih dgn rapi' }, { en: 'handy', id: 'berguna' },
            { en: 'haphazard', id: 'serampangan' }, { en: 'harangue', id: 'pidato mendesak' },
            { en: 'harass', id: 'mengusik' }, { en: 'hardship', id: 'kesulitan' },
            { en: 'harm', id: 'membahayakan' }, { en: 'harmony', id: 'harmoni' },
            { en: 'harness', id: 'memanfaatkan' }, { en: 'harsh', id: 'keras' },
            { en: 'hasty', id: 'terburu-buru' }, { en: 'haul', id: 'mengangkut' },
            { en: 'havoc', id: 'malapetaka' }, { en: 'hazardous', id: 'berbahaya' },
            { en: 'hector', id: 'menggertak' }, { en: 'hedonism', id: 'hedonisme' },
            { en: 'hedonist', id: 'hedonis' }, { en: 'heed', id: 'mengindahkan' },
            { en: 'heighten', id: 'mempertinggi' }, { en: 'helical', id: 'spiral' },
            { en: 'herald', id: 'bentara' }, { en: 'heretic', id: 'orang murtad' },
            { en: 'heterogeneous', id: 'heterogen' }, { en: 'heyday', id: 'kejayaan' },
            { en: 'hilarious', id: 'ria' }, { en: 'hinder', id: 'menghalangi' },
            { en: 'hoax', id: 'lelucon' }, { en: 'hoist', id: 'kerekan' },
            { en: 'holocaust', id: 'bencana' }, { en: 'homogeneous', id: 'homogen' },
            { en: 'homonym', id: 'homonim' }, { en: 'hospice', id: 'rumah penginapan' },
            { en: 'hospitable', id: 'ramah' }, { en: 'hospitality', id: 'keramahan' },
            { en: 'hostage', id: 'sandera' }, { en: 'hostel', id: 'asrama' },
            { en: 'hostile', id: 'bermusuhan' }, { en: 'hub', id: 'pusat' },
            { en: 'hue', id: 'warna' }, { en: 'huge', id: 'besar' },
            { en: 'humiliate', id: 'menghina' }, { en: 'hurl', id: 'melemparkan' },
            { en: 'hyperbole', id: 'penghebatan' }, { en: 'hypocrite', id: 'munafik' },
            { en: 'idea', id: 'ide' }, { en: 'ideal', id: 'ideal' },
            { en: 'idiosyncrasy', id: 'keistimewaan' }, { en: 'idle', id: 'menganggur' },
            { en: 'illiterate', id: 'buta huruf' }, { en: 'illusion', id: 'ilusi' },
            { en: 'illustrate', id: 'menjelaskan' }, { en: 'imaginary', id: 'imajiner' },
            { en: 'imbibe', id: 'menyerap' }, { en: 'immense', id: 'besar sekali' },
            { en: 'imminent', id: 'dekat' }, { en: 'immortal', id: 'abadi' },
            { en: 'immune', id: 'kebal' }, { en: 'immutable', id: 'tidak berubah' },
            { en: 'impair', id: 'merusak' }, { en: 'impartial', id: 'berimbang' },
            { en: 'impecunious', id: 'miskin' }, { en: 'impediment', id: 'halangan' },
            { en: 'impel', id: 'mendorong' },
            { en: 'impending', id: 'mendatang' }, { en: 'imperceptible', id: 'tak kelihatan' },
            { en: 'implement', id: 'melaksanakan' }, { en: 'impose', id: 'memaksakan' },
            { en: 'impromptu', id: 'mendadak' }, { en: 'improvise', id: 'berimprovisasi' },
            { en: 'impute', id: 'menyalahkan' }, { en: 'incalculable', id: 'tak terhitung' },
            { en: 'incandescent', id: 'pijar' }, { en: 'incense', id: 'dupa' },
            { en: 'incentive', id: 'insentif' }, { en: 'incessant', id: 'tak henti-hentinya' },
            { en: 'incipient', id: 'yg baru jadi' }, { en: 'incise', id: 'menoreh' },
            { en: 'incognito', id: 'yg menyamar' }, { en: 'incompatible', id: 'tidak kompatibel' },
            { en: 'incongruous', id: 'tdk sesuai' }, { en: 'inconsonant', id: 'tidak selaras' },
            { en: 'increment', id: 'kenaikan' }, { en: 'incriminate', id: 'memberatkan' },
            { en: 'incumbent', id: 'berkewajiban' }, { en: 'indemnify', id: 'mengganti rugi' },
            { en: 'indict', id: 'mendakwa' }, { en: 'indifferent', id: 'acuh tak acuh' },
            { en: 'indigenous', id: 'pribumi' }, { en: 'indigent', id: 'fakir' },
            { en: 'indignant', id: 'marah' }, { en: 'indiscriminate', id: 'sembarangan' },
            { en: 'indispensable', id: 'sangat diperlukan' }, { en: 'indistinct', id: 'kabur' },
            { en: 'induce', id: 'menyebabkan' }, { en: 'inept', id: 'janggal' },
            { en: 'inevitable', id: 'tak terelakkan' }, { en: 'infamous', id: 'hina' },
            { en: 'infinite', id: 'tak terbatas' }, { en: 'infinitesimal', id: 'kecil sekali' },
            { en: 'infringe', id: 'melanggar' }, { en: 'infuriate', id: 'membuat sangat marah' },
            { en: 'ingenious', id: 'terampil' }, { en: 'ingenuous', id: 'terus terang' },
            { en: 'ingredient', id: 'bahan' }, { en: 'inhabit', id: 'mendiami' },
            { en: 'inherent', id: 'inheren' }, { en: 'inhibit', id: 'menghalangi' },
            { en: 'inhospitable', id: 'tidak ramah' }, { en: 'inimical', id: 'bermusuhan' },
            { en: 'initial', id: 'awal' }, { en: 'injurious', id: 'berbahaya' },
            { en: 'innate', id: 'asli' }, { en: 'innocuous', id: 'tdk berbahaya' },
            { en: 'innovative', id: 'inovatif' }, { en: 'inopportune', id: 'tidak tepat waktu' },
            { en: 'inordinate', id: 'banyak sekali' }, { en: 'inroad', id: 'penjebolan' },
            { en: 'insatiate', id: 'yg tak pernah puas' }, { en: 'insinuate', id: 'menyusup' },
            { en: 'insipid', id: 'hambar' }, { en: 'insolent', id: 'kurang ajar' },
            { en: 'insolvent', id: 'bangkrut' }, { en: 'inspire', id: 'mengilhami' },
            { en: 'instantaneous', id: 'segera' }, { en: 'instigate', id: 'menghasut' },
            { en: 'insult', id: 'menghina' }, { en: 'insurrection', id: 'pemberontakan' },
            { en: 'intact', id: 'utuh' }, { en: 'integral', id: 'integral' },
            { en: 'intelligible', id: 'jelas' }, { en: 'intense', id: 'intens' },
            { en: 'interim', id: 'sementara' }, { en: 'interminable', id: 'tak berkesudahan' },
            { en: 'intervene', id: 'campur tangan' }, { en: 'intimate', id: 'intim' },
            { en: 'intimidate', id: 'mengancam' }, { en: 'intractable', id: 'degil' },
            { en: 'intrepid', id: 'pemberani' }, { en: 'intricate', id: 'rumit' },
            { en: 'intrigue', id: 'intrik' }, { en: 'intrude', id: 'mengganggu' },
            { en: 'invaluable', id: 'tak ternilai' }, { en: 'inventory', id: 'inventarisasi' },
            { en: 'inveterate', id: 'sudah berurat akar' },
            { en: 'invincible', id: 'tak terkalahkan' }, { en: 'inviting', id: 'mengundang' },
            { en: 'involuntary', id: 'tidak disengaja' },
            { en: 'irate', id: 'marah' }, { en: 'irreversible', id: 'yg tak dpt diubah' },
            { en: 'irrevocable', id: 'yg tdk ditarik kembali' }, { en: 'irritable', id: 'pemarah' },
            { en: 'itinerant', id: 'berkeliling' }, { en: 'jagged', id: 'bergerigi' },
            { en: 'jeopardy', id: 'bahaya' }, { en: 'jolly', id: 'riang' },
            { en: 'jolt', id: 'sentakan' }, { en: 'jovial', id: 'periang' },
            { en: 'judicious', id: 'bijaksana' }, { en: 'juncture', id: 'titik waktu' },
            { en: 'juvenile', id: 'remaja' }, { en: 'keen', id: 'tajam' },
            { en: 'key', id: 'kunci' }, { en: 'knack', id: 'ketangkasan' },
            { en: 'lack', id: 'kekurangan' }, { en: 'lag', id: 'ketinggalan' },
            { en: 'laggard', id: 'yg ketinggalan' }, { en: 'landscape', id: 'pemandangan' },
            { en: 'landslide', id: 'tanah longsor' }, { en: 'languid', id: 'lesu' },
            { en: 'lapse', id: 'selang' }, { en: 'lassitude', id: 'kelesuan' },
            { en: 'last', id: 'terakhir' }, { en: 'latent', id: 'tersembunyi' },
            { en: 'laud', id: 'menyanjung' }, { en: 'lavish', id: 'mewah' },
            { en: 'lax', id: 'longgar' }, { en: 'leaflet', id: 'selebaran' },
            { en: 'leash', id: 'rantai anjing' }, { en: 'leavening', id: 'ragi' },
            { en: 'leeward', id: 'di bawah angin' }, { en: 'legendary', id: 'legendaris' },
            { en: 'legitimate', id: 'sah' }, { en: 'lenient', id: 'lunak' },
            { en: 'lessen', id: 'mengurangi' }, { en: 'lethal', id: 'mematikan' },
            { en: 'lethargic', id: 'lesu' }, { en: 'levity', id: 'kesembronoan' },
            { en: 'lewd', id: 'cabul' }, { en: 'liable', id: 'bertanggung jawab' },
            { en: 'liaison', id: 'hubungan' }, { en: 'licentious', id: 'cabul' },
            { en: 'likely', id: 'mungkin' }, { en: 'linger', id: 'berkepanjangan' },
            { en: 'link', id: 'tautan' }, { en: 'litigate', id: 'mengajukan perkara' },
            { en: 'loafer', id: 'pemalas' },
            { en: 'loathsome', id: 'menjijikkan' }, { en: 'locate', id: 'menemukan' },
            { en: 'locomotion', id: 'daya penggerak' }, { en: 'lodestar', id: 'pedoman' },
            { en: 'lofty', id: 'luhur' }, { en: 'long', id: 'panjang' },
            { en: 'loom', id: 'membayang' },
            { en: 'loophole', id: 'jalan keluar' }, { en: 'lucid', id: 'jelas' },
            { en: 'lucrative', id: 'menguntungkan' }, { en: 'lugubrious', id: 'murung' },
            { en: 'lukewarm', id: 'suam-suam kuku' },
            { en: 'lull', id: 'menidurkan' }, { en: 'lullaby', id: 'nina-bobok' },
            { en: 'lumber', id: 'kayu' }, { en: 'luminous', id: 'bercahaya' },
            { en: 'lunatic', id: 'gila' }, { en: 'lure', id: 'memikat' },
            { en: 'lurid', id: 'seram' }, { en: 'lurk', id: 'mengintai' },
            { en: 'luster', id: 'kilau' }, { en: 'lustrous', id: 'berkilau' },
            { en: 'luxurious', id: 'mewah' }, { en: 'magnificent', id: 'indah' },
            { en: 'magnify', id: 'memperbesar' }, { en: 'magnitude', id: 'besarnya' },
            { en: 'maiden', id: 'gadis' }, { en: 'makeshift', id: 'sementara' },
            { en: 'makeup', id: 'rias' }, { en: 'malady', id: 'penyakit' },
            { en: 'malediction', id: 'laknat' }, { en: 'malice', id: 'kedengkian' },
            { en: 'malign', id: 'memfitnah' }, { en: 'malignant', id: 'ganas' },
            { en: 'mandatory', id: 'wajib' }, { en: 'manifest', id: 'nyata' },
            { en: 'mar', id: 'mengotori' }, { en: 'maritime', id: 'maritim' },
            { en: 'marked', id: 'jelas' }, { en: 'massacre', id: 'pembunuhan masal' },
            { en: 'masterpiece', id: 'karya besar' }, { en: 'matriculation', id: 'pendaftaran' },
            { en: 'mature', id: 'dewasa' }, { en: 'maxim', id: 'pepatah' },
            { en: 'meager', id: 'kurus' }, { en: 'meddle', id: 'mencampuri' },
            { en: 'mediate', id: 'menengahi' }, { en: 'meek', id: 'penurut' },
            { en: 'memorable', id: 'mudah diingat' },
            { en: 'menace', id: 'ancaman' }, { en: 'mend', id: 'memperbaiki' },
            { en: 'mercurial', id: 'lincah' }, { en: 'merge', id: 'menggabungkan' },
            { en: 'meritorious', id: 'berfaedah' }, { en: 'mesmerize', id: 'memikat' },
            { en: 'meteor', id: 'meteor' }, { en: 'meticulous', id: 'jelimet' },
            { en: 'mild', id: 'ringan' }, { en: 'millennia', id: 'ribuan' },
            { en: 'mingle', id: 'bergaul' }, { en: 'minuscule', id: 'amat kecil' },
            { en: 'minute', id: 'sangat kecil' },
            { en: 'mirth', id: 'kegembiraan' }, { en: 'misanthropic', id: 'pembenci orang' },
            { en: 'misappropriate', id: 'menyalahgunakan' }, { en: 'misdemeanor', id: 'perbuatan kurang baik' },
            { en: 'misrepresent', id: 'memberi gambaran keliru' },
            { en: 'mock', id: 'mengejek' }, { en: 'moderate', id: 'moderat' },
            { en: 'molest', id: 'menganiaya' }, { en: 'monitor', id: 'mengamati' },
            { en: 'monotonous', id: 'membosankan' }, { en: 'monumental', id: 'monumental' },
            { en: 'moral', id: 'moral' }, { en: 'morale', id: 'semangat juang' },
            { en: 'morbid', id: 'mengerikan' }, { en: 'mortify', id: 'menyinggung perasaan' },
            { en: 'mournful', id: 'sedih' }, { en: 'mundane', id: 'duniawi' },
            { en: 'murky', id: 'suram' }, { en: 'mute', id: 'bisu' },
            { en: 'mutiny', id: 'pemberontakan' }, { en: 'mysterious', id: 'gaib' },
            { en: 'mythical', id: 'mitos' }, { en: 'naughty', id: 'nakal' },
            { en: 'nausea', id: 'mual' }, { en: 'neglect', id: 'pengabaian' },
            { en: 'negligible', id: 'sepele' }, { en: 'negotiate', id: 'merundingkan' },
            { en: 'nestor', id: 'orang bijak tua' },
            { en: 'nibble', id: 'menggigit' }, { en: 'nimble', id: 'gesit' },
            { en: 'nocturnal', id: 'nokturnal' }, { en: 'nonchalant', id: 'acuh tak acuh' },
            { en: 'notable', id: 'penting' }, { en: 'notify', id: 'memberitahukan' },
            { en: 'notion', id: 'gagasan' }, { en: 'notorious', id: 'terkenal jahat' },
            { en: 'novel', id: 'baru/unik' },
            { en: 'novice', id: 'orang baru' }, { en: 'noxious', id: 'berbahaya' },
            { en: 'obese', id: 'gendut' }, { en: 'obituary', id: 'berita kematian' },
            { en: 'objective', id: 'tujuan' }, { en: 'oblique', id: 'miring' },
            { en: 'oblivious', id: 'lupa' }, { en: 'oblong', id: 'bujur' },
            { en: 'obscene', id: 'saru' }, { en: 'obscure', id: 'mengaburkan' },
            { en: 'obsequious', id: 'menjilat' },
            { en: 'obsolete', id: 'usang' }, { en: 'obstinate', id: 'keras kepala' },
            { en: 'obtrude', id: 'memaksakan kehendak' }, { en: 'obvious', id: 'jelas' },
            { en: 'odd', id: 'aneh' }, { en: 'odor', id: 'bau' },
            { en: 'offhand', id: 'begitu saja' }, { en: 'offspring', id: 'keturunan' },
            { en: 'olympian', id: 'agung' },
            { en: 'omen', id: 'pertanda' }, { en: 'ominous', id: 'yg beralamat buruk' },
            { en: 'ooze', id: 'mengeluarkan' }, { en: 'opportune', id: 'tepat' },
            { en: 'opulent', id: 'mewah' }, { en: 'orbit', id: 'orbit' },
            { en: 'ordeal', id: 'siksaan' }, { en: 'ornamental', id: 'hias' },
            { en: 'orotund', id: 'nyaring' }, { en: 'ostracize', id: 'mengasingkan dr masyarakat' },
            { en: 'outgoing', id: 'ramah' }, { en: 'outlet', id: 'jalan keluar' },
            { en: 'outlook', id: 'pandangan' }, { en: 'outrage', id: 'kebiadaban' },
            { en: 'outskirt', id: 'pinggiran kota' }, { en: 'outspread', id: 'terhampar' },
            { en: 'outstanding', id: 'terkemuka' }, { en: 'outworn', id: 'usang' },
            { en: 'overall', id: 'secara keseluruhan' }, { en: 'overcast', id: 'mendung' },
            { en: 'overcome', id: 'mengatasi' }, { en: 'overdue', id: 'terlambat' },
            { en: 'overlook', id: 'mengabaikan' }, { en: 'override', id: 'mengesampingkan' },
            { en: 'overrun', id: 'menyerbu' }, { en: 'oversee', id: 'mengawasi' },
            { en: 'oversight', id: 'kekhilafan' }, { en: 'overstate', id: 'melebih-lebihkan' },
            { en: 'overt', id: 'terang-terangan' }, { en: 'overtake', id: 'menyusul' },
            { en: 'overwhelm', id: 'membanjiri' }, { en: 'pace', id: 'kecepatan' },
            { en: 'pacesetter', id: 'pembuka jalan' }, { en: 'pacifist', id: 'pasifis' },
            { en: 'pacify', id: 'menenangkan' }, { en: 'pact', id: 'pakta' },
            { en: 'painstaking', id: 'telaten' }, { en: 'pale', id: 'pucat' },
            { en: 'paltry', id: 'remeh' }, { en: 'panacea', id: 'obat mujarab' },
            { en: 'panic', id: 'panik' }, { en: 'paramour', id: 'kekasih' },
            { en: 'particle', id: 'partikel' }, { en: 'path', id: 'jalur' },
            { en: 'patronage', id: 'perlindungan' }, { en: 'peculiar', id: 'aneh' },
            { en: 'pedantic', id: 'sok pintar' },
            { en: 'penetrate', id: 'menembus' }, { en: 'penurious', id: 'kikir' },
            { en: 'percapita', id: 'perkapita' }, { en: 'perceive', id: 'melihat' },
            { en: 'perceptible', id: 'jelas' }, { en: 'perch', id: 'bertengger' },
            { en: 'perennial', id: 'abadi' }, { en: 'perforate', id: 'melubangi' },
            { en: 'peril', id: 'bahaya' }, { en: 'perilous', id: 'berbahaya' },
            { en: 'periodic', id: 'berkala' }, { en: 'permeate', id: 'menyerap' },
            { en: 'pernicious', id: 'jahat' }, { en: 'perpetual', id: 'abadi' },
            { en: 'perplexing', id: 'membingungkan' }, { en: 'persecute', id: 'menganiaya' },
            { en: 'perspicuous', id: 'yg mudah dipahami' }, { en: 'pertain', id: 'berhubungan' },
            { en: 'pertinent', id: 'bersangkutan' }, { en: 'pervade', id: 'meliputi' },
            { en: 'petulant', id: 'pemarah' }, { en: 'phenomenal', id: 'luar biasa' },
            { en: 'pierce', id: 'menembus' }, { en: 'pillage', id: 'penjarahan' },
            { en: 'placate', id: 'menenangkan' }, { en: 'placid', id: 'tenang' },
            { en: 'plague', id: 'wabah' }, { en: 'plausible', id: 'masuk akal' },
            { en: 'plead', id: 'mengaku' }, { en: 'plethora', id: 'kebanyakan' },
            { en: 'plush', id: 'mewah' }, { en: 'poise', id: 'sikap tenang' },
            { en: 'poky', id: 'sempit' }, { en: 'ponder', id: 'merenungkan' },
            { en: 'portion', id: 'porsi' }, { en: 'portray', id: 'menggambarkan' },
            { en: 'posthumous', id: 'anumerta' }, { en: 'postpone', id: 'menunda' },
            { en: 'potent', id: 'ampuh' }, { en: 'pounce', id: 'menerkam' },
            { en: 'powerful', id: 'kuasa' }, { en: 'preach', id: 'berkhotbah' },
            { en: 'precarious', id: 'genting' }, { en: 'precept', id: 'aturan' },
            { en: 'precious', id: 'berharga' }, { en: 'precipitation', id: 'pengendapan' },
            { en: 'precise', id: 'tepat' }, { en: 'predicament', id: 'keadaan sulit' },
            { en: 'preface', id: 'kata pengantar' }, { en: 'premier', id: 'perdana menteri' },
            { en: 'prerogative', id: 'hak istimewa' }, { en: 'presage', id: 'pertanda' },
            { en: 'prescribe', id: 'menentukan' }, { en: 'pressing', id: 'mendesak' },
            { en: 'presumptuous', id: 'lancang' }, { en: 'pretense', id: 'kepura-puraan' },
            { en: 'pretext', id: 'dalih' }, { en: 'prevail', id: 'menang' },
            { en: 'prevalent', id: 'lazim' }, { en: 'prior', id: 'sebelumnya' },
            { en: 'probe', id: 'penyelidikan' }, { en: 'probity', id: 'kejujuran' },
            { en: 'proclaim', id: 'menyatakan' }, { en: 'procure', id: 'mendapatkan' },
            { en: 'prodigal', id: 'pemboros' }, { en: 'prodigious', id: 'luar biasa' },
            { en: 'profane', id: 'mencemarkan' }, { en: 'profound', id: 'mendalam' },
            { en: 'profuse', id: 'berlimpah' },
            { en: 'proliferate', id: 'berkembang biak' }, { en: 'prolific', id: 'produktif' },
            { en: 'prolong', id: 'memperpanjang' }, { en: 'prominent', id: 'menonjol' },
            { en: 'promiscuous', id: 'serampangan (seksual)' },
            { en: 'prompt', id: 'mendorong' }, { en: 'pronounced', id: 'jelas' },
            { en: 'prophesy', id: 'bernubuat' }, { en: 'proportional', id: 'sebanding' },
            { en: 'propriety', id: 'kesopanan' }, { en: 'proscribe', id: 'mengharamkan' },
            { en: 'prosper', id: 'menjadi makmur' }, { en: 'protagonist', id: 'tokoh utama' },
            { en: 'protracted', id: 'larut' }, { en: 'provenance', id: 'asal' },
            { en: 'provisional', id: 'sementara' }, { en: 'provoke', id: 'memprovokasi' },
            { en: 'prow', id: 'haluan kapal' }, { en: 'prudent', id: 'bijaksana' },
            { en: 'pseudonym', id: 'nama samaran' }, { en: 'pugnacious', id: 'suka berkelahi' },
            { en: 'pulverize', id: 'menghancurleburkan' }, { en: 'pungent', id: 'tajam' },
            { en: 'pursue', id: 'mengejar' }, { en: 'puzzling', id: 'membingungkan' },
            { en: 'quaint', id: 'aneh tapi menarik' },
            { en: 'quake', id: 'gempa' }, { en: 'quandary', id: 'dilema' },
            { en: 'quarrel', id: 'bertengkar' }, { en: 'quash', id: 'membatalkan' },
            { en: 'quench', id: 'memuaskan' }, { en: 'query', id: 'pertanyaan' },
            { en: 'quest', id: 'pencarian' }, { en: 'quicksilver', id: 'air raksa' },
            { en: 'quote', id: 'mengutip' }, { en: 'radiant', id: 'berseri-seri' },
            { en: 'radical', id: 'radikal' }, { en: 'ragged', id: 'compang-camping' },
            { en: 'raid', id: 'serangan' }, { en: 'random', id: 'acak' },
            { en: 'range', id: 'jarak' }, { en: 'rash', id: 'gegabah' },
            { en: 'ration', id: 'jatah' }, { en: 'raw', id: 'mentah' },
            { en: 'raze', id: 'meruntuhkan' }, { en: 'rebellion', id: 'pemberontakan' },
            { en: 'recede', id: 'surut' }, { en: 'reception', id: 'penerimaan' },
            { en: 'recess', id: 'reses' }, { en: 'reciprocal', id: 'timbal-balik' },
            { en: 'reckless', id: 'sembrono' }, { en: 'reclaim', id: 'memperoleh kembali' },
            { en: 'recollect', id: 'mengingat kembali' }, { en: 'reconcile', id: 'mendamaikan' },
            { en: 'recount', id: 'menceritakan' }, { en: 'recrimination', id: 'tuduh-menuduh' },
            { en: 'recruit', id: 'rekrut' }, { en: 'recurring', id: 'berulang' },
            { en: 'redeem', id: 'menebus' }, { en: 'redoubtable', id: 'dahsyat' },
            { en: 'refine', id: 'memperhalus' }, { en: 'refuge', id: 'pengungsian' },
            { en: 'refurbished', id: 'diperbaharui' }, { en: 'refute', id: 'menyanggah' },
            { en: 'rehabilitate', id: 'memperbaiki' }, { en: 'rehearse', id: 'berlatih' },
            { en: 'reign', id: 'memerintah' }, { en: 'reimburse', id: 'membayar kembali' },
            { en: 'reiterate', id: 'mengulangi' }, { en: 'rejoice', id: 'bersuka cita' },
            { en: 'rejoicing', id: 'kegembiraan' }, { en: 'release', id: 'melepaskan' },
            { en: 'relegate', id: 'membuang' }, { en: 'reliable', id: 'handal' },
            { en: 'relinquish', id: 'melepaskan' }, { en: 'relish', id: 'menikmati' },
            { en: 'remedy', id: 'obat' }, { en: 'reminisce', id: 'mengenangkan' },
            { en: 'remit', id: 'mengampuni' }, { en: 'remnant', id: 'sisa' },
            { en: 'remote', id: 'terpencil' }, { en: 'remunerate', id: 'menggaji' },
            { en: 'render', id: 'memberikan' }, { en: 'renounce', id: 'meninggalkan' },
            { en: 'renowned', id: 'terkenal' }, { en: 'repeal', id: 'mencabut' },
            { en: 'repel', id: 'mengusir' }, { en: 'repercussion', id: 'dampak' },
            { en: 'replenish', id: 'mengisi kembali' }, { en: 'reproach', id: 'mencela' },
            { en: 'reprobate', id: 'terkutuk' }, { en: 'reproduce', id: 'meniru' },
            { en: 'reprove', id: 'menegur' }, { en: 'repugnance', id: 'kebencian' },
            { en: 'repute', id: 'reputasi' }, { en: 'rescind', id: 'membatalkan' },
            { en: 'resent', id: 'marah' }, { en: 'reserved', id: 'pendiam' },
            { en: 'residual', id: 'sisa' }, { en: 'resolute', id: 'tegas' },
            { en: 'resound', id: 'menggaung' }, { en: 'restless', id: 'gelisah' },
            { en: 'resume', id: 'mulai lagi' }, { en: 'resurrection', id: 'kebangkitan' },
            { en: 'retaliate', id: 'membalas' }, { en: 'reticent', id: 'segan' },
            { en: 'retract', id: 'menarik' }, { en: 'retraction', id: 'pencabutan' },
            { en: 'retreat', id: 'mundur' }, { en: 'retrieve', id: 'mendapatkan kembali' },
            { en: 'retroact', id: 'berjalan surut' }, { en: 'revamp', id: 'merubah' },
            { en: 'reveal', id: 'mengungkapkan' }, { en: 'reverberate', id: 'bergema' },
            { en: 'revere', id: 'menghormati' }, { en: 'revoke', id: 'mencabut' },
            { en: 'riddle', id: 'teka-teki' }, { en: 'rigid', id: 'kaku' },
            { en: 'rigor', id: 'kekakuan' }, { en: 'rip', id: 'merobek' },
            { en: 'ripe', id: 'matang' }, { en: 'risky', id: 'berisiko' },
            { en: 'roam', id: 'berkeliaran' }, { en: 'rotundity', id: 'kegendutan' },
            { en: 'rough', id: 'kasar' }, { en: 'route', id: 'rute' },
            { en: 'rudiment', id: 'dasar-dasar' },
            { en: 'rudimentary', id: 'belum sempurna' }, { en: 'rugged', id: 'kasar' },
            { en: 'rummage', id: 'menggeledah' }, { en: 'rumor', id: 'desas-desus' },
            { en: 'rural', id: 'pedesaan' }, { en: 'ruthless', id: 'kejam' },
            { en: 'salvage', id: 'menyelamatkan' }, { en: 'sanction', id: 'izin atau hukuman' },
            { en: 'sanctuary', id: 'suaka' }, { en: 'sanguine', id: 'optimis' },
            { en: 'saturate', id: 'jenuh' }, { en: 'saucy', id: 'kurang ajar' },
            { en: 'scale', id: 'skala' }, { en: 'scan', id: 'mengamati' },
            { en: 'scant', id: 'sedikit' }, { en: 'scarce', id: 'langka' },
            { en: 'scatter', id: 'menyebarkan' }, { en: 'scattered', id: 'tersebar' },
            { en: 'scent', id: 'bau' }, { en: 'scholarly', id: 'ilmiah' },
            { en: 'scope', id: 'cakupan' }, { en: 'scorch', id: 'menghanguskan' },
            { en: 'scorn', id: 'cemooh' }, { en: 'scrap', id: 'sisa' },
            { en: 'scribble', id: 'tulisan cakar ayam' }, { en: 'scrupulous', id: 'teliti' },
            { en: 'scrutinize', id: 'menyimak' }, { en: 'seasoned', id: 'berpengalaman' },
            { en: 'secluded', id: 'terpencil' }, { en: 'secondhand', id: 'bekas' },
            { en: 'sedition', id: 'hasutan' }, { en: 'seduction', id: 'penggodaan' },
            { en: 'seemly', id: 'pantas' }, { en: 'self-reliant', id: 'mandiri' },
            { en: 'sensational', id: 'sensasional' }, { en: 'sentinel', id: 'penjaga' },
            { en: 'sequential', id: 'berurutan' }, { en: 'sequester', id: 'menyita' },
            { en: 'serene', id: 'tenang' }, { en: 'serpent', id: 'ular' },
            { en: 'sever', id: 'memutuskan' }, { en: 'severe', id: 'parah' },
            { en: 'shabby', id: 'lusuh' }, { en: 'shatter', id: 'menghancurkan' },
            { en: 'sheer', id: 'belaka' }, { en: 'shimmer', id: 'berkilau' },
            { en: 'shiver', id: 'menggigil' }, { en: 'shortcoming', id: 'kekurangan' },
            { en: 'shred', id: 'mengiris' }, { en: 'shrewd', id: 'lihai' },
            { en: 'shrill', id: 'melengking' }, { en: 'shrink', id: 'menyusut' },
            { en: 'shun', id: 'menghindari' }, { en: 'shunned', id: 'dijauhi' },
            { en: 'shy', id: 'malu' }, { en: 'significant', id: 'penting' },
            { en: 'signify', id: 'menandakan' }, { en: 'simulate', id: 'menirukan' },
            { en: 'simultaneous', id: 'serentak' }, { en: 'singular', id: 'tunggal' },
            { en: 'sip', id: 'menyesap' }, { en: 'skeptical', id: 'skeptis' },
            { en: 'sketch', id: 'sketsa' }, { en: 'sketchy', id: 'kurang lengkap' },
            { en: 'skew', id: 'condong' }, { en: 'skid', id: 'selip' },
            { en: 'skinny', id: 'kurus' }, { en: 'slack', id: 'kendur' },
            { en: 'slander', id: 'fitnah' }, { en: 'slender', id: 'ramping' },
            { en: 'slim', id: 'langsing' }, { en: 'sluggish', id: 'lamban' },
            { en: 'sly', id: 'licik' }, { en: 'smolder', id: 'membara' },
            { en: 'snare', id: 'jerat' }, { en: 'snub', id: 'penghinaan' },
            { en: 'soak', id: 'merendam' }, { en: 'sojourn', id: 'persinggahan' },
            { en: 'solace', id: 'penghiburan' }, { en: 'solicit', id: 'mengumpulkan' },
            { en: 'somber', id: 'muram' }, { en: 'sophisticated', id: 'mutakhir' },
            { en: 'sophistry', id: 'cara berpikir yg menyesatkan' }, { en: 'sort', id: 'jenis' },
            { en: 'sound', id: 'suara' }, { en: 'sovereign', id: 'berdaulat' },
            { en: 'span', id: 'merentang' }, { en: 'spartan', id: 'spartan' },
            { en: 'spawn', id: 'menelurkan' }, { en: 'specific', id: 'spesifik' },
            { en: 'specimen', id: 'contoh' }, { en: 'spectacular', id: 'spektakuler' },
            { en: 'spell', id: 'mengeja' }, { en: 'spin', id: 'berputar' },
            { en: 'spirited', id: 'berjiwa' }, { en: 'splash', id: 'guyuran' },
            { en: 'splendid', id: 'hebat' }, { en: 'splice', id: 'sambatan' },
            { en: 'spoil', id: 'merusak' }, { en: 'sporadic', id: 'sporadis' },
            { en: 'spot', id: 'tempat' }, { en: 'spouse', id: 'pasangan' },
            { en: 'sprout', id: 'tunas' }, { en: 'spur', id: 'memacu' },
            { en: 'spurious', id: 'palsu' }, { en: 'squamous', id: 'skuamosa' },
            { en: 'squander', id: 'memboroskan' }, { en: 'stable', id: 'stabil' },
            { en: 'stagnant', id: 'tergenang' }, { en: 'stain', id: 'noda' },
            { en: 'stale', id: 'basi' }, { en: 'stall', id: 'kios' },
            { en: 'stately', id: 'megah' }, { en: 'stationary', id: 'diam' },
            { en: 'statusquo', id: 'statusquo' }, { en: 'staunch', id: 'setia' },
            { en: 'stealthy', id: 'diam-diam' }, { en: 'steep', id: 'curam' },
            { en: 'stentorian', id: 'nyaring' }, { en: 'stern', id: 'tegas' },
            { en: 'stifle', id: 'menahan' },
            { en: 'stifling', id: 'gerah' }, { en: 'still', id: 'masih' },
            { en: 'stoic', id: 'sangat tabah' }, { en: 'stout', id: 'gemuk/kuat' },
            { en: 'strenuous', id: 'berat' }, { en: 'stretch', id: 'meregang' },
            { en: 'strife', id: 'perselisihan' }, { en: 'strive', id: 'berjuang' },
            { en: 'struggle', id: 'berjuang' }, { en: 'stubborn', id: 'keras kepala' },
            { en: 'stumble', id: 'tersandung' }, { en: 'stun', id: 'membuat pingsan/terkejut' },
            { en: 'stupendous', id: 'menakjubkan' }, { en: 'sturdy', id: 'kuat' },
            { en: 'sublime', id: 'agung' },
            { en: 'submerge', id: 'merendamkan' }, { en: 'subsequent', id: 'berikut' },
            { en: 'subside', id: 'reda' }, { en: 'subsidiary', id: 'anak perusahaan' },
            { en: 'substantial', id: 'besar' }, { en: 'subterranean', id: 'di bawah tanah' },
            { en: 'subtle', id: 'halus' }, { en: 'succumb', id: 'mengalah' },
            { en: 'sullen', id: 'cemberut' }, { en: 'summit', id: 'puncak' },
            { en: 'sundry', id: 'bermacam-macam' }, { en: 'superb', id: 'hebat' },
            { en: 'superficial', id: 'dangkal' }, { en: 'supplant', id: 'menggantikan' },
            { en: 'supple', id: 'lentur' }, { en: 'supplicate', id: 'memohon' },
            { en: 'surfeit', id: 'kejenuhan' }, { en: 'surge', id: 'gelora' },
            { en: 'surly', id: 'bermuka masam' }, { en: 'surmise', id: 'menduga' },
            { en: 'surpass', id: 'melampaui' }, { en: 'susceptible', id: 'rentan' },
            { en: 'suspend', id: 'menangguhkan' }, { en: 'sway', id: 'bergoyang' },
            { en: 'sweeping', id: 'menyapu' }, { en: 'swift', id: 'cepat' },
            { en: 'swivel', id: 'putar' }, { en: 'sybaritic', id: 'termanja' },
            { en: 'synagogue', id: 'sinagoga' }, { en: 'synopsis', id: 'ringkasan' },
            { en: 'synthesize', id: 'mempersatukan' }, { en: 'taciturn', id: 'pendiam' },
            { en: 'tact', id: 'kebijaksanaan' }, { en: 'tactic', id: 'taktik' },
            { en: 'taint', id: 'mencemari' }, { en: 'tale', id: 'kisah' },
            { en: 'tame', id: 'menjinakkan' }, { en: 'tamper', id: 'merusakkan' },
            { en: 'tangle', id: 'kekusutan' }, { en: 'tantalize', id: 'menggiurkan' },
            { en: 'tantamount', id: 'sama' }, { en: 'tardy', id: 'terlambat' },
            { en: 'tarnish', id: 'menodai' }, { en: 'tart', id: 'asam (rasa)' },
            { en: 'taunt', id: 'mengejek' }, { en: 'tautology', id: 'ulangan yg tdk berguna' },
            { en: 'tawdry', id: 'menyolok' }, { en: 'taxonomy', id: 'taksonomi' },
            { en: 'tease', id: 'menggoda' }, { en: 'tedious', id: 'membosankan' },
            { en: 'telling', id: 'jitu' }, { en: 'temperate', id: 'sedang' },
            { en: 'tempting', id: 'gurih' }, { en: 'tender', id: 'lembut' },
            { en: 'tenor', id: 'makna umum' },
            { en: 'tentative', id: 'sementara' }, { en: 'tepid', id: 'hangat-hangat kuku' },
            { en: 'ternary', id: 'terner' }, { en: 'tether', id: 'menambatkan' },
            { en: 'thaw', id: 'mencair' }, { en: 'thermal', id: 'panas' },
            { en: 'thorough', id: 'teliti' }, { en: 'thoroughfare', id: 'jalan ramai' },
            { en: 'thrifty', id: 'hemat' }, { en: 'thrilling', id: 'yg merawankan hati' },
            { en: 'thrive', id: 'berkembang' }, { en: 'thwart', id: 'menggagalkan' },
            { en: 'tidings', id: 'kabar' }, { en: 'tilt', id: 'memiringkan' },
            { en: 'timid', id: 'malu-malu' }, { en: 'tint', id: 'warna' },
            { en: 'tiresome', id: 'melelahkan' }, { en: 'toil', id: 'bekerja keras' },
            { en: 'tolerant', id: 'toleran' }, { en: 'torpid', id: 'tumpul' },
            { en: 'torrent', id: 'semburan' }, { en: 'torture', id: 'menyiksa' },
            { en: 'toupee', id: 'rambut palsu' }, { en: 'tow', id: 'menarik' },
            { en: 'towering', id: 'yg meluap-luap' }, { en: 'toxic', id: 'racun' },
            { en: 'trailblazer', id: 'perintis' }, { en: 'trait', id: 'sifat' },
            { en: 'trajectory', id: 'lintasan' }, { en: 'tranquility', id: 'ketenangan' },
            { en: 'transmute', id: 'mengubah' },
            { en: 'transparent', id: 'jelas' }, { en: 'transpose', id: 'mengubah urutan' },
            { en: 'traverse', id: 'melintasi' }, { en: 'treacherous', id: 'berbahaya' },
            { en: 'tremendous', id: 'dahsyat' }, { en: 'tremor', id: 'getaran' },
            { en: 'trepidation', id: 'kegemparan' }, { en: 'tribunal', id: 'pengadilan' },
            { en: 'trickle', id: 'menetes' },
            { en: 'trigger', id: 'mencetuskan' }, { en: 'triumph', id: 'kemenangan' },
            { en: 'trivial', id: 'sepele' }, { en: 'trying', id: 'sulit' },
            { en: 'tug', id: 'menariknya' }, { en: 'tumble', id: 'jatuh' },
            { en: 'tumultuous', id: 'rusuh' }, { en: 'turbulent', id: 'bergolak' },
            { en: 'turmoil', id: 'kekacauan' }, { en: 'twinkle', id: 'berkelip' },
            { en: 'tyro', id: 'orang baru' }, { en: 'ultimate', id: 'terakhir' },
            { en: 'unalienable', id: 'yg tak dpt diambil' }, { en: 'unattended', id: 'tanpa perawatan' },
            { en: 'unbearable', id: 'tak tertahankan' }, { en: 'uncanny', id: 'luar biasa' },
            { en: 'uncouth', id: 'kasar' }, { en: 'undercover', id: 'rahasia' },
            { en: 'undergo', id: 'menjalani' }, { en: 'underlying', id: 'pokok' },
            { en: 'underscore', id: 'menggarisbawahi' }, { en: 'undertake', id: 'melakukan' },
            { en: 'undulate', id: 'yg berombak-ombak' }, { en: 'unerring', id: 'tepat' },
            { en: 'unexpendable', id: 'tidak dapat dikorbankan' },
            { en: 'ungainly', id: 'kaku' }, { en: 'uniform', id: 'seragam' },
            { en: 'unilateral', id: 'sepihak' }, { en: 'unique', id: 'unik' },
            { en: 'universal', id: 'universal' }, { en: 'unquenchable', id: 'tak terpadamkan' },
            { en: 'unruly', id: 'susah diatur' },
            { en: 'unscathed', id: 'tanpa cedera' }, { en: 'unsound', id: 'kurang sehat' },
            { en: 'upheaval', id: 'pergolakan' }, { en: 'uphold', id: 'menegakkan' },
            { en: 'upkeep', id: 'pemeliharaan' }, { en: 'uproar', id: 'gempar' },
            { en: 'uptight', id: 'tegang' }, { en: 'urge', id: 'mendesak' },
            { en: 'urgent', id: 'mendesak' }, { en: 'urinary', id: 'kemih' },
            { en: 'usurp', id: 'merebut' }, { en: 'utensil', id: 'perkakas' },
            { en: 'utter', id: 'mengucapkan' }, { en: 'vacant', id: 'kosong' },
            { en: 'vacuous', id: 'hampa' }, { en: 'vague', id: 'samar-samar' },
            { en: 'vain', id: 'sia-sia' }, { en: 'valedictory', id: 'pidato perpisahan' },
            { en: 'valiant', id: 'gagah' }, { en: 'valid', id: 'sah' },
            { en: 'vanguard', id: 'garda depan' }, { en: 'vanish', id: 'lenyap' },
            { en: 'vanity', id: 'kesombongan' }, { en: 'variable', id: 'variabel' },
            { en: 'variation', id: 'variasi' }, { en: 'vast', id: 'luas' },
            { en: 'vaunt', id: 'membual' },
            { en: 'vehemence', id: 'gelora' }, { en: 'vein', id: 'pembuluh darah' },
            { en: 'venerate', id: 'memuliakan' }, { en: 'venereal', id: 'kelamin' },
            { en: 'venomous', id: 'berbisa' }, { en: 'venturesome', id: 'berani' },
            { en: 'venue', id: 'tempat' }, { en: 'verbose', id: 'bertele-tele' },
            { en: 'verge', id: 'ambang' }, { en: 'verify', id: 'memeriksa' },
            { en: 'versatile', id: 'serba guna' }, { en: 'vessel', id: 'kapal' },
            { en: 'vex', id: 'menyusahkan' }, { en: 'viable', id: 'dapat hidup/dilakukan' },
            { en: 'vicinity', id: 'sekitarnya' }, { en: 'vie', id: 'bertanding' },
            { en: 'vigilance', id: 'kewaspadaan' }, { en: 'vigorous', id: 'kuat' },
            { en: 'villainous', id: 'jahat' },
            { en: 'vindicate', id: 'membenarkan' }, { en: 'visceral', id: 'mendalam' },
            { en: 'vital', id: 'vital' }, { en: 'vivid', id: 'jelas' },
            { en: 'vogue', id: 'mode' }, { en: 'volatile', id: 'volatil' },
            { en: 'voluptuous', id: 'bahenol' }, { en: 'vomit', id: 'memuntahkan' },
            { en: 'vouch', id: 'menjamin' }, { en: 'vow', id: 'bersumpah' },
            { en: 'vulnerable', id: 'rentan' }, { en: 'wade', id: 'mengarungi' },
            { en: 'wag', id: 'mengipaskan' }, { en: 'wage', id: 'upah' },
            { en: 'wail', id: 'meratap' }, { en: 'walkout', id: 'pemogokan' },
            { en: 'wan', id: 'pucat' }, { en: 'wander', id: 'mengembara' },
            { en: 'wane', id: 'menyusut' }, { en: 'ward', id: 'bangsal' },
            { en: 'ware', id: 'barang dagangan' },
            { en: 'warp', id: 'melengkung' }, { en: 'wary', id: 'waspada' },
            { en: 'waxy', id: 'lunak' }, { en: 'weary', id: 'lelah' },
            { en: 'well-to-do', id: 'orang kaya' }, { en: 'wholesome', id: 'sehat' },
            { en: 'wicked', id: 'jahat' }, { en: 'widen', id: 'memperluas' },
            { en: 'widespread', id: 'meluas' }, { en: 'wile', id: 'tipu muslihat' },
            { en: 'wily', id: 'licik' }, { en: 'winsome', id: 'menarik' },
            { en: 'wise', id: 'bijaksana' }, { en: 'withdraw', id: 'menarik' },
            { en: 'wither', id: 'melayu' }, { en: 'withhold', id: 'menahan' },
            { en: 'withstand', id: 'menahan' }, { en: 'witty', id: 'jenaka' },
            { en: 'woe', id: 'duka' }, { en: 'wonder', id: 'bertanya-tanya' },
            { en: 'wound', id: 'luka' }, { en: 'wrinkle', id: 'kerut' },
            { en: 'yearn', id: 'merindukan' }, { en: 'yield', id: 'menghasilkan' },
            { en: 'zealous', id: 'tekun' }, { en: 'zenith', id: 'zenit' },
            { en: 'zone', id: 'daerah' }
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
                if(next50Button) next50Button.disabled = true;
                return;
            }
            skipNavigationControls.classList.remove('hide');
            prev50Button.disabled = currentQuestionIndex === 0;
            next50Button.disabled = currentQuestionIndex === (orderedQuestions.length - 1);

            if (orderedQuestions.length <= 1) {
                prev50Button.disabled = true;
                next50Button.disabled = true;
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
                orderedQuestions = savedData.orderedQuestions; // Gunakan urutan yang disimpan (yang seharusnya sudah urut)
                currentQuestionIndex = savedData.currentQuestionIndex;
                score = savedData.score;
            } else {
                clearProgress();
                // Gunakan urutan soal langsung dari 'questions' yang sudah diurutkan secara abjad
                // Tidak ada pengacakan di sini.
                orderedQuestions = [...questions]; // Membuat salinan array questions yang sudah terurut
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
            updateSkipButtonStates();
        }

        function showQuestion(questionData) {
            questionElement.innerText = questionData.question;
            answerButtonsElement.innerHTML = '';
            const shuffledAnswers = [...questionData.answers].sort(() => Math.random() - 0.5); // Pilihan jawaban tetap diacak
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
            }, 1500);
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
