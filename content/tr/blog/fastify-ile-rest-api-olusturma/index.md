---
title: 'Fastify ile RESTful API Oluşturma'
# subtitle: "Blog post subtitle :zap:"
summary: Fastify, Node.js için yüksek performans ve düşük kaynak tüketimi sağlamaya odaklanan modern bir web framework'tür. Bu yazıda Fastify'ın ne olduğunu, nasıl kullanıldığını ve en iyi uygulama önerilerini detaylı olarak anlatacağım.
date: 2025-02-07  
cardimage: '/tr/blog/fastify-ile-rest-api-olusturma/images/cover.png'
featureimage: 'tr/blog/fastify-ile-rest-api-olusturma/images/cover.png'
featured: true
# caption: Image caption
categories: ['Node.js']
tags: ['nodejs', 'fastify']
authors:
  - Çağatay: /images/author.jpeg
---

Herkese merhabalar! Bu yazımda sizlere Node.js için hızlı ve düşük kaynak tüketen bir web framework olan Fastify'ı anlatacağım. Birlikte basit bir TODO API'si oluşturacağız ve Fastify'ın özelliklerinin geliştirme sürecinizi nasıl daha verimli hale getirebileceğini açıklayacağım.

Bu projenin tüm kaynak koduna GitHub üzerinden ulaşabilirsiniz: [fastify-nodejs-restful-api](https://github.com/cagatayturkann/blogProjects/tree/main/fastify-nodejs-restful-api)

### Fastify Nedir ve Neden Kullanmalıyız?

Fastify, Node.js için geliştirilmiş, yüksek performansa ve düşük kaynak kullanımına odaklanan modern bir web framework'tür. Node.js geliştiricilerinin çoğu Express.js'e aşina olsa da, Fastify bazı önemli avantajlar sunuyor:
- Express'e göre 2 kata kadar daha hızlı
- Dahili şema doğrulama sistemi
- Otomatik Swagger dokümantasyonu
- Plugin tabanlı mimari

### Projemizi Oluşturalım

Öncelikle projemizi kuralım. İlk olarak bazı bağımlılıkları yüklememiz gerekiyor. Yeni bir dizin oluşturun ve şu komutları çalıştırın:

```bash
mkdir fastify-todo-api
cd fastify-todo-api
npm init -y
```

Şimdi ihtiyacımız olan paketleri yükleyelim:

```json
{
  "dependencies": {
    "fastify": "^3.29.0",
    "fastify-swagger": "^5.2.0",
    "uuid": "^8.3.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.16"
  }
}
```

### Proje Yapısı

Kodlamaya başlamadan önce proje yapımızı düzenleyelim. Temiz ve bakımı kolay bir yaklaşım izleyeceğiz:

```
fastify-todo-api/
├── app.js           # Ana sunucu dosyası
├── items.js         # Veri deposu
├── routes/
│   └── todoRouter.js    # Route tanımlamaları
└── controllers/
    └── todoController.js # İstek işleyicileri
```

### İlk Fastify Sunucumuzu Oluşturalım

Ana sunucu dosyamızla (`app.js`) başlayalım. İşte temel bir Fastify sunucusunu nasıl kurarız:

```javascript
const fastify = require('fastify')({ logger: true });

// Swagger dokümantasyonunu ayarlıyoruz
fastify.register(require('fastify-swagger'), {
  exposeRoute: true,
  routePrefix: '/docs',
  swagger: {
    info: { title: 'fastify-api' },
  },
});

// Route'larımızı kaydediyoruz
fastify.register(require('./routes/todoRouter'));

const PORT = 5000;
const start = async () => {
  try {
    await fastify.listen(PORT);
  } catch (error) {
    fastify.log.error(error);
    process.exit(1);
  }
};

start();
```

Bu kodda neler oluyor?
- Loglama özelliği etkin bir Fastify örneği oluşturuyoruz
- Swagger dokümantasyonunu ayarlıyoruz (/docs adresinden erişilebilir)
- Route'larımızı Fastify'ın plugin sistemi ile kaydediyoruz
- Sunucuyu 5000 portunda başlatıyoruz

### Fastify'ın Şema Doğrulama Sistemini Anlayalım

Fastify'ın en güçlü özelliklerinden biri şema doğrulama sistemidir. `todoRouter.js` dosyasında nasıl kullanacağımıza bakalım:

```javascript
// Önce bir TODO öğesinin nasıl görüneceğini tanımlıyoruz
const Item = {
  type: 'object',
  properties: {
    id: { type: 'string' },
    name: { type: 'string' },
  },
};

// Sonra endpoint'lerimiz için şemalar oluşturuyoruz
const getItemsOpts = {
  schema: {
    response: {
      200: {
        type: 'array',
        items: Item,
      },
    },
  },
  handler: getItems,
};
```

Bu neden özel?
- Fastify otomatik olarak tüm gelen ve giden verileri doğrular
- Bu şemalardan Swagger dokümantasyonu oluşturur
- Serileştirmeyi optimize ederek performansı artırır
- Hataları handler'lara ulaşmadan yakalar

### Controller'larımızı Oluşturalım

Şimdi `todoController.js` dosyasında istekleri nasıl işleyeceğimize bakalım:

```javascript
let items = require('../items');
const { v4: uuidv4 } = require('uuid');

// Tüm öğeleri getir
const getItems = (req, reply) => {
  reply.send(items);
};

// Yeni öğe oluştur
const addItem = (req, reply) => {
  const { name } = req.body;
  const item = {
    id: uuidv4(),
    name,
  };
  items = [...items, item];
  reply.code(201).send(item);
};
```

Fastify'ın yanıt işlemeyi nasıl kolaylaştırdığına dikkat edin:
- Content-Type başlıklarını manuel ayarlamaya gerek yok
- Durum kodları ve yanıt gönderme için method chaining
- Otomatik yanıt serileştirme

### API'mizi Test Edelim

Her şeyi ayarladığımıza göre, API'mizi test edelim. curl veya herhangi bir API test aracı kullanabilirsiniz:

```bash
# Tüm öğeleri al
curl http://localhost:5000/items

# Yeni öğe oluştur
curl -X POST \
  http://localhost:5000/items \
  -H 'Content-Type: application/json' \
  -d '{"name":"Fastify Öğren"}'
```

### Bilmeniz Gereken Performans Özellikleri

Fastify tesadüfen hızlı değil. İşte neden bu kadar iyi performans gösteriyor:

1. **Şema Tabanlı Serileştirme**
   - Daha hızlı doğrulama için şemaları önceden derler
   - JSON serileştirmeyi optimize eder
   - İşlem yükünü azaltır

2. **Verimli Yönlendirme**
   - Route eşleştirme için radix tree kullanır
   - Regex tabanlı yönlendirmeden daha hızlıdır
   - Optimize edilmiş parametre işleme

3. **Hafif Çekirdek**
   - Minimum temel yük
   - Özellik eklemek için plugin sistemi
   - Verimli bellek kullanımı

### Önerdiğim En İyi Uygulamalar

Fastify ile çalıştıktan sonra, işime yarayan bazı pratikler:

1. **Her Zaman Şema Kullanın**
   ```javascript
   fastify.get('/items', {
     schema: {
       response: {
         200: itemSchema
       }
     }
   })
   ```

2. **Plugin'lerle Organize Edin**
   ```javascript
   fastify.register(require('./routes/items'))
   fastify.register(require('./routes/users'))
   ```

3. **Hataları Düzgün Yönetin**
   ```javascript
   fastify.setErrorHandler(function (error, request, reply) {
     reply.status(error.statusCode || 500).send({error: error.message})
   })
   ```

### Sonuç

Fastify, yüksek performanslı Node.js API'leri oluşturmak için mükemmel bir temel sağlıyor. Şema doğrulama ve swagger dokümantasyonu gibi dahili özellikleri, onu modern web uygulamaları için harika bir seçim haline getiriyor. Gelecek yazılarımda, daha gelişmiş Fastify özelliklerini ve bunları production ortamında nasıl etkili bir şekilde kullanabileceğimizi anlatacağım. Okuduğunuz için teşekkürler!

Tüm bu özellikler hakkında daha detaylı bilgi için [Fastify dokümantasyonunu](https://www.fastify.io/docs/latest/) incelemeyi unutmayın.



