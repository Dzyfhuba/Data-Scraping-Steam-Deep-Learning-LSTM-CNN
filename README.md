# Analisis Sentimen Ulasan Steam

Notebook ini menganalisis sentimen ulasan berbahasa Inggris untuk game *Where Winds Meet* dari Steam. Alurnya mencakup pengambilan data dari Steam Web API, penyusunan dataset, pembersihan teks, exploratory data analysis, dan perbandingan tiga model deep learning:

- LSTM
- CNN
- Hybrid LSTM-CNN

## Persyaratan

- Python 3.10 atau yang lebih baru
- Koneksi internet jika ingin mengambil ulasan langsung dari Steam
- RAM dan waktu komputasi yang cukup untuk melatih tiga model TensorFlow

TensorFlow dapat berjalan lebih lambat pada CPU. Penggunaan GPU bersifat opsional dan bergantung pada konfigurasi TensorFlow di mesin Anda.

## Clone Repository

```powershell
git clone https://github.com/Dzyfhuba/Data-Scraping-Steam-Deep-Learning-LSTM-CNN.git
cd Data-Scraping-Steam-Deep-Learning-LSTM-CNN
```

## Instalasi

Disarankan menggunakan virtual environment agar dependensi proyek tidak bercampur dengan instalasi Python lain.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m pip install jupyter ipykernel
python -m ipykernel install --user --name data-scraping-steam --display-name "Python (data-scraping-steam)"
```

Jika PowerShell memblokir aktivasi environment, jalankan PowerShell sebagai pengguna biasa dan gunakan salah satu alternatif berikut:

```powershell
# Command Prompt
.venv\Scripts\activate.bat

# Atau jalankan notebook tanpa aktivasi dengan interpreter environment
.\.venv\Scripts\python.exe -m jupyter notebook
```

## Menjalankan Notebook

Jalankan Jupyter Notebook dari folder repository:

```powershell
python -m notebook
```

Buka `data_scraping_deep_learning_steam.ipynb`, pilih kernel `Python (data-scraping-steam)`, lalu jalankan cell secara berurutan dari atas ke bawah.

## Data dan Scraping

Repository ini sudah menyertakan `dataset.json` sebagai artefak dataset hasil pemrosesan sebelumnya. Pada alur notebook saat ini, cell-cell berikutnya menggunakan variabel `hasil_scraping`, sehingga jalankan cell scraping terlebih dahulu jika ingin menjalankan notebook dari awal sampai akhir. Untuk mengambil data terbaru dari Steam:

1. Jalankan cell scraping pada bagian **Pengumpulan Data (Scraping)**.
2. Notebook menggunakan App ID `3564740` untuk *Where Winds Meet*.
3. Cell tersebut mengambil hingga 50.000 ulasan berbahasa Inggris dan menyimpan hasil pemrosesan ke `dataset.json`.

Scraping memerlukan koneksi internet dan dapat memerlukan waktu cukup lama. Cell tersebut juga menulis ulang `dataset.json`; jangan menjalankannya jika ingin mempertahankan dataset yang sudah ada.

## Urutan Analisis

Notebook menjalankan proses berikut:

1. Mengambil dan meninjau ulasan Steam.
2. Membentuk label `Negatif`, `Positif`, dan `Netral`.
3. Membersihkan teks dan menghapus duplikat.
4. Membagi data menjadi training, validation, dan testing set secara stratified.
5. Membuat visualisasi distribusi data, panjang ulasan, dan word cloud.
6. Melakukan tokenisasi serta padding sequence.
7. Melatih model LSTM, CNN, dan Hybrid LSTM-CNN.
8. Membandingkan accuracy, precision, recall, dan F1-score.
9. Menguji inferensi pada contoh review baru.

## Catatan Pelabelan

Steam menyediakan label thumbs up/down melalui field `voted_up`. Kelas netral bukan label asli Steam. Notebook membuatnya sebagai *weak label* untuk review dengan skor compound VADER pada rentang `[-0.05, 0.05]`. Karena itu, hasil evaluasi perlu dipahami sebagai eksperimen weak supervision, bukan ground truth manual.

## Struktur Proyek

```text
.
├── data_scraping_deep_learning_steam.ipynb  # Notebook utama
├── dataset.json                             # Dataset ulasan yang tersedia
├── requirements.txt                         # Dependensi Python
└── README.md                                # Panduan proyek
```

Folder `temp/` digunakan untuk data pendukung NLTK dan tidak perlu diubah secara manual.

## Troubleshooting Singkat

- Jika `SentimentIntensityAnalyzer` meminta resource NLTK, jalankan ulang cell terkait. Notebook akan mengunduh `vader_lexicon` secara otomatis.
- Jika kernel tidak muncul di Jupyter, aktifkan `.venv` lalu jalankan kembali perintah `ipykernel install`.
- Jika scraping gagal, periksa koneksi internet dan coba lagi nanti. API Steam dapat membatasi atau menolak request tertentu.
- Jika proses training terlalu lama, kurangi `epochs`, ukuran batch, atau jumlah data pada cell training/scraping.