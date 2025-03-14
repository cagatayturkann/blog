---
title: 'RAG ile Akıllı E-ticaret Deneyimleri Oluşturma'
summary: Bu makalede, Retrieval-Augmented Generation (RAG) teknolojisinin e-ticaret uygulamalarını nasıl dönüştürebileceğini inceleyeceğiz. Pratik bir örnek proje üzerinden vektör veritabanları, anlamsal arama, konuşma hafızası ve çok dilli desteğin uygulanmasını tartışacağız.
date: 2025-03-14T10:00:00+03:00
cardimage: '/blog/tr/rag-ile-eticaret-deneyimleri-olusturma/images/cover.svg'
featureimage: '/blog/tr/rag-ile-eticaret-deneyimleri-olusturma/images/cover.svg'
featured: true
categories: ['AI']
tags: ['rag', 'vectordb', 'nodejs', 'ai']
translationKey: 'ecommerce-rag-implementation'
authors:
  - Çağatay: /images/author.jpeg
---

# RAG ile Akıllı E-ticaret Deneyimleri Oluşturma

## Giriş

E-ticaret platformları, daha kişiselleştirilmiş ve verimli alışveriş deneyimleri sunmak için sürekli gelişiyor. Geleneksel arama ve öneri sistemleri, müşteri sorgularının arkasındaki nüanslı niyeti anlamada genellikle yetersiz kalıyor. İşte tam bu noktada Retrieval-Augmented Generation (RAG) devreye giriyor; büyük dil modellerinin gücünü hassas bilgi erişimiyle birleştirerek daha akıllı alışveriş asistanları oluşturuyor.

Bu makalede, aşağıdaki özelliklere sahip bir RAG tabanlı e-ticaret asistanının nasıl uygulanacağını inceleyeceğiz:

- Karmaşık ürün sorgularını anlama ve yanıtlama
- Takip soruları için konuşma bağlamını koruma
- Sorunsuz çok dilli destek sağlama
- Vektör veritabanından doğru ürün bilgilerini getirme
- Halüsinasyonlardan kaçınarak insan benzeri yanıtlar üretme

Bu projenin tüm kodlarına [GitHub deposundan](https://github.com/cagatayturkann/ecommerceRAG) erişebilirsiniz.
Asistanı [buradan](https://ecommerce-rag.vercel.app/) test edebilirsiniz.

## 1. E-ticaret için RAG'ı Anlamak

### RAG Nedir?

Retrieval-Augmented Generation (RAG), büyük dil modellerini (LLM'ler) harici bilgi kaynaklarından alınan ilgili bilgilerle geliştiren bir yapay zeka mimarisidir. Geleneksel LLM'lerin aksine, RAG sistemleri güncel ve alana özgü bilgilere erişebilir.

RAG süreci üç ana adımdan oluşur:

1. **Erişim**: Kullanıcı bir soru sorduğunda, sistem ilgili bilgileri veritabanında arar
2. **Zenginleştirme**: Erişilen bilgiler LLM'e gönderilen isteme eklenir
3. **Üretim**: LLM, hem önceden eğitilmiş bilgisine hem de erişilen bilgilere dayanarak bir yanıt üretir

### RAG Neden E-ticaret İçin Mükemmel?

E-ticaret, RAG'ı özellikle değerli kılan benzersiz zorluklar sunar:

- **Ürün Katalogları Sık Sık Değişir**: Yeni ürünler eklenir, fiyatlar değişir ve stok dalgalanır
- **Spesifik Ürün Detayları Önemlidir**: Müşteriler özellikler, uyumluluk ve özellikler hakkında doğru bilgiye ihtiyaç duyar
- **Sorgu Niyeti Karmaşıktır**: "1000 doların altında oyun için iyi bir laptop göster" gibi sorgular birden çok kısıt içerir
- **Takip Soruları Yaygındır**: "Arkadan aydınlatmalı klavyesi var mı?" gibi sorular önceki sorulardan bağlam gerektirir

Geleneksel arama sistemleri anahtar kelimeleri eşleştirir ancak anlamsal anlayışta zorlanır. RAG, semantik arama ile LLM'lerin konuşma yeteneklerini birleştirerek bu boşluğu doldurur.

### E-ticarette RAG vs Geleneksel Arama

| Özellik | Geleneksel Arama | RAG Tabanlı Arama |
| ------- | ------------------ | ---------------- |
| Sorgu Anlama | Anahtar kelime eşleştirme | Anlamsal anlama |
| Sonuç İlgisi | Metin benzerliğine dayalı | Anlam ve bağlama dayalı |
| Belirsizlik Yönetimi | Sınırlı | Güçlü |
| Takip Soruları | Yeni arama gerektirir | Bağlamı korur |
| Yanıt Formatı | Ürün listesi | Ürün detaylarıyla konuşma |
| Ürün Bilgisi | İndekslenen alanlarla sınırlı | Tüm ürün detaylarını içerebilir |

## 2. Vektör Veritabanları ve Gömme İşlemleri

### Vektör Veritabanlarının Rolü

Vektör veritabanları, yüksek boyutlu vektörleri (gömmeleri) verimli bir şekilde depolamak ve sorgulamak için tasarlanmış özel depolama sistemleridir. E-ticaret RAG sistemimizde, anlamsal aramanın temelini oluştururlar.

Vektör veritabanlarının temel özellikleri şunlardır:

- **Benzerlik Araması**: Bir sorgu vektörüne anlamsal olarak benzer vektörleri bulma
- **Verimli İndeksleme**: Hızlı erişim için HNSW (Hierarchical Navigable Small World) gibi algoritmalar
- **Hibrit Arama**: Vektör benzerliğini geleneksel filtrelerle birleştirme
- **Metadata Depolama**: Vektörlerin yanında ürün bilgilerini tutma

E-ticaret RAG projemiz için, güçlü özellikleri, GraphQL API'si ve hibrit arama yetenekleri nedeniyle Weaviate'i vektör veritabanı olarak seçtik.

### Ürün Gömmelerini Oluşturma

Gömmeler, anlamsal anlamı yakalayan metinlerin sayısal temsilleridir. Sistemimizde, ürün bilgileri için gömmeleri Google'ın text-embedding-004 modelini kullanarak oluşturuyoruz.

İşte projemizdeki gömme oluşturma uygulaması:

```javascript
const { GoogleGenerativeAI } = require('@google/generative-ai');
require('dotenv').config();

// Get API key from environment variables
const GEMINI_API_KEY = process.env.GEMINI_API_KEY;

// Initialize API client
const genAI = GEMINI_API_KEY ? new GoogleGenerativeAI(GEMINI_API_KEY) : null;

/**
 * Generates an embedding vector for the given text
 * @param {String} text - Text to generate embedding for
 * @returns {Array} - Embedding vector
 */
async function getEmbedding(text) {
  const model = genAI.getGenerativeModel({ model: 'text-embedding-004' });
  const result = await model.embedContent(text);
  return result.embedding.values;
}
```

Bu gömmeler, ürün açıklamalarını, başlıklarını ve özelliklerini anlamsal benzerlik için karşılaştırılabilecek vektörlere dönüştürmemizi sağlar.

### Vektör Aramasını Uygulama

Ürünlerimiz vektör veritabanına gömüldükten sonra, artık anlamsal arama yapabiliriz. Aşağıdaki kod, bir kullanıcı sorgusuna dayalı olarak ilgili ürünlerin nasıl alınacağını gösterir:

```javascript
/**
 * Retrieves product information from Weaviate vector database
 * @param {String} query - Search query
 * @returns {Array} - Product information
 */
const getProductInfo = async (query) => {
  try {
    const embedding = await getEmbedding(query);

    // Use fixed alpha value for vector search
    const alpha = 0.5;

    // Clean query text for GraphQL - make it safe
    const cleanQuery = query
      .substring(0, 100) // Keep query at reasonable length
      .replace(/[^\w\s]/gi, '') // Remove non-alphanumeric characters
      .replace(/"/g, '') // Remove double quotes
      .trim();

    const data = JSON.stringify({
      query: `{
        Get {
          Ecommerce (
            hybrid: {
              query: "${cleanQuery}"
              alpha: ${alpha},
              vector: ${JSON.stringify(embedding)},
            }
            limit: 3
          ) {
            data
            _additional { score }
          }
        }
      }`,
    });

    const config = {
      method: 'post',
      url: process.env.WEAVIATE_URL + '/v1/graphql',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${process.env.WEAVIATE_API_KEY}`,
      },
      data: data,
    };

    const response = await axios.request(config);
    return response.data.data.Get.Ecommerce.map((item) => item.data);
  } catch (error) {
    console.error('Error during Weaviate query:', error);
    return [];
  }
};
```

Sorgumuzdaki `hybrid` parametresi, vektör aramasını (anlamsal benzerlik) anahtar kelime eşleştirmesiyle birleştirmemizi sağlar. `alpha` değeri (0.5), her iki yaklaşıma da eşit ağırlık vererek anlamsal anlama ile anahtar kelime ilgisi arasında bir denge sağlar.

## 3. Konuşma Yönetimi ve Bağlam

### Konuşma Geçmişini Koruma

Etkili bir e-ticaret asistanı oluşturmadaki temel zorluklardan biri, konuşma bağlamını korumaktır. Kullanıcılar genellikle önceki sorguları veya yanıtları referans alan takip soruları sorarlar.

Projemizde, konuşma geçmişini saklamak için MongoDB kullanıyoruz. Her konuşma, mesaj dizisi içeren bir belge olarak saklanır:

```javascript
/**
 * Creates a new conversation
 * @param {String} title - Conversation title (optional)
 * @returns {Promise<Object>} - Created conversation
 */
const createConversation = async (title = null) => {
  try {
    // Assign default title if null
    const conversationTitle = title || 'New Conversation';
    
    const conversation = new Conversation({
      title: conversationTitle,
      messages: []
    });
    
    await conversation.save();
    return conversation;
  } catch (error) {
    console.error('Error creating conversation:', error);
    throw error;
  }
};

/**
 * Adds a message to a conversation
 * @param {String} conversationId - Conversation ID
 * @param {String} role - Message role (user/assistant)
 * @param {String} content - Message content
 * @returns {Promise<Object>} - Updated conversation
 */
const addMessageToConversation = async (conversationId, role, content) => {
  try {
    const conversation = await Conversation.findById(conversationId);
    if (!conversation) {
      throw new Error(`Conversation not found with ID: ${conversationId}`);
    }
    
    conversation.messages.push({
      role,
      content,
      timestamp: new Date()
    });
    
    conversation.updatedAt = new Date();
    await conversation.save();
    return conversation;
  } catch (error) {
    console.error('Error adding message to conversation:', error);
    throw error;
  }
};
```

### Takip Sorularını Tespit Etme

Sorunsuz bir konuşma deneyimi sağlamak için, sistemimizin bir kullanıcının ne zaman takip sorusu sorduğunu tespit etmesi gerekir. Bu amaçla özel bir sınıflandırıcı ajan kullanıyoruz:

```javascript
/**
 * Determines if a message is a follow-up question based on conversation history
 * @param {Array} conversationHistory - Array of previous conversation messages
 * @returns {String} - JSON string indicating if message is a follow-up (true/false)
 */
const classifierAgent = async (conversationHistory) => {
  try {
    // Format conversation history for the AI model
    const formattedHistory = conversationHistory
      .map((msg) => `${msg.role === 'user' ? 'User' : 'Assistant'}: ${msg.content}`)
      .join('\n');

    const response = await axios({
      method: 'post',
      url: 'https://openrouter.ai/api/v1/chat/completions',
      headers: {
        Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      data: {
        model: 'google/gemini-2.0-flash-001',
        messages: [
          {
            role: 'system',
            content: `You are a sophisticated AI agent specializing in determining if a user's message is a follow-up question to a previous conversation. A follow-up question is a question that refers to or builds upon a previous question or response in the conversation. For example, if a user asks about a product and then asks about its price, the second question is a follow-up question.

            Analyze the conversation history and determine if the last user message is a follow-up question. Return only a JSON boolean value (true or false) as plain text.`,
          },
          {
            role: 'user',
            content: `Here is the conversation history:\n${formattedHistory}\n\nIs the last user message a follow-up question?`,
          },
        ],
      },
    });

    return response.data.choices[0].message.content.replace(/\n+$/, '');
  } catch (error) {
    console.error('OpenRouter API error:', error);
    return 'false';
  }
};
```

Bir takip sorusu tespit edildiğinde, arama sorgusunu önceki mesajlardan gelen bağlamı içerecek şekilde genişletiyoruz:

```javascript
// If it's a follow-up question, include previous user questions
if (isFollowUp && conversationHistory.length >= 3) {
  // Find previous user messages (last 2 user messages)
  const userMessages = conversationHistory.filter((msg) => msg.role === 'user').slice(-2);

  if (userMessages.length >= 2) {
    const previousUserMessage = userMessages[0].content; // Previous user message
    searchQuery = `${await translatorAgent(previousUserMessage)} ${translatedMessage}`;
    console.log(`Expanded search query (follow-up): "${searchQuery}"`);
  }
}
```

Bu yaklaşım, bir kullanıcı belirli bir dizüstü bilgisayar hakkında soru sorduktan sonra "Peki ya pil ömrü nasıl?" gibi bir soru sorduğunda, sistemimizin bağlamı anlamasını ve ilgili bilgileri sağlamasını sağlar.

## 4. Çok Dilli Destek

### Otomatik Dil Algılama ve Çeviri

E-ticaret asistanımızı küresel bir kitleye erişilebilir kılmak için otomatik dil algılama ve çeviri özelliklerini uyguladık. Bu, kullanıcıların anlamsal arama yeteneklerini korurken sistem ile tercih ettikleri dilde etkileşime girmelerini sağlar.

Kullanıcı sorgularını işleme için İngilizce'ye çevirmek üzere bir çevirmen ajan kullanıyoruz:

```javascript
/**
 * Translates user messages to English
 * @param {String} message - User message to translate
 * @returns {String} - Translated message in English
 */
const translatorAgent = async (message) => {
  try {
    const response = await axios({
      method: 'post',
      url: 'https://openrouter.ai/api/v1/chat/completions',
      headers: {
        Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      data: {
        model: 'google/gemini-2.0-flash-001',
        messages: [
          {
            role: 'system',
            content: `You are a sophisticated AI agent specializing in translating user queries from multiple languages into English. Before translating, you identify and correct typos, grammatical errors, and punctuation mistakes in the source text to ensure clarity and readability. Your translations should maintain the original context and be culturally nuanced. Return only the translated text as plain text.`,
          },
          {
            role: 'user',
            content: message,
          },
        ],
      },
    });

    return response.data.choices[0].message.content.replace(/\n+$/, '');
  } catch (error) {
    console.error('OpenRouter API error:', error);
    return message; // Fallback to original message
  }
};
```

Sistem daha sonra kullanıcının sorgusuyla aynı dilde yanıt verir. Bu, kullanıcının dilini eşleştirmesi için talimat verilen AI servisimiz tarafından yönetilir:

```javascript
const messageContent = `
  You are a helpful shopping assistant that can answer questions about products. Based on the user's question "${prompt}", analyze the product information in the context: ${context} and return information only about the most relevant matching product(s). Answer questions related to the ${category} of the product(s). Answer as humanly as possible and in the same language as the ${prompt}, but be careful with special names such as brand, model, etc. - don't translate those.
  
  // ... additional instructions ...
`;
```

### Dil Duyarlı Ürün Bilgileri

Ürün bilgilerini görüntülerken, sistemin kullanıcının dil tercihini korumasını sağlıyoruz. AI, marka adları, model numaraları ve teknik özellikleri korurken açıklamaları kullanıcının dilinde oluşturur.

Örneğin, bir kullanıcı Türkçe olarak "Oyun için iyi bir laptop önerir misiniz?" diye sorduğunda, sistem:

1. Sorguyu İngilizce'ye çevirir: "Can you recommend a good laptop for gaming?"
2. İngilizce sorgu kullanarak anlamsal arama gerçekleştirir
3. Vektör veritabanından ilgili oyun dizüstü bilgisayarlarını getirir
4. Teknik terimleri koruyarak oyun dizüstü bilgisayarlarını Türkçe olarak açıklayan bir yanıt oluşturur

Bu yaklaşım, her dil için ayrı ürün veritabanları gerektirmeden sorunsuz bir çok dilli deneyim sağlar.

## 5. AI Yanıt Üretimi

### Bağlamsal Yanıt Üretimi

İlgili ürün bilgilerini aldıktan sonra, yardımcı ve bağlamsal bir yanıt üretmemiz gerekiyor. Bu amaçla Gemini API'sini kullanıyoruz ve gerekirse OpenRouter'a yedekleme yapıyoruz:

```javascript
/**
 * Generates a response using Gemini or falls back to OpenRouter API
 * @param {String} prompt - User message
 * @param {String} context - Product information or other context
 * @param {String} category - Message category
 * @param {Array} conversationHistory - Conversation history
 * @returns {String} - AI response
 */
const generateGeminiResponse = async (prompt, context, category, conversationHistory = []) => {
  try {
    // Format conversation history for the AI model
    const formattedHistory = conversationHistory
      .map((msg) => `${msg.role === 'user' ? 'User' : 'Assistant'}: ${msg.content}`)
      .join('\n');

    const messageContent = `
      You are a helpful shopping assistant that can answer questions about products. Based on the user's question "${prompt}", analyze the product information in the context: ${context} and return information only about the most relevant matching product(s). Answer questions related to the ${category} of the product(s). Answer as humanly as possible and in the same language as the ${prompt}, but be careful with special names such as brand, model, etc. - don't translate those.
      
      If the category is [PRODUCT_INFO] and the question is about general product information (like price, features, specifications etc), include [SHOW_PRODUCT_INFO] at the end of your response so that product information can be formatted and displayed to the user.
      
      If the question is specifically about product reviews, ratings or customer feedback, do not include [SHOW_PRODUCT_INFO]. Instead, focus on providing a summary of the reviews and ratings from the available data.

      Also consider follow up questions. If the user asks a follow up question about a product mentioned earlier, your answer should be about the SAME product. Do not switch to a different product unless the user explicitly asks about a new product.

      At the end of your response, include a JSON object with an array of product IDs that you referenced in your answer, in the format: [PRODUCT_IDS]{"ids":["id1","id2"]}[/PRODUCT_IDS]

      Here is the conversation history so far:
      ${formattedHistory}
      
      User: ${prompt}
      Assistant:
    `;

    try {
      // Use Gemini API
      const model = genAI.getGenerativeModel({
        model: 'gemini-2.0-flash',
        systemInstruction: messageContent,
      });

      const res = await model.generateContent(prompt);
      return res.response.text();
    } catch (geminiError) {
      // Fall back to OpenRouter API if Gemini fails
      console.log('Gemini API failed, trying OpenRouter API...');

      const openRouterResponse = await axios.post(
        'https://openrouter.ai/api/v1/chat/completions',
        {
          model: 'anthropic/claude-3-opus:beta',
          messages: [{ role: 'user', content: messageContent }],
        },
        {
          headers: {
            Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
            'Content-Type': 'application/json',
          },
        }
      );

      return openRouterResponse.data.choices[0].message.content;
    }
  } catch (error) {
    console.error('Error generating AI response:', error);
    return 'Sorry, an error occurred while generating a response: ' + error.message;
  }
};
```

### Ürün Bilgisi Görüntüleme

Kullanıcı deneyimini geliştirmek için ürün bilgilerini görsel olarak çekici bir şekilde biçimlendiriyoruz. AI yanıtı, ürün detaylarının görüntülenmesi gerektiğinde özel bir etiket `[SHOW_PRODUCT_INFO]` içerir:

```javascript
/**
 * Formats product information into HTML for display
 * @param {Array} products - Array of product objects
 * @returns {String} - HTML formatted product information
 */
const formatProductInfo = (products) => {
  if (!products || products.length === 0) {
    return 'İlgili ürün bilgisi bulunamadı.';
  }

  if (products.error) {
    return products.message;
  }

  let productsHTML = '<div class="products-container">';

  products.forEach((product) => {
    const parsedProduct = typeof product === 'string' ? JSON.parse(product) : product;

    productsHTML += `
      <div class="product-card">
        <div class="product-image">
          <img src="${parsedProduct.thumbnail || parsedProduct.images?.[0]}" 
               alt="${parsedProduct.title || 'Ürün Görseli'}" 
               loading="lazy">
        </div>
        <div class="product-info">
          <h3 class="product-title">${parsedProduct.title || 'İsim belirtilmemiş'}</h3>
          <p class="product-description">${parsedProduct.description || 'Açıklama belirtilmemiş'}</p>
          <div class="product-price-container">
            <span class="product-price">$${parsedProduct.price || 'Fiyat belirtilmemiş'}</span>
          </div>
          <a href="/product/${parsedProduct.id}" class="product-detail-button" target="_blank">
            Detaylar
          </a>
        </div>
      </div>
    `;
  });

  productsHTML += '</div>';
  return productsHTML;
};
```

AI ayrıca yanıtında ürün ID'lerini belirterek hangi ürünlerin görüntüleneceğini de belirleyebilir:

```javascript
// Extract product IDs from AI response
const productIdsMatch = response.match(/\[PRODUCT_IDS\](.*?)\[\/PRODUCT_IDS\]/);

let productsToShow;
if (productIdsMatch) {
  // Get product IDs specified by AI
  const productIds = JSON.parse(productIdsMatch[1]).ids;

  // Filter products to only show those with specified IDs
  productsToShow = products.filter((product) => {
    const parsedProduct = typeof product === 'string' ? JSON.parse(product) : product;
    return productIds.includes(parsedProduct.id.toString());
  });

  // Remove ID tags from response
  response = response.replace(/\[PRODUCT_IDS\].*?\[\/PRODUCT_IDS\]/, '');
} else {
  // Show all products if no IDs specified
  productsToShow = products;
}

// Format product information and add to response
const productInfo = productService.formatProductInfo(productsToShow);
response = response + productInfo;
```

Bu yaklaşım, AI'nin konuşma tarzında bir yanıt sağlarken aynı zamanda uygun olduğunda yapılandırılmış ürün bilgilerini de görüntülemesine olanak tanır.

## 6. Mesaj Kategorizasyonu

### Kullanıcı Niyetini Anlama

Daha ilgili yanıtlar sağlamak için, kullanıcı mesajlarını niyetlerine göre kategorize ediyoruz. Bu, sistemin nasıl yanıt vereceğini ve hangi bilgileri dahil edeceğini belirlemesine yardımcı olur:

```javascript
/**
 * Categorizes user messages into predefined categories
 * @param {String} message - User message to categorize
 * @returns {String} - Category of the message
 */
const categorizerAgent = async (message) => {
  try {
    const response = await axios({
      method: 'post',
      url: 'https://openrouter.ai/api/v1/chat/completions',
      headers: {
        Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      data: {
        model: 'google/gemini-2.0-flash-001',
        messages: [
          {
            role: 'system',
            content: `You are a sophisticated AI agent specializing in categorizing user queries into one of the following categories:
            [PRODUCT_INFO] - Questions about product information, features, specifications, price, etc.
            [PRODUCT_COMPARISON] - Questions comparing multiple products
            [PRODUCT_RECOMMENDATION] - Questions asking for product recommendations
            [PRODUCT_AVAILABILITY] - Questions about product availability
            [PRODUCT_REVIEWS] - Questions about product reviews or ratings
            [GENERAL_INQUIRY] - General questions not related to specific products
            [CUSTOMER_SERVICE] - Questions about customer service, shipping, returns, etc.
            [OTHER] - Any other type of question
            
            Return only the category as plain text.`,
          },
          {
            role: 'user',
            content: message,
          },
        ],
      },
    });

    return response.data.choices[0].message.content.replace(/\n+$/, '');
  } catch (error) {
    console.error('OpenRouter API error:', error);
    return '[GENERAL_INQUIRY]';
  }
};
```

### Kategoriye Göre Yanıt Özelleştirme

Farklı soru kategorileri farklı türde yanıtlar gerektirir. Sistemimiz tespit edilen kategoriye göre davranışını ayarlar:

- **[PRODUCT_INFO]**: Detaylı ürün özellikleri içerir ve ürün kartlarını görüntüler
- **[PRODUCT_COMPARISON]**: Ürünler arasındaki farkları vurgular
- **[PRODUCT_RECOMMENDATION]**: Kullanıcı kriterlerine göre ürünler önerir
- **[PRODUCT_REVIEWS]**: Müşteri geri bildirimleri ve değerlendirmelerine odaklanır
- **[GENERAL_INQUIRY]**: Belirli ürün detayları olmadan genel bilgi sağlar

Bu kategorizasyon, sistemin çok çeşitli kullanıcı sorgularına daha ilgili ve yardımcı yanıtlar sağlamasına yardımcı olur.

## 7. Sistem Mimarisi ve Uygulama

### Temiz Mimari Tasarımı

EcommerceRAG projemiz, net bir sorumluluk ayrımı ile temiz mimari desenini takip eder:

- **Controllers**: HTTP isteklerini ve yanıtlarını yönetir
- **Services**: İş mantığını ve harici API etkileşimlerini içerir
- **Routes**: API uç noktalarını tanımlar
- **Utils**: Yardımcı fonksiyonları sağlar
- **Config**: Yapılandırma ayarlarını yönetir
- **Middlewares**: Ara yazılım fonksiyonlarını uygular

Bu modüler tasarım, kod tabanını daha bakımı kolay, test edilebilir ve ölçeklenebilir hale getirir.

### Ana Bileşenler

Sistemimizin ana bileşenleri şunları içerir:

1. **Chat Controller**: Kullanıcı mesajlarını işler ve yanıt üretimini koordine eder
2. **Vector Service**: Weaviate vektör veritabanı ile etkileşimleri yönetir
3. **AI Service**: AI model etkileşimlerini yönetir (Gemini ve OpenAI)
4. **Product Service**: Ürünle ilgili servisleri sağlar
5. **Conversation Service**: Konuşma kalıcılığını ve erişimini yönetir
6. **Agent Controller**: Çeviri, kategorizasyon ve sınıflandırma için ajan mantığını içerir

### İşlem Akışı

Sistemimizde ana işlem akışı `processChat` fonksiyonu tarafından yönetilir:

```javascript
/**
 * Processes chat messages and generates AI responses
 * @param {Object} req - Express request object
 * @param {Object} res - Express response object
 */
const processChat = async (req, res) => {
  try {
    let { message, conversationId } = req.body;

    if (!message) {
      return res.status(400).json({ error: 'Message is required' });
    }

    // Categorize the message
    const category = await categorizerAgent(message);

    // Get existing conversation or create new one
    let conversation;
    try {
      if (conversationId) {
        conversation = await conversationService.getConversationById(conversationId);
      } else {
        conversation = await conversationService.createConversation();
      }
    } catch (error) {
      // Create a new conversation in case of error
      conversation = await conversationService.createConversation();
    }

    // Add user message to conversation
    await conversationService.addMessageToConversation(conversation._id, 'user', message);

    // Get conversation history (last 4 messages)
    const conversationHistory = await conversationService.getConversationMessages(conversation._id, 4);

    // Translate message to English
    let translatedMessage = await translatorAgent(message);

    // Determine if message is a follow-up question
    let isFollowUp = false;
    if (conversationHistory.length >= 3) {
      // Use classifierAgent to determine if it's a follow-up
      const response = await classifierAgent(conversationHistory);
      const parsedResponse = JSON.parse(response);
      isFollowUp = parsedResponse;
    }

    // Prepare search query
    let searchQuery = translatedMessage;
    if (isFollowUp && conversationHistory.length >= 3) {
      // Find previous user messages (last 2 user messages)
      const userMessages = conversationHistory.filter((msg) => msg.role === 'user').slice(-2);
      if (userMessages.length >= 2) {
        const previousUserMessage = userMessages[0].content; // Previous user message
        searchQuery = `${await translatorAgent(previousUserMessage)} ${translatedMessage}`;
      }
    }

    // Get product information from Weaviate
    const products = await vectorService.getProductInfo(searchQuery);

    // Process response based on product information
    let response;
    if (products && products.length > 0) {
      // Send product information to AI in JSON format
      const productContext = JSON.stringify(products);

      // Send category and conversation history to AI
      response = await aiService.generateGeminiResponse(message, productContext, category, conversationHistory);
      
      // Clean [SHOW_PRODUCT_INFO] tag
      const cleanedResponse = response.replace('[SHOW_PRODUCT_INFO]', '');
      await conversationService.addMessageToConversation(conversation._id, 'assistant', cleanedResponse);

      // Check if product information should be displayed
      if (response.includes('[SHOW_PRODUCT_INFO]')) {
        // Process and display product information
        // ... (code omitted for brevity)
      }
    } else {
      // If no product information found
      const productInfo = JSON.stringify({ message: 'İlgili ürün bilgisi bulunamadı.' });
      response = await aiService.generateGeminiResponse(message, productInfo, category, conversationHistory);
      const cleanedResponse = response.replace('[SHOW_PRODUCT_INFO]', '');
      await conversationService.addMessageToConversation(conversation._id, 'assistant', cleanedResponse);
    }

    // Return response
    res.json({
      response: response,
      conversationId: conversation._id.toString(),
      timestamp: new Date().toISOString(),
    });
  } catch (error) {
    console.error('Chat processing error:', error);
    res.status(500).json({ error: 'Mesaj işlenirken bir hata oluştu: ' + error.message });
  }
};
```

Bu fonksiyon, kullanıcı mesajını almaktan AI tarafından oluşturulan yanıtı döndürmeye kadar tüm süreci orkestra eder.

## 8. Frontend Entegrasyonu

### Sohbet Widget'ı Uygulaması

RAG sistemimizi kullanıcılara erişilebilir kılmak için, herhangi bir e-ticaret web sitesine kolayca entegre edilebilen bir sohbet widget'ı uyguladık. Widget, vanilla JavaScript ile oluşturulmuştur ve herhangi bir sayfaya basit bir script etiketi ile eklenebilir:

```html
<script src="https://your-domain.com/js/widget.js"></script>
```

Widget, kullanıcıların AI asistanımızla etkileşime girmesi için temiz, sezgisel bir arayüz sağlar:

```javascript
// Widget initialization
function initWidget() {
  // Create widget HTML
  const widgetContainer = document.createElement('div');
  widgetContainer.innerHTML = createWidgetHTML();
  document.body.appendChild(widgetContainer);

  // Set up event listeners
  const chatForm = document.getElementById('chachaForm');
  const messageInput = document.getElementById('chachaMessageInput');
  const chatMessages = document.getElementById('chachaMessages');
  
  // Initialize conversationId as null (no longer using localStorage)
  let conversationId = null;

  // Add welcome message
  addMessage('Merhaba! Alışverişinizde size nasıl yardımcı olabilirim?', 'bot');

  // Form submission handler
  chatForm.addEventListener('submit', async (e) => {
    e.preventDefault();
    const message = messageInput.value.trim();
    if (!message) return;

    // Add user message to chat
    addMessage(message, 'user');
    messageInput.value = '';
    messageInput.style.height = 'auto';

    // Add loading indicator
    const loadingMessage = addLoadingMessage();

    try {
      // Send message to API
      const response = await fetch('/api/chat', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          message,
          conversationId,
        }),
      });

      // Remove loading indicator
      loadingMessage.remove();

      if (response.ok) {
        const data = await response.json();
        
        // Add AI response to chat
        if (data.response) {
          addMessage(data.response, 'bot', true);
        } else if (data.data && data.data.message) {
          addMessage(data.data.message, 'bot', true);
        } else if (data.message) {
          addMessage(data.message, 'bot', true);
        } else {
          addMessage('Üzgünüm, bir hata oluştu. Lütfen tekrar deneyin.', 'bot');
        }

        // Store conversation ID for future messages
        if (data.conversationId) {
          conversationId = data.conversationId;
        }
      } else {
        addMessage('Üzgünüm, bir hata oluştu. Lütfen tekrar deneyin.', 'bot');
      }
    } catch (error) {
      console.error('Error:', error);
      loadingMessage.remove();
      addMessage('Üzgünüm, bir hata oluştu. Lütfen tekrar deneyin.', 'bot');
    }
  });
}

### Duyarlı Tasarım

Sohbet widget'ı hem masaüstü hem de mobil cihazlarda iyi çalışacak şekilde duyarlı olarak tasarlanmıştır:

```javascript
// Mobile device detection
const isMobile = window.matchMedia('(max-width: 480px)').matches;

// Chat toggle functionality
chatToggle.addEventListener('click', () => {
  chatContainer.classList.toggle('collapsed');

  // Prevent body scroll when chat is open on mobile
  if (isMobile && !chatContainer.classList.contains('collapsed')) {
    document.body.style.overflow = 'hidden';
  } else if (isMobile) {
    document.body.style.overflow = '';
  }
});

// Window resize handler
window.addEventListener('resize', () => {
  const isMobileNow = window.matchMedia('(max-width: 480px)').matches;

  // Update body scroll behavior on resize
  if (isMobileNow && !chatContainer.classList.contains('collapsed')) {
    document.body.style.overflow = 'hidden';
  } else if (isMobileNow) {
    document.body.style.overflow = '';
  }
});
```

### HTML Oluşturma ve Stil

Widget, temiz, modern bir görünüm için CSS stilleri içerir:

```css
.chacha-widget {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 1000;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.chat-toggle {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: #2563EB;
  color: white;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
}

.chat-container {
  position: absolute;
  bottom: 80px;
  right: 0;
  width: 350px;
  height: 500px;
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 5px 25px rgba(0, 0, 0, 0.2);
  display: flex;
  flex-direction: column;
  transition: all 0.3s ease;
  overflow: hidden;
}

/* Additional styles omitted for brevity */
```

Widget, mesajlar ve ürün kartları için HTML'i dinamik olarak oluşturarak zengin bir kullanıcı deneyimi sağlar.

## 9. Test ve Değerlendirme

### Postman ile API Testi

Sistemimizin doğru çalıştığından emin olmak için, tüm uç noktaları test etmek üzere kapsamlı bir Postman koleksiyonu oluşturduk:

```json
{
  "info": {
    "name": "ecommerceRAG API",
    "description": "A collection for testing the ecommerceRAG API endpoints",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Chat",
      "description": "Endpoints for chat functionality",
      "item": [
        {
          "name": "Process Chat Message",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"message\": \"What laptops do you have?\",\n    \"conversationId\": null\n}"
            },
            "url": {
              "raw": "{{baseUrl}}/api/chat",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "api",
                "chat"
              ]
            },
            "description": "Process a chat message and get a response from the AI"
          }
        },
        {
          "name": "Process Follow-up Message",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"message\": \"What is the price range?\",\n    \"conversationId\": \"{{conversationId}}\"\n}"
            },
            "url": {
              "raw": "{{baseUrl}}/api/chat",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "api",
                "chat"
              ]
            },
            "description": "Process a follow-up message in an existing conversation"
          }
        }
      ]
    },
    {
      "name": "Conversations",
      "description": "Endpoints for managing conversations",
      "item": [
        {
          "name": "Get All Conversations",
          "request": {
            "method": "GET",
            "url": {
              "raw": "{{baseUrl}}/api/conversations",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "api",
                "conversations"
              ]
            },
            "description": "Get a list of all conversations"
          }
        }
        // Additional endpoints omitted for brevity
      ]
    },
    {
      "name": "Products",
      "description": "Endpoints for product information",
      "item": [
        {
          "name": "Get Product Detail",
          "request": {
            "method": "GET",
            "url": {
              "raw": "{{baseUrl}}/product/{{productId}}",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "product",
                "{{productId}}"
              ]
            },
            "description": "Get detailed information about a specific product"
          }
        }
      ]
    }
  ]
}
```

### Performans Değerlendirmesi

Sistemimizi birkaç temel metrik üzerinde değerlendirdik:

1. **Yanıt Süresi**: Yanıt üretmek için gereken ortalama süre
2. **İlgililik**: Getirilen ürünlerin kullanıcı sorgusuna ne kadar iyi eşleştiği
3. **Konuşma Tutarlılığı**: Sistemin takip sorularında bağlamı ne kadar iyi koruduğu
4. **Dil Desteği**: Farklı dillerdeki yanıtların doğruluğu

Testimiz, sistemin bu metrikler genelinde iyi performans gösterdiğini, özellikle konuşma bağlamını koruma ve ilgili ürün bilgilerini sağlama konularında güçlü sonuçlar elde ettiğini gösterdi.

## 10. Gelecek İyileştirmeler

Mevcut uygulamamız bir e-ticaret RAG sistemi için sağlam bir temel sağlarken, gelecekte iyileştirilebilecek birkaç alan bulunmaktadır:

### Kişiselleştirme

Future versions could incorporate user preferences and purchase history to provide more personalized recommendations. This would involve:

- Tracking user interactions and preferences
- Incorporating user profile information in the RAG process
- Adjusting product rankings based on user history

### Enhanced Product Comparison

We could improve the system's ability to compare products by:

- Implementing structured product attribute extraction
- Creating specialized prompts for comparison queries
- Developing visual comparison displays

### Voice Interface

Adding voice input and output would make the system more accessible and convenient, especially for mobile users:

- Implementing speech-to-text for user queries
- Adding text-to-speech for AI responses
- Supporting voice-specific interaction patterns

### Analytics and Feedback Loop

Implementing analytics would help improve the system over time:

- Tracking successful vs. unsuccessful interactions
- Analyzing common user queries and pain points
- Using feedback to fine-tune the RAG process

## Conclusion

In this article, we've explored how Retrieval-Augmented Generation (RAG) can transform e-commerce applications by providing more intelligent, context-aware product search and recommendations. We've implemented a complete RAG system that combines vector databases, semantic search, conversation memory, and multi-language support to create a powerful shopping assistant.

The key components of our system include:

- **Vector Database**: Storing product embeddings for semantic search
- **Conversation Management**: Maintaining context for follow-up questions
- **Multi-language Support**: Allowing users to interact in their preferred language
- **AI Response Generation**: Creating helpful, contextual responses
- **Message Categorization**: Understanding user intent
- **Clean Architecture**: Ensuring maintainability and scalability

By combining these components, we've created a system that can understand complex product queries, maintain conversation context, and provide accurate, helpful responses across multiple languages.

You can access the complete code for this project at [GitHub repository](https://github.com/cagatayturkan/ecommerceRAG) and use it as a starting point for your own RAG-based applications.

---