---
title: "TypeScript'i Anlamak - Bölüm 5: TypeScript Derleyicisi"
summary: Bu makalede, TypeScript derleyicisini detaylı bir şekilde inceleyeceğiz. Derleyici ayarları, yapılandırma seçenekleri ve proje yapılandırmasının nasıl yönetileceğini öğreneceğiz.
date: 2025-02-12T17:05:00+03:00
cardimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum5/images/cover.svg'
featureimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum5/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'compiler', 'configuration']
translationKey: 'typescript-compiler-guide'
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! TypeScript serimizin bu bölümünde, kodlarımızın nasıl JavaScript'e dönüştüğünü ve bu süreci nasıl yönetebileceğimizi inceleyeceğiz. TypeScript derleyicisi ve yapılandırma seçenekleri, projelerimizin temelini oluşturan önemli konular arasında yer alıyor.

## TypeScript Derleyicisi Nedir?

TypeScript derleyicisi (tsc), yazdığımız TypeScript kodunu tarayıcıların anlayabileceği JavaScript koduna dönüştüren araçtır. Modern TypeScript özelliklerini kullanarak yazdığımız kodları, hedeflediğimiz JavaScript sürümüne uygun hale getirir.

### Projeyi Başlatma: tsc --init

TypeScript projemizi başlatırken ilk adımımız, bir yapılandırma dosyası oluşturmak. Bunu `tsc --init` komutu ile yapabiliyoruz:

```bash
npx tsc --init
```

Bu komut, projemizin kök dizininde bir `tsconfig.json` dosyası oluşturur. Bu dosya, derleyicinin davranışını belirleyen temel ayarları içerir:

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## Watch Mode ile Geliştirme

Geliştirme sürecinde kodumuzda yaptığımız değişiklikleri anında derlemek için Watch Mode kullanabiliriz:

```bash
tsc --watch
# veya
tsc -w
```

Watch Mode'un sağladığı avantajlar:

1. Dosya değişikliklerini otomatik algılar
2. Anlık derleme gerçekleştirir
3. Hataları gerçek zamanlı gösterir
4. Geliştirme sürecini hızlandırır

```typescript
// örnek.ts
let mesaj = 'Merhaba TypeScript!';
console.log(mesaj);

// Watch Mode aktifken, dosyayı kaydettiğinizde
// otomatik olarak JavaScript dosyası güncellenir
```

## Çoklu Dosya Derleme

TypeScript derleyicisi, projemizdeki tüm TypeScript dosyalarını tek bir komutla derleyebilir. Proje dizininde `tsc` komutunu çalıştırdığımızda, tüm `.ts` dosyaları otomatik olarak derlenir:

```typescript
// kullanici.ts
export interface Kullanici {
  id: number;
  ad: string;
}

// servis.ts
import { Kullanici } from './kullanici';
export class KullaniciServisi {
  // servis kodları
}

// Her iki dosya da tek komutla derlenir
```

## tsconfig.json ve Temel Ayarlar

### Files Seçeneği

`tsconfig.json` dosyasında `files` seçeneği, derlenecek dosyaları açıkça belirtmemizi sağlar:

```json
{
  "compilerOptions": {
    // diğer seçenekler
  },
  "files": ["src/main.ts", "src/utils/helpers.ts", "src/types/index.d.ts"]
}
```

### Include ve Exclude Seçenekleri

Projemizde hangi dosyaların derlenip hangilerinin derlenmeyeceğini belirlemek için `include` ve `exclude` seçeneklerini kullanırız:

```json
{
  "compilerOptions": {
    // diğer seçenekler
  },
  "include": [
    "src/**/*" // src klasörü altındaki tüm dosyalar
  ],
  "exclude": [
    "node_modules", // node_modules klasörü hariç
    "**/*.test.ts", // test dosyaları hariç
    "src/temp/*" // geçici dosyalar hariç
  ]
}
```

Bu yapılandırma özellikle:

- `node_modules` klasörünü derleme dışında tutmak
- Test dosyalarını üretim kodundan ayırmak
- Belirli dizinleri veya dosya türlerini hariç tutmak için kullanışlıdır

### OutDir: Çıktı Dizini Belirleme

Derlenmiş JavaScript dosyalarının kaydedileceği yeri `outDir` seçeneği ile belirleriz:

```json
{
  "compilerOptions": {
    "outDir": "./dist"
  }
}
```

Bu yapılandırma ile:

- TypeScript dosyaları `src` klasöründe kalır
- Derlenen JavaScript dosyaları `dist` klasörüne kaydedilir
- Proje yapısı daha düzenli hale gelir

Örnek proje yapısı:

```
proje/
├── src/
│   ├── index.ts
│   └── utils/
│       └── helpers.ts
├── dist/
│   ├── index.js
│   └── utils/
│       └── helpers.js
└── tsconfig.json
```

### Target: JavaScript Sürüm Hedefi

`target` seçeneği, TypeScript kodumuzun hangi JavaScript sürümüne derleneceğini belirler:

```json
{
  "compilerOptions": {
    "target": "es2020"
    // Diğer seçenekler: "es3", "es5", "es6", "es2016", "es2017", "esnext"
  }
}
```

Farklı hedeflerin etkisini gösteren bir örnek:

```typescript
// TypeScript kodu
class Hayvan {
  constructor(public isim: string) {}
}

// target: "es5" için çıktı
var Hayvan = /** @class */ (function () {
  function Hayvan(isim) {
    this.isim = isim;
  }
  return Hayvan;
})();

// target: "es2020" için çıktı
class Hayvan {
  constructor(isim) {
    this.isim = isim;
  }
}
```

## Strict Mode ve Null Kontrolleri

TypeScript'in tip güvenliği özelliklerini en üst düzeyde kullanmak için `strict` modunu etkinleştirebiliriz:

```json
{
  "compilerOptions": {
    "strict": true,
    "strictNullChecks": true
  }
}
```

Bu ayarların etkisi:

```typescript
// strict: false
let isim: string;
isim = null; // Sorun yok

// strict: true ve strictNullChecks: true
let isim: string;
isim = null; // Hata! Type 'null' is not assignable to type 'string'

// Doğru kullanım
let isim: string | null;
isim = null; // Şimdi çalışır
```

## JavaScript Dosyalarını Dahil Etme

Bazı projelerde JavaScript ve TypeScript dosyalarını birlikte kullanmamız gerekebilir. `allowJs` seçeneği bu durumu mümkün kılar:

```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true // JavaScript dosyalarında tip kontrolü yapar
  }
}
```

Bu özellik özellikle:

- Mevcut JavaScript projelerini TypeScript'e geçirirken
- Üçüncü parti JavaScript kütüphaneleriyle çalışırken
- Kademeli TypeScript adaptasyonu yaparken kullanışlıdır

Örnek kullanım:

```typescript
// utils.js (JavaScript dosyası)
export function topla(a, b) {
  return a + b;
}

// index.ts (TypeScript dosyası)
import { topla } from './utils.js';
const sonuc = topla(5, 3); // TypeScript tip kontrolü çalışır
```

## Best Practices

1. **Yapılandırmayı Projeye Göre Ayarlayın**

   ```json
   {
     "compilerOptions": {
       "target": "es2020", // Modern tarayıcılar için
       "module": "esnext", // Modern modül sistemi
       "strict": true, // Sıkı tip kontrolü
       "outDir": "./dist", // Çıktı dizini
       "rootDir": "./src" // Kaynak dizini
     }
   }
   ```

2. **Watch Mode'u Etkin Kullanın**

   ```bash
   # package.json
   {
     "scripts": {
       "dev": "tsc --watch",
       "build": "tsc"
     }
   }
   ```

3. **Proje Yapısını Düzenli Tutun**

   ```
   proje/
   ├── src/           # TypeScript kaynak dosyaları
   ├── dist/          # Derlenen JavaScript dosyaları
   ├── tests/         # Test dosyaları
   ├── tsconfig.json  # TypeScript yapılandırması
   └── package.json
   ```

4. **Uygun Include/Exclude Yapılandırması**
   ```json
   {
     "include": ["src/**/*"],
     "exclude": ["node_modules", "**/*.test.ts", "**/*.spec.ts"]
   }
   ```

## Sonuç

TypeScript derleyicisi ve yapılandırma seçenekleri, modern web geliştirme süreçlerinin önemli bir parçasıdır. Doğru yapılandırma ile:

- Daha güvenli kod yazabilir
- Geliştirme sürecini hızlandırabilir
- Proje yapısını daha iyi organize edebilir
- Takım çalışmasını kolaylaştırabilirsiniz

Bir sonraki yazımızda TypeScript'in daha ileri seviye özelliklerini inceleyeceğiz. Görüşmek üzere!
