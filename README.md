# Ulusal Faktoring — Kredi Riski Tahmin Modeli

Çek temerrüt olasılığını tahmin eden, Optuna ile optimize edilmiş 3-model ensemble (LightGBM + CatBoost + XGBoost) çözümü.

## Proje Özeti

| | |
|---|---|
| **Hedef** | Çeklerin vade öncesi temerrüt olasılığını tahmin etmek |
| **Veri** | ~100.000 çek, 20+ özellik, Ocak–Ekim 2024 |
| **Temerrüt Oranı** | %5.87 (16:1 dengesizlik) |
| **Birincil Metrik** | ROC-AUC (model optimizasyonu) |
| **F1 Stratejisi** | Doğrulama seti üzerinde 500 noktalı grid arama ile eşik değeri optimizasyonu |
| **Doğrulama** | Zamana dayalı bölüştürme + StratifiedKFold CV (5 katlı) |


## Metodoloji

1. **Keşifsel Analiz** — Segment keşfi (Mikro vs KOBİ), MNAR eksik veri analizi
2. **Özellik Mühendisliği** — Eksik bayrakları, alan bilgisi özellikleri, segment-özel değişkenler
3. **Ön İşleme** — Sadece eğitim setinden imputation, zamana dayalı train/val/test bölüştürme
4. **Modelleme** — Optuna ile saf AUC optimizasyonu (50 deneme × 3 model), AUC-ağırlıklı ensemble
5. **F1 Eşik Optimizasyonu** — Doğrulama setinde hassas threshold belirleme (0.5 yerine optimal eşik)
6. **Değerlendirme** — AUC, Gini, KS, PSI, SHAP, kalibrasyon analizi

## Temel Teknik Kararlar

- **Skorlama**: Optuna saf AUC ile optimize eder; F1 ayrı bir adımda eşik değeri ayarı ile ele alınır
- **Dengesizlik**: `scale_pos_weight` tüm modellerde tutarlı şekilde kullanılır
- **Eksik Veri**: MNAR (Missing Not At Random) — eksiklik bilgilendirici sinyal olarak ele alınır
- **Veri Sızıntısı Önleme**: İmputation istatistikleri sadece eğitim setinden, threshold sadece doğrulama setinden
