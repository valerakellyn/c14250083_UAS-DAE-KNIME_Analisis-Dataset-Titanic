# 🚢 Laporan Analisis Data Eksplorasi Dataset Titanic

Laporan ini menyajikan hasil analisis data Titanic, berfokus pada langkah-langkah persiapan data, pemrosesan, visualisasi untuk menemukan insight, pemodelan klasifikasi, serta kesimpulan utama berdasarkan workflow KNIME.

---

## Persiapan Data (Data Preparation)

Tahap ini memastikan data bersih dan siap untuk analisis lanjutan dan pemodelan prediktif.

* **Pemasukan Data:** Dataset Titanic dimuat menggunakan *CSV Reader* untuk membaca data mentah.

* **Penanganan Missing Values:** Node *Missing Value* digunakan untuk mengimputasi nilai kosong pada kolom seperti `Age`, `Embarked`, dan lainnya.

* **Rekayasa Fitur (Feature Engineering):**
  Konversi tipe data dilakukan dengan *Number to String* untuk mempermudah visualisasi. Node **Rule Engine** digunakan untuk menghasilkan tiga fitur turunan:

  ### Aturan Rule Engine

  Rule ini dibuat untuk menghasilkan fitur `Sex_num`, `Embarked_num`, dan `Age_group`.

  **1. Konversi Gender ke Numerik (`Sex_num`)**

  ```
  $Sex$ = "male" => 0
  $Sex$ = "female" => 1
  ```

  **2. Konversi Embarked ke Numerik (`Embarked_num`)**

  ```
  $Embarked$ = "S" => 0
  $Embarked$ = "C" => 1
  $Embarked$ = "Q" => 2
  ```

  **3. Pengelompokan Usia (`Age_group`)**

  ```
  $Age$ <= 14 => "Child"
  $Age$ <= 24 => "Youth"
  $Age$ <= 64 => "Adult"
  TRUE => "Elderly"
  ```

  Fitur-fitur ini mempermudah analisis pola dan meningkatkan performa model klasifikasi.

* **Pemilihan Kolom:** Node *Column Filter* digunakan untuk mengambil fitur-fitur yang relevan seperti `Pclass`, `Sex`, `Age`, `Fare`, `Embarked`, dan fitur-fitur hasil Rule Engine.

---

## **Workflow KNIME (Structured with Numbering + Branches)**

Berikut struktur workflow dalam bentuk penomoran dengan percabangan (hierarchical tree):

```
1. CSV Reader
   |
   ├── 2. Missing Value
   |       |
   |       └── 3. Column Filter
   |               |
   |               └── 4. Rule Engine
   |                       |
   |                       └── 5. Number to String
   |                               |
   |                               ├── 6. Branch: Exploratory Data Analysis (EDA)
   |                               |       |
   |                               |       ├── 6.1 GroupBy → Pie Chart: Distribusi Class
   |                               |       ├── 6.2 GroupBy → Pie Chart: Distribusi Gender
   |                               |       ├── 6.3 GroupBy → Pie Chart: Survivors vs Non-survivors
   |                               |       ├── 6.4 GroupBy → Bar Chart: Gender vs Survival
   |                               |       ├── 6.5 GroupBy → Bar Chart: Class vs Survival
   |                               |       ├── 6.6 GroupBy → Bar Chart: Age Group vs Survival
   |                               |       ├── 6.7 GroupBy → Heatmap Gender–Class–Survival
   |                               |       └── 6.8 GroupBy → Box Plot: Fare vs Class
   |                               |
   |                               └── 7. Branch: Classification Modeling
   |                                       |
   |                                       ├── 7.1 Table Partitioner (Train–Test Split)
   |                                       |       |
   |                                       |       ├── 7.2 Decision Tree Learner
   |                                       |       |        |
   |                                       |       |        └── 7.3 Decision Tree Predictor
   |                                       |       |                 |
   |                                       |       |                 └── 7.4 Scorer (Accuracy = 0.77)
   |                                       |       |
   |                                       └── (Test dataset passes into Predictor)
   |
   └── Workflow End
```

Workflow ini menampilkan dua alur besar: **EDA** dan **Modeling**, yang berangkat dari node yang sama yaitu **Number to String**.

---

## Pemrosesan Data (Data Processing)

Tahap ini berfokus pada transformasi data untuk analisis statistik dan persiapan modeling.

* **Agregasi:** Node *GroupBy* digunakan untuk menghitung rata-rata persentase selamat berdasarkan kategori seperti kelas, gender, dan kelompok usia.

* **Pembagian Data:** Node *Table Partitioner* memisahkan data menjadi training dan testing sehingga model dapat diuji secara adil.

* **Statistik Deskriptif:** Node *Statistics* memberikan ringkasan statistik numerik seperti mean usia, minimum tarif, dan sebaran umum populasi.

---

## Visualisasi dan Interpretasi (Visualization and Interpretation)

Visualisasi membantu memahami pola dalam dataset dan menghubungkannya dengan tingkat kelangsungan hidup penumpang (`Survived`).
Bagian ini juga sudah dilengkapi dengan analisis mendalam untuk setiap visualisasi.

---

### Distribusi Fitur Utama

#### Pie Chart: Distribusi Class (Kelas)

![Pie Chart: Distribusi Class (Kelas)](Visualisasi_Titanic/Pie%20Chart_%20Distribusi%20Class%20(Kelas).png)

Mayoritas penumpang berada di **Kelas 3**, menunjukkan dominasi kelas ekonomi. Banyaknya penumpang kelas 3 menjelaskan mengapa angka kematian Titanic sangat tinggi, karena kelompok terbesar justru memiliki tingkat keselamatan paling rendah.

---

#### Pie Chart: Distribusi Gender

![Pie Chart: Distribusi Gender](Visualisasi_Titanic/Pie%20Chart_%20Distribusi%20Gender.png)

Jumlah penumpang pria lebih banyak dibandingkan wanita. Ketidakseimbangan ini memberikan gambaran awal tentang mengapa mayoritas korban Titanic adalah pria.

---

#### Pie Chart: Distribusi Penumpang yang Selamat

![Pie Chart: Distribusi Penumpang yang Selamat](Visualisasi_Titanic/Pie%20Chart_%20Distribusi%20Penumpang%20yang%20Selamat.png)

Sebagian besar penumpang tidak selamat. Proporsi kelompok selamat yang kecil menunjukkan betapa kurang efektifnya proses evakuasi Titanic.

---

### Hubungan Antar Fitur dengan Kelangsungan Hidup

#### Bar Chart: Gender VS Tingkat Keselamatan

![Bar Chart: Gender VS Tingkat Keselamatan](Visualisasi_Titanic/Bar%20Chart_%20Gender%20VS%20Tingkat%20Keselamatan.png)

Wanita memiliki survival rate **~74%**, sedangkan pria hanya **~19%**.
Ini adalah indikator paling kuat dari semua faktor — kebijakan *women and children first* sangat terlihat jelas di data.

---

#### Bar Chart: Class (Kelas) VS Tingkat Keselamatan

![Bar Chart: Class (Kelas) VS Tingkat Keselamatan](Visualisasi_Titanic/Bar%20Chart_%20Class%20(Kelas)%20VS%20Tingkat%20Keselamatan.png)

Survival rate menurun seiring turunnya kelas:

* Kelas 1 → ~63%
* Kelas 2 → ~47%
* Kelas 3 → ~24%

Hal ini sesuai dengan letak kabin dan akses fisik ke dek sekoci.

---

#### Bar Chart: Kelompok Usia VS Tingkat Keselamatan

![Bar Chart: Kelompok Usia VS Tingkat Keselamatan](Visualisasi_Titanic/Bar%20Chart_%20Kelompok%20Usia%20VS%20Tingkat%20Keselamatan.png)

Anak-anak memiliki survival tertinggi (~58%), sedangkan kelompok lansia memiliki survival terendah (~9%).
Mobilitas dan prioritas penyelamatan menjadi faktor utamanya.

---

#### Heatmap: Gender, Class, dan Survival

![Heatmap: Gender, Class (Kelas), dan Tingkat Keselamatan](Visualisasi_Titanic/Heatmap_%20Gender,%20Class%20(Kelas),%20dan%20Tingkat%20Keselamatan.png)

Pola paling menonjol:

* Wanita kelas 1 → peluang selamat paling tinggi.
* Pria kelas 3 → peluang selamat paling rendah.
* Gender + Kelas = kombinasi paling menentukan survival.

---

#### Box Plot: Distribusi Tarif

![Box Plot: Distribusi Tarif VS Class (Kelas)](Visualisasi_Titanic/Box%20Plot_%20Distribusi%20Tarif%20VS%20Class%20(Kelas).png)

Fare meningkat drastis pada kelas 1, dengan banyak outlier tarif mahal. Hal ini menunjukkan adanya variasi tipe kabin mewah dan menjelaskan gap sosial-ekonomi di kapal.

---

## Klasifikasi Data: Decision Tree

Tahap ini bertujuan memprediksi `Survived` menggunakan model *Decision Tree*. Penjelasan ini telah diperluas secara detail.

---

### Proses Pemodelan

* **Train–test split 70:30**
* **Decision Tree Learner** dilatih menggunakan fitur:

  * `Pclass`
  * `Sex_num`
  * `Age`
  * `Fare`
  * `SibSp`
  * `Parch`
  * `Embarked_num`
* Pohon mencari informasi paling kuat untuk membuat keputusan, menggunakan indeks Gini atau Entropy.
* **Decision Tree Predictor** digunakan untuk menghasilkan prediksi.
* **Scorer** menghitung akurasi model.

---

### Analisis Model

* **Akurasi Model = 0.77 (77%)**
  Cukup baik untuk model sederhana tanpa tuning.

* **Root Node = `Sex_num`**
  Gender adalah faktor paling kuat. Pola yang ditemukan:

  ```
  Jika Sex_num = 1 (female) → Peluang selamat sangat tinggi
  Jika Sex_num = 0 (male):
        Jika Pclass = 3 → Hampir pasti tidak selamat
        Jika Pclass = 1 → Peluang selamat meningkat
  ```

Pola ini sangat konsisten dengan seluruh hasil EDA dan visualisasi.

---

## Konklusi Utama

* **Gender adalah faktor paling dominan**
  Wanita memiliki peluang jauh lebih tinggi untuk selamat.

* **Kelas sosial sangat memengaruhi keselamatan**
  Penumpang kelas 1 berada pada posisi paling menguntungkan.

* **Usia memengaruhi peluang selamat**
  Anak-anak adalah prioritas penyelamatan setelah wanita.

* **Model Decision Tree efektif dengan aturan sederhana**
  Hanya dengan `Sex` dan `Pclass`, model sudah bisa mencapai akurasi 77%.

---


