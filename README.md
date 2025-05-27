<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kuis Pengetahuan Arduino Dasar</title> <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px; /* Menambahkan padding ke body bisa berguna untuk layar kecil */
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
            grid-template-columns: repeat(2, 1fr); /* Selalu 2 kolom untuk jawaban */
            gap: 10px;
            margin-bottom: 20px;
        }

        .btn {
            background-color: #007bff; /* Default button color */
            color: white;
            border: none;
            padding: 12px 15px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            transition: background-color 0.2s ease, box-shadow 0.2s ease;
            word-wrap: break-word; /* Baik untuk teks panjang */
            min-height: 50px;
            display: flex;
            align-items: center;
            justify-content: center;
            outline: none; /* Sebaiknya biarkan outline untuk aksesibilitas atau style custom focus */
            font-weight: bold;
        }

        /* SARAN: Kurangi !important. Urutkan CSS berdasarkan spesifisitas atau urutan kemunculan. */
        /* Contoh penataan ulang (mungkin perlu penyesuaian lebih lanjut): */

        /* Default hover/focus (kecuali untuk tombol yang sudah dikoreksi/salah atau tombol khusus) */
        .btn:not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q):hover:not([disabled]) {
            background-color: #0056b3; /* Warna hover default */
        }
        .btn:not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q):focus:not([disabled]) {
            box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.5); /* Focus default */
        }

        /* Tombol Jawaban Benar */
        .btn.correct { background-color: #28a745; box-shadow: none; }
        .btn.correct:hover:not([disabled]) { background-color: #218838; }
        .btn.correct:focus:not([disabled]) {
            background-color: #28a745;
            box-shadow: 0 0 0 3px rgba(40, 167, 69, 0.6);
        }

        /* Tombol Jawaban Salah */
        .btn.wrong { background-color: #dc3545; box-shadow: none; }
        .btn.wrong:hover:not([disabled]) { background-color: #c82333; }
        .btn.wrong:focus:not([disabled]) {
            background-color: #dc3545;
            box-shadow: 0 0 0 3px rgba(220, 53, 69, 0.6);
        }

        /* Tombol Skip & Navigasi Khusus */
        .skip-btn {
            background-color: #17a2b8; /* Ganti warna agar beda dari jawaban benar, misal info blue */
            color: white;
            padding: 8px 12px;
            font-size: 0.9em;
            min-width: 80px;
        }
        .skip-btn:hover:not([disabled]) {
            background-color: #138496; /* Darker info blue */
        }
        .skip-btn:disabled {
            background-color: #a1d2db; /* Light info blue */
            color: #e9f5ec;
        }

        .btn-prev-q {
            background-color: #ffc107; /* Ganti warna, misal warning yellow */
            color: #212529; /* Teks gelap agar kontras */
            padding: 8px 12px;
            font-size: 0.9em;
            min-width: 80px;
        }
        .btn-prev-q:hover:not([disabled]) {
            background-color: #e0a800; /* Darker warning yellow */
        }
        .btn-prev-q:disabled {
            background-color: #ffeeba; /* Light warning yellow */
            color: #666666;
        }
        
        /* Generic Disabled State (jika tidak ada style disabled khusus) */
        /* Ini akan berlaku untuk tombol jawaban setelah dijawab, dan tombol start/continue jika disabled */
        .btn:disabled {
            cursor: not-allowed;
            opacity: 0.65;
        }
        /* Style untuk tombol jawaban (bukan skip/prev) yang disabled TAPI BUKAN karena sudah dikoreksi (correct/wrong) */
        /* Ini akan menimpa background .btn jika tombol jawaban belum dipilih tapi sudah disabled karena alasan lain (jarang terjadi di flow ini)*/
        /* Atau untuk start/continue button jika disabled */
        .btn:disabled:not(.correct):not(.wrong):not(.skip-btn):not(.btn-prev-q) {
             background-color: #6c757d; /* Warna abu-abu untuk disabled umum */
             color: #ccc;
        }


        .controls {
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        #skip-navigation-controls {
            justify-content: space-between;
            margin-top: 40px;
            margin-bottom: 10px;
        }

        .hide { display: none !important; } /* !important di sini mungkin OK untuk utility class */
    </style>
</head>
<body>
    <div class="quiz-container">
        <h1>Kuis Kosakata Dasar</h1> <p id="completion-message" class="hide">Selamat Kuis Sudah Selesai 🎉</p>
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
                <button id="prev-question-btn" class="btn btn-prev-q">&lt;</button><button id="next-50-btn" class="btn skip-btn">50 &raquo;</button>
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
        const prevQuestionButton = document.getElementById('prev-question-btn');
        const next50Button = document.getElementById('next-50-btn');
        const JUMP_AMOUNT = 50;

        let orderedQuestions, currentQuestionIndex;
        let score = 0;
        let questionTimeout;

        // Daftar kata mentah (Inggris: Indonesia)
        // SARAN: Pastikan daftar ini cukup panjang dan beragam untuk kualitas kuis yang baik.
        const rawVocabularyList = [
            // Isi dibawah sini yah
            { en: 'abandon', id: 'meninggalkan' }, { en: 'abate', id: 'mereda' },
            { en: 'abbreviation', id: 'singkatan' }, { en: 'abduct', id: 'menculik' },
            { en: 'abhor', id: 'jijik' }, { en: 'abject', id: 'hina' },
            { en: 'able', id: 'sanggup' }, { en: 'abolish', id: 'menghapuskan' },
            { en: 'abominate', id: 'merasa jijik' }, { en: 'abort', id: 'menggugurkan' },
            { en: 'abrupt', id: 'tiba-tiba' }, { en: 'absolve', id: 'membebaskan' },
            // Tambahkan lebih banyak kosakata di sini...
            // Contoh: minimal ada 4-5 entri untuk pembuatan distraktor yang lebih baik.
            { en: 'accept', id: 'menerima' }, { en: 'achieve', id: 'mencapai' },
            { en: 'acquire', id: 'memperoleh' }, { en: 'act', id: 'bertindak' },
            // Batas akhirnya sampai sini 
        ];

        let questions = [];

        // Urutkan vocabulary list berdasarkan kata Inggris (sekali di awal)
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

            if (rawVocabularyList.length < 1) {
                console.warn("rawVocabularyList kosong, tidak ada soal yang bisa dibuat.");
                return;
            }
             // Jika jumlah kosakata kurang dari 4, sulit membuat 3 distraktor unik.
            const MIN_VOCAB_FOR_GOOD_DISTRACTORS = 4;

            rawVocabularyList.forEach(vocabItem => {
                const correctAnswer = vocabItem.id;
                const distractors = [];

                // Coba dapatkan distraktor dari daftar terjemahan lain
                let potentialDistractors = allIndonesianTranslations.filter(trans => trans !== correctAnswer);
                // Acak daftar calon distraktor
                potentialDistractors.sort(() => Math.random() - 0.5);

                for (let i = 0; i < potentialDistractors.length && distractors.length < 3; i++) {
                    if (!distractors.includes(potentialDistractors[i])) {
                        distractors.push(potentialDistractors[i]);
                    }
                }

                // Jika distraktor masih kurang dari 3 (misal karena daftar kosakata sangat kecil)
                // Gunakan fallback yang lebih sederhana.
                const fallbackBase = ["Pilihan A", "Pilihan B", "Pilihan C", "Pilihan D"];
                let fallbackIndex = 0;
                while (distractors.length < 3) {
                    // Pastikan fallback tidak sama dengan jawaban benar atau distraktor yang sudah ada
                    let candidateFallback = fallbackBase[fallbackIndex % fallbackBase.length];
                    if (rawVocabularyList.length < MIN_VOCAB_FOR_GOOD_DISTRACTORS) {
                         // Tambahkan suffix acak jika daftar kosakata sangat kecil untuk menghindari duplikasi total
                        candidateFallback += ` ${Math.floor(Math.random()*10)}`;
                    }

                    if (candidateFallback !== correctAnswer && !distractors.includes(candidateFallback)) {
                        distractors.push(candidateFallback);
                    } else if (rawVocabularyList.length < MIN_VOCAB_FOR_GOOD_DISTRACTORS && distractors.length < 3) {
                        // Jika masih bentrok dan kosakata sedikit, paksa tambahkan sesuatu yang unik
                        distractors.push(`Opsi Tambahan ${distractors.length + 1}`);
                    }
                    fallbackIndex++;
                    if (fallbackIndex > fallbackBase.length * 2 && distractors.length < 3) { // Safety break
                         // Jika setelah beberapa kali masih belum cukup, isi sisanya
                        const needed = 3 - distractors.length;
                        for(let j=0; j<needed; j++) {
                            distractors.push(`Jawaban Lain ${distractors.length +1}`);
                        }
                        break;
                    }
                }

                const answerOptions = [
                    { text: correctAnswer, correct: true },
                    // Pastikan ada 3 distraktor
                    { text: distractors[0] || "Distraktor 1", correct: false },
                    { text: distractors[1] || "Distraktor 2", correct: false },
                    { text: distractors[2] || "Distraktor 3", correct: false }
                ];

                questions.push({
                    question: vocabItem.en,
                    answers: answerOptions
                });
            });
        }

        generateQuestions(); // Panggil sekali untuk menyiapkan daftar soal

        function saveProgress() {
            if (!questionContainerElement.classList.contains('hide') && orderedQuestions && currentQuestionIndex < orderedQuestions.length) {
                const progress = {
                    currentQuestionIndex: currentQuestionIndex,
                    score: score,
                    orderedQuestions: orderedQuestions // Simpan urutan soal yang sedang dipakai
                };
                localStorage.setItem('quizProgress', JSON.stringify(progress));
            }
        }

        function loadProgress() {
            const savedProgress = localStorage.getItem('quizProgress');
            if (savedProgress) {
                try {
                    const progressData = JSON.parse(savedProgress);
                    // Validasi lebih ketat: apakah struktur data sesuai?
                    if (progressData && typeof progressData.currentQuestionIndex === 'number' &&
                        typeof progressData.score === 'number' && Array.isArray(progressData.orderedQuestions) &&
                        progressData.orderedQuestions.length > 0 &&
                        // Cek apakah soal yang disimpan masih relevan dengan daftar soal saat ini (berdasarkan jumlah)
                        // Ini penting jika Anda mengubah rawVocabularyList
                        progressData.orderedQuestions.length === questions.length &&
                        progressData.currentQuestionIndex < progressData.orderedQuestions.length) {
                        return progressData;
                    } else {
                        console.warn("Saved progress data is invalid or doesn't match current questions. Clearing.");
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
        prevQuestionButton.addEventListener('click', () => navigateQuestions(-1));
        next50Button.addEventListener('click', () => navigateQuestions(JUMP_AMOUNT));

        function navigateQuestions(amount) {
            clearTimeout(questionTimeout); // Hentikan timer otomatis pindah soal
            if (!orderedQuestions || orderedQuestions.length === 0) return;

            let newIndex = currentQuestionIndex + amount;
            if (newIndex < 0) newIndex = 0;
            else if (newIndex >= orderedQuestions.length) newIndex = orderedQuestions.length - 1;

            if (newIndex !== currentQuestionIndex) {
                currentQuestionIndex = newIndex;
                setNextQuestion(); // Ini akan menyimpan progress
            } else {
                updateSkipButtonStates(); // Perbarui state tombol jika indeks tidak berubah (misal sudah di ujung)
            }
        }

        function updateSkipButtonStates() {
            if (!orderedQuestions || orderedQuestions.length === 0 || questionContainerElement.classList.contains('hide')) {
                skipNavigationControls.classList.add('hide');
                // Tidak perlu cek if(prev50Button) jika yakin elemen selalu ada
                prev50Button.disabled = true;
                prevQuestionButton.disabled = true;
                next50Button.disabled = true;
                return;
            }
            skipNavigationControls.classList.remove('hide');
            const isFirstQuestion = currentQuestionIndex === 0;
            const isLastQuestion = currentQuestionIndex === (orderedQuestions.length - 1);

            prev50Button.disabled = isFirstQuestion;
            prevQuestionButton.disabled = isFirstQuestion;
            next50Button.disabled = isLastQuestion;

            // Jika hanya ada satu soal, semua tombol navigasi disabled
            if (orderedQuestions.length <= 1) {
                prev50Button.disabled = true;
                prevQuestionButton.disabled = true;
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
                // Jika kuis tidak disembunyikan (misal setelah refresh halaman saat kuis aktif)
                // Pastikan UI sesuai, meskipun idealnya loadProgress menangani ini via startGame
                initialControls.classList.add('hide');
                updateSkipButtonStates(); // Mungkin perlu dipanggil di sini jika kuis dilanjutkan otomatis
            }
        });

        startButton.addEventListener('click', () => startGame(false));
        continueButton.addEventListener('click', () => startGame(true));

        function startGame(isContinuing = false) {
            clearTimeout(questionTimeout);
            completionMessageElement.classList.add('hide');
            
            initialControls.classList.add('hide');
            questionContainerElement.classList.remove('hide');
            questionCounterElement.classList.remove('hide');

            const savedData = loadProgress(); // Selalu coba load progress terbaru

            if (isContinuing && savedData) {
                orderedQuestions = savedData.orderedQuestions; // Gunakan urutan soal yang tersimpan
                currentQuestionIndex = savedData.currentQuestionIndex;
                score = savedData.score;
                startButton.innerText = 'Mulai'; // Tetap "Mulai" jika Lanjutkan gagal & mulai baru
            } else {
                clearProgress(); // Hapus progres lama jika mulai baru atau continue gagal
                // SARAN: Opsi untuk mengacak soal saat mulai baru
                // orderedQuestions = [...questions].sort(() => Math.random() - 0.5); // Acak
                orderedQuestions = [...questions]; // Tidak acak (sesuai urutan di rawVocabularyList)
                currentQuestionIndex = 0;
                score = 0;
                startButton.innerText = 'Mulai';
            }
            
            if (!orderedQuestions || orderedQuestions.length === 0) {
                showResults(); // Tampilkan pesan jika tidak ada soal
                completionMessageElement.innerText = "Tidak ada soal untuk ditampilkan. Mohon tambahkan kosakata.";
                completionMessageElement.style.color = "#dc3545"; // Warna merah untuk error
                completionMessageElement.classList.remove('hide');
                startButton.innerText = 'Mulai'; // Kembalikan teks tombol mulai
                initialControls.classList.remove('hide'); // Tampilkan lagi tombol awal
                questionContainerElement.classList.add('hide');
                questionCounterElement.classList.add('hide');
                return;
            }
            setNextQuestion();
        }

        function setNextQuestion() {
            resetState();
            if (orderedQuestions && currentQuestionIndex < orderedQuestions.length) {
                questionCounterElement.innerText = `${currentQuestionIndex + 1} / ${orderedQuestions.length}`;
                showQuestion(orderedQuestions[currentQuestionIndex]);
                saveProgress(); // Simpan progress setiap kali soal baru ditampilkan
                // Hapus fokus dari elemen aktif (misalnya tombol jawaban sebelumnya)
                if (document.activeElement && typeof document.activeElement.blur === 'function') {
                    document.activeElement.blur();
                }
            } else {
                showResults(); // Tampilkan hasil jika semua soal selesai
            }
            updateSkipButtonStates();
        }

        function showQuestion(questionData) {
            questionElement.innerText = questionData.question;
            answerButtonsElement.innerHTML = ''; // Kosongkan tombol jawaban sebelumnya
            // Acak urutan pilihan jawaban
            const shuffledAnswers = [...questionData.answers].sort(() => Math.random() - 0.5);

            shuffledAnswers.forEach(answer => {
                const button = document.createElement('button');
                button.innerText = answer.text;
                button.classList.add('btn');
                if (answer.correct) {
                    button.dataset.correct = "true"; // Simpan status benar sebagai string "true"
                }
                button.addEventListener('click', selectAnswer);
                answerButtonsElement.appendChild(button);
            });
        }

        function resetState() {
            clearTimeout(questionTimeout);
            // Tidak perlu loop manual untuk menghapus children jika akan menggunakan innerHTML = ''
            // di showQuestion. Tapi jika ingin lebih eksplisit:
            // while (answerButtonsElement.firstChild) {
            //     answerButtonsElement.removeChild(answerButtonsElement.firstChild);
            // }
            // Juga pastikan tombol-tombol jawaban yang lama (jika tidak dihapus via innerHTML)
            // tidak lagi memiliki kelas 'correct' atau 'wrong' jika diperlukan.
            // Namun, karena kita innerHTML = '', ini sudah bersih.
        }

        function selectAnswer(e) {
            const selectedButton = e.target;
            const correct = selectedButton.dataset.correct === 'true'; // Bandingkan dengan string "true"

            if (correct) {
                score++;
            }

            Array.from(answerButtonsElement.children).forEach(button => {
                setStatusClass(button, button.dataset.correct === 'true');
                button.disabled = true; // Nonaktifkan semua tombol jawaban setelah dipilih
            });

            saveProgress(); // Simpan progres setelah menjawab

            // Otomatis pindah ke soal berikutnya atau tampilkan hasil
            questionTimeout = setTimeout(() => {
                if (orderedQuestions && currentQuestionIndex < orderedQuestions.length - 1) {
                    currentQuestionIndex++;
                    setNextQuestion();
                } else if (orderedQuestions && currentQuestionIndex === orderedQuestions.length - 1) {
                    // Ini adalah soal terakhir, tampilkan hasil
                    showResults();
                }
                 // Tidak perlu kondisi lain, jika kuis selesai, showResults() akan dipanggil.
            }, 1500); // Waktu Perpindahan soal (misal 1.5 detik)
        }

        function setStatusClass(element, isCorrect) {
            clearStatusClass(element); // Hapus kelas status sebelumnya
            if (isCorrect) {
                element.classList.add('correct');
            } else {
                element.classList.add('wrong');
            }
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
            
            completionMessageElement.innerText = `Kuis Selesai! Skor Anda: ${score} / ${orderedQuestions ? orderedQuestions.length : 0} 🎉`;
            completionMessageElement.style.color = "#28a745"; // Warna hijau untuk sukses
            completionMessageElement.classList.remove('hide');
            
            startButton.innerText = 'Ulangi Kuis';
            initialControls.classList.remove('hide');
            continueButton.classList.add('hide'); // Sembunyikan tombol lanjutkan setelah kuis selesai
            
            clearProgress(); // Hapus progres setelah kuis selesai dan hasil ditampilkan
        }
    </script>
</body>
</html>
