---
title: Blog Post with Inline Images
subtitle: "Blog post subtitle :zap:"
summary: Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
date: 2023-11-24
cardimage: /images/photo1_card.jpeg
featureimage: /images/photo1.jpeg
caption: Image caption
authors:
  - Christian: /images/author.jpeg
---
Use the shortcode "figArray" to add images to your blog post. Add your images to a subfolder. Call the figArray shortcode using the following syntax:

```
{{</* figArray subfolder="<subfoldername>" figCaption="Some caption" numCols=2 */>}}
```
Both "figCaption" and "numCols" are optional. The shortcode will try to guess the best number of columns to use for the array of figures if "numCols" is not passed.
You will need one subfolder containing images per call to the shortcode. The image files need to be one of the following types: png, jpg, jpeg or webp.

{{< figArray subfolder="images" figCaption="A nice figure caption :wave:" >}}

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec id erat enim. Ut cursus magna sed luctus auctor. Sed eu augue dignissim, lobortis ipsum eu, dictum nisi. Integer varius ex maximus quam lobortis accumsan. Morbi mollis vulputate metus, aliquam feugiat arcu porta a. Quisque id justo ultricies, lacinia elit quis, pulvinar odio. Fusce feugiat at velit vitae lobortis. Nulla fringilla metus et sem mattis posuere :zap:.

**Heading**
- List item 1
- List item 2
- List item 3

Cras ligula velit, aliquet ac orci dapibus, molestie ultricies libero. Donec at bibendum est. Phasellus vulputate dapibus quam vel accumsan. Curabitur at felis euismod, lobortis urna id, lobortis dolor. Cras tortor ligula, euismod quis lacus faucibus, condimentum fringilla dolor. Mauris in maximus nisi. Phasellus facilisis lacus quis mi cursus, a ornare mi maximus. Nullam nunc lacus, tincidunt varius risus nec, pellentesque vulputate ligula. Etiam id purus et tortor porta mattis. Donec id sapien nulla. Vivamus at malesuada tellus, id ultricies ante. Maecenas ullamcorper mi massa, at rutrum risus aliquet a. Donec sem tortor, molestie quis ex a, faucibus commodo augue. Morbi convallis sem vel tellus facilisis, et sodales felis consequat. Aliquam ut ante tristique, volutpat lectus vestibulum, egestas sapien.

Donec tellus est, faucibus eget ultricies ac, posuere non augue. Fusce ultrices lectus quis nunc lacinia, non tincidunt lectus ultrices. Morbi sodales nisi at felis luctus, eu convallis tortor commodo. Morbi tristique nibh neque, vel tristique dolor laoreet eget. Phasellus felis erat, mattis at suscipit id, faucibus in dolor. In vitae odio at lectus tincidunt dignissim. Fusce risus nisl, hendrerit a ultricies vitae, porta id sapien. Nam elit nunc, hendrerit ut sem quis, ultrices varius leo. Nullam eget lectus in sapien venenatis iaculis at at turpis. Etiam iaculis magna porttitor augue tempus suscipit. Interdum et malesuada fames ac ante ipsum primis in faucibus. Nullam suscipit nibh leo, in pellentesque urna luctus et. Duis diam ipsum, posuere nec tellus sit amet, dignissim feugiat massa. Etiam ut sollicitudin lorem. Quisque commodo libero non mauris viverra malesuada. Morbi vitae auctor felis.

Maecenas ac dignissim dolor. Sed vitae nisl vel ante rutrum tincidunt ac et diam. Integer id dignissim quam. Vestibulum quis enim sit amet tellus tincidunt sagittis ut vitae nunc. Sed hendrerit, quam ut fermentum imperdiet, augue purus cursus felis, in ultricies elit mauris in risus. Morbi hendrerit imperdiet vehicula. Etiam porttitor magna eu quam laoreet ullamcorper. Etiam a erat ante. Curabitur pharetra, lacus in porttitor cursus, libero lacus consectetur dui, sit amet auctor tellus magna et enim. Pellentesque tristique molestie fringilla. Vivamus sit amet tincidunt quam. Morbi eu nisi quam. Nunc ultrices vel sem sit amet aliquam.

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

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1702248407591/035d0d44-fe2f-4f58-9bec-2fe234241130.png align="center")

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