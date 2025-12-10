# Laporan Analisis Data Eksplorasi Dataset Titanic

Laporan ini menyajikan hasil analisis data Titanic, terdiri dari penjelasan persiapan data, pemrosesan, visualisasi untuk menemukan insight, pemodelan klasifikasi, serta kesimpulan utama.

---

## Persiapan Data (Data Preparation)

Tahap ini memastikan data bersih dan siap untuk analisis lanjutan dan pemodelan prediktif.

* **Pemasukan Data:** Dataset Titanic dimuat menggunakan *CSV Reader* untuk membaca data mentah.

* **Penanganan Missing Values:** Node *Missing Value* digunakan untuk mengimputasi nilai kosong pada kolom seperti `Age`, `Embarked`, dan lainnya. Nilai akan diisi dengan nilai rata-rata atau frekuensi tertinggi

* **Rekayasa Fitur (Feature Engineering):**
  Konversi tipe data dilakukan dengan *Number to String* untuk mempermudah visualisasi. Node **Rule Engine** digunakan untuk menghasilkan 3 fitur turunan:

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

* **Pemilihan Kolom:** Node *Column Filter* digunakan untuk mengambil fitur-fitur yang relevan untuk analisis seperti `Pclass`, `Sex`, `Age`, `Fare`, `Embarked`, dan fitur-fitur hasil Rule Engine.

---

## **Workflow KNIME**

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
---

## Pemrosesan Data (Data Processing)

Tahap ini berfokus pada transformasi data untuk analisis statistik dan persiapan modeling.

* **Agregasi:** Node *GroupBy* digunakan untuk menghitung rata-rata persentase selamat berdasarkan kategori seperti kelas, gender, dan kelompok usia.
![Pengaturan Node GroupBy Group Column](Visualisasi_Titanic/Setting%20GroupBy%20Node.png)
![Pengaturan Node GroupBy Agregrasi Mean Survived](Visualisasi_Titanic/Setting%20GroupBy%20Node_1.png)

* **Pembagian Data:** Node *Table Partitioner* memisahkan data menjadi training dan testing sehingga model dapat diuji secara adil.

* **Statistik Deskriptif:** Node *Statistics* memberikan ringkasan statistik numerik seperti mean usia, tarif, dan sebaran umum populasi.

---

## Visualisasi dan Interpretasi (Visualization and Interpretation)

Visualisasi membantu memahami pola dalam dataset dan menghubungkannya dengan tingkat kelangsungan hidup penumpang (`Survived`).

---

### Distribusi Fitur Utama

#### Pie Chart: Distribusi Penumpang yang Selamat

![Pie Chart: Distribusi Penumpang yang Selamat](Visualisasi_Titanic/Pie%20Chart_%20Distribusi%20Penumpang%20yang%20Selamat.png)

Sebagian besar penumpang tidak selamat dari bencana Titanic.

---

#### Pie Chart: Distribusi Class (Kelas)

![Pie Chart: Distribusi Class (Kelas)](Visualisasi_Titanic/Pie%20Chart_%20Distribusi%20Class%20(Kelas).png)

Mayoritas penumpang berada di **Kelas 3**, menunjukkan dominasi kelas ekonomi. Banyaknya penumpang kelas 3 bisa menjelaskan mengapa angka kematian Titanic tinggi.

---

#### Pie Chart: Distribusi Gender

![Pie Chart: Distribusi Gender](Visualisasi_Titanic/Pie%20Chart_%20Distribusi%20Gender.png)

Jumlah penumpang pria lebih banyak dibandingkan wanita. Ketidakseimbangan ini memberikan gambaran awal tentang mengapa mayoritas korban Titanic adalah pria.

---

### Hubungan Antar Fitur dengan Kelangsungan Hidup

#### Bar Chart: Gender VS Tingkat Keselamatan

![Bar Chart: Gender VS Tingkat Keselamatan](Visualisasi_Titanic/Bar%20Chart_%20Gender%20VS%20Tingkat%20Keselamatan.png)

Wanita memiliki survival rate **~74%**, sedangkan pria hanya **~19%**.
Ini adalah indikator paling kuat dari semua faktor, Gender adalah prediktor kelangsungan hidup yang paling kuat. Temuan ini sangat mendukung kebijakan "wanita dan anak-anak dahulu" (women and children first).

---

#### Box Plot: Distribusi Tarif

![Box Plot: Distribusi Tarif VS Class (Kelas)](Visualisasi_Titanic/Box%20Plot_%20Distribusi%20Tarif%20VS%20Class%20(Kelas).png)

Tarif (Fare) berkorelasi langsung dengan Kelas penumpang, di mana Kelas 1 memiliki median tarif tertinggi dan variabilitas harga yang paling besar. Sebaliknya, Kelas 3 memiliki tarif termurah dan tersempit, mengindikasikan tarif yang seragam dan terjangkau.

---

#### Bar Chart: Class (Kelas) VS Tingkat Keselamatan

![Bar Chart: Class (Kelas) VS Tingkat Keselamatan](Visualisasi_Titanic/Bar%20Chart_%20Class%20(Kelas)%20VS%20Tingkat%20Keselamatan.png)

Survival rate menurun seiring turunnya kelas:

* Kelas 1 → ~63%
* Kelas 2 → ~47%
* Kelas 3 → ~24%

Ini menunjukkan adanya korelasi kuat antara status sosial/ekonomi dan peluang keselamatan; semakin tinggi kelas (semakin mahal tiketnya), semakin besar peluang untuk selamat. Tingkat keselamatan penumpang Kelas 1 berada di kisaran 63%, sementara penumpang Kelas 3 hanya memiliki peluang selamat sekitar 23%. Hal ini mengindikasikan adanya bias prioritas penyelamatan atau akses yang lebih baik ke sekoci untuk penumpang kelas atas.

---

#### Bar Chart: Kelompok Usia VS Tingkat Keselamatan

![Bar Chart: Kelompok Usia VS Tingkat Keselamatan](Visualisasi_Titanic/Bar%20Chart_%20Kelompok%20Usia%20VS%20Tingkat%20Keselamatan.png)

Anak-anak memiliki survival tertinggi (~58%), sedangkan kelompok lansia memiliki survival terendah (~9%).
Grafik ini menunjukkan bahwa usia adalah dalam peluang keselamatan, dengan Anak-anak memiliki tingkat kelangsungan hidup tertinggi, mendekati 58%. Sebaliknya, Lansia memiliki tingkat keselamatan terendah di antara semua kategori, hanya sekitar 9%, mengindikasikan bahwa lansia adalah kelompok yang paling rentan. Hal ini memverifikasi bahwa kebijakan "wanita dan anak-anak dahulu" diterapkan dengan ketat, tetapi juga menyoroti kerentanan kelompok usia tertua.

---

#### Heatmap: Gender, Class, dan Survival

![Heatmap: Gender, Class (Kelas), dan Tingkat Keselamatan](Visualisasi_Titanic/Heatmap_%20Gender,%20Class%20(Kelas),%20dan%20Tingkat%20Keselamatan.png)

Pola paling menonjol:

* Wanita kelas 1 → peluang selamat paling tinggi.
* Pria kelas 3 → peluang selamat paling rendah.
* Gender + Kelas = kombinasi paling menentukan survival.

Heatmap ini menunjukkan bahwa Gender adalah faktor dominan dalam tingkat keselamatan, penumpang wanita di semua kelas memiliki probabilitas selamat yang sangat tinggi (diwakili warna merah tua), sedangkan penumpang pria di semua kelas memiliki peluang selamat yang sangat rendah (diwakili warna kuning pucat). Prioritas penyelamatan diberikan secara ketat berdasarkan Gender, dengan Wanita Kelas 1 dan 2 memiliki peluang terbaik untuk selamat di seluruh kapal.

---

## Klasifikasi Data: Decision Tree

Tahap ini bertujuan memprediksi `Survived` menggunakan model *Decision Tree*.

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
* Pohon mencari informasi paling kuat untuk membuat keputusan, menggunakan indeks Gini.
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

Berikut adalah ringkasan poin-poin kunci yang didapatkan dari Eksplorasi Data (EDA) Titanic dan pemodelan Klasifikasi.

### 1. Gender (Jenis Kelamin) Adalah Prediktor Utama

* **Dominasi Mutlak:** **Jenis Kelamin** adalah faktor yang paling dominan dan terpenting dalam memprediksi kelangsungan hidup.
* **Peluang Selamat:** Penumpang **wanita** memiliki tingkat keselamatan yang sangat tinggi (sekitar **74%**), secara langsung mendukung kebijakan **"wanita dan anak-anak dahulu"**.
* **Peluang Rendah:** Penumpang pria memiliki tingkat keselamatan yang sangat rendah (sekitar **19%**), terlepas dari kelas tiket mereka.
* **Validasi Model:** Model Decision Tree mengonfirmasi hal ini dengan menempatkan pemisahan pertama (root split) pada fitur **`Gender`**.

### 2. Status Sosial/Ekonomi Memengaruhi Peluang Selamat

* **Hierarki Kelas:** Terdapat korelasi kuat antara **Kelas Tiket** dan peluang keselamatan, menunjukkan adanya bias prioritas.
* **Peluang Tertinggi:** Penumpang **Kelas 1** memiliki peluang selamat tertinggi (sekitar **63%**).
* **Peluang Terendah:** Penumpang **Kelas 3** memiliki peluang selamat terendah (sekitar **23%**), meskipun mereka merupakan kelompok penumpang terbesar.
* **Tarif:** Analisis *Box Plot* mengonfirmasi bahwa **Tarif (Fare)** berkorelasi langsung dengan Kelas (Kelas 1 memiliki tarif termahal dan paling bervariasi).

### 3. Usia Memainkan Peran Kritis

* **Prioritas Anak-anak:** Kelompok **Anak-anak (Child)** menunjukkan tingkat keselamatan yang tinggi (sekitar **58%**), menandakan prioritas penyelamatan yang diberikan kepada usia muda.
* **Kelompok Paling Rentan:** Kelompok **Lansia (Elderly)** memiliki tingkat keselamatan paling rendah di seluruh dataset (sekitar **9%**), menyoroti kerentanan mereka.

### 4. Kualitas dan Keterbatasan Model

* **Akurasi:** Model Decision Tree mencapai akurasi sekitar **77%** pada data pengujian.
* **Kesimpulan Pemodelan:** Akurasi ini menunjukkan bahwa *insight* yang diperoleh dari EDA (yakni, fokus pada **Gender** dan **Kelas**) sudah cukup untuk membangun model prediktif yang andal.


---


