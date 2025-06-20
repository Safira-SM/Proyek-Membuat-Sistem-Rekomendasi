# Laporan Proyek Machine Learning - Safira Maulidia

## Project Overview

Pengguna mungkin kesulitan untuk menemukan lagu yang sesuai dengan selera pribadi. Jadi sistem rekomendasi musik ini berbasis konten yang dapat menyarankan lagu-lagu dengan karakteristik audio yag mirip. Harapannya sistem ini dapat meningkatkan pengalaman pengguna dalam menemukan lagu yang sesuai dengan selera mereka.


**Rubrik/Kriteria Tambahan (Opsional)**:
- Sistem rekomendasi yang baik membuat pengguna lebih mudah menemukan lagu yang sesuai dengan selera mereka.
- Masalah diselesaikan dengan membangun sistem rekomendasi berbasis content-based filtering 

## Business Understanding

Pada bagian ini, Anda perlu menjelaskan proses klarifikasi masalah.  
Bagian laporan ini mencakup:

### Problem Statements

- Bagaimana cara mengidentifikasi lagu-lagu yang mirip berdasarkan fitur audio ?
- Apakah sistem dapat merekomendasikan lagu yang relevan jika pengguna menyukai sebuah lagu tertentu ?
- metode apa yang efektif untuk mengukur kemiripan lagu ?

### Goals

- Membangun sistem rekomendai musik berbasis konten yang dapat menyarankan lagu dengan fitur audio yang mirip
- Merekomendasikan lagu yang relevan dengan lagu yang di input pengguna
- Menmberikan daftar rekomendasi top-n lagu untuk satu input lagu. 


**Rubrik/Kriteria Tambahan (Opsional)**:
### Solution statements
- Menggunakan metode Content_Based Filtering berdasarkan fitur audio dari setiap lagu 
- Menghitung kemiripan cosine similarity antar fitur audio lagu 
- Membangun fitur rekomendasi untuk daftar lagu yang serupa berdasarkan lagu yang di input


## Data Understanding
- Dataset yang digunakan diambil dari kaggle 
- Link : https://www.kaggle.com/datasets/zaheenhamidani/ultimate-spotify-tracks-db


Informasi Umum Dataset :
- Jumlah baris: 232,725 
- Jumlah kolom: 18


Variabel dalam dataset:

- track_name : Nama lagu
- artist_name : Nama artis
- genre : Genre dari lagu
- track_id  : identifikasi unik setiap lagu
- popularity : Skor popularitas berdasarkan metrik internal Spotify
- acousticness : Tingkat akustik sebuah lagu 
- danceability : Seberapa cocok sebuah lagu untuk menari 
- duration_ms : Durasi lagu dalam milidetik
- energy : Intensitas dan aktivitas lagu 
- instrumentalness : Kemungkinan lagu tidak mengandung vokal 
- key : Nada dasar lagu 
- liveness : Kemungkinan lagu dilakukan langsung di depan audiens 
- loudness : Volume keseluruhan lagu dalam desibel 
- mode : Mayor atau minor 
- speechiness : Seberapa banyak elemen ucapan dalam lagu 
- tempo : Kecepatan lagu dalam BPM
- time_signature : Penanda birama 
- valence : Ukuran kepositifan musikal lagu 



**Rubrik/Kriteria Tambahan (Opsional)**:
- Terdapat missing value pada fitur track_name yang akan digunakan, yang sudah diatas dengan menghapus missing value pada data tersebut.

## Data Preparation
langkah - langkah yanh dilakukan pada tahap ini yaitu, 


**Rubrik/Kriteria Tambahan (Opsional)**: 
1. Mengelompokkan data kedalam df_data yang akan digunakan 
`df_data = df[['track_name', 'artist_name', 'genre', 'acousticness', 'danceability', 'energy', 'instrumentalness', 'liveness', 'loudness', 'speechiness', 'tempo', 'valence']].copy()`

2. Menghapus data duplikat
`df_data.drop_duplicates(inplace=True)`

3. Melihat missing value data, dan ditemukan 1 missing value dalam kolom track_name
`df_data.isnull().sum()`

4. Manghapus missing value pada kolom track_name
`df_data.dropna(subset=['track_name'], inplace=True)`

5. Mengambil 1000 sample agar dapat mempercepat proses cosine similarity 
`df_sample = df_data.sample(n=1000, random_state=42).reset_index(drop=True)`

6. Melakukan Standarisasi pada fitur audio agar setiap fitur memiliki skala yang sebanding 
`scaler = StandardScaler()`



Alasan : diperlukan melakukan standarisasi agar nilai fitur yang memiliki rentang yang berbeda tidak mendominasi perhitungan kemiripan

## Modeling
- Menghitung kemiripan dengan cosinne antara lagu berdasarkan fitur audio terstandarisasi 
- output yang dihasilkan berupa daftar lagu rekomendasi dengan nama lagu, artis dan genre

**Rubrik/Kriteria Tambahan (Opsional)**: 
1. Cosine Similarity   
`similarity_matrix = cosine_similarity(df_scaled_sample)`
- Cara kerja: Mengukur sudut antar dua vektor fitur. Semakin kecil sudutnya, semakin mirip.
- Kelebihan: Tidak terpengaruh oleh skala dan efektif saat dimensi banyak (fitur numerik banyak).
- Kekuranganya : Kurang efektif jika fitur dominan

2. Fungsi `recommend_song(song_title, df, similarity_matrix, top_n)` , digunakan untuk menerima input pengguna dan mengembalikan top_n lagu yang terdekat berdasarkan kemiripan fitur. 

Contoh hasil top-n :
| No. | Track Name                   | Artist Name         | Genre   |
|-----|------------------------------|----------------------|---------|
| 0   | Land Of The Free             | The Killers          | Rock    |
| 1   | Safe and Sound               | Christian Leave      | Indie   |
| 2   | somethingfartoogoodtofeel    | The Japanese House   | R&B     |
| 3   | Have A Little Faith In Me    | John Hiatt           | Folk    |
| 4   | Asshole                      | Denis Leary          | Comedy  |



## Evaluation
Untuk evaluasi dari performa sistem rekomendasi ini dilakukan dengan menggunakan metriks Precision@5 dan Recall@5. Kedua metriks ini mengevaluasi seberapa relevan hasil rekomendasi berdasarkan genre lagunya. 

- Precision@5 : mengukur propoersi dari 5 rekomendasi teratas yang memiliki genre sama dengan lagu input.    
`precision = sum(relevant_indecis)/k`
- Recall@5 : mengukur seberapa besar proporsi lagu segenre yang berhasil direkomendasikan 
`recall = sum(relevant_indecis)/total_relevant if total_relevant > 0 else 0`

**Rubrik/Kriteria Tambahan (Opsional)**: 
hasil dari pengujian 3 lagu :

- Precision for Crave It Still: 0.0
- Recall for Crave It Still: 0.0
- Precision for I Knew You Were Trouble: 0.0
- Recall for I Knew You Were Trouble: 0.0
- Precision for Define Dancing: 0.4
- Recall for Define Dancing: 0.047619047619047616

Dari hasil evaluasi ini menunjukkan bahwa untuk beberapa lagu masi kurang optimal tetapi untuk lagu Define Dancing mendapatkan hasil yang lebih baik. 

Meskipun sistem rekomendasi dibangun dengan menggunakan fitur-fitur audio tetapi evaluasi dilakukan dengan menggunakan fitur genre karenan kesamaan genre dapat mencerminkan kemiripan karakteristik audio dalam konteks yang umum. 

**---Ini adalah bagian akhir laporan---**


