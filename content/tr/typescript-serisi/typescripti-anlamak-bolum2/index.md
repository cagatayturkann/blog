---
title: "TypeScript'i Anlamak - Bölüm 2: Fonksiyonlar"
summary: Bu makalede, TypeScript fonksiyonlarını detaylı bir şekilde inceleyeceğiz. Parametre tipleri, dönüş tipleri ve TypeScript'i benzersiz ve güçlü kılan özel fonksiyon tiplerini ele alacağız.
date: 2025-02-08T23:21:00+03:00
cardimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum2/images/cover.svg'
featureimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum2/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'javascript', 'fonksiyonlar']
translationKey: "understanding-typescript-part2"
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! Önceki yazımızda TypeScript'teki temel tipleri incelemiştik. Bugün, fonksiyonları ve TypeScript'in onları nasıl daha güçlü ve güvenli hale getirdiğini öğreneceğiz. Basit örneklerle başlayıp, adım adım daha ileri konulara geçeceğiz.

## Fonksiyon Parametre Tipleri

TypeScript'te fonksiyon parametrelerinin tipini belirleyebiliriz. Bu özellik, yanlış tip bir değer gönderildiğinde daha kod çalıştırılmadan hata almamızı sağlar. Basit bir örnekle başlayalım:

```typescript
// Tipli parametrelerle fonksiyon oluşturma
const ogrenciyiCesaretle = (isim: string) => {
  return `Hey, ${isim}, harika gidiyorsun!`;
};

// Bu çalışır
ogrenciyiCesaretle('sen'); // Çıktı: "Hey, sen, harika gidiyorsun!"

// Bu TypeScript hatası verir
ogrenciyiCesaretle(85); // Hata: 'number' tipi 'string' tipine atanamaz
```

Her parametreden sonra yazdığımız tip açıklaması (örneğin `: string`), TypeScript'e bu fonksiyonun hangi tipte değerler bekleyeceğini söyler. Bu sayede hataları daha kod yazarken yakalayabiliriz.

## Çoklu Parametreler

Fonksiyonlar birden fazla parametre alabilir ve her parametrenin kendi tipini belirleyebiliriz:

```typescript
function kullaniciOlustur(isim: string, yas: number, aktifMi: boolean) {
  return {
    isim,
    yas,
    aktifMi,
  };
}

// Doğru kullanım
kullaniciOlustur('Ahmet', 25, true);

// TypeScript bu hataları yakalar
kullaniciOlustur('Ahmet', '25', true); // Hata: yaş number olmalı
kullaniciOlustur('Ahmet'); // Hata: eksik parametreler
kullaniciOlustur('Ahmet', 25, 'evet'); // Hata: aktifMi boolean olmalı
```

Bu örnekte `kullaniciOlustur` fonksiyonu üç farklı tipte parametre alıyor. TypeScript, bu parametrelerin doğru tipte ve eksiksiz gönderildiğinden emin olmamızı sağlıyor.

## Fonksiyon Dönüş Tipleri

TypeScript ile bir fonksiyonun hangi tipte değer döndüreceğini de belirtebiliriz. TypeScript çoğu zaman dönüş tipini otomatik olarak anlayabilse de (buna type inference denir), açıkça belirtmek kodunuzu daha okunabilir ve bakımı kolay hale getirir:

```typescript
const sayilariTopla = (x: number, y: number): number => {
  return x + y;
};

sayilariTopla(5, 5); // Dönüş: 10

const metinBirlestir = (a: string, b: string): string => {
  return a + ' ' + b;
};

metinBirlestir('Merhaba', 'Dünya'); // Dönüş: "Merhaba Dünya"
```

Bu örneklerde parametre listesinden sonra gelen `: number` ve `: string` ifadeleri, fonksiyonların hangi tipte değer döndüreceğini belirtir. Bu sayede:

1. Fonksiyonun ne döndüreceği konusunda dokümentasyon sağlamış oluruz
2. Yanlış tipte bir değer döndürmeye çalıştığımızda hemen hata alırız
3. Fonksiyonu kullanan diğer geliştiriciler, ne bekleyeceklerini önceden bilirler

## void Dönüş Tipi

Bazen fonksiyonlarımız herhangi bir değer döndürmez, sadece bir işlem yaparlar. TypeScript'te bunu belirtmek için `void` tipini kullanırız:

```typescript
const kullaniciyiUyar = (mesaj: string): void => {
  alert(mesaj);
  // Hiçbir şey döndürmüyoruz
};

const logla = (data: any): void => {
  console.log(data);
  // return yazmadık bile
};
```

`void` dönüş tipi, fonksiyonun bir değer döndürmeyeceğini söyler. Bu özellikle önemlidir çünkü:

1. Kodunuzu okuyan diğer geliştiriciler fonksiyondan bir değer beklememeleri gerektiğini bilirler
2. Yanlışlıkla bir değer döndürmeye çalışırsanız TypeScript hata verir
3. API'lerinizi daha net ve anlaşılır hale getirir

## Opsiyonel Parametreler ve Varsayılan Değerler

TypeScript'te parametreleri iki şekilde esnek hale getirebiliriz: opsiyonel parametreler ve varsayılan değerler.

### Opsiyonel Parametreler

Bir parametrenin sonuna `?` işareti koyarak o parametrenin opsiyonel olduğunu belirtiriz:

```typescript
function kisiSelam(isim: string, unvan?: string) {
  if (unvan) {
    return `Merhaba ${unvan} ${isim}`;
  }
  return `Merhaba ${isim}`;
}

kisiSelam('Ahmet'); // Çıktı: "Merhaba Ahmet"
kisiSelam('Ahmet', 'Dr.'); // Çıktı: "Merhaba Dr. Ahmet"
```

### Varsayılan Değerler

Parametrelere varsayılan değerler atayarak, değer gönderilmediğinde ne kullanılacağını belirleyebiliriz:

```typescript
function kahveSiparis(kahveTuru: string = 'Americano', boyut: string = 'orta', sut: boolean = false) {
  let siparis = `${boyut} boy ${kahveTuru}`;
  if (sut) siparis += ' sütlü';
  return siparis;
}

kahveSiparis(); // "orta boy Americano"
kahveSiparis('Latte'); // "orta boy Latte"
kahveSiparis('Espresso', 'küçük'); // "küçük boy Espresso"
kahveSiparis('Mocha', 'büyük', true); // "büyük boy Mocha sütlü"
```

Opsiyonel parametreler ve varsayılan değerler arasındaki farklar:

1. **Opsiyonel Parametreler (`?`)**

   - Parametre gönderilmezse `undefined` olur
   - Fonksiyon içinde kontrol etmeniz gerekir
   - Daha esnek ama daha fazla kontrol gerektirir

2. **Varsayılan Değerler (`= değer`)**
   - Parametre gönderilmezse belirlediğiniz değer kullanılır
   - Ekstra kontrol gerektirmez
   - Daha az esnek ama kullanımı daha kolay

### Önemli Nokta: Parametre Sırası

Hem opsiyonel hem zorunlu parametreler kullanırken, zorunlu parametreler her zaman önce gelmelidir:

```typescript
// DOĞRU
function dogru(zorunlu: string, opsiyonel?: string) {}

// YANLIŞ - TypeScript hata verir
function yanlis(opsiyonel?: string, zorunlu: string) {} // Hata!
```

Bu kural varsayılan değerler için de geçerlidir:

```typescript
// DOĞRU
function dogru2(zorunlu: string, varsayilan: string = 'default') {}

// YANLIŞ
function yanlis2(varsayilan: string = 'default', zorunlu: string) {} // Hata!
```

Bu kurala uymak, kodunuzun daha mantıklı ve anlaşılır olmasını sağlar. Ayrıca JavaScript'in fonksiyon çağrılarını nasıl işlediğiyle de uyumludur.

## Anonim Fonksiyonlar ve Tip Çıkarımı

TypeScript'in en güçlü özelliklerinden biri, tipleri otomatik olarak çıkarabilme yeteneğidir. Bu özellikle anonim fonksiyonlarda çok kullanışlıdır:

```typescript
const sayilar = [1, 2, 3, 4, 5];

// TypeScript otomatik olarak 'sayi' parametresinin number olduğunu anlar
sayilar.forEach((sayi) => {
  console.log(sayi.toFixed(2)); // TypeScript bunu güvenli buluyor
});

// Dizi metotlarında da tip çıkarımı çalışır
const kareler = sayilar.map((sayi) => sayi * sayi);
// kareler dizisinin tipi number[] olarak çıkarılır
```

Tip çıkarımı kod yazarken işimizi kolaylaştırır, ancak her zaman tip belirtmemek kodunuzu daha az okunabilir hale getirebilir. Bu yüzden dengeyi iyi kurmak önemlidir.

## never Tipi

`never` tipi, TypeScript'te özel bir tiptir ve asla gerçekleşmeyecek durumları temsil eder. İki ana kullanım senaryosu vardır:

1. **Sonsuz Döngüler:** Asla tamamlanmayan fonksiyonlar

```typescript
function sonsuzdDongu(): never {
  while (true) {
    console.log('Bu fonksiyon asla bitmeyecek!');
  }
}

function sonsuzOzyineleme(): never {
  return sonsuzOzyineleme();
}
```

2. **Her Zaman Hata Fırlatan Fonksiyonlar:**

```typescript
function hataFirlat(mesaj: string): never {
  throw new Error(mesaj);
}

function hataKontrol(deger: string | number) {
  if (typeof deger === 'string') {
    console.log('Bu bir string:', deger);
  } else if (typeof deger === 'number') {
    console.log('Bu bir number:', deger);
  } else {
    // Bu noktada deger tipi 'never' olur
    // Çünkü buraya ulaşmak imkansızdır
    hataFirlat('Bu asla olmamalı!');
  }
}
```

`never` ile `void` arasındaki farkı anlamak önemlidir:

- `void`: Fonksiyon bir değer döndürmez
- `never`: Fonksiyon asla tamamlanmaz veya her zaman bir hata fırlatır

## Fonksiyon Aşırı Yüklemesi (Function Overloads)

TypeScript'te bir fonksiyonu farklı parametre tipleriyle kullanmak istediğimizde fonksiyon aşırı yüklemesini kullanabiliriz:

```typescript
// Aşırı yükleme imzaları
function birlestir(a: string, b: string): string;
function birlestir(a: number, b: number): number;

// Gerçek implementasyon
function birlestir(a: string | number, b: string | number): string | number {
  if (typeof a === 'string' && typeof b === 'string') {
    return a.concat(b);
  }
  if (typeof a === 'number' && typeof b === 'number') {
    return a + b;
  }
  throw new Error('Parametreler aynı tipte olmalıdır!');
}

console.log(birlestir('Merhaba, ', 'Dünya')); // "Merhaba, Dünya"
console.log(birlestir(5, 10)); // 15
// birlestir("5", 10);  // Hata! Bu kombinasyon tanımlı değil
```

Fonksiyon aşırı yüklemesi sayesinde:

1. Aynı fonksiyonu farklı parametre tipleriyle kullanabiliriz
2. Her kombinasyon için doğru dönüş tipini belirleyebiliriz
3. TypeScript hangi kombinasyonların geçerli olduğunu kontrol eder

## En İyi Uygulamalar

TypeScript fonksiyonları yazarken izlemeniz gereken bazı önemli pratikler vardır. İşte bu pratikler ve nedenleri:

1. **Her Zaman Parametre Tiplerini Belirtin**

   ```typescript
   // KÖTÜ
   function kotu(isim) {
     return `Merhaba ${isim}`;
   }

   // İYİ
   function iyi(isim: string): string {
     return `Merhaba ${isim}`;
   }
   ```

   Tip belirtmek kodunuzu daha okunabilir yapar ve hataları önler.

2. **Dönüş Tiplerini Düşünün**

   ```typescript
   // Otomatik çıkarım bazen yeterlidir
   const topla = (a: number, b: number) => a + b;

   // Ama karmaşık fonksiyonlarda dönüş tipini belirtmek daha iyidir
   function veriIsle(data: any[]): ProcessedData {
     // Karmaşık işlemler...
     return islenmisSonuc;
   }
   ```

3. **Opsiyonel Parametreleri Sona Koyun**

   ```typescript
   // KÖTÜ
   function kotu(opsiyonel?: string, zorunlu: string) {}

   // İYİ
   function iyi(zorunlu: string, opsiyonel?: string) {}
   ```

4. **any Tipinden Kaçının**

   ```typescript
   // KÖTÜ
   function herhangiVeri(data: any) {
     return data.birsey(); // Tehlikeli!
   }

   // İYİ
   function tipliVeri<T>(data: T) {
     // Tip güvenli işlemler
   }
   ```

5. **Fonksiyon Dokümantasyonu Yazın**
   ```typescript
   /**
    * Verilen sayıları toplar ve sonucu döndürür
    * @param sayilar - Toplanacak sayı dizisi
    * @returns Toplam değer
    */
   function topla(...sayilar: number[]): number {
     return sayilar.reduce((a, b) => a + b, 0);
   }
   ```

## Hızlı Başvuru

İşte TypeScript'te en sık kullanılan fonksiyon yapılarının hızlı bir özeti:

### 1. Temel Fonksiyon Tanımlamaları

```typescript
// Normal fonksiyon
function selamla(isim: string): string {
  return `Merhaba ${isim}`;
}

// Ok fonksiyonu
const selamla2 = (isim: string): string => `Merhaba ${isim}`;
```

### 2. Parametre Çeşitleri

```typescript
// Opsiyonel parametre
function log(mesaj: string, seviye?: string) {}

// Varsayılan değerli parametre
function baglan(url: string = 'localhost') {}

// Rest parametresi
function toplam(...sayilar: number[]): number {
  return sayilar.reduce((a, b) => a + b, 0);
}
```

### 3. Dönüş Tipleri

```typescript
// Değer döndüren
function topla(a: number, b: number): number {
  return a + b;
}

// Void dönüş
function logla(mesaj: string): void {
  console.log(mesaj);
}

// Never dönüş
function hataFirlat(mesaj: string): never {
  throw new Error(mesaj);
}
```

### 4. Fonksiyon Tipleri

```typescript
// Fonksiyon tipi tanımlama
type MatematikIslemi = (a: number, b: number) => number;

// Fonksiyon tipi kullanma
const toplama: MatematikIslemi = (a, b) => a + b;
const cikarma: MatematikIslemi = (a, b) => a - b;
```

## Sonuç

TypeScript'in fonksiyonlar için sunduğu tip sistemi, JavaScript geliştirmede çok değerli bir araçtır. Tip güvenliği sayesinde:

- Hataları erken yakalarsınız
- Kodunuz daha okunabilir olur
- IDE desteği daha iyi çalışır
- Refactoring yapmak kolaylaşır

Bu özelliklerin hepsi bir araya geldiğinde, daha güvenilir ve bakımı kolay uygulamalar geliştirmenize yardımcı olur.
