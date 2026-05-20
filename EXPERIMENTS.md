# Experiment Log

Catat setiap percobaan hyperparameter di sini. **Minimal 5 eksperimen.**

> Tips: ubah **satu hyperparameter pada satu waktu** agar bisa mengisolasi efeknya. Setelah memahami efek tiap variabel, baru gabungkan untuk hasil terbaik.

---

## 📋 Tabel Ringkasan

Isi tabel ini setelah selesai semua eksperimen.

| # | Hidden | Neurons | Activation | Optimizer | LR     | Batch | Epochs | Dropout | Test Acc | Train Time |
|---|--------|---------|------------|-----------|--------|-------|--------|---------|----------|------------|
| 0 | 1      | 64      | relu       | sgd       | 0.01   | 32    | 10     | 0.0     | ~85%     | ~30s       |
| 1 | 2      | 128     | relu       | adam      | 0.01   | 64    | 15     | 0.0     | 85.83%   | 66.6s      |
| 2 | 2      | 64      | tanh       | adam      | 0.1    | 16    | 20     | 0.2     | 10.00%   | 193.8s     |
| 3 | 3      | 256     | relu       | adamax    | 0.0001 | 32    | 30     | 0.2     | 88.11%   | 498.1s     |
| 4 | 3      | 512     | relu       | adam      | 0.001  | 128   | 30     | 0.2     | 89.51%   | 397.5      |
| 5 | 3      | 256     | relu       | adam      | 0.0005 | 128   | 40     | 0.2     | 89.23%   | 281.1      |

> **Eksperimen #0** = baseline (jangan ubah, ini patokan kalian).

---

## 🧪 Detail Setiap Eksperimen

Gunakan template di bawah untuk SETIAP eksperimen.

---

### Eksperimen #1

**Apa yang diubah dari baseline:**
> Mengganti optimizer dari SGD → Adam, menambah hidden layer dari 1 → 2, dan meningkatkan neuron dari 64 → 128, serta menaikkan learning rate menjadi 0.01.

**Hipotesis sebelum run:**
> Adam adalah optimizer adaptif yang biasanya lebih stabil dibanding SGD, sehingga diharapkan konvergensi lebih cepat dan akurasi meningkat.

**Hasil:**
- Test accuracy: 85.83%
- Train accuracy: 88.18%
- Validation accuracy: 86.33%
- Train time: 66.6 detik
- Apakah overfit/underfit? Cukup fit

**Observasi & Insight:**
> Penggantian SGD ke Adam meningkatkan stabilitas training. Namun learning rate 0.01 sedikit besar untuk Adam sehingga peningkatan akurasi masih terbatas.

**Rencana eksperimen berikutnya:**
> Menurunkan learning rate ke 0.001 agar Adam bekerja lebih optimal.

---

### Eksperimen #2

**Apa yang diubah:**
> Mengganti activation dari ReLU → tanh, mengecilkan batch size menjadi 16, menambah epoch menjadi 20, serta menambahkan dropout 0.2.

**Hipotesis:**
> tanh dapat membantu jika data sudah dinormalisasi, namun kemungkinan training lebih lambat. Dropout diharapkan mengurangi overfitting.

**Hasil:**
- Test accuracy: 10.00%
- Train accuracy: 9.89%
- Validation accuracy: 10.32%
- Train time: 193.8 detik
- Apakah overfit/underfit? Underfitting berat

**Observasi:**
> Model gagal belajar dengan baik. Kombinasi learning rate terlalu besar (0.1) + tanh menyebabkan training tidak stabil. Akurasi mendekati random guess (10% untuk 10 kelas).

**Rencana eksperimen berikutnya:**
> Kembali ke ReLU, menurunkan learning rate, dan menggunakan Adam sebagai optimizer.
---

### Eksperimen #3

**Apa yang diubah:**
> Meningkatkan kompleksitas model dengan menambah hidden layer menjadi 3, neuron menjadi 256, mengganti optimizer ke Adamax, serta menurunkan learning rate menjadi 0.0001, dan menambahkan dropout 0.2.

**Hipotesis:**
> Model yang lebih dalam dengan learning rate kecil akan meningkatkan kemampuan generalisasi dan mengurangi overfitting.

**Hasil:**
- Test accuracy: 88.11%
- Train accuracy: 89.30%
- Validation accuracy: 88.73%
- Train time: 498.1 detik
- Apakah overfit/underfit? Cukup fit

**Observasi:**
> Model dengan 3 hidden layer dan 256 neuron mulai mampu menangkap pola lebih kompleks dibanding baseline. Namun learning rate yang sangat kecil (0.0001) membuat proses learning lambat (slow convergence). Walaupun akurasi sudah meningkat hingga ~88%, training time sangat lama (498 detik) menunjukkan efisiensi kurang optimal.

**Rencana eksperimen berikutnya:**
> Meningkatkan learning rate ke 0.001 dan mencoba Adam optimizer untuk mempercepat konvergensi tanpa mengorbankan akurasi.
---

### Eksperimen #4

**Apa yang diubah:**
> Menggunakan 3 hidden layer dengan 512 neuron, activation ReLU, optimizer Adam, learning rate 0.001, batch size 128, epoch 30, serta dropout 0.2.

**Hipotesis:**
> Dengan kapasitas model yang lebih besar (512 neuron), model diharapkan mampu menangkap fitur visual Fashion-MNIST lebih detail sehingga akurasi meningkat. Adam dengan learning rate 0.001 diperkirakan lebih stabil dibanding Adamax pada eksperimen sebelumnya.

**Hasil:**
- Test accuracy: 89.51%
- Train accuracy: 92.58%
- Validation accuracy: 89.35%
- Train time: 397.5 detik
- Apakah overfit/underfit? Cukup fit (sedikit overfitting ringan)

**Observasi:**
> Peningkatan neuron ke 512 memberikan kenaikan akurasi dibanding eksperimen sebelumnya. Namun training time menjadi lebih lama. Dropout 0.2 cukup membantu menahan overfitting meskipun gap train-validation mulai terlihat.

**Rencana eksperimen berikutnya:**
> Mengurangi neuron menjadi 256 tetapi mempertahankan Adam dan dropout untuk melihat apakah performa bisa tetap stabil dengan waktu training lebih efisien.
---

### Eksperimen #5

**Apa yang diubah:**
> Menggunakan 3 hidden layer dengan 256 neuron, activation ReLU, optimizer Adam, learning rate 0.0005, batch size 128, epoch 40, serta dropout 0.2.

**Hipotesis:**
> Menurunkan neuron dari 512 → 256 tetapi menambah epoch dan menjaga learning rate kecil diharapkan memberikan keseimbangan antara akurasi dan efisiensi training.

**Hasil:**
- Test accuracy: 89.23%%
- Train accuracy: 93.94%
- Validation accuracy: 89.43%
- Train time: 281.1 detik
- Apakah overfit/underfit? Cukup fit (paling stabil)

**Observasi:**
> Model ini memberikan trade-off terbaik antara akurasi dan waktu training. Walaupun sedikit lebih rendah dari eksperimen #4, model ini lebih efisien dan stabil dengan gap train-validation yang kecil.
---

## 🏆 Konfigurasi Terbaik

Setelah semua eksperimen, salin konfigurasi terbaik kalian ke sini:

```python
HIDDEN_LAYERS     = 3
NEURONS_PER_LAYER = 256
ACTIVATION        = relu
DROPOUT_RATE      = 0.2
OPTIMIZER         = adam
LEARNING_RATE     = 0.0005
BATCH_SIZE        = 128
EPOCHS            = 40
```

**Test accuracy final: 89.23%**
