# Refleksi Tim

> Jawaban dalam Bahasa Indonesia. Maksimal 1 halaman (~500 kata). Yang dinilai adalah **kedalaman pemahaman**, bukan panjang tulisan.

---

## 1. Parameter vs Hyperparameter

Berdasarkan eksperimen yang kalian lakukan, jelaskan dengan **kata-kata kalian sendiri**:
- Apa yang termasuk **parameter** dalam model kalian, dan apa yang termasuk **hyperparameter**?
- Manakah yang berubah saat training berjalan, dan manakah yang ditentukan oleh kalian sebelum training?

**Jawaban:**
> **Parameter** adalah nilai-nilai internal model yang dipelajari secara otomatis selama proses training, yaitu **bobot (weights)** dan **bias** pada setiap koneksi antar neuron. Pada konfigurasi terbaik kami (3 hidden layer × 512 neuron), total parameter yang dipelajari mencapai ratusan ribu nilai — semuanya diperbarui oleh backpropagation tanpa campur tangan kami.
>
> **Hyperparameter** adalah nilai yang kami tentukan *sebelum* training dimulai, seperti `HIDDEN_LAYERS`, `NEURONS_PER_LAYER`, `LEARNING_RATE`, `BATCH_SIZE`, `EPOCHS`, `OPTIMIZER`, `ACTIVATION`, dan `DROPOUT_RATE`. Nilai-nilai ini tidak berubah selama training berjalan — mereka mengontrol *bagaimana* model belajar, bukan *apa* yang dipelajarinya.
>
> Singkatnya: **parameter berubah saat training** (diupdate tiap iterasi backprop), sedangkan **hyperparameter ditentukan oleh kami** dan tetap konstan sepanjang training.

---

## 2. Hyperparameter dengan Dampak Terbesar

Dari semua hyperparameter yang kalian eksperimen, mana yang menurut kalian memberikan **dampak paling besar** terhadap akurasi? Mengapa demikian — apa yang kalian amati pada kurva loss/accuracy?

**Jawaban:**
> Berdasarkan eksperimen kami, **Learning Rate** memberikan dampak paling drastis. Hal ini paling jelas terlihat pada Eksperimen #2, di mana kami menggunakan `learning_rate = 0.1` bersama optimizer Adam — hasilnya akurasi hanya **10%**, setara tebak acak untuk 10 kelas. Kurva loss tidak turun sama sekali, bahkan cenderung tidak stabil.
>
> Sebaliknya, saat kami menurunkan learning rate ke `0.001` (Eksperimen #4), model berhasil mencapai **89.51%** dengan kurva loss yang turun mulus dan konsisten. Ini menunjukkan bahwa learning rate terlalu besar menyebabkan gradient update "melompati" minimum, sehingga model tidak pernah konvergen.
>
> **Jumlah hidden layer dan neuron** juga berpengaruh signifikan — transisi dari 1 layer ke 3 layer meningkatkan akurasi dari ~85% ke ~89%. Namun efeknya tidak se-ekstrem learning rate yang salah konfigurasi.

---

## 3. Learning Rate

Coba set `LEARNING_RATE = 1.0` (atau bahkan lebih besar) dan jalankan sekali. Apa yang terjadi pada kurva loss? Hubungkan jawaban kalian dengan rumus:

$$W_j = W_j - \lambda \frac{\partial F(W_j)}{\partial W_j}$$

**Jawaban:**
> Ketika `LEARNING_RATE` disetel terlalu besar (seperti 0.1 pada Eksperimen #2, atau misalnya 1.0), yang terjadi pada kurva loss adalah **tidak turun atau bahkan naik secara tidak stabil**. Akurasi stagnan di sekitar 10% — persis seperti random guess.
>
> Dalam rumus $W_j = W_j - \lambda \frac{\partial F}{\partial W_j}$, nilai $\lambda$ (learning rate) menentukan **seberapa besar langkah** yang diambil saat memperbarui bobot. Jika $\lambda$ terlalu besar, bobot diperbarui dengan lompatan yang terlalu jauh sehingga justru **melewati titik minimum loss** dan berosilasi atau bahkan divergen. Model tidak pernah "mendarat" di area optimal, sehingga loss tidak pernah turun secara berarti.

---

## 4. Batch Size & Trade-off

Bandingkan eksperimen dengan **batch size kecil** (misal 16) vs **batch size besar** (misal 256). Apa yang kalian amati dari sisi waktu training, stabilitas kurva loss, dan akurasi akhir?

**Jawaban:**
> Dari perbandingan eksperimen kami:
>
> - **Batch size 16 (Eksperimen #2):** Training time sangat lama (**193.8 detik** untuk 20 epoch), dan kurva loss sangat tidak stabil karena setiap update bobot hanya berdasarkan 16 sampel sehingga gradient-nya "noisy". Akurasi akhir juga buruk (dipengaruhi faktor learning rate yang besar, tapi batch kecil memperparah instabilitas).
>
> - **Batch size 128 (Eksperimen #4 & #5):** Training lebih cepat dan efisien. Kurva loss turun dengan lebih mulus karena gradient dihitung dari lebih banyak sampel per iterasi sehingga estimasinya lebih stabil. Akurasi terbaik dicapai di konfigurasi ini (89.51%).
>
> Ini sesuai dengan teori: **batch kecil → update lebih sering tapi noisy, training lambat secara wall-clock**. **Batch besar → update lebih stabil, memanfaatkan paralelisme GPU dengan baik**, namun membutuhkan memori lebih besar. Trade-off yang kami temukan konsisten dengan slide kuliah.

---

## 5. Feed Forward & Back Propagation

Pada saat kalian menekan `model.fit(...)`, sebenarnya proses feed forward dan back propagation berjalan **ribuan kali**. Hitung kira-kira berapa kali back propagation terjadi pada salah satu eksperimen kalian.

**Jawaban:**
> Mengambil **Eksperimen #4** sebagai contoh:
> - Jumlah data training = 60.000 sampel (90% dari 60k setelah validation split = 54.000)
> - Batch size = 128
> - Epochs = 30
>
> Jumlah iterasi (steps per epoch) = 54.000 / 128 ≈ **422 iterasi/epoch**
>
> Total backpropagation = 422 × 30 = **± 12.660 kali**
>
> Artinya, dalam satu eksperimen, bobot dan bias model diperbarui lebih dari **12 ribu kali**. Pada iterasi pertama, bobot dimulai dari nilai acak kecil (inisialisasi), sehingga prediksi model sangat buruk. Setelah ribuan iterasi backprop, bobot dan bias telah disetel sedemikian rupa sehingga model mampu mengenali pola visual Fashion-MNIST dengan akurasi ~89.5% — jauh dari kondisi awal yang acak.

---

## 6. Kapan Deep Learning Tepat Digunakan?

Berdasarkan pengalaman kalian dengan Fashion-MNIST, menurut kalian apakah masalah ini *benar-benar* membutuhkan deep learning, atau bisa diselesaikan dengan machine learning klasik?

**Jawaban:**
> Secara teknis, masalah Fashion-MNIST **bisa diselesaikan dengan ML klasik** seperti Logistic Regression atau SVM, dan kemungkinan mencapai akurasi 80–85%. Namun deep learning memberikan nilai tambah yang nyata di sini karena mampu **mempelajari representasi fitur secara hierarkis** — dari tepi dan tekstur sederhana di layer awal, hingga pola yang lebih kompleks (bentuk kerah, sol sepatu, dsb.) di layer yang lebih dalam.
>
> Deep learning mulai *benar-benar dibutuhkan* ketika input berdimensi tinggi dan memiliki struktur spasial yang kompleks, misalnya gambar beresolusi tinggi, audio, atau teks. Untuk Fashion-MNIST yang "hanya" 28×28 piksel, MLP sederhana sudah cukup baik. Namun untuk dataset gambar dunia nyata (ImageNet, foto produk, medical imaging), tanpa deep learning — terutama CNN — hasil ML klasik akan jauh tertinggal.
>
> Kesimpulan: untuk Fashion-MNIST, deep learning **bukan satu-satunya pilihan**, tapi ia memberikan akurasi lebih tinggi dengan lebih sedikit feature engineering manual.

---

## 7. Refleksi Tim

- Tantangan apa yang paling sulit?
- Apa pelajaran terpenting yang kalian dapat dari aktivitas ini?
- Jika diberi waktu lebih, apa yang ingin kalian coba lagi?

**Jawaban:**
> **Tantangan terbesar** adalah memahami *mengapa* suatu kombinasi hyperparameter gagal total, seperti yang terjadi pada Eksperimen #2 (akurasi 10%). Awalnya kami menduga masalah pada arsitektur, namun setelah menganalisis lebih dalam, kami menyadari bahwa learning rate 0.1 dengan optimizer Adam adalah penyebab utamanya — Adam secara internal memiliki adaptive scaling, sehingga learning rate yang sudah besar menjadi sangat tidak stabil.
>
> **Pelajaran terpenting:** Hyperparameter tuning bukan soal mencoba semua kombinasi secara acak, tapi tentang **memahami efek kausal satu variabel pada satu waktu**. Dengan mengubah satu hyperparameter per eksperimen, kami bisa benar-benar mengisolasi dampaknya. Selain itu, kami belajar bahwa model yang lebih besar (lebih banyak neuron/layer) tidak selalu lebih baik — efisiensi dan generalisasi juga penting.
>
> **Jika diberi waktu lebih**, kami ingin mencoba: (1) **Learning Rate Scheduler** seperti cosine annealing atau ReduceLROnPlateau agar learning rate turun otomatis seiring training; (2) **Early Stopping** untuk menghindari overfitting pada epoch tinggi; (3) **Batch Normalization** di antara layer untuk mempercepat konvergensi; dan (4) arsitektur **CNN** yang seharusnya jauh lebih optimal untuk data gambar dibanding MLP flat.
