---
title: "TypeScript'i Anlamak - Bölüm 6: Sınıflar"
summary: Bu makalede, TypeScript'in sınıf yapısını detaylı bir şekilde inceleyeceğiz. Sınıf tanımlamaları, erişim belirleyicileri, getter/setter metodları ve soyut sınıflar gibi konuları ele alacağız.
date: 2025-02-12T18:15:00+03:00
cardimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum6/images/cover.svg'
featureimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum6/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'classes', 'oop']
translationKey: "typescript-classes"
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! TypeScript serimizin bu bölümünde, nesne yönelimli programlamanın temel yapı taşlarından biri olan sınıfları inceleyeceğiz. TypeScript'in sınıf yapısının JavaScript'e göre avantajlarını, tip güvenliğinin nasıl sağlandığını ve kodunuzu daha güvenli hale getirmek için kullanabileceğiniz özellikleri öğreneceğiz.

## TypeScript'te Sınıf Kavramı

TypeScript sınıfları, JavaScript sınıflarının tüm özelliklerini desteklerken, ek olarak tip güvenliği ve nesne yönelimli programlama için güçlü araçlar sunar. Basit bir örnekle başlayalım:

```typescript
// JavaScript'te bir sınıf
class JsKullanici {
  constructor(isim) {
    this.isim = isim;
  }
}

// TypeScript'te aynı sınıf
class TsKullanici {
  isim: string;  // Tip tanımı
  
  constructor(isim: string) {  // Parametre tipi
    this.isim = isim;
  }
}
```

TypeScript versiyonunda:
- Sınıf özelliklerinin tipleri açıkça belirtilir
- Constructor parametreleri tip güvenliğine sahiptir
- IDE'ler daha iyi kod tamamlama desteği sağlar

## Temel Sınıf Yapısı

Bir TypeScript sınıfı oluştururken kullanabileceğimiz temel yapılar şunlardır:

```typescript
class Kullanici {
  // Sınıf özellikleri (fields)
  id: number;
  isim: string;
  private email: string;
  readonly kayitTarihi: Date;

  // Constructor metodu
  constructor(id: number, isim: string, email: string) {
    this.id = id;
    this.isim = isim;
    this.email = email;
    this.kayitTarihi = new Date();
  }

  // Sınıf metodu
  bilgileriGoster(): string {
    return `${this.isim} (ID: ${this.id})`;
  }
}

// Sınıfı kullanma
const kullanici = new Kullanici(1, "Ahmet Yılmaz", "ahmet@ornek.com");
console.log(kullanici.bilgileriGoster());  // "Ahmet Yılmaz (ID: 1)"
```

Bu örnekte:
1. Sınıf özellikleri için tip tanımlamaları yaptık
2. Constructor'da parametre tiplerini belirttik
3. readonly ile değişmez bir özellik tanımladık
4. private ile erişimi kısıtladık
5. Metod dönüş tipini belirttik

## Class Fields ve Erişim Belirleyiciler

TypeScript'te üç tür erişim belirleyici vardır: public, private ve protected. Bunların kullanımını ve JavaScript ile farklarını inceleyelim:

### Public Erişim Belirleyici

Varsayılan erişim düzeyidir ve herhangi bir belirteç kullanmaya gerek yoktur:

```typescript
class Araba {
  marka: string;      // public varsayılan olarak
  public model: string; // açıkça public olarak belirtilmiş

  constructor(marka: string, model: string) {
    this.marka = marka;
    this.model = model;
  }
}

const araba = new Araba("Toyota", "Corolla");
console.log(araba.marka);   // Erişilebilir
console.log(araba.model);   // Erişilebilir
```

### Private Erişim Belirleyici

Private özellikler sadece sınıf içinden erişilebilir. TypeScript'te iki farklı yöntemle private özellik tanımlayabiliriz:

```typescript
class Hesap {
  private _bakiye: number;       // TypeScript private
  #harcamalar: number[];        // JavaScript private field (#)

  constructor(baslangicBakiye: number) {
    this._bakiye = baslangicBakiye;
    this.#harcamalar = [];
  }

  paraYatir(miktar: number): void {
    this._bakiye += miktar;
  }

  harcamaEkle(miktar: number): void {
    this.#harcamalar.push(miktar);
    this._bakiye -= miktar;
  }
}

const hesap = new Hesap(1000);
// hesap._bakiye;     // Hata: Private özelliğe erişilemez
// hesap.#harcamalar; // Hata: Private field'a erişilemez
```

JavaScript ve TypeScript'in private uygulamaları arasındaki farklar:
- TypeScript'in private belirteci derleme zamanında çalışır
- JavaScript'in # private fields özelliği çalışma zamanında çalışır
- # kullanımı gerçek bir erişim kısıtlaması sağlar
- private keyword'ü sadece TypeScript tarafında koruma sağlar

### Protected Erişim Belirleyici

Protected özellikler, sınıfın kendisi ve alt sınıfları tarafından erişilebilir:

```typescript
class Hayvan {
  protected tur: string;

  constructor(tur: string) {
    this.tur = tur;
  }
}

class Kedi extends Hayvan {
  miyavla(): string {
    return `Ben bir ${this.tur}, miyav!`; // tur özelliğine erişebilir
  }
}

const kedi = new Kedi("kedi");
// kedi.tur; // Hata: Protected özelliğe dışarıdan erişilemez
```

## Readonly ve Parameter Properties

### Readonly Özellikleri

readonly belirteci, bir özelliğin sadece ilk atama sırasında değer alabileceğini ve sonrasında değiştirilemeyeceğini belirtir:

```typescript
class Dokuman {
  readonly id: string;
  readonly olusturmaTarihi: Date;
  baslik: string;

  constructor(id: string, baslik: string) {
    this.id = id;  // OK
    this.olusturmaTarihi = new Date();  // OK
    this.baslik = baslik;
  }

  idDegistir(yeniId: string) {
    this.id = yeniId;  // Hata: readonly özellik değiştirilemez
  }
}
```

### Parameter Properties

TypeScript'in sunduğu özel bir kısayol olan parameter properties, constructor parametrelerini otomatik olarak sınıf özelliklerine dönüştürür:

```typescript
// Uzun yol
class Urun {
  readonly id: string;
  private _fiyat: number;
  public stok: number;

  constructor(id: string, fiyat: number, stok: number) {
    this.id = id;
    this._fiyat = fiyat;
    this.stok = stok;
  }
}

// Parameter properties kullanarak
class UrunKisa {
  constructor(
    readonly id: string,
    private _fiyat: number,
    public stok: number
  ) {}
}
```

Bu özellik sayesinde:
- Daha az kod yazarsınız
- Özellik tanımları ve atamaları otomatik yapılır
- Kod daha okunabilir hale gelir

## Getter ve Setter Metodları

TypeScript'te get ve set anahtar kelimeleriyle özel erişim metodları tanımlayabilirsiniz:

```typescript
class Urun {
  private _fiyat: number;

  constructor(fiyat: number) {
    this._fiyat = fiyat;
  }

  // Getter metodu
  get fiyat(): number {
    return this._fiyat;
  }

  // Setter metodu
  set fiyat(yeniFiyat: number) {
    if (yeniFiyat < 0) {
      throw new Error("Fiyat negatif olamaz!");
    }
    this._fiyat = yeniFiyat;
  }
}

const urun = new Urun(100);
console.log(urun.fiyat);    // Getter çağrılır: 100
urun.fiyat = 150;          // Setter çağrılır
// urun.fiyat = -50;       // Hata fırlatır
```

Getter ve setter metodları:
- Özelliğe erişimi kontrol etmenizi sağlar
- Değer doğrulama mantığı ekleyebilirsiniz
- Özellik değişimlerini izleyebilirsiniz
- Hesaplanmış değerler döndürebilirsiniz

## Sınıflar ve Interface'ler

TypeScript'te sınıflar bir veya daha fazla interface'i implement edebilir:

```typescript
interface CanliVarlik {
  isim: string;
  yasiyorMu: boolean;
  hareket(): void;
}

interface YiyecekTuketici {
  beslen(yiyecek: string): void;
}

class Insan implements CanliVarlik, YiyecekTuketici {
  constructor(public isim: string) {
    this.yasiyorMu = true;
  }

  yasiyorMu: boolean;

  hareket(): void {
    console.log("İki ayak üzerinde yürüyor");
  }

  beslen(yiyecek: string): void {
    console.log(`${yiyecek} yiyor`);
  }
}
```

Bu yapı:
- Tip güvenliği sağlar
- Kodun bakımını kolaylaştırır
- Interface'lerin tüm gereksinimlerinin karşılanmasını garanti eder

## Soyut (Abstract) Sınıflar

Soyut sınıflar, doğrudan örneklenemeyen ve alt sınıflar için şablon görevi gören sınıflardır:

```typescript
abstract class Sekil {
  abstract alanHesapla(): number;
  abstract cevreyiHesapla(): number;

  bilgileriGoster(): string {
    return `Alan: ${this.alanHesapla()}, Çevre: ${this.cevreyiHesapla()}`;
  }
}

class Dikdortgen extends Sekil {
  constructor(private genislik: number, private yukseklik: number) {
    super();
  }

  alanHesapla(): number {
    return this.genislik * this.yukseklik;
  }

  cevreyiHesapla(): number {
    return 2 * (this.genislik + this.yukseklik);
  }
}

// const sekil = new Sekil(); // Hata: Soyut sınıf örneklenemez
const dikdortgen = new Dikdortgen(5, 3);
console.log(dikdortgen.bilgileriGoster()); // "Alan: 15, Çevre: 16"
```

Soyut sınıfların avantajları:
- Ortak davranışları tek bir yerde tanımlayabilirsiniz
- Alt sınıfları belirli metodları uygulamaya zorlayabilirsiniz
- Kodun yeniden kullanılabilirliğini artırır

Soyut sınıfların önemli özellikleri:

1. Hem abstract hem concrete (normal) metodlar içerebilirler
2. Generic tiplerle kullanılabilirler
3. Protected metodlarla alt sınıflara yardımcı fonksiyonlar sağlayabilirler
4. Ortak davranışları tek bir yerde toplayarak kod tekrarını önlerler

Soyut sınıflar vs Interface'ler:

- Abstract sınıflar implementasyon içerebilirken interface'ler sadece yapıyı tanımlar
- Bir sınıf birden fazla interface implement edebilir ama sadece bir abstract sınıfı extend edebilir
- Abstract sınıflar constructor içerebilir, interface'ler içeremez
- Abstract sınıflar erişim belirleyicileri kullanabilir (private, protected, public)

## Best Practices

1. **Private Özellikleri Doğru Seçin**
   ```typescript
   class KullaniciServisi {
     // TypeScript private: Sadece derleme zamanı kontrolü
     private _apiUrl: string;
     
     // JavaScript private field: Gerçek erişim kısıtlaması
     #apiKey: string;
   }
   ```

2. **Parameter Properties'i Etkili Kullanın**
   ```typescript
   // Kısa ve net kod için parameter properties
   class Konfigurasyon {
     constructor(
       private readonly apiUrl: string,
       private readonly timeout: number,
       public readonly versiyonNo: string
     ) {}
   }
   ```

3. **Interface'leri Etkin Kullanın**
   ```typescript
   interface VeriDeposu {
     kaydet(veri: any): Promise<void>;
     getir(id: string): Promise<any>;
   }

   class PostgreSQLDepo implements VeriDeposu {
     // Interface'in gerektirdiği metodları uygula
   }

   class MongoDBDepo implements VeriDeposu {
     // Aynı interface, farklı implementasyon
   }
   ```

4. **Erişim Belirleyicilerini Bilinçli Seçin**
   ```typescript
   class BankaHesabi {
     private _bakiye: number;           // Dışarıdan erişim yok
     protected _hesapNo: string;        // Alt sınıflar erişebilir
     public readonly hesapTuru: string; // Herkes okuyabilir ama değiştiremez
   }
   ```

## Sonuç

TypeScript sınıfları, JavaScript sınıflarının sunduğu tüm özellikleri tip güvenliği ile birleştirerek güçlü bir nesne yönelimli programlama deneyimi sunar. Erişim belirleyicileri, readonly özellikleri, getter/setter metodları ve soyut sınıflar gibi özellikler, kodunuzu daha güvenli ve bakımı kolay hale getirir.
Sınıfları kullanırken:
- Erişim belirleyicilerini doğru seçerek kod güvenliğini artırabilir
- Parameter properties ile daha az kod yazabilir
- Interface'ler ile tip güvenliğini sağlayabilir
- Soyut sınıflar ile kod tekrarını azaltabilirsiniz

Bir sonraki yazımızda TypeScript'in daha ileri seviye özelliklerini inceleyeceğiz. Görüşmek üzere!