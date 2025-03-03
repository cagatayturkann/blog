---
title: "TypeScript'i Anlamak - Bölüm 1: Temeller"
summary: TypeScript, JavaScript'e statik tip desteği ekleyen güçlü bir üst kümedir. Bu makale serisinde, TypeScript'i temellerden ileri seviyeye kadar keşfedeceğiz ve temel tipler ve tip çıkarımı ile başlayacağız.
date: 2025-02-08T23:19:00+03:00
cardimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum1/images/cover.svg'
featureimage: 'tr/blog/typescript-serisi/typescripti-anlamak-bolum1/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'javascript']
translationKey: "understanding-typescript-part1"
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! Yakın zamanda TypeScript'i derinlemesine öğrenmeye başlayan bir geliştirici olarak, bu öğrenme yolculuğumu sizlerle paylaşmak istiyorum. Bu seride, TypeScript'i en temelinden başlayarak inceleyeceğiz, neden var olduğunu ve JavaScript geliştirme deneyimimizi nasıl daha iyi hale getirebileceğini anlayacağız.

## TypeScript Nedir?

TypeScript, en basit tanımıyla tip desteği eklenmiş JavaScript'tir. Ancak bu tanım, TypeScript'in tüm özelliklerini karşılamıyor. Microsoft tarafından geliştirilen TypeScript, JavaScript'e statik tip desteği ekleyen bir üst kümedir (superset). Bu, tüm geçerli JavaScript kodunun aynı zamanda geçerli TypeScript kodu olduğu, ancak TypeScript'in daha sağlam ve sürdürülebilir kod yazmamıza yardımcı olan ek özellikler sunduğu anlamına gelir.

TypeScript'in JavaScript'ten nasıl farklılaştığına dair basit bir örnek:

```typescript
// JavaScript
let greeting = "Hello";
greeting = 42; // JavaScript'te çalışır, ancak sorunlara yol açabilir

// TypeScript
let greeting: string = "Hello";
greeting = 42; // Hata: 'number' tipi 'string' tipine atanamaz
```

## Neden Tipler?

TypeScript'in tip sistemi birkaç önemli avantaj sağlar:

1. **Hataları bulmamıza yardımcı olur:** Tip sistemi, kodumuz çalışmadan önce hataları yakalayabilir
2. **Kodumuzu yazarken analiz eder:** Editörümüzde gerçek zamanlı geri bildirim sağlar
3. **Sadece geliştirme aşamasında vardır:** Tipler, kod JavaScript'e derlendiğinde kaldırılır

Bu özellikler, TypeScript'i özellikle büyük kod tabanları ve takım çalışmaları için değerli kılar.

## TypeScript'te Temel Tipler

Şimdi TypeScript'teki temel tipleri inceleyelim:

### 1. String (Metin)

String'ler TypeScript'te metin değerlerini temsil eder. İşte nasıl kullanıldıklarına dair bir örnek:

```typescript
// String değişkeni tanımlama
let myString: string = "Merhaba!!!";

// Farklı bir tipe ATANMAZ
myString = 100; // Hata

// Aynı tipte bir değere ATANABİLİR
myString = "Yeni metin!!!"; // Çalışır
```

Bu örnekte, `myString`'i bir string olarak tanımladıktan sonra, TypeScript sadece string değerler atayabilmemizi sağlar.

### 2. Number (Sayı)

TypeScript, diğer dillere göre sayı yönetimini basitleştirir:

```typescript
// Sayı değişkeni tanımlama
let myNumber: number = 42;

// Farklı bir tipe ATANMAZ
myNumber = "Ben bir string'im!"; // Hata

// Aynı tipte bir değere ATANABİLİR
myNumber = 60; // Çalışır
```

Bazı programlama dillerinin aksine (float, int vb. gibi birden çok sayı tipi olan), TypeScript (JavaScript gibi) tüm sayısal değerler için sadece `number` tipini kullanır.

### 3. Boolean (Mantıksal)

Boolean değerler doğru/yanlış durumlarını temsil eder:

```typescript
// Boolean değişkeni tanımlama
const myBoolean: boolean = true;

// Farklı bir tipe ATANMAZ
myBoolean = 87; // Hata

// Aynı tipte bir değere ATANABİLİR
myBoolean = false; // Çalışır
```

## Tip Çıkarımı (Type Inference)

TypeScript'in en güçlü özelliklerinden biri, tipleri otomatik olarak çıkarabilme yeteneğidir. Bu, her zaman tipleri açıkça belirtmemiz gerekmediği anlamına gelir:

```typescript
// Bir değişkeni tip belirtmeden değer ile tanımlama
let x = 27;

x = 'Yirmi yedi'; // Hata - 'string' tipi 'number' tipine atanamaz
```

Bu örnekte TypeScript, `x` değişkeninin tipini ilk değerine bakarak otomatik olarak `number` olarak belirler. Bu özellik, tip güvenliğini korurken daha az kod yazmamızı sağlar.

## 'any' Tipi

Bazen katı tip kontrolünden daha fazla esnekliğe ihtiyaç duyarız. İşte bu noktada `any` tipi devreye girer:

```typescript
// 'any' tipi ile değişken tanımlama
const myComplicatedData: any = "Karmaşık olacağım!";

// Herhangi bir tipe ATANABİLİR - tip kontrolleri kapalı!
myComplicatedData = 87;        // Çalışır
myComplicatedData = 'abc...';  // Çalışır
myComplicatedData = true;      // Çalışır
```

### 'any' Ne Zaman Kullanılmalı?

Her ne kadar `any` kullanımından genellikle kaçınmamız gerekse de, meşru kullanım durumları vardır. İşte gerçek bir senaryo:

```typescript
// Harici API verisiyle çalışırken
function handleAPIResponse(response: any) {
    // API yanıtının tam yapısını bilmeyebiliriz
    // özellikle üçüncü taraf API'lerle çalışırken
    console.log(response.data);        // Çalışır
    console.log(response.status);      // Çalışır
    console.log(response.someField);   // Çalışır
}

// Eski JavaScript koduyla çalışırken
declare const oldJavaScriptLibrary: any;
// Kütüphaneyi TypeScript hataları olmadan kullanabiliriz
oldJavaScriptLibrary.someOldMethod();
```

`any` tipinin gerekli olabileceği yaygın senaryolar:
1. Yanıt yapısının bilinmediği veya dinamik olduğu harici API'lerle entegrasyon sırasında
2. JavaScript'ten TypeScript'e geçiş sürecinde (geçici kullanım)
3. TypeScript tip tanımlamaları olmayan üçüncü taraf kütüphanelerle çalışırken
4. Tipin tahmin edilemeyeceği gerçekten dinamik içeriklerle uğraşırken

Ancak unutmayın ki `any` kullanmak, TypeScript'in tip kontrolü avantajlarını ortadan kaldırır. Son çare olarak kullanılmalı ve mümkün olduğunda uygun tipler tanımlanmalıdır.

## Pratik Örnek

Öğrendiklerimizi birleştiren pratik bir örneğe bakalım:

```typescript
// Farklı tiplerle değişkenler oluşturma
let kullaniciAdi: string = "Ahmet Yılmaz";
let yas: number = 30;
let girisYapildi: boolean = true;

// Tip çıkarımı kullanma
let sonGirisTarihi = new Date();  // TypeScript Date tipini çıkarır
let girisAdedi = 5;              // TypeScript number tipini çıkarır

// Bu değişkenlerle çalışma
function kullaniciBilgileriniGoster() {
    console.log(`Kullanıcı: ${kullaniciAdi}`);
    console.log(`Yaş: ${yas}`);
    console.log(`Giriş Yapıldı: ${girisYapildi}`);
    console.log(`Son Giriş: ${sonGirisTarihi}`);
    console.log(`Giriş Sayısı: ${girisAdedi}`);
}

// TypeScript bu hataları yakalar:
kullaniciAdi = 123;          // Hata: 'number' tipi 'string' tipine atanamaz
yas = "otuz";               // Hata: 'string' tipi 'number' tipine atanamaz
girisYapildi = "evet";      // Hata: 'string' tipi 'boolean' tipine atanamaz
```

Bu örnek, TypeScript'in gerçek bir uygulama senaryosunda tip güvenliğini nasıl sağladığını ve yaygın tip hatalarını daha oluşmadan nasıl engellediğini gösteriyor.

## Best Practices (En İyi Uygulamalar)

1. **Mümkün Olduğunda Tip Çıkarımını Kullanın**
   - TypeScript tipi doğru çıkarabiliyorsa, tip açıklamaları eklemeyin
   - Bu, kodunuzu daha temiz ve bakımı daha kolay hale getirir

2. **'any' Kullanımından Kaçının**
   - `any` kullanmak, TypeScript'in tüm avantajlarını ortadan kaldırır
   - Sadece gerçekten gerektiğinde kullanın

3. **Gerektiğinde Açık Olun**
   - TypeScript'in çıkarımı yeterli olmadığında tip açıklamaları ekleyin
   - Bu, kod okunabilirliğini artırır ve hataları yakalamaya yardımcı olur

## Sonuç

Bu TypeScript'e giriş, tipler ve tip çıkarımının temellerini kapsıyor. TypeScript, JavaScript'e güçlü bir tip sistemi ekleyerek daha güvenilir kod yazmamıza yardımcı olur. Başlangıçta ekstra iş gibi görünse de, projeleriniz büyüdükçe ve karmaşıklaştıkça faydaları net bir şekilde ortaya çıkar.

Serinin 2. bölümünde daha ileri TypeScript konularını inceleyeceğiz. Takipte kalın!