# 🚀 Hacettepe Datathon: Industrial Level Prediction (Ranked 25/46)

Bu depo, **Hacettepe Datathon** kapsamında sunulan endüstriyel bir veri setinde tank sıvı seviyesi (`bk_level`) tahmini yapmak için geliştirilen makine öğrenmesi modelini içermektedir. Yarışmaya **tek başıma** katılarak 46 takım arasından **25. sırayı** elde ettim.

## 📊 Proje Özeti
Projenin temel amacı; bir üretim tesisindeki vanaların durumları (açık/kapalı), makine ID'leri ve zaman damgalarını kullanarak anlık sıvı seviyesini en düşük **MAE (Mean Absolute Error)** ile tahmin etmektir. Veri seti, gerçek dünya üretim senaryolarından türetilmiş gürültülü ve karmaşık bir yapıya sahiptir.



---

## 🛠️ Teknik Yaklaşım ve Özellik Mühendisliği (Feature Engineering)

Modelin başarısındaki temel faktör, verideki sızıntıyı (leakage) önlemek ve endüstriyel sürecin fiziksel kurallarını koda dökmektir:

### 1. Zaman Sızıntısı ve Doğrulama Stratejisi
* **Data Leakage Kontrolü:** Eğitim setindeki hatalı (bk_level = 0) veriler temizlenerek modelin yanlış veriden öğrenmesi engellendi.
* **GroupKFold (5-Fold):** Veriler `batchkey` bazında gruplandı. Bu sayede model, eğitim sırasında gördüğü bir üretim sürecini (batch) test sırasında asla görmeyerek "gerçek dünya" performansını simüle etti. Bu, zaman serisi projelerinde aşırı öğrenmeyi (overfitting) engelleyen en kritik adımdır.

### 2. Gelişmiş Özellik Türetimi
* **Kümülatif Toplamlar (Cumsum):** Hızlı ve yavaş dozaj vanalarının toplam çalışma süreleri hesaplanarak tanka giren toplam sıvı miktarı modellendi.
* **Zaman Pencereleri (Rolling Windows):** Son 10 ve 30 adımlık hareketli ortalamalar (`rolling mean`) ile sistemin anlık değişim hızı ve "ivmesi" yakalandı.
* **Durum Değişimleri (Diff):** Vanaların açılma/kapanma anları `diff()` fonksiyonu ile saptandı.
* **Domain-Specific Logic:** Transfer ve dozaj komutları (`commandno`) ayrıştırılarak makine bazlı özel durumlar (Machine 243 gibi) sisteme özellik olarak eklendi.

### 3. Model Hiperparametreleri
* **Algoritma:** LightGBM Regressor
* **Metrik:** `objective='mae'` (Yarışma değerlendirme kriteri olan MAE'ye doğrudan optimize edildi).
* **Early Stopping:** Model hata yapmaya başladığında eğitimi durduran 50 raundluk sabır süresi eklendi.



---

## 📂 Dosya Yapısı
* `main.py`: Modelin eğitim, özellik mühendisliği ve tahmin süreçlerini içeren ana Python scripti.
* `README.md`: Proje dokümantasyonu.
* `sonuc_submission.csv`: Yarışmaya gönderilen final tahmin çıktıları.

---

## 🚀 Nasıl Çalıştırılır?

1. Gerekli kütüphaneleri yükleyin:
   ```bash
   pip install pandas numpy lightgbm scikit-learn matplotlib
