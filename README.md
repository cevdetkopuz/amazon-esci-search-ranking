Markdown# Amazon ESCI: E-Commerce Search & Ranking Optimization

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![dbt](https://img.shields.io/badge/dbt-FF694B?style=flat-square&logo=dbt&logoColor=white)
![BigQuery](https://img.shields.io/badge/Google%20BigQuery-4285F4?style=flat-square&logo=google-cloud&logoColor=white)
![NLP](https://img.shields.io/badge/NLP-XLM--RoBERTa-green?style=flat-square)
![Tableau](https://img.shields.io/badge/Visualization-Tableau-e97627?style=flat-square&logo=tableau&logoColor=white)

**Analytics Engineering ve NLP teknikleri kullanılarak "Kullanıcı neyi aradı ve biz neyi gösterdik?" sorusuna yanıt veren uçtan uca sıralama optimizasyonu projesi.**

---

| [Executive Summary](#executive-summary-star-method) | [Technical Architecture](#technical-architecture) | [Repository Structure](#repository-structure) | [Setup & Usage](#setup--usage) | [Key Results](#key-results--analysis) |
| :--- | :--- | :--- | :--- | :--- |

---

</div>

## Executive Summary (STAR Method)

Modern e-ticarette kullanıcıların %60'ından fazlası tam ürün adını bilmeden arama yapmaktadır. Geleneksel anahtar kelime eşleştirme algoritmaları bu anlamsal niyeti yakalayamamakta, bu da alakasız sonuçlara ve gelir kaybına neden olmaktadır. Bu proje, Amazon ESCI (Exact, Substitute, Complement, Irrelevant) veri setini kullanarak bu anlamsal açığı kapatmayı hedefler.

| Aşama | Detaylar |
| :--- | :--- |
| **Situation** | Çok dilli (ABD, JP, ES) e-ticaret veri setinin analizi, sorgular ve sonuçlar arasında anlamsal anlamdan ziyade kelime benzerliğine dayalı bir kopukluk olduğunu ortaya koymuştur. |
| **Task** | Sıralama kalitesini artırmak için kullanıcı sorgusu ile ürün arasındaki ilişkiyi dört alaka düzeyinde (Exact, Substitute, Complement, Irrelevant) sınıflandırabilen bir sistem tasarlamak. |
| **Action** | **Analytics Engineering:** Google BigQuery üzerinde dbt kullanarak 2.6M+ satırlık ham veriyi Staging, Intermediate ve Marts katmanlarına dönüştüren ELT pipeline orkestrasyonu. <br> **NLP Modeling:** Çok dilli XLM-RoBERTa Transformer modelinin anlamsal benzerlik skorları üretmek üzere eğitilmesi. |
| **Result** | Model, alakalı ve alakasız ürünleri ayırt etmede yüksek başarı göstermiştir. Japonya ve İspanya yerelleştirmelerinde sıralama doğruluğu temel yöntemlere göre önemli ölçüde artırılmıştır. |

---

## Technical Architecture

Proje, ölçeklenebilirlik için Google Cloud Platform ve modüler veri dönüşümü için dbt kullanarak modern bir veri yığını mimarisini takip eder.

```mermaid
flowchart TD
    subgraph Raw_Data [Data Ingestion]
        A[("Amazon ESCI Dataset")] -->|Load| B(Google Cloud Storage)
        B -->|External Table| C[("BigQuery: esci_raw")]
    end

    subgraph ELT_Pipeline [dbt Analytics Engineering]
        direction TB
        C --> D[Staging Layer]
        D --> E[Intermediate Layer]
        E --> F[Marts Layer]
    end

    subgraph Consumption [Downstream Consumers]
        F -->|Training Data| G["XLM-RoBERTa Model"]
        F -->|Analytics| H["Tableau Dashboards"]
    end

    style Raw_Data fill:#f9f9f9,stroke:#333,stroke-width:1px
    style ELT_Pipeline fill:#e1f5fe,stroke:#0277bd,stroke-width:2px
    style Consumption fill:#fff3e0,stroke:#ff9800,stroke-width:2px
Repository StructureBu depo, veri dönüşümü ve modelleme için net katmanlara ayrılmış endüstri standardı Analytics Engineering uygulamalarını takip eder.Plaintext├── models/                  # ANALYTICS ENGINEERING (dbt)
│   ├── staging/             # Ham verinin temizlenmesi ve standartlaştırılması (Raw -> Staging)
│   ├── intermediate/        # İş mantığı, JOIN işlemleri ve veri sızıntısını önleyen Train/Test ayrımı
│   └── marts/               # BI ve ML tüketimi için hazır nihai tablolar
├── notebooks/               # DATA SCIENCE & ML
│   └── 03_model_training_xlm_roberta.ipynb # XLM-RoBERTa eğitim ve değerlendirme süreçleri
├── dashboards/              # BUSINESS INTELLIGENCE
│   └── search_relevance_overview.png # Tableau/PowerBI analiz çıktıları
├── macros/                  # UTILITIES
│   └── generate_schema_name.sql # BigQuery şema yönetimi için özel makrolar
└── dbt_project.yml          # Ana dbt yapılandırma dosyası
Setup & Usage1. Ön Koşullardbt Cloud Hesabı (Ücretsiz geliştirici planı yeterlidir)Google Cloud Platform Hesabı (BigQuery etkinleştirilmiş)Python 3.9+ (Yerel ML çalışmaları için)2. Veri Pipeline Kurulumu (dbt Cloud)Bu depoyu GitHub hesabınıza forklayın.dbt Cloud üzerinde yeni bir proje oluşturun ve deponuza bağlayın.BigQuery bağlantısını seçin ve Service Account JSON anahtarınızı yükleyin.Pipeline'ı oluşturmak için dbt IDE üzerinde şu komutu çalıştırın:Bashdbt build
Key Results & Analysis1. Model PerformansıXLM-RoBERTa modeli, farklı dillerdeki alakalı ve alakasız ürünleri ayırt etmede %90'ın üzerinde ayrıştırma gücü göstermiştir.YerelleştirmeSorguBaşarılı Eşleşme (Skor)Hatalı Eşleşme (Skor)FarkUS (English)gaming monitor 144hzASUS (100%)Logitech (5%)94%JP (Japanese)炊飯器Panasonic (100%)Nike (2%)97%ES (Spanish)teclado mecánicoLogitech (100%)Nespresso (5%)94%2. İş ÇıkarımlarıSorgu Uzunluğu Etkisi: Sorgu uzunluğu arttıkça alaka skorları düşmektedir. 60 karakterden uzun sorgular alakasız sonuçlara daha yatkındır.Kelime Çakışması: Sorgu ve ürün başlığı arasındaki yüksek kelime benzerliği alaka düzeyini garanti etmez, bu da anlamsal modellerin gerekliliğini kanıtlar.ContributorsİsimGitHubLinkedInCevdet KopuzcevdetkopuzProfilCem ÖzdoğangcemozdoganProfilZehra İstemihanzistemihanProfilEmirhan KümüşemirhankumusProfil
