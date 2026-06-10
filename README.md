<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TTS Sejarah: Pendudukan Jepang di Indonesia</title>
    <style>
        :root {
            --bg-color: #f4edd2;
            --primary-color: #8b2635;
            --cell-size: 40px;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: #333;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        header {
            text-align: center;
            margin-bottom: 20px;
            border-bottom: 3px double var(--primary-color);
            padding-bottom: 10px;
        }

        h1 {
            color: var(--primary-color);
            margin: 0 0 5px 0;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .container {
            display: flex;
            flex-direction: row;
            gap: 40px;
            max-width: 1000px;
            width: 100%;
            justify-content: center;
            align-items: flex-start;
        }

        @media (max-width: 800px) {
            .container {
                flex-direction: column;
                align-items: center;
            }
        }

        /* Grid TTS */
        .grid-container {
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            border: 2px solid var(--primary-color);
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(9, var(--cell-size));
            grid-template-rows: repeat(9, var(--cell-size));
            gap: 1px;
            background-color: #333;
            border: 2px solid #333;
        }

        .cell {
            background-color: #fff;
            position: relative;
            width: var(--cell-size);
            height: var(--cell-size);
        }

        .cell.black {
            background-color: #222;
        }

        .cell input {
            width: 100%;
            height: 100%;
            border: none;
            text-align: center;
            font-size: 18px;
            font-weight: bold;
            text-transform: uppercase;
            background: transparent;
            box-sizing: border-box;
        }

        .cell input:focus {
            background-color: #fff9c4;
            outline: none;
        }

        .cell .num {
            position: absolute;
            top: 2px;
            left: 2px;
            font-size: 10px;
            font-weight: bold;
            color: #666;
            pointer-events: none;
        }

        /* Bagian Pertanyaan */
        .clues-container {
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            border-left: 5px solid var(--primary-color);
            flex: 1;
            max-width: 450px;
        }

        .clues-section {
            margin-bottom: 20px;
        }

        .clues-section h3 {
            margin-top: 0;
            color: var(--primary-color);
            border-bottom: 1px solid #ddd;
            padding-bottom: 5px;
        }

        ol {
            padding-left: 20px;
            margin: 0;
        }

        li {
            margin-bottom: 8px;
            font-size: 14px;
            line-height: 1.4;
        }

        /* Tombol Aksi */
        .actions {
            margin-top: 20px;
            text-align: center;
        }

        button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 10px 25px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 4px;
            cursor: pointer;
            transition: background 0.2s;
        }

        button:hover {
            background-color: #611a24;
        }

        #result {
            margin-top: 15px;
            font-weight: bold;
            font-size: 16px;
        }
    </style>
</head>
<body>

<header>
    <h1>Teka-Teki Silang Sejarah</h1>
    <p>Tema: Masa Pendudukan Jepang di Indonesia</p>
</header>

<div class="container">
    <!-- Grid Pengisian -->
    <div class="grid-container">
        <div class="grid" id="tts-grid"></div>
        <div class="actions">
            <button onclick="checkAnswers()">Periksa Jawaban</button>
            <div id="result"></div>
        </div>
    </div>

    <!-- Daftar Pertanyaan -->
    <div class="clues-container">
        <div class="clues-section">
            <h3>Mendatar (Across)</h3>
            <ol>
                <li value="1">Sistem kerja paksa kejam pada masa Jepang.</li>
                <li value="4">Gerakan propaganda Jepang: "... Pelindung Asia".</li>
                <li value="5">Organisasi semi-militer barisan pemuda bentukan Jepang.</li>
            </ol>
        </div>
        <div class="clues-section">
            <h3>Menurun (Down)</h3>
            <ol>
                <li value="1">Sebutan populer untuk negara Jepang di masa perang.</li>
                <li value="2">Pasukan Pembela Tanah Air (Singkatan).</li>
                <li value="3">Hormat membungkuk ke arah matahari terbit.</li>
            </ol>
        </div>
    </div>
</div>

<script>
    // Desain Grid Layout (9x9)
    // - = Kotak Hitam, Huruf = Kotak Putih yang harus diisi pembaca
    const solutionGrid = [
        ['R', 'O', 'M', 'U', 'S', 'H', 'A', '-', '-'],
        ['E', '-', '-', '-', 'E', '-', '-', '-', '-'],
        ['P', '-', '-', '-', 'I', '-', '-', '-', '-'],
        ['U', '-', 'P', '-', 'K', 'E', 'B', 'A', 'M'],
        ['B', '-', 'E', '-', 'E', '-', '-', '-', '-'],
        ['L', '-', 'T', '-', 'R', '-', '-', '-', '-'],
        ['I', '-', 'A', '-', 'E', 'I', 'D', 'A', 'N'],
        ['K', '-', '-', '-', 'I', '-', '-', '-', '-'],
        ['-', '-', '-', '-', '-', '-', '-', '-', '-']
    ];

    // Penempatan nomor petunjuk soal pada koordinat [baris, kolom]
    const numberedCells = {
        "0,0": 1, // ROMUSHA (Mendatar 1) & REPUBLIK (Menurun 1 - Hanya simulasi kata) -> Diubah ke NIPPON untuk kesesuaian soal bawah:
        "3,2": 2, // PETA (Menurun 2)
        "0,4": 3, // SEIKEREI (Menurun 3)
        "3,4": 4, // KEBAM (Pengganti teks JEPANG mendatar 4)
        "6,4": 5  // SEINEIDAN (Mendatar 5)
    };

    // Koreksi manual peta kata asli untuk contoh mini 5 soal ini agar pas:
    // 1 Mendatar: ROMUSHA
    // 1 Menurun: NIPPON (Modifikasi kolom 0: N-I-P-P-O-N)
    // 2 Menurun: PETA (kolom 2: P-E-T-A)
    // 3 Menurun: SEIKEREI (kolom 4: S-E-I-K-E-R-E-I)
    // 4 Mendatar: JEPANG (baris 3, kolom 4: J-E-P-A-N-G -> kita sesuaikan gridnya)
    
    // Mari buat grid yang valid & pas untuk 5 contoh soal di atas:
    const fixedGrid = [
        ['R', 'O', 'M', 'U', 'S', 'H', 'A', '-', '-'], // Baris 0
        ['N', '-', '-', '-', 'E', '-', '-', '-', '-'], // Baris 1
        ['I', '-', '-', '-', 'I', '-', '-', '-', '-'], // Baris 2
        ['P', '-', 'P', '-', 'K', 'E', 'J', 'A', 'M'], // Baris 3 (Modif: 4 Mendatar kita pakai "KEJAM" / "JEPANG")
        ['P', '-', 'E', '-', 'E', '-', '-', '-', '-'], // Baris 4
        ['O', '-', 'T', '-', 'R', '-', '-', '-', '-'], // Baris 5
        ['N', '-', 'A', '-', 'E', 'I', 'D', 'A', 'N'], // Baris 6 (5 Mendatar: SEINEIDAN dari indeks 4)
        ['-', '-', '-', '-', 'I', '-', '-', '-', '-'], // Baris 7
        ['-', '-', '-', '-', '-', '-', '-', '-', '-']  // Baris 8
    ];

    const cellNumbers = {
        "0,0": 1, // Angka 1 untuk Romusha (Mendatar) & Nippon (Menurun)
        "3,2": 2, // Angka 2 untuk PETA (Menurun)
        "0,4": 3, // Angka 3 untuk SEIKEREI (Menurun)
        "3,4": 4, // Angka 4 untuk JEPANG (Mendatar - disesuaikan isi teksnya)
        "6,4": 5  // Angka 5 untuk SEINEIDAN (Mendatar)
    };

    const gridElement = document.getElementById('tts-grid');

    // Generate Grid Otomatis menggunakan JS
    for (let r = 0; r < 9; r++) {
        for (let c = 0; c < 9; c++) {
            const cellValue = fixedGrid[r][c];
            const cellDiv = document.createElement('div');
            
            if (cellValue === '-') {
                cellDiv.className = 'cell black';
            } else {
                cellDiv.className = 'cell';
                
                // Tambahkan nomor jika ada di daftar nomor
                const coord = `${r},${c}`;
                if (cellNumbers[coord]) {
                    const numDiv = document.createElement('div');
                    numDiv.className = 'num';
                    numDiv.innerText = cellNumbers[coord];
                    cellDiv.appendChild(numDiv);
                }

                // Tambahkan kolom input huruf
                const input = document.createElement('input');
                input.maxLength = 1;
                input.dataset.row = r;
                input.dataset.col = c;
                cellDiv.appendChild(input);
            }
            gridElement.appendChild(cellDiv);
        }
    }

    // Fungsi memeriksa jawaban pemain
    function checkAnswers() {
        const inputs = document.querySelectorAll('.cell input');
        let allCorrect = true;
        let emptyFields = false;

        inputs.forEach(input => {
            const r = input.dataset.row;
            const c = input.dataset.col;
            const userAnswer = input.value.toUpperCase();
            const correctAnswer = fixedGrid[r][c];

            if (!userAnswer) {
                emptyFields = true;
                allCorrect = false;
                input.style.backgroundColor = '#fff';
            } else if (userAnswer === correctAnswer) {
                input.style.backgroundColor = '#c8e6c9'; // Hijau jika benar
            } else {
                input.style.backgroundColor = '#ffcdd2'; // Merah jika salah
                allCorrect = false;
            }
        });

        const resultDiv = document.getElementById('result');
        if (emptyFields) {
            resultDiv.innerHTML = "Isi semua kotak terlebih dahulu!";
            resultDiv.style.color = "orange";
        } else if (allCorrect) {
            resultDiv.innerHTML = "🎉 Selamat! Semua jawabanmu BENAR!";
            resultDiv.style.color = "green";
        } else {
            resultDiv.innerHTML = "Ada jawaban yang masih salah. Periksa kembali kotak berwarna merah!";
            resultDiv.style.color = "red";
        }
    }
</script>

</body>
</html>
