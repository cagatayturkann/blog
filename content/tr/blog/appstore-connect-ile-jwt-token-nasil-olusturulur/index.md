---
title: "App Store Connect API için JWT Token Nasıl Oluşturulur?"
# subtitle: "Blog post subtitle :zap:"
summary: App Store Connect API, App Store Connect'te gerçekleştirdiğiniz işlemleri otomatikleştiren bir REST API'dir. Bu API, geliştiricilere uygulama gönderme ve güncelleme işlemlerinden, iç uygulama satın almalarını yönetmeye, uygulama performansını ve kullanıcı etkileşimini..
date: 2023-11-25
cardimage:  /tr/blog/appstore-connect-ile-jwt-token-nasil-olusturulur/images/bg.jpg
featureimage: /tr/blog/appstore-connect-ile-jwt-token-nasil-olusturulur/images/bg.jpg
featured: true
categories: ["Geliştirme", "iOS"]
tags: ["jwt", "app-store", "api"]
translationKey: "app-store-connect-jwt"
# caption: Image caption
authors:
  - Çağatay: /images/author.jpeg
---
\
Herkese merhabalar! App Store Connect API hakkında ilk blog yazısına hoşgeldiniz. Bu yazımda sizlere kısaca App Store Connect API'den ve API'yi kullanabilmek için nasıl JWT token oluşturabileceğinizi anlatacağım.

### App Store Connect API Nedir?

App Store Connect API, App Store Connect'te gerçekleştirdiğiniz işlemleri otomatikleştiren bir REST API'dir. Bu API, geliştiricilere uygulama gönderme ve güncelleme işlemlerinden, iç uygulama satın almalarını yönetmeye, uygulama performansını ve kullanıcı etkileşimini kapsamlı raporlar aracılığıyla izlemeye, müşteri değerlendirmelerine ve geri bildirimlere etkili bir şekilde yanıt vermeye kadar çeşitli görevleri sorunsuz bir şekilde yerine getirme imkanı sunar.

### Neden JWT Token?

Token olmadan, App Store Connect API'den yanıt alamazsınız, ancak genellikle API, kimlik doğrulama ve yetkilendirme amaçları için JSON Web Token (JWT) gerektirir. API bağlamında JWT token, istemcinin kimliğini doğrulamak ve istenen kaynaklara erişim için gerekli izinlere sahip olduğunu sağlamak için güvenli ve standart bir yöntem olarak hizmet eder.

### JWT Token Nasıl Oluşturulur?

App Store Connect API'yi kullanmak için JWT tokenları oluşturmadan önce birkaç adımı tamamlamanız gerekmektedir.

* App Store Connect GUI üzerinden API Key oluşturun
    
* Özel anahtarı p8 formatında kaydedin.
    
* Issuer ID ve API Key ID değerlerini kopyalayın.
    

### API Key Oluşturma

API anahtarı oluşturmak için, hesabınızla App Store Connect web arayüzüne giriş yapın ve Users&Access sayfasına gidin. Sayfadaki "Keys" sekmesine tıklayın. "Keys" sekmesini görebilmek için hesabınızın bu izne sahip olması gerekmektedir. Belirli bir amaç için API anahtarı veya tüm App Store Connect API'ına erişim sağlayan yönetici API anahtarı oluşturabilirsiniz.

{{< figure src="./images/apiKey.png" alt="api key page">}}

'Active' metni yanındaki artı ikonuna tıklayın, anahtar için bir isim yazın, açılan modalda anahtara erişebilecek rolleri seçin ve 'Generate' düğmesine tıklayın. Ardından anahtar oluşturulacak ve listelenecektir.

### Özel Anahtarı Kaydedin

API Anahtarı oluşturulduktan sonra Özel Anahtarı bilgisayarınıza kaydetmeniz gerekiyor. Anahtar genellikle .p8 formatındadır. Özel anahtarla uğraşırken akılda tutmanız gereken bazı önemli noktalar bulunmaktadır.

* Özel anahtar yalnızca bir kez kaydedilebilir. Kaydedildikten sonra mutlaka güvenli bir şekilde saklanması önemlidir.
    
* Özel anahtar asla süresi dolmaz ve geçerli olduğu sürece çalışır; hatta tehlikeye girmiş olsa bile. Eğer anahtarınızın artık güvenli olmadığını düşünüyorsanız, en kısa sürede App Store Connect'ten iptal edin ve yeni bir anahtar alın.
    

### Issuer ID ve API Key ID Değerlerini Kopyalayın

JWT token oluşturmadan önce son adım, Users&Access sayfasında bulabileceğiniz Issuer ID ve API Key ID'yi kopyalamaktır.

### JWT Token Oluşturma

Daha önce belirttiğim gibi, JWT, App Store Connect API tarafından kullanılan belirli bir tokenı oluşturmak için kullanılır. Token oluşturma süreci aşağıdaki adımları içerir:

* **IssuerID:** User&Access sayfasından kopyalanan Issuer ID bilgisi.
    
* **Private Key:** .p8 formatında kaydedilen özel anahtar.
    
* **Expiration Time:** Maksimum 20 dakika, token 20 dakikadan fazla geçerli olamaz, bu nedenle süresi dolmadan yeni bir token oluşturduğumuzdan emin olmalıyız.
    
* **Audience:** Genellikle 'applestoreconnect-v1' olarak ayarlanan API sürüm değeri ile sabit bir değerdir.
    
* **Algorithm:** JWT algoritması, örneğin ES256, token oluşturmak için gereklidir.
    

Gerekli tüm detaylara sahip olduktan sonra, istediğiniz dil kullanarak JWT token oluşturabileceksiniz. Ben bu süreç için Node.js kullanacağım.

Aşağıdaki komutları bilgisayarınızda sırasıyla çalıştırın:

```plaintext
mkdir appStoreToken
cd appStoreToken
npm init -y
npm i jsonwebtoken
touch index.js
```

Aşağıdaki kodu index.js dosyanıza yapıştırın ve ilgili alanları kendi bilgilerinizle değiştirip kaydedin.

```javascript
const fs = require("fs");
const jwt = require("jsonwebtoken");
const privateKey = fs.readFileSync("yourPrivateKey.p8");
const apiKeyId = "Your API Key ID";
const issuerId = "Your Issuer ID";

let now = Math.round(new Date().getTime() / 1000); // Notice the /1000
let nowPlus20 = now + 1199; // 1200 === 20 minutes

let payload = {
  iss: issuerId,
  exp: nowPlus20,
  aud: "appstoreconnect-v1",
};

let signOptions = {
  algorithm: "ES256", 
  header: {
    alg: "ES256",
    kid: apiKeyId,
    typ: "JWT",
  },
};

let token = jwt.sign(payload, privateKey, signOptions);
console.log("@token: ", token);
```

ve son olarak aşağıdaki komutu çalıştırın.

```plaintext
node index.js
```

Bu işlem, App Store Connect API'ye erişim için kullanabileceğimiz uzun bir token döndürecektir. Ayrıca, API'yi 20 dakika sonrasında kullanmaya devam etmek istiyorsak başka bir token oluşturmamız gerekecektir.

### Sonuç

Bu yazıdma, App Store Connect API için Node.js kullanarak bir JWT tokenı nasıl oluşturulur konusunu anlatmaya çalıştım. Bu, API'ye olan isteklerinizi doğrulamanın kritik bir adımıdır. Gelecekteki yazılarda, App Store Connect API'yi kullanmanın diğer yollarını anlatmaya çalışacağım. Okuduğunuz için teşekkür ederim.

[Bu yazıyı İngilizce oku](https://cagatayturkan.com/blog-en/how-to-create-jwt-token-using-app-store-connect-api/)