---
title: 'Vektör Veritabanları ve Weaviate: Anlamsal Arama Uygulamaları Oluşturma'
summary: Bu makale, Weaviate'e odaklanarak vektör veritabanlarını inceliyor, temel kavramları, mimarisi ve uygulama stratejilerini kapsıyor. Vektör veritabanlarının anlamsal arama yeteneklerini nasıl sağladığını, geleneksel veritabanlarına göre avantajlarını ve Weaviate ile güçlü arama uygulamaları nasıl oluşturulacağını öğrenin.
date: 2025-03-14T12:00:00+03:00
cardimage: '/blog/tr/vector-veritabanlari-ve-weaviate/images/cover.svg'
featureimage: '/blog/tr/vector-veritabanlari-ve-weaviate/images/cover.svg'
featured: true
categories: ['AI']
tags: ['vectordb']
translationKey: 'vector-databases-weaviate'
authors:
  - Çağatay: /images/author.jpeg
---

# Vektör Veritabanları ve Weaviate: Anlamsal Arama Uygulamaları Oluşturma

## Giriş

Veri depolama ve erişim sistemlerinin sürekli gelişen dünyasında, vektör veritabanları geleneksel veritabanları ile modern yapay zeka uygulamaları arasındaki boşluğu dolduran devrim niteliğinde bir teknoloji olarak ortaya çıktı. Tam eşleşmelerde ve yapılandırılmış sorgularda mükemmel olan geleneksel veritabanlarının aksine, vektör veritabanları verilerin arkasındaki anlamı ve bağlamı anlamak için tasarlanmıştır, böylece yeni nesil uygulamaları güçlendiren benzerlik tabanlı arama yetenekleri sağlar.

Bu makalede şunları inceleyeceğiz:

- Vektör veritabanlarının arkasındaki temel kavramlar ve geleneksel sistemlerden nasıl farklılaştıkları
- Vektör veritabanlarının mimarisi ve temel bileşenleri
- Önde gelen açık kaynaklı vektör veritabanı platformlarından biri olan Weaviate'in derinlemesine incelenmesi
- Weaviate ile güçlü anlamsal arama uygulamalarının nasıl uygulanacağı
- En iyi uygulamalar, optimizasyon stratejileri ve gerçek dünya kullanım örnekleri

İster vektör veritabanlarına yeni başlıyor olun, ister mevcut bilginizi geliştirmek istiyor olun, bu rehber bu güçlü teknolojiyi anlamanız ve kullanmanız için ihtiyacınız olan teorik temeli sağlayacaktır.

## 1. Vektör Veritabanlarını Anlamak: Temel Kavramlar

### Vektör Veritabanları Nedir?

Vektör veritabanı, verilerin yüksek boyutlu vektör temsillerini depolamak, yönetmek ve sorgulamak için tasarlanmış özel bir veritabanı sistemidir. Bu vektörler (gömme olarak da adlandırılır), metin, görüntü, ses veya sayısal formatta temsil edilebilen diğer verilerin anlamsal anlamını yakalar.

Vektör veritabanlarının arkasındaki temel fikir basit ama güçlüdür: verileri anlamsal ilişkileri koruyan vektörlere dönüştürerek, tam eşleşmeler yerine anlama dayalı benzerlik aramaları gerçekleştirebiliriz. Bu, geleneksel veritabanı sistemleriyle daha önce zor veya imkansız olan çok çeşitli uygulamaları mümkün kılar.

### Vektör Gömmeleri: Yapı Taşları

Vektör veritabanlarının kalbinde gömmeler—yüksek boyutlu bir uzayda verilerin sayısal temsilleri—bulunur. İşte nasıl çalıştıkları:

1. **Gömme Oluşturma**: Veriler (metin, görüntü veya ses gibi) onu kayan noktalı sayılardan oluşan bir vektöre dönüştüren bir gömme modeli aracılığıyla işlenir.
2. **Boyutsal Temsil**: Bu vektörler genellikle yüzlerce veya binlerce boyuta sahiptir ve her boyut verinin anlamsal özelliklerinin bir yönünü yakalar.
3. **Anlamsal Yakınlık**: Benzer anlamlara veya özelliklere sahip öğeler, bu yüksek boyutlu uzayda birbirine yakın konumlandırılır.

Örneğin, bir metin gömme uzayında, "mutlu" ve "neşeli" kelimeleri, "mutlu" ve "otomobil" kelimelerine göre daha fazla anlamsal benzerlik paylaştıkları için birbirlerine daha yakın konumlandırılır.

{{< figure src="./images/vectorEmbeddings.svg.png" alt="Illustration of text embeddings in vector space">}}

### Vektör Veritabanları ve Geleneksel Veritabanları

Vektör veritabanlarının değerini anlamak için, geleneksel veritabanı sistemlerinden nasıl farklılaştıklarını anlamak önemlidir:

| Özellik                | Geleneksel Veritabanları                         | Vektör Veritabanları                        |
| ---------------------- | ------------------------------------------------ | ------------------------------------------- |
| **Veri Depolama**      | Yapılandırılmış kayıtlar, tablolar, belgeler     | Vektör gömmeleri + meta veriler             |
| **Sorgu Tipi**         | Tam eşleşmeler, aralık sorguları, birleştirmeler | Benzerlik araması, en yakın komşular        |
| **Arama Paradigması**  | "Kriterlere tam uyan kayıtları bul"              | "Bu referansa benzer öğeleri bul"           |
| **İndeksleme**         | B-ağaçları, hash indeksleri, ters indeksler      | Yaklaşık En Yakın Komşu (ANN) indeksleri    |
| **Kullanım Durumları** | İşlemsel veriler, yapılandırılmış raporlama      | Anlamsal arama, öneriler, sınıflandırma     |
| **Sorgu Karmaşıklığı** | SQL, karmaşık sorgu dilleri                      | Vektör benzerliği + isteğe bağlı filtreleme |

Temel fark, verileri anlama yaklaşımında yatar: geleneksel veritabanları verileri literal değerler olarak ele alırken, vektör veritabanları veri noktaları arasındaki anlamsal ilişkileri anlar.

## 2. Vektör Veritabanlarının Mimarisi

### Vektör Veritabanının Temel Bileşenleri

Modern vektör veritabanları, birlikte çalışan birkaç temel bileşenden oluşur:

1. **Vektör Depolama Katmanı**: Yüksek boyutlu vektör verilerini verimli bir şekilde depolamaktan sorumludur
2. **İndeksleme Mekanizması**: Benzer vektörlerin hızlı bir şekilde alınmasını sağlar
3. **Meta Veri Depolama**: Her vektör hakkında ek bilgileri (orijinal metin, kategoriler, zaman damgaları vb.) depolar
4. **Sorgu İşleme Motoru**: Vektör benzerlik aramalarını ve isteğe bağlı filtrelemeyi yönetir
5. **API Katmanı**: Veri ekleme ve sorgulama için arayüzler sağlar

Bu bileşenler, vektörleri benzerliğe dayalı olarak depolama ve alma temel işlevini sunmak için birlikte çalışır.

### Yaklaşık En Yakın Komşu (ANN) Algoritmaları

Vektör veritabanlarındaki en büyük zorluklardan biri, yüksek boyutlu uzaylarda benzer vektörleri verimli bir şekilde bulmaktır. Veri kümesi büyüdükçe tam en yakın komşu aramaları hesaplama açısından yasaklayıcı hale gelir. İşte burada Yaklaşık En Yakın Komşu (ANN) algoritmaları devreye girer.

Vektör veritabanlarında kullanılan popüler ANN algoritmaları şunları içerir:

- **HNSW (Hiyerarşik Gezinilebilir Küçük Dünya)**: Verimli gezinme için çok katmanlı bir grafik yapısı oluşturur
- **IVF (Ters Dosya İndeksi)**: Daha hızlı arama için vektör uzayını kümelere böler
- **PQ (Ürün Nicemleme)**: Benzerlik ilişkilerini korurken vektörleri sıkıştırır
- **FAISS (Facebook AI Benzerlik Araması)**: Ölçeklenebilir benzerlik araması için birden çok tekniği birleştirir

Bu algoritmalar, arama doğruluğu ve performans arasında ödünleşimler yapar ve genellikle milyonlarca vektörle bile milisaniyenin altında sorgu süreleri sağlar.

### Vektör Veritabanı Mimari Modelleri

Vektör veritabanları farklı mimari yaklaşımlarla uygulanabilir:

1. **Bağımsız Vektör Veritabanları**: Özellikle vektör depolama ve erişimi için tasarlanmış amaca özel sistemler (örn. Weaviate, Pinecone)
2. **Geleneksel Veritabanlarına Vektör Uzantıları**: Mevcut veritabanı sistemlerine vektör yetenekleri ekleme (örn. pgvector ile PostgreSQL)
3. **Hibrit Mimariler**: Vektör aramasını geleneksel arama yetenekleriyle birleştirme

Her yaklaşımın kendi avantajları vardır, bağımsız vektör veritabanları genellikle saf vektör tabanlı iş yükleri için en iyi performansı sunar.

## 3. Weaviate: Açık Kaynaklı Bir Vektör Veritabanı

### Weaviate Nedir?

Weaviate, bir vektör arama motorunun yeteneklerini geleneksel bir veritabanı sisteminin özellikleriyle birleştiren güçlü bir açık kaynaklı vektör veritabanıdır. Üretim ortamlarında beklenen güvenilirlik ve ölçeklenebilirlikle anlamsal arama yetenekleri sağlamak için tasarlanmıştır.

Weaviate'in temel özellikleri şunları içerir:

- **GraphQL API**: Sorgular ve mutasyonlar için sezgisel arayüz
- **RESTful API**: Standart HTTP işlemleri için alternatif arayüz
- **Hibrit Arama**: Vektör aramasını anahtar kelime tabanlı erişimle birleştirme
- **Modüler Mimari**: Farklı gömme modelleri için takılabilir vektörleştirici modülleri
- **Şema Tanımı**: Esnek veri modelleme yetenekleri
- **Çapraz Referanslar**: Veritabanı içindeki nesneleri bağlama yeteneği
- **Sınıflandırma Yetenekleri**: Veri sınıflandırması için yerleşik makine öğrenimi

### Weaviate Mimarisi

Weaviate'in mimarisi birkaç temel bileşenden oluşur:

1. **Vektör İndeksi**: Benzerlik araması için HNSW tabanlı vektör indeksi
2. **Nesne Deposu**: Hem vektör verileri hem de ilişkili meta veriler için depolama
3. **GraphQL Arayüzü**: Weaviate ile etkileşim için birincil API
4. **Vektörleştirme Modülleri**: Gömmeler oluşturmak için takılabilir bileşenler
5. **Sorgu Planlayıcısı**: Arama sorgularını optimize eder ve yürütür

Bu mimari, Weaviate'in kullanıcı dostu bir arayüzü korurken yüksek performanslı vektör araması sunmasını sağlar.

### Weaviate'de Veri Modelleme

Weaviate, veri modellemede esnek bir şema tabanlı yaklaşım kullanır. Weaviate'deki her veri nesnesi:

1. Bir sınıfa aittir (ilişkisel veritabanlarındaki tablolara benzer)
2. Özelliklere sahiptir (nesneyi tanımlayan nitelikler)
3. İlişkili bir vektör gömmesine sahiptir (otomatik olarak oluşturulur veya içe aktarılır)
4. Diğer nesnelere çapraz referanslara sahip olabilir

Makaleler koleksiyonu için basit bir Weaviate şeması şöyle görünebilir:

```json
{
  "classes": [
    {
      "class": "Article",
      "description": "A news or blog article",
      "properties": [
        {
          "name": "title",
          "dataType": ["text"],
          "description": "The title of the article"
        },
        {
          "name": "content",
          "dataType": ["text"],
          "description": "The main content of the article"
        },
        {
          "name": "publishedDate",
          "dataType": ["date"],
          "description": "When the article was published"
        },
        {
          "name": "author",
          "dataType": ["Author"],
          "description": "The author of the article"
        }
      ]
    },
    {
      "class": "Author",
      "description": "A person who writes articles",
      "properties": [
        {
          "name": "name",
          "dataType": ["text"],
          "description": "The author's full name"
        },
        {
          "name": "biography",
          "dataType": ["text"],
          "description": "The author's biographical information"
        }
      ]
    }
  ]
}
```

Bu şema tabanlı yaklaşım, Weaviate'i geleneksel veritabanı sistemlerine aşina olan geliştiriciler için erişilebilir kılar.

## 4. Vektör Veritabanlarının Temel Özellikleri

### Anlamsal Arama

Vektör veritabanlarının en önemli özelliği, anahtar kelimeler yerine anlama dayalı sonuçlar bulan anlamsal aramadır. Bu şunları içerir:

1. **Kavramsal Eşleştirme**: Farklı terminoloji kullansalar bile bir sorgunun kavramıyla eşleşen sonuçları bulma
2. **Bağlamsal Anlama**: Sorguların arkasındaki amacı tanıma ve buna göre eşleştirme
3. **Çok Dilli Destek**: Anlamsal benzerliğe dayalı olarak farklı dillerde ilgili içeriği bulma

Anlamsal arama, özellikle karmaşık veya nüanslı sorgular için geleneksel anahtar kelime tabanlı yaklaşımlara göre arama kalitesini önemli ölçüde iyileştirir.

### Hibrit Arama

Vektör araması anlamsal ilişkileri yakalamada mükemmel olsa da, ilgili tam anahtar kelime eşleşmelerini kaçırabilir. Hibrit arama, her iki dünyanın en iyi yanlarını birleştirir:

1. **Vektör Araması**: Anlamsal benzerliği ve kavramsal eşleştirmeyi yakalar
2. **Anahtar Kelime Araması**: Tam terim eşleşmelerinin kaçırılmadığından emin olur
3. **Füzyon Yöntemleri**: Her iki yaklaşımdan gelen sonuçları akıllıca birleştirir

Hibrit arama, özellikle genel amaçlı arama uygulamaları için tek başına her iki yaklaşımdan daha iyi genel arama kalitesi sunar.

### Çok Modlu Arama

Gelişmiş vektör veritabanları, tek bir sorgu kullanarak farklı medya türleri arasında arama yapabilme yeteneği olan çok modlu aramayı destekler:

1. **Çapraz Modal Arama**: Bir modaliteyi başka bir modaliteyi aramak için kullanma (örn. metinden görüntüye arama)
2. **Birleşik Gömme Vektörleri**: Farklı veri türlerini paylaşılan bir vektör uzayında temsil etme
3. **Çok Modlu Sorgular**: Tek bir sorguda birden fazla modaliteyi birleştirme

Bu özellik, metin açıklamalarını kullanarak görüntüleri aramak veya bir görüntüyle ilgili metin içeriği bulmak gibi güçlü uygulamaları mümkün kılar.

### Sınıflandırma ve Öneri

Aramanın ötesinde, vektör veritabanları diğer önemli özellikleri sağlar:

1. **Otomatik Sınıflandırma**: Yeni öğeleri mevcut kategorize edilmiş öğelere benzerliğe göre sınıflandırma
2. **Öneri Sistemleri**: Vektör benzerliğine dayalı olarak ilgili öğeleri önerme
3. **Anomali Tespiti**: Vektör uzayındaki aykırı değerleri tanımlama

Bu özellikler, vektör veritabanlarını sadece arama uygulamaları için değil, çok çeşitli AI destekli sistemler için değerli kılar.

## 5. Vektör Veritabanlarının Avantajları

### Gelişmiş Arama Kalitesi

Vektör veritabanları, arama kalitesinde önemli iyileştirmeler sunar:

1. **Amacı Anlama**: Sadece anahtar kelimeleri eşleştirmek yerine sorguların arkasındaki anlamı yakalama
2. **Eş Anlamlıları İşleme**: Aynı anlama sahip farklı terimleri tanıma
3. **Kavram Eşleştirme**: Farklı terminoloji kullansa bile aynı kavramla ilgili sonuçları bulma
4. **Bağlam Duyarlılığı**: Sorguların nüansını ve bağlamını anlama

Bu iyileştirmeler, daha iyi kullanıcı deneyimlerine ve daha etkili bilgi erişimine dönüşür.

### Ölçeklenebilirlik ve Performans

Modern vektör veritabanları, üretim düzeyinde performans için tasarlanmıştır:

1. **Saniyeden Az Sorgu Süreleri**: Milyonlarca vektörle bile
2. **Yatay Ölçekleme**: İş yüklerini birden fazla düğüme dağıtma
3. **Verimli Depolama**: Yüksek boyutlu vektör verileri için optimize edilmiş
4. **Düşük Gecikme**: Gerçek zamanlı uygulamalar için

Bu ölçeklenebilirlik, vektör veritabanlarını küçük uygulamalardan kurumsal ölçekli dağıtımlara kadar her şey için uygun hale getirir.

### AI İş Akışlarıyla Entegrasyon

Vektör veritabanları modern AI iş akışlarına sorunsuz bir şekilde uyum sağlar:

1. **LLM Entegrasyonu**: Büyük dil modellerini erişim yetenekleriyle geliştirme
2. **Gömme Vektörü İşlem Hattı Entegrasyonu**: Doğrudan gömme modelleriyle çalışma
3. **Veri İşleme Desteği**: Sürekli güncellemeleri ve yeni verileri işleme
4. **API Öncelikli Tasarım**: Mevcut sistemlerle kolay entegrasyon

Bu entegrasyon potansiyeli, vektör veritabanlarını AI altyapı yığınında kilit bir bileşen haline getirir.

## 6. Vektör Veritabanları için Yaygın Kullanım Durumları

### Anlamsal Belge Araması

Vektör veritabanları, anlama dayalı ilgili belgeleri bulmada mükemmeldir:

- **Kurumsal Bilgi Tabanları**: Çalışanların ilgili bilgileri bulmasına yardımcı olma
- **Hukuki Belge Araması**: Kavramlara dayalı emsal ve içtihat bulma
- **Araştırma Veritabanları**: Farklı terminolojiler arasında ilgili makaleleri keşfetme
- **İçerik Yönetimi**: İçeriği temalara göre düzenleme ve erişme

### Öneri Sistemleri

Vektör benzerliği etkili öneri sistemlerini güçlendirir:

- **Ürün Önerileri**: "Bunu görüntüleyen müşteriler şunları da görüntüledi..."
- **İçerik Keşfi**: Benzer temalara sahip makaleler, videolar veya müzikler bulma
- **Kişi Eşleştirme**: Arkadaşlık uygulamaları, profesyonel ağ oluşturma, takım oluşturma
- **Eğitim Kaynakları**: Benzer konularda öğrenme materyalleri önerme

### Görüntü ve Medya Araması

Metin ötesinde, vektör veritabanları multimedya içeriğini etkili bir şekilde işler:

- **Ters Görüntü Araması**: Bir referansa benzer görüntüler bulma
- **İçerik Tabanlı Medya Erişimi**: Benzer içeriğe sahip videolar veya ses bulma
- **Dijital Varlık Yönetimi**: İçeriğe dayalı medyayı düzenleme ve erişme
- **Görsel Ürün Araması**: Bir referansa benzeyen ürünler bulma

### RAG (Erişim Destekli Üretim)

Vektör veritabanları RAG sistemlerinde kilit bir bileşendir:

- **Sorgu Tabanlı Belge Erişimi**: LLM için ilgili belgeleri bulma
- **Bilgi Temellendirme**: LLM yanıtlarını temellendirmek için gerçek bilgiler sağlama
- **Alan Adaptasyonu**: Genel LLM'lerin alana özgü bilgilere erişmesine izin verme
- **Gerçek Doğrulama**: LLM tarafından oluşturulan içeriği doğrulamak için bilgi erişimi

## 7. Uygulamalarda Weaviate'i Uygulama

### Weaviate Sunucusu Kurma

Weaviate ile çalışmanın ilk adımı bir sunucu kurmaktır. Bu birkaç şekilde yapılabilir:

1. Docker kullanarak (en yaygın yöntem)
2. Bulut tabanlı seçenekler
3. Kaynak koddan derleme

Uygulama bölümünde, Weaviate'in kendi sandbox yöntemi ve örnek kodlar paylaşacağım.

### Veri Şemanızı Tanımlama

Verileri içe aktarmadan önce, şemanızı tanımlamanız gerekir. Bu şunları içerir:

1. Sınıfları tanımlama (benzer nesnelerin koleksiyonları)
2. Her sınıf için özellikleri belirleme
3. Sınıflar arasında çapraz referanslar kurma
4. Vektörleştirici modülleri yapılandırma

Uygulama bölümü, Weaviate'de bir şema tanımlama ve oluşturma için kod içerecektir.

### Weaviate'e Veri İçe Aktarma

Şemanız tanımlandıktan sonra, verileri içe aktarabilirsiniz. Bu genellikle şunları içerir:

1. Verilerinizi doğru formatta hazırlama
2. Verimli içe aktarmalar için verileri toplu işleme
3. Hataları ve doğrulamayı yönetme
4. İçe aktarma sürecini izleme

Uygulama bölümünde verimli veri içe aktarma için kod örnekleri sunacağım.

### Hibrit Aramayı Uygulama

Hibrit arama, vektör aramasını geleneksel anahtar kelime aramasıyla birleştirir. Uygulama şunları içerir:

1. Her iki arama türünü yapılandırma
2. Her biri için uygun ağırlıkları belirleme
3. Optimal sonuçlar için parametreleri ayarlama
4. Sonuçları işleme ve yönetme

Uygulama kodu, hibrit arama sorgularını nasıl kurup yürüteceğinizi gösterecektir.

## 8. En İyi Uygulamalar ve Optimizasyon Stratejileri

### Şema Tasarımı En İyi Uygulamaları

Etkili şema tasarımı, Weaviate performansı için çok önemlidir:

1. **Uygun Özellik Türleri**: Her özellik için doğru veri türlerini kullanma
2. **Vektörleştirme Yapılandırması**: Verileriniz için doğru vektörleştiriciyi seçme
3. **Çapraz Referans Planlama**: Verimli nesne ilişkileri tasarlama
4. **İndeksleme Stratejisi**: Kullanım durumunuz için HNSW parametrelerini yapılandırma

### Veri İçe Aktarma Optimizasyonu

Verimli veri içe aktarma, daha hızlı değer elde etmeyi sağlar:

1. **Toplu İşleme**: Daha verimli içe aktarmalar için nesneleri gruplandırma
2. **Paralel İşleme**: Daha hızlı içe aktarmalar için birden fazla iş parçacığı kullanma
3. **Artımlı Güncellemeler**: Mevcut verileri güncelleme stratejileri
4. **Hata Yönetimi**: İçe aktarma hatalarını düzgün bir şekilde yönetme

### Sorgu Optimizasyon Teknikleri

Sorguları optimize etmek hem performansı hem de sonuç kalitesini iyileştirir:

1. **Vektör Arama Parametreleri**: Mesafe metriklerini ve k değerlerini ayarlama
2. **Hibrit Arama Yapılandırması**: Vektör ve anahtar kelime bileşenlerini dengeleme
3. **Filtre Optimizasyonu**: Meta veri filtrelerinin verimli kullanımı
4. **Sonuç İşleme**: Arama sonuçlarını işleme ve dönüştürme

### Ölçekleme ve Üretim Hususları

Üretim dağıtımları için, bu en iyi uygulamaları göz önünde bulundurun:

1. **Kaynak Tahsisi**: CPU, bellek ve depolama gereksinimleri
2. **Yedekleme Stratejileri**: Veri dayanıklılığını sağlama
3. **İzleme ve Günlük Tutma**: Performansı ve hataları takip etme
4. **Yüksek Kullanılabilirlik**: Yedeklilik ve yük devretme için yapılandırma

## 9. Zorluklar ve Sınırlamalar

### Vektör Veritabanı Zorlukları

Güçlü olmalarına rağmen, vektör veritabanları belirli zorluklarla gelir:

1. **Soğuk Başlangıç Sorunu**: Mevcut gömme vektörleri olmadan ilk kurulum
2. **Gömme Vektörü Kayması**: Zaman içinde gömme modellerindeki değişiklikler
3. **Açıklanabilirlik**: Belirli sonuçların neden döndürüldüğünü anlama
4. **Kaynak Yoğunluğu**: Geleneksel veritabanlarından daha yüksek hesaplama gereksinimleri

### Yaygın Tuzakları Ele Alma

Vektör veritabanlarıyla ilgili yaygın sorunlardan kaçınmak için:

1. **Arama Kalitesini Test Edin**: Gerçek sorgularla arama sonuçlarını düzenli olarak değerlendirin
2. **Performansı İzleyin**: Sorgu sürelerini ve kaynak kullanımını takip edin
3. **Gömme Vektörlerini Güncelleyin**: Modeller değiştiğinde gömme vektörlerini yeniden oluşturun
4. **Kademeli Olarak Ayarlayın**: Küçük değişiklikler yapın ve etkiyi ölçün

---

## Uygulama: Weaviate ile Geliştirme

https://weaviate.io/developers/weaviate/quickstart adresindeki adımları izleyerek bir kendimize bir endpoint ve api key oluşturuyoruz.

Bir proje başlatıyoruz localimizde veya github reposundan ve sırasıyla aşağıdaki komutları çalıştırıyoruz.

```bash
npm init -y
npm install axios openai weaviate-client
```

Projemizi oluşturup gerekli bileşenleri yükledikten sonra https://dummyjson.com/products adresinden aldığımız örnek dataları embedding haline getirmek için aşağıdaki scripti kullanıyoruz.

```javascript
import axios from 'axios';
import { writeFileSync } from 'fs';
import { OpenAI } from 'openai';
import fs from 'fs';

// Initialize OpenAI API client
const openai = new OpenAI({
  apiKey: '', // Add your OpenAI API key here
});

/**
 * Converts product title to URL-friendly format
 * @param {string} title - Product title
 * @returns {string} - Formatted URI string
 */
function formatProductUri(title) {
  if (!title) return '';
  return title.toLowerCase().replace(/\s+/g, '-');
}

/**
 * Processes product data and generates embeddings
 * @param {Object} product - Raw product data
 * @returns {Object} - Formatted product with vector embedding
 */
async function formatProductData(product) {
  try {
    // Combine product data as string for embedding
    const productString = JSON.stringify(product);

    // Generate embedding using OpenAI
    const embeddingResponse = await openai.embeddings.create({
      model: 'text-embedding-ada-002',
      input: productString,
    });

    const embedding = embeddingResponse.data[0].embedding;

    // Return product in the required format
    return {
      data: product,
      id: formatProductUri(product?.title) + '-' + product?.id,
      vectorContent: embedding,
    };
  } catch (error) {
    console.error(`Error processing product: ${product.title}`, error);
    throw error;
  }
}

/**
 * Main function to fetch products from API and process them
 * Generates vector embeddings for each product
 */
async function fetchAndProcessProducts() {
  try {
    console.log('Fetching products...');
    const response = await axios.get('https://dummyjson.com/products?limit=200');
    const products = response.data.products;
    fs.writeFileSync('products.json', JSON.stringify(products, null, 2));

    console.log(`Fetched ${products.length} products. Processing...`);

    // Process all products
    const processedProducts = [];
    let index = 0;

    // Process each product sequentially to avoid rate limits
    for (const product of products) {
      index++;
      console.log(`Processing product ${index}/${products.length}: ${product.title}`);

      try {
        const formattedProduct = await formatProductData(product);
        processedProducts.push(formattedProduct);

        // Add delay every 10 products to avoid OpenAI rate limits
        if (index % 10 === 0) {
          console.log('Waiting 1 second for rate limit...');
          await new Promise((resolve) => setTimeout(resolve, 1000));
        }
      } catch (error) {
        console.error(`Error processing product: ${product.title}`, error);
        // Continue despite errors
        continue;
      }
    }

    // Save results to JSON file
    writeFileSync('processed_products.json', JSON.stringify(processedProducts, null, 2));
    console.log("Processing complete. Results saved to 'processed_products.json'");
  } catch (error) {
    console.error('Error occurred:', error);
  }
}

// Start the process
fetchAndProcessProducts();
```

Bu script bize 'processed_products.json' isminde datanın kendisini ve embed edilmiş halini barındıran bir json dosyası verecek. Daha sonra bu datayı aşağıdaki script ile weaviate bulut ortamına import ediyoruz. Yukarıda anlatılanlardan farklı olarak şema yaklaşımını burada kullanmıyorum daha çok işin mantığını kavramayı anlatamaya çalıştığım için.

```javascript
/**
 * Import script for loading product data into Weaviate vector database
 * This script reads product data from a JSON file and imports it into a Weaviate collection
 */
import weaviate, { generateUuid5 } from 'weaviate-client';
import { readFileSync } from 'fs';

// Load product data from JSON file
const products = JSON.parse(readFileSync('./processed_products.json', 'utf8'));

// Weaviate cloud service connection details
const WCD_URL = ''; // Add your WCD URL here
const WCD_API_KEY = ''; // Add your WCD API key here

const wcdUrl = WCD_URL;
const wcdApiKey = WCD_API_KEY;

// Validate connection details are available
if (!wcdUrl || !wcdApiKey) {
  throw new Error('Missing required environment variables WCD_URL or WCD_API_KEY');
}

// Initialize Weaviate client
const client = await weaviate.connectToWeaviateCloud(wcdUrl, {
  authCredentials: new weaviate.ApiKey(wcdApiKey),
});

/**
 * Main function to import product data into Weaviate
 */
async function main() {
  try {
    console.log('Starting data import to Weaviate...');
    console.log('products recieved', products.length);

    // Transform product data into Weaviate objects
    const dataObjects = products.map((item) => ({
      properties: {
        data: JSON.stringify(item.data),
      },
      // Generate a deterministic UUID based on collection name and item ID
      id: generateUuid5('Ecommerce', item.id),
      // Use vector if available, otherwise let Weaviate generate it
      vector: Array.isArray(item.vectorContent) ? item.vectorContent : undefined,
    }));
    console.log('dataObjects received', dataObjects.length);

    // Get reference to the Ecommerce collection
    const collection = await client.collections.get('Ecommerce');
    // Insert all objects in a batch operation
    const result = await collection.data.insertMany(dataObjects);
    console.log('Insertion response: ', result);

    console.log('Data import completed');
  } catch (error) {
    console.error('Error:', error);
  }
}

// Run the main function
main().catch(console.error);
```

Bu script ile oluşturulan json dosyasını weaviate sistemine yüklemiş oluyoruz. Burada iki kısım önemli.

1. Eğer aldığınız datanın içerisinde kendinea ait bir ID değeri var ise bunu kullanmak için properties kısmında ayrıca belirtebilirsiniz veya farklı bir property ismi ile tanımlamanız gerekiyor çünkü Weaviate'in kendi Id parametresi var ve bu UUID formatında olmalı bu yüzden direkt datanın id alanını orada kullanamıyoruz. Ancak Weavaite'in kendi UUID oluşturucusunu kullanabiliriz örnekte olduğu gibi.
2. Diğer alan ise collection name. Burası aslında class ismini temsil ediyor. Arama yaparken, data silerken vb tüm işlemlerde buradaki collection ismi kullanılıyor.

Datamızı import ettikten sonra ise geldi datayı search etme işlemine. Yukarıda çeşitli arama çeşitlerinden hem Weaviate özelinde hem vektör veritabanları özelinde bahsetmiştim. Ben aşağıdaki scriptte Weaviate'in graphql özelliğini ve hybrid arama özelliğini kullandım.

```javascript
// Import required libraries
// axios for making HTTP requests
import axios from 'axios';
// OpenAI SDK for generating embeddings
import OpenAI from 'openai';

// Initialize OpenAI client with API key
const openai = new OpenAI({
  apiKey: '', //Add your openAI API key
});

// Array of questions to process
const questions = ['do you have macbook?'];

/**
 * Generate an embedding vector for the given text using OpenAI's API
 * @param {string} text - The text to generate an embedding for
 * @returns {Promise<Array<number>>} The embedding vector
 */
async function getEmbedding(text) {
  const response = await openai.embeddings.create({
    model: 'text-embedding-ada-002',
    input: text,
  });
  return response.data[0].embedding;
}

/**
 * Search Weaviate database using hybrid search (text + vector)
 * @param {string} question - The question to search for
 * @param {Array<number>} vector - The embedding vector of the question
 * @returns {Promise<Object>} Search results and metadata
 */
async function searchWeaviate(question, vector) {
  // Track response time
  const startTime = Date.now();

  // Prepare GraphQL query for Weaviate
  const data = JSON.stringify({
    query: `{
      Get {
        Ecommerce (
          hybrid: {
            query: "${question}"
            alpha: 0.5,
            vector: ${JSON.stringify(vector)}
          }
          limit: 3
        ) {
          data
          _additional { score }
        }
      }
    }`,
  });

  // Configure request parameters
  const config = {
    method: 'post',
    url: 'https://ltwfnj0szusp5t1natqdw.c0.us-west3.gcp.weaviate.cloud/v1/graphql',
    headers: {
      'Content-Type': 'application/json',
      Authorization: 'Bearer AUV3GwGDrHKwVCJpFFxKc6ppUl7xXF54uQAM',
    },
    data: data,
  };

  try {
    // Execute the request
    const response = await axios.request(config);
    const endTime = Date.now();

    // Format and return results
    return {
      question: question,
      results: response.data.data.Get.Ecommerce.map((item) => item.data),
      responseTime: `${endTime - startTime}ms`,
    };
  } catch (error) {
    // Handle errors
    console.error(`Error for question "${question}":`, error);
    return {
      question: question,
      error: error.message,
      responseTime: null,
    };
  }
}

/**
 * Main function to process all questions
 */
async function main() {
  for (const question of questions) {
    try {
      // Generate embedding for the question
      const embedding = await getEmbedding(question);

      // Search Weaviate using the question and its embedding
      const result = await searchWeaviate(question, embedding);

      // Output results
      console.log('\n-------------------');
      console.log('Question:', result.question);
      console.log('Products:', result.results);
      console.log('Response Time:', result.responseTime);
    } catch (error) {
      console.error(`Failed to process question "${question}":`, error);
    }
  }
}

// Execute the main function
main();
```

Bu script questions içerisinde yer alan soruyu/soruları alarak sırayla weaviate sunucularında arama yapıyor. Bunu yaparken

```javascript
const data = JSON.stringify({
  query: `{
      Get {
        Ecommerce (
          hybrid: {
            query: "${question}"
            alpha: 0.5,
            vector: ${JSON.stringify(vector)}
          }
          limit: 3
        ) {
          data
          _additional { score }
        }
      }
    }`,
});
```

kısmındaki Weaviate hybrid arama özelliğini kullanıyor. Burada query kısmında direkt sorunun kendisi yer alıyor. vector kısmında ise sorunun embedding haline çevrilmiş versiyonu yer alıyor. Böylece hem text hem vector olarak hybrid arama gerçekleştiriyor. alpha kısmı ise aramanın ağırlığını belirtiyor. Artırdıkça vector aramasının ağırlığı, azalttıkça ise kelime aramasının ağırlığı artıyor. limit kısmında ise dönen data sayısını belirtiyoruz. data kısmında ise dönmesini istediğimi property ismini belirtiyoruz. score kısmı ise arama sonuçları için belirlenen skorları gösteriyor.

## Sonuç

Weaviate gibi vektör veritabanları, verileri depolama, erişme ve anlama şeklimizde önemli bir ilerlemeyi temsil eder. Anlamsal ilişkileri yakalayarak ve benzerlik tabanlı aramayı mümkün kılarak, geleneksel veritabanı sistemleriyle daha önce pratik olmayan yeni yeteneklerin kilidini açarlar.

AI yazılım geliştirmeyi dönüştürmeye devam ederken, vektör veritabanları dil modellerini, arama sistemlerini ve kullanıcı arayüzlerini birbirine bağlamada giderek daha merkezi bir rol oynayacaktır. Yeteneklerini, mimarisini ve uygulama stratejilerini anlamak, AI destekli uygulamalarla çalışan geliştiriciler için temel bir beceri haline gelmektedir.
