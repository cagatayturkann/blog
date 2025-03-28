---
title: 'Modern AI Teknolojileri ve Node.js Entegrasyonu'
summary: Bu makalede, yapay zeka teknolojilerinin JavaScript ekosistemindeki uygulamalarını inceleyeceğiz. AI agent'lar, tool calling, conversation memory, context yönetimi gibi temel kavramların Node.js uygulamalarında nasıl hayata geçirileceğini örnek projeler üzerinden ele alacağız.
date: 2025-03-01T10:00:00+03:00
cardimage: '/blog/tr/modern-ai-teknolojileri-nodejs-entegrasyonu/images/cover.svg'
featureimage: '/blog/tr/modern-ai-teknolojileri-nodejs-entegrasyonu/images/cover.svg'
featured: true
categories: ['AI', 'Node.js', 'JavaScript']
tags: ['ai', 'nodejs']
translationKey: 'ai-nodejs-integration'
authors:
  - Çağatay: /images/author.jpeg
---

# Modern AI Teknolojileri ve Node.js Entegrasyonu

## Giriş

Yapay zeka teknolojileri, özellikle dil modelleri, yazılım geliştirme dünyasını hızla dönüştürüyor. Node.js gibi popüler bir platform ile AI teknolojilerini entegre etmek, JavaScript ekosisteminde çalışan geliştiricilere inanılmaz fırsatlar sunuyor. Bu makalede, modern AI konseptlerini Node.js çerçevesinde nasıl uygulayabileceğimizi inceleyeceğiz.

Bu yazı size şunları kazandıracak:

- AI terminolojisini ve temel kavramlarını anlama
- Node.js ile LLM (Large Language Model) entegrasyonu
- AI agent'lar oluşturma ve araç çağırma (tool calling) yeteneğini anlama
- Açık kaynak AI teknolojilerini (Ollama, LiteLLM, LlamaCPP) Node.js uygulamalarında kullanma
- Otomatik iş akışları için N8n ile AI entegrasyonu

Projelerin tam kodlarına [GitHub repository](https://github.com/cagatayturkann/blogProjects/tree/main/nodejs-ai-examples) adresinden erişebilirsiniz.

## 1. AI Agent'lar: Akıllı Asistanlar Geliştirme

### AI Agent Nedir?

AI agent, LLM'leri (Large Language Models) kullanarak belirli görevleri gerçekleştirmek üzere tasarlanmış yazılım sistemleridir. Bu ajanlar, insan benzeri düşünme, karar verme ve çevresiyle etkileşim kurma yeteneklerine sahiptir. AI agent'ı, belirli görevleri yerine getirmek üzere tasarlanmış otonom bir yazılım bileşenidir. Bu agentlar:

- Veri alır (input): Örneğin, kullanıcıdan gelen sorgular veya sistemden gelen veriler.
- İşlem yapar (processing): Bu veriler üzerinde analiz yapar, karar verir veya model çalıştırır.
- Çıktı verir (output): Sonuçları veya eylemleri gerçekleştirir.

Modern AI agent'lar tipik olarak şu bileşenleri içerir:

1. **LLM (Beyin)**: Doğal dil anlama ve üretme yeteneği
2. **Bellek (Memory)**: Geçmiş etkileşimleri hatırlama
3. **Araçlar (Tools)**: API'ler, veritabanları ve diğer sistemlerle etkileşime geçme yeteneği
4. **Planlama**: Karmaşık görevleri alt görevlere bölebilme

AI Agent'lar reaktif yani herhangi bir kullanıcı tetikleyicisi olmadan kendi başına veri toplama, çevri analizi vs işlemler yapmayan basit agentlar olabileceği gibi daha yaygın olarak otonom da olabilir. Otonom bir agent; örneğin, bir akıllı ev sisteminde olduğu gibi, ortamı sürekli izleyebilir, belirli durumları algılayıp kendi kararlarını verebilir. Bu tarz bir agent; sürekli sensör vb kaynaklardan veri toplar, bu verileri analiz eder ve bu analize göre kendi başına belirli görevler başlatır veya ayarlar.

### Node.js ile AI Agent Oluşturma

Node.js ve OpenAI API kullanarak basit bir otonom agent örneği yapalım. Bu örnekte, ajan belirli aralıklarla "sensör verisi" üretip, bu veriye göre OpenAI'dan aldığı karara göre hareket ediyor. Öncelikle gerekli paketi indiriyoruz.

```bash
npm install openai
```

Daha sonra ise kodumuz aşağıdaki şekilde.

```javascript
const { OpenAI } = require('openai');

// OpenAI yapılandırması: API anahtarınızı buraya ekleyin.

const openai = new OpenAI({
  apiKey: 'your-api-key',
});

// Otonom ajan fonksiyonu
async function autonomousAgent() {
  console.log('Ajan çalışmaya başladı...');

  // 1. Adım: Sensör verisini simüle et (örneğin, rastgele bir sayı)
  const sensorData = Math.random() * 100;
  console.log('Sensör verisi:', sensorData);

  // 2. Adım: Sensör verisine dayalı karar vermek için OpenAI'ya prompt gönder
  const prompt = `Sensör verisi: ${sensorData}. Eğer veri 50'den büyükse "Alarm", 50 veya daha düşükse "Normal" sonucunu tek kelime olarak ver.`;

  try {
    const response = await openai.chat.completions.create({
      model: 'gpt-4o-mini',
      messages: [{ role: 'user', content: prompt }],
      max_tokens: 10,
      temperature: 0.3,
    });

    const decision = response.choices[0].message.content.trim();
    console.log('Ajan Kararı:', decision);

    // 3. Adım: OpenAI'dan gelen karara göre hareket et
    if (decision === 'Alarm') {
      console.log('Alarm durumu tespit edildi, gerekli işlemleri başlatıyor...');
      // Burada alarmı tetiklemek veya bildirim göndermek gibi işlemler yapılabilir.
    } else if (decision === 'Normal') {
      console.log('Durum normal, bekleme modunda...');
      // Normal durumda yapılacak başka işlemler eklenebilir.
    } else {
      console.log('Bilinmeyen karar, tekrar kontrol ediliyor...');
      // Beklenmeyen durumlarda hata yönetimi yapılabilir.
    }
  } catch (error) {
    console.error('Hata oluştu:', error);
  }
}

// Ajanı her 30 saniyede bir otomatik olarak çalıştır.
setInterval(autonomousAgent, 10000);

// Program başladığında hemen bir kere çalıştır.
autonomousAgent();
```

Bu kod parçasını daha detaylı açıklayacak olursak

1. **Sensör Verisi Üretimi:** Agent, her çalıştığında rastgele bir sayı üretiyor. Bu sayı, örnek bir sensör verisi olarak düşünülüyor.
2. **Karar Süreci:** Üretilen sensör verisi, bir prompt içerisinde OpenAI API'ya gönderiliyor. Bu prompt, veriye göre "Alarm" veya "Normal" kelimesi döndürmesini istiyor.
3. **Eylem:** OpenAI'dan gelen yanıt, agentın kararını belirliyor. Eğer yanıt "Alarm" ise, agent alarm durumunu tetikleyen işlemleri gerçekleştiriyor; "Normal" ise, bekleme modunda kalıyor.
4. **Otonomluk:** Agent, kullanıcı müdahalesi olmadan belirli aralıklarla (burada her 30 saniyede bir) kendi başına çalışarak, sensör verisini değerlendiriyor ve otomatik olarak karar veriyor.

Bu örnek, otonom agent kavramını, sadece kullanıcı girdisine bağlı olmayan, kendi kendini tetikleyen ve belirli şartlara göre karar veren bir yapı olarak göstermektedir. Böylece, sistem belirli bir zaman döngüsünde bağımsız çalışarak çevresel veriye göre eylem alabiliyor.

### AI Agent Çalışma Prensibi

AI agent'lar genellikle şu adımları izler:

1. **Düşünme**: İstenen görev için bir plan oluşturma
2. **Aracı Seçme**: Görevi tamamlamak için hangi aracı kullanacağına karar verme
3. **Eylem**: Seçilen aracı kullanarak belirli bir eylemi gerçekleştirme
4. **Gözlemleme**: Eylemin sonucunu inceleme
5. **İlerleme**: Sonuca göre yeni bir düşünme-eylem döngüsü başlatma

Bu yaklaşım ReAct (Reasoning and Acting) olarak bilinir ve LLM'in düşünmeyle eylemi birleştirmesini sağlar.

### Neden Bu Özellik Önemli?

- Otomatikleştirilebilir işlemleri akıllı asistanlara devredebilirsiniz
- İnsan kullanıcılara daha doğal bir arayüz sunabilirsiniz
- Karmaşık iş akışlarını modüler araçlarla basitleştirebilirsiniz

## 2. Embedding, RAG, VectorDB

### Embedding

Embedding, metin veya diğer veri türlerini sayısal vektörlere dönüştürme işlemidir. Bu vektörler, verinin anlamsal özelliklerini sayısal formatta temsil eder. Örneğin, "köpek" ve "kedi" kelimeleri birbirine yakın vektörlerle temsil edilirken, "araba" daha uzak bir vektörle temsil edilir.

Embeddingler şu amaçlarla kullanılır:

- Metin benzerliği hesaplama
- Doküman sınıflandırma
- Semantik arama
- Öneriler oluşturma
- Duygu analizi
- Metin özetleme
- Doğal dil işleme görevleri

Embedding'lerin çalışma prensibi şu şekildedir:

1. **Tokenization**: Metin önce daha küçük parçalara (token) ayrılır
2. **Vektör Dönüşümü**: Her token, yüzlerce veya binlerce boyutlu bir vektöre dönüştürülür
3. **Normalizasyon**: Vektörler normalize edilerek karşılaştırılabilir hale getirilir

Embedding'lerin en önemli özelliği, benzer anlamlara sahip içeriklerin vektör uzayında birbirine yakın konumlanmasıdır. Bu sayede, metin tabanlı aramalarda kelime eşleştirmeden ziyade anlam bazlı sonuçlar elde edilebilir. Örneğin, "mutlu" ve "sevinçli" kelimeleri vektör uzayında birbirine yakın konumlanırken, "üzgün" kelimesi uzak bir noktada yer alır.

### RAG (Retrieval Augmented Generation)

RAG, büyük dil modellerinin (LLM) mevcut bilgi tabanını harici kaynaklarla zenginleştiren bir tekniktir. RAG sistemi şu şekilde çalışır:

1. **Retrieval (Getirme)**:

   - Kullanıcı sorusu ile ilgili dokümanlar veya bilgiler veritabanından çekilir
   - Semantic search kullanılarak en alakalı içerikler bulunur
   - Embedding benzerliği ile dokümanlar sıralanır

2. **Augmentation (Zenginleştirme)**:

   - Bulunan bilgiler LLM'e gönderilen prompt'a eklenir
   - Bilgiler önem sırasına göre düzenlenir
   - Bağlam penceresi optimize edilir

3. **Generation (Üretim)**:
   - LLM, zenginleştirilmiş bağlamı kullanarak yanıt üretir
   - Yanıt, verilen kaynaklara referans verebilir
   - Güvenilirlik skoru hesaplanabilir

RAG'ın avantajları:

- Güncel bilgi kullanımı
- Doğruluk oranının artması
- Özelleştirilmiş yanıtlar
- Hallüsinasyonların azalması
- Kaynakların takip edilebilirliği
- Dinamik bilgi güncelleme imkanı
- Domain-specific bilgi entegrasyonu

### Vector DB

Vector veritabanları, embedding vektörlerini depolamak ve bu vektörler üzerinde benzerlik araması yapmak için özelleştirilmiş veritabanı sistemleridir. Geleneksel veritabanlarından farklı olarak, vektörler arasındaki benzerliği hızlı bir şekilde hesaplayabilirler.

Vector DB'lerin temel özellikleri:

- Yüksek boyutlu vektörleri verimli şekilde depolama
- Benzerlik bazlı arama (cosine similarity, euclidean distance)
- Hızlı yakın komşu (nearest neighbor) sorguları
- Ölçeklenebilirlik
- CRUD operasyonları
- Metadata filtreleme
- Batch işlem desteği
- Vektör indeksleme

Vector DB'ler özellikle şu alanlarda kullanılır:

- Semantik doküman araması
- Öneri sistemleri
- Görüntü ve ses benzerliği analizi
- Doğal dil işleme uygulamaları
- Yüz tanıma sistemleri
- Anomali tespiti
- Çapraz modal arama (text-to-image, image-to-text)

Popüler Vector DB çözümleri:

- Pinecone: Tam yönetilen, ölçeklenebilir çözüm
- Weaviate: Açık kaynak, self-hosted seçeneği
- Milvus: Yüksek performanslı, dağıtık mimari
- Qdrant: Rust tabanlı, hızlı ve hafif
- ChromaDB: Python odaklı, başlangıç için ideal

## 3. Tool Calling: AI'a Yeni Yetenekler Kazandırma

### Tool Calling Nedir?

Tool calling (araç çağırma), bir AI modelinin dış fonksiyonları veya API'leri çağırabilme yeteneğidir. Bu, AI'ın kendi bilgi sınırları dışındaki işlemleri gerçekleştirmesini sağlar.

Modern LLM'ler, JSON formatında araç çağrısı yapabilme yeteneğine sahiptir. Bu, modelin gerçekleştirmek istediği eylemi ve gerekli parametreleri tanımlamasını sağlar.

### Node.js ile Tool Calling

OpenAI API'sini kullanarak tool calling implementasyonu yapalım. Aşağıdaki örnekte, Node.js kullanarak "tool calling" kavramını gösteren bir agent oluşturuyoruz. Bu agent iki farklı "aracı" çağırıyor:

- **Hava Durumu API'si (OpenWeatherMap):** Agent, belirli bir konum için güncel hava durumu verisini alıyor. - **OpenAI API:** Aldığı hava durumu verisini kullanarak, günün nasıl geçtiğine dair kısa bir analiz istiyor.

Bu iki aracı ardışık şekilde kullanarak, ajanın dış kaynaklardan veri toplayıp bu veriyi işlediğini görebiliriz.

Öncelikle gerekli kütüphaneleri indiriyoruz

```bash
npm install axios openai
```

Örnek kodumuz ise aşağıdaki şekilde.

```javascript
const axios = require('axios');
const { OpenAI } = require('openai');
// API anahtarlarınızı girin
const WEATHER_API_KEY = '<your-api-key>'; // OpenWeatherMap API anahtarınız

// Hava durumu sorgulanacak konum
const LOCATION = 'Istanbul';

// OpenAI yapılandırması
const openai = new OpenAI({
  apiKey: '<your-api-key>',
});

// 1. Adım: Hava Durumu API'sini çağıran fonksiyon
async function getWeatherData(location) {
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${location}&appid=${WEATHER_API_KEY}&units=metric`
    );
    return response.data;
  } catch (error) {
    console.error('Hava durumu API hatası:', error);
    return null;
  }
}

// 2. Adım: OpenAI API'ye veriyi gönderip analiz yapan fonksiyon
async function analyzeWeather(weatherData) {
  if (!weatherData) return 'Hava durumu verisi alınamadı.';

  // Hava durumu verisine dayalı prompt oluşturma
  const prompt = `Şu an ${weatherData.name}'de hava ${weatherData.weather[0].description} ve sıcaklık ${weatherData.main.temp}°C. Bu hava durumuna göre, gün nasıl geçiyor ve ne tür bir aktivite önerirsiniz? Kısa bir özet ver.`;

  try {
    const response = await openai.chat.completions.create({
      model: 'gpt-4o-mini',
      messages: [{ role: 'user', content: prompt }],
      max_tokens: 60,
      temperature: 0.7,
    });
    return response.choices[0].message.content.trim();
  } catch (error) {
    console.error('OpenAI API hatası:', error);
    return 'Hava durumu analiz edilemedi.';
  }
}

// Ana fonksiyon: Araçları sırasıyla çağırır
async function main() {
  console.log('Tool Calling Ajanı başlatılıyor...');

  // Hava durumu verisini almak için aracı çağırma
  const weatherData = await getWeatherData(LOCATION);
  console.log('Alınan Hava Durumu Verisi:', weatherData);

  // OpenAI API'ye veriyi gönderip analiz alıyoruz
  const analysis = await analyzeWeather(weatherData);
  console.log('Analiz Sonucu:', analysis);
}

main();
```

Bu örnekte, agent "tool calling" yaparak iki farklı dış kaynağı (OpenWeatherMap ve OpenAI) kullanıyor. Öncelikle hava durumu verisi toplanıyor; ardından bu veri, bir analiz için OpenAI API'sine gönderiliyor. Böylece, ajanın kendi yeteneklerinin ötesinde dış kaynaklardan faydalanması sağlanıyor.

### Farklı Araç Türleri

AI sistemlerinize entegre edebileceğiniz bazı araç türleri:

1. **Veritabanı İşlemleri**: Veri sorgulama, ekleme, güncelleme
2. **API Çağrıları**: Harici servislere bağlanma (hava durumu, borsa, haberler)
3. **Dosya İşlemleri**: Dosya okuma, yazma, dönüştürme
4. **Hesaplamalar**: Karmaşık matematiksel işlemler
5. **Takvim/Zamanlama**: Etkinlik oluşturma, hatırlatıcı ayarlama

### Tool Calling Best Practices

1. **Açık Tanımlamalar**: Araçlarınızı açık ve net tanımlayın, parametreleri dokumentasyon içerisinde detaylandırın
2. **Güvenlik Kontrolleri**: Kullanıcı girdilerini doğrulayın
3. **Graceful Failure**: Araçlar hata verdiğinde zarif bir şekilde geri dönüş yapın
4. **Aşamalı Geliştirme**: Önce basit araçlarla başlayın, sonra karmaşıklığı artırın

## 4. Conversation Memory: AI'ya Hafıza Kazandırma

### Conversation Memory Nedir?

Conversation memory, bir AI sisteminin önceki etkileşimleri hatırlama ve buna göre yanıt verme yeteneğidir. Bu özellik, sohbetin tutarlı ve bağlamsal olmasını sağlar.

### Neden Önemli?

- Bağlamsal tutarlılık sağlar. Kullanıcılar her seferinde bağlam vermek zorunda kalmaz. Yani diyalog sırasında, agent önceki mesajları 'hatırlar'. Böylece, örneğin bir sohbet başlangıcından verilen bilgiler, sonraki yanıtların daha anlamlı ve tutarlı olmasını sağlar.
- AI, önceki isteklere referans verebilir
- Daha doğal ve insana benzer etkileşimler sağlar

Conversation memory için örnek bir kullanımdan şu şekilde bahsedebiliriz. Bir chatbot, kullanıcının adını, ilgili alanlarını ya da daha önce sorduğu soruları belleğinde tutarak, sonraki yanıtlarda bu bilgileri referans alır. Böylece, her mesaj birbirine bağlı hale gelir ve sohbet bir doğal bir akış kazanır. Bu bellek genellikle, önceki mesajların belirli bir kısmını saklayarak gerçekleştirilir. Dil modelleri, bu geçmiş bilgiyi input olarak kullanarak, daha iyi bir bağlam anlayışı ile yanıt üretir. Konuşma belleğinin etkin kullanımı, bellek boyutu ve uzun diyaloglarda bilgilerin güncelliğini koruma gibi zorlukları da beraberinde getirir. Sonuç olarak, konuşma belleği yani conversation memory, AI Agent'larının daha 'akıılı' ve bağlamsal yanıtlar üretebilmesi için kritik bir bileşendir. Bu özellik, uzun ve anlamlı sohbetler kurulmasına olanak tanır.

### Node.js ile Conversation Memory

Aşağıdaki örnek; conversation memory tutan bir chatbot örneği. Bu örnekte her kullanıcının sohbet geçmişi MongoDB'de tutuluyor ve yeni bir mesaj geldiğinde geçmişi prpmpt olarak OpenAI'ya gönderiyoruz.

Önce gerekli kütüphaneleri yükleyelim:

```bash
npm install mongodb express openai
```

Örnek kodumuz ise aşağıdaki şekilde:

```javascript
const express = require('express');
const { MongoClient } = require('mongodb');
const { OpenAI } = require('openai');

const app = express();
app.use(express.json());

const OPENAI_API_KEY = '<your-api-key>';
const MONGO_URI = 'mongodb://localhost:27017';
const DATABASE_NAME = 'conversationDB';
const COLLECTION_NAME = 'conversations';

// OpenAI yapılandırması
const openai = new OpenAI({
  apiKey: OPENAI_API_KEY,
});

// MongoDB bağlantısı
const client = new MongoClient(MONGO_URI, { useUnifiedTopology: true });
let conversationCollection;

client
  .connect()
  .then(() => {
    const db = client.db(DATABASE_NAME);
    conversationCollection = db.collection(COLLECTION_NAME);
    console.log("MongoDB'ye bağlanıldı.");
  })
  .catch((err) => console.error('MongoDB bağlantı hatası:', err));

// Chat endpoint'i
app.post('/chat', async (req, res) => {
  const { userId, message } = req.body;
  if (!userId || !message) {
    return res.status(400).send({ error: 'userId ve message gerekli.' });
  }

  try {
    // Kullanıcının önceki sohbet geçmişini al
    let conversation = await conversationCollection.findOne({ userId });
    if (!conversation) {
      conversation = { userId, messages: [] };
    }

    // Kullanıcı mesajını geçmişe ekle
    conversation.messages.push({ role: 'user', content: message });

    // Konuşma geçmişini prompt'a dönüştür
    let prompt = '';
    conversation.messages.forEach((msg) => {
      prompt += (msg.role === 'user' ? 'Kullanıcı' : 'Ajan') + `: ${msg.content}\n`;
    });
    prompt += 'Ajan:'; // Ajanın yanıt vermesi için

    // OpenAI API çağrısı
    const response = await openai.chat.completions.create({
      model: 'gpt-4o-mini',
      messages: [{ role: 'user', content: prompt }],
      max_tokens: 100,
      temperature: 0.7,
    });
    const agentReply = response.choices[0].message.content.trim();

    // Ajan yanıtını geçmişe ekle
    conversation.messages.push({ role: 'agent', content: agentReply });

    // Güncellenmiş sohbeti MongoDB'ye kaydet (upsert)
    await conversationCollection.updateOne({ userId }, { $set: { messages: conversation.messages } }, { upsert: true });

    res.send({ reply: agentReply });
  } catch (error) {
    console.error('Sohbet işlenirken hata:', error);
    res.status(500).send({ error: 'Sunucu hatası' });
  }
});

app.listen(3000, () => {
  console.log('Sunucu 3000 portunda çalışıyor.');
});
```

Bu örnek kodda, her kullanıcının sohbet geçmişi userId ile MongoDB'de saklanıyor. Yeni bir mesaj geldiğinde, bu geçmiş alınarak OpenAI prompt'una dahil ediliyor. Böylece, model önceki konuşmayı da göz önünde bulundarak yanıt üretiyor.

Örnek cURL isteği:

```bash
curl --location 'http://localhost:3000/chat' \
--header 'Content-Type: application/json' \
--data '{
    "userId": "123",
    "message": "istanbul'da hava kaç derece"
}'
```

Örnek senaryo ise şu şekilde gelişebilir:
User => bugün İstanbul'da hava kaç derece
Agent => İstanbul'da hava 23 derece ve güneşli
User => peki ya İzmir?
Agent=> İzmir'de hava 26 derece ve güneşli.

Agent burada geçmişi tuttuğu için ikinci soruda 'hava durumu' vb. kelimeler geçmemesine rağmen sadece 'peki ya İzmir' seçeneğinden sohbet gemmişini tuttuğu için hava durumuu bilgisini alacak ve ona göre cevabı verecektir.

## 5. Model Context Protocol: LLM Bağlamını Yönetme

Model Context Protocol yani MCP; bir dil modeline (örneğin GPT-4, Claude 3.7 Sonnet) hangi bilgilerin, hangi formatta ve nasıl sağlanacağına dair belirli kuralları ve yapılandırmaları ifade eder. Bu kavramı daha iyi anlamak için aşağıdaki başlıklara da değinelim.

1. **Bağlamın Tanımlanması ve Formatlanması**
   Bağlam Nedir => Dil modelleri, yanıt üretirken sadece kendilerine gönderilen girdiye (prompt) dayanır. Bu girdi, sohbetin geçmişi, sistem yönergeleri, kullanıcı soruları ve hatta araç çağrılarından elde edilen veriler gibi çeşitli unsurları içerebilir.
   Protokol Nedir => Protokol ifadesi, bu bilgilerin nasıl düzenleneceğini, sıralanacağını ve formatlanacağını belirleyen kuralları ifade eder. Örneğin OpenAI'nın ChatGPT modeli, sohbet geçmişini 'system', 'user' ve 'assistant' rolleriyle ayırır. Bu yapı, modelin hangi bilgileri öncelikli olarak dikkate alacağını belirler.
2. **Konuşma Belleği ve Bağlam Yönetimi**
   Konuşma Belleği => Konuşma belleği, önceki mesajların hatırlanmasını sağlayan bir mekanizmadır. Ancak model, durum bilgisini doğrudan saklamaz; her seferinde önceki mesajlar prompt'a eklenerek bağlam sağlanır.
   Token Sınırı => Modellerin işleyebileceği toplam token sayısı sınırlıdır. Bu nedenle, hangi geçmiş mesajların dahil edileceği, hangi bilgilerin özetleneceği gibi kararlar "model context protocol" kapsamında ele alınır. Bu, modelin en alakalı bilgiyi görmesini sağlar.
3. **Dinamik ve Statik Bilgiler**
   Dinamik Bilgiler => Sohbet sırasında sürekli güncellenen bilgiler (örneğin, kullanıcı mesajları, araçlardan gelen yanıtlar) dinamik bağlamın bir parçası olarak prompt'a eklenir.
   Statik Bilgiler => Bazı sistem yönergeleri, modelin nasıl davranması gerektiğini belirten sabit komutlar veya bilgiler de olabilir. Örneğin, modelin belirli bir ton veya dilde yanıt vermesini istemek gibi.
4. **Uygulama Örneği**
   Diyelim ki bir chatbot oluşturuyoruz. MCP bu chatbot projesinde şu mantıkla çalışabilir

- **Sistem Mesajı =>** "Bu bir müşteri destek sohbetidir. Cevaplarını nazik ve yardımcı bir üslupla ver."
- **Kullanıcı Mesajları =>** "Merhaba, hesabımda sorun var" gibi geçmiş mesajlar.
- **Araç Çağrıları =>** Eğer dış API'lerden veri alınıyorsa, bu verilerde uygun şekilde prompt'a dahil edilir.
- **Prompt Oluşturma =>** Tüm bu bilgiler belirli bir sırayla ve formatla (örneğin, Kullanıcı: ..., Agent: ...) birleştirilir ve modelin yanıt üretmesi için gönderilir.

Özetleyecek olursak MCP, dil modeline hangi bilgilerin verileceğini, bu bilgilerin nasıl yapılandırılacağını ve modelin nasıl daha etkili yanıtlar üreteceğini belirleyen bir rehber gibidir. Doğru bir protokol oluşturmak, modelin bağlamı daha iyi anlamasına ve daha tutarlı, alakalı cevaplar vermesine yardımcı olur. Bu kavram, özellikle uzun ve karmaşık diyaloglarda, doğru bilgiyi modelin anlayabileceği sınır dahilinde tutmak için kritik bir öneme sahiptir.

## 6. Fine-tuning: LLM'leri Özelleştirme

Fine-tuning, önceden eğitilmiş bir dil modelini (LLM) belirli bir görev veya domain için özelleştirme sürecidir. Bu süreç, modelin belirli bir kullanım senaryosu için daha doğru ve tutarlı yanıtlar üretmesini sağlar.

### Fine-tuning Ne Zaman Kullanılmalı?

Fine-tuning şu durumlarda özellikle faydalıdır:

- Özel domain bilgisi gerektiren görevler
- Tutarlı format veya stil gerektiren çıktılar
- Marka sesi ve ton uyumu gereken durumlar
- Teknik terminoloji kullanımı
- Çok sayıda benzer görev tekrarı

### Fine-tuning Avantajları

1. **Daha İyi Performans**:

   - Domain-specific görevlerde daha doğru yanıtlar
   - Daha tutarlı çıktı formatı
   - Azaltılmış hallüsinasyonlar

2. **Maliyet Optimizasyonu**:

   - Daha kısa promptlar kullanabilme
   - Daha az token tüketimi
   - Daha hızlı yanıt süreleri

3. **Özelleştirilmiş Davranış**:
   - Marka diline uygun yanıtlar
   - Tutarlı ton ve üslup
   - Özel kuralları öğrenebilme

### Fine-tuning Süreci

1. **Veri Hazırlama**:

   - Eğitim verisi toplama
   - Veri temizleme ve formatlama
   - Prompt-completion çiftleri oluşturma

2. **Model Seçimi**:

   - Base model belirleme
   - Model parametrelerini ayarlama
   - Eğitim stratejisi seçimi

3. **Eğitim**:

   - Hiperparametre optimizasyonu
   - Eğitim metriklerini izleme
   - Cross-validation kontrolleri

4. **Değerlendirme**:
   - Model performans analizi
   - A/B testleri
   - İnsan değerlendirmesi

### Fine-tuning Best Practices

1. **Veri Kalitesi**:

   - Yüksek kaliteli eğitim verisi kullanın
   - Veri çeşitliliğini sağlayın
   - Dengeli veri dağılımı oluşturun

2. **Model Seçimi**:

   - Görev için uygun boyutta model seçin
   - Maliyet-performans dengesini gözetin
   - Base model performansını değerlendirin

3. **Eğitim Stratejisi**:

   - Kademeli fine-tuning uygulayın
   - Overfitting'i önleyin
   - Düzenli değerlendirme yapın

4. **Deployment**:
   - Model versiyonlaması yapın
   - Performans izleme mekanizmaları kurun
   - Geri bildirim döngüsü oluşturun

## 7. Ollama, LiteLLM, LlamaCPP: Açık Kaynak AI Çözümleri

Açık kaynaklı LLM'ler ve araçlar, AI geliştirme süreçlerini daha erişilebilir hale getiriyor. Bu bölümde, Node.js uygulamalarınızda kullanabileceğiniz popüler açık kaynak AI çözümlerini inceleyeceğiz.

- **Ollama:** Ollama, kullanıcıların büyük dil modellerini (ör. LLaMA, GPT türevi modeller) kendi makinelerinde çalıştırmasını sağlayan, genellikle kullanıcı dostu bir arayüz sunan platformlardır. Bu tür uygulamalar, modelin kurulumu, güncellemeleri ve entegrasyonunu kolaylaştırmak amacıyla tasarlanır; böylece, kullanıcının internet bağlantısına bağımlı olmadan yerel olarak güvenli ve hızlı erişim sağlanabilir.
- **LiteLLM:** Litellm, "lightweight" yani hafif anlamına gelen bir yaklaşımla geliştirilmiş, büyük dil modelleriyle çalışmayı kolaylaştıran bir kütüphane ya da araçtır. Genellikle minimal kaynak kullanımı, esneklik ve hızlı prototipleme imkanı sunar. Bu tür araçlar, model eğitiminden ziyade, var olan büyük modellerin daha az kaynakla çalıştırılmasına odaklanır.
- **LlamaCpp:** LlamaCpp, Meta'nın LLaMA modeli gibi büyük dil modellerinin C++ ile optimize edilmiş bir implementasyonudur. Bu kütüphane, özellikle quantized (azaltılmış hassasiyetli) modelleri CPU üzerinde verimli bir şekilde çalıştırmayı hedefler. Böylece, güçlü GPU'lara ihtiyaç duymadan, yerel makinede LLM çalıştırmak mümkün hale gelir.

### Açık Kaynak AI Çözümleri Karşılaştırması

| Özellik             | Ollama   | LiteLLM    | LlamaCPP |
| ------------------- | -------- | ---------- | -------- |
| Kurulum Kolaylığı   | ★★★★★    | ★★★★☆      | ★★☆☆☆    |
| Performans          | ★★★★☆    | ★★★★☆      | ★★★★★    |
| API Uyumluluğu      | ★★★☆☆    | ★★★★★      | ★★☆☆☆    |
| Kaynak Kullanımı    | ★★★☆☆    | ★★★★☆      | ★★★★★    |
| Çoklu Model Desteği | ★★★★☆    | ★★★★★      | ★★★☆☆    |
| Maliyet             | Ücretsiz | Ücretsiz\* | Ücretsiz |

Özetle, hepsi büyük dil modellerinin yerel veya optimize edilmiş ortamlarda kullanımını kolaylaştırmaya yönelik araçlar olsa da;

- Ollama daha kapsamlı bir platform olarak, kullanımı basitleştirip entegre çözümler sunarken,
- Litellm hafif ve modüler yapısıyla geliştiricilere esneklik sağlamak istiyor,
- LlamaCpp ise özellikle performans ve düşük kaynak kullanımı açısından C++ ile optimize edilmiş bir çözüm sunuyor.

Bu araçlar, hangi donanım ve kullanım senaryosunda çalıştırılacağına göre tercih edilebilir. Böylece, örneğin geliştirici ortamınızda hızlı prototipleme yapmak için litellm kullanabilir, performans odaklı yerel uygulamalarda ise llamaCpp tercih edilebilir; Ollama ise daha kapsamlı bir kullanıcı deneyimi arayanlara hitap edebilir.

## 8. N8n: AI İş Akışları Otomasyonu

N8n, kod yazmadan veya minimum kod ile iş akışlarını otomatikleştirmenize olanak tanıyan açık kaynaklı bir otomasyon aracıdır. N8n'in esnek yapısı, AI hizmetlerini diğer uygulamalarla kolayca entegre etmenizi sağlar. N8N, tamamen açık kaynaklıdır; bu sayede kaynak koduna erişebilir, özelleştirebilir ve kendi ihtiyaçlarınıza göre uyarlayabilirsiniz. Sürükle bırak arayüzü sayesinde, her biri belirli bir işlemi yapan "node"lardan oluşan akışlar tasarlayabilirsiniz. Bu sayede, teknik bilgisi az olan kullanıcılar bile karmaşık iş akışları oluşturabilir.

N8N, çok sayıda API ve servisle enteagrasyon sağlayarak, örneğin e-posta, veritabanı, sosyal medya, dosya depolama gibi farklı sistemler arasında veri transferi veya otomatik işlemler yapmanıza olanak tanır. N8N'i kendi sunucularınızda barındırabilirsiniz. Bu, verilerin kontrolünün tamamen sizde olmasını sağlar ve özellikle gizlilik veya compliance konularında avantaj sunar.

Örneğin, bir müşteri destek sürecinde, e-posta aldığında otomatik olarak bir veritabanına kayıt eklemek, ardından Slack üzerinden bildirim göndermek istiyorsan, n8n ile bu adımları görsel olarak düzenleyip otomatik hale getirebilirsin.

### N8n ile Yapılabilecek AI İş Akışları

1. **Belge İşleme ve Özetleme**

   - Gelen e-posta eklerini otomatik olarak özetleme
   - PDF belgeleri analiz edip veri çıkarma

2. **Müşteri Destek Otomasyonu**

   - Gelen müşteri sorularını analiz edip sınıflandırma
   - Basit sorulara otomatik yanıt oluşturma, karmaşık soruları yönlendirme

3. **Sosyal Medya Yönetimi**

   - Belirli konularda otomatik içerik üretme
   - Yorumları duygu analizi ile sınıflandırma ve yanıtlama

4. **Veri Analizi ve Raporlama**
   - Periyodik verileri analiz etme ve özetleme
   - Veri anormalliklerini tespit edip uyarı gönderme

### N8n ve AI Entegrasyonunun Avantajları

1. **Kod Yazmadan Otomasyon**: Teknik olmayan ekip üyeleri bile komplex AI iş akışları oluşturabilir
2. **Çoklu Servis Entegrasyonu**: 200+ entegrasyon ile AI'ı tüm iş süreçlerinize dahil edebilirsiniz
3. **Self-Hosting**: Hassas veriler içeren AI işlemleri için kendi altyapınızda çalıştırabilirsiniz
4. **Esnek Tetikleyiciler**: Zamanlayıcı, webhook, olay bazlı tetikleyiciler ile AI işlemlerini başlatabilirsiniz

### Son Söz

AI teknolojileri hızla gelişiyor ve bu teknolojilerin Node.js ekosistemindeki entegrasyonu giderek kolaylaşıyor. Bu makalede gördüğümüz teknikler, AI destekli uygulamalar geliştirmek için sağlam bir temel oluşturuyor.

AI agent'lar, tool calling, conversation memory ve bağlam yönetimi gibi temel kavramların yanı sıra, embedding'ler, RAG (Retrieval Augmented Generation) ve vector veritabanları gibi modern AI teknolojilerini kullanarak daha güçlü ve akıllı uygulamalar geliştirebilirsiniz. Fine-tuning ile modellerinizi özelleştirerek, domain-specific görevlerde daha başarılı sonuçlar elde edebilirsiniz.

Açık kaynak modeller ve N8n gibi otomasyon araçlarıyla birlikte, bu teknolojileri kullanarak:

- Daha doğru ve bağlama duyarlı yanıtlar üreten chatbot'lar
- Akıllı doküman işleme ve analiz sistemleri
- Semantik arama ve öneri motorları
- Otomatikleştirilmiş iş akışları
- Domain-specific AI asistanları

gibi çözümler geliştirebilirsiniz.

Bu makalede gösterilen tüm kod örneklerine ve daha fazlasına [GitHub Repository](https://github.com/cagatayturkann/blogProjects/tree/main/nodejs-ai-examples) adresinden erişebilirsiniz.

Yapay zeka yolculuğunuzda başarılar dilerim!

---
