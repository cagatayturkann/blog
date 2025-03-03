---
title: "TypeScript'i Anlamak - Bölüm 4: Interfaces"
summary: Bu yazıda, TypeScript'in önemli özelliklerinden biri olan interface'leri detaylı bir şekilde inceleyeceğiz. Type Aliases ile farklarını anlayacak ve pratik kullanım senaryolarını öğreneceğiz.
date: 2025-02-12T16:51:00+03:00
cardimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum4/images/cover.svg'
featureimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum4/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'interfaces', 'type-system']
translationKey: "typescript-interfaces"
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! TypeScript serimizin bu bölümünde, tip sisteminin en güçlü özelliklerinden biri olan interface'leri detaylı bir şekilde inceleyeceğiz. Interface'lerin ne olduğunu, neden kullanmamız gerektiğini ve gerçek dünya uygulamalarında nasıl fayda sağladığını öğreneceğiz.

## Interface Nedir ve Neden Kullanmalıyız?

Interface'ler, TypeScript'te objelerin yapısını tanımlamamızı sağlayan bir özelliktir. Bilale anlatır gibi söylersek:

"Düşün ki bir inşaat planı çiziyorsun. Bu planda binanın kaç katı olacağı, odaların boyutları, pencerelerin yerleri gibi detaylar var. Interface de aynı bunun gibi - bir objenin 'planını' çiziyor. Hangi özellikleri olacak, bu özellikler hangi tipte olacak, hepsini önceden belirliyoruz."

İşte basit bir örnek:

```typescript
// Basit bir interface tanımı
interface Kullanici {
  // Zorunlu özellikler
  id: number; // Kullanıcının benzersiz numarası
  ad: string; // Kullanıcının adı
  email: string; // E-posta adresi

  // Opsiyonel özellikler (? işareti ile belirtilir)
  telefon?: string; // Telefon numarası (opsiyonel)
  yas?: number; // Yaş bilgisi (opsiyonel)
}

// Interface'i kullanma
const yeniKullanici: Kullanici = {
  id: 1,
  ad: 'Ahmet Yılmaz',
  email: 'ahmet@ornek.com',
  // telefon ve yas opsiyonel olduğu için yazmak zorunda değiliz
};

// TypeScript burada bizi korur
const hataliKullanici: Kullanici = {
  id: '1', // Hata! string bir değer number tipine atanamaz
  ad: 'Mehmet',
  email: true, // Hata! boolean bir değer string tipine atanamaz
};
```

### Interface Kullanmanın Avantajları

1. **Kod Güvenliği:**

```typescript
// Interface ile tanımlanmış fonksiyon
function kullaniciGuncelle(id: number, kullanici: Kullanici) {
  // TypeScript sayesinde kullanici objesinin yapısından eminiz
  console.log(`${kullanici.ad} güncelleniyor...`);
  // ... güncelleme işlemleri
}

// Bu çalışır
kullaniciGuncelle(1, {
  id: 1,
  ad: 'Ahmet',
  email: 'ahmet@ornek.com',
});

// Bu hata verir - eksik ve yanlış özellikler
kullaniciGuncelle(1, {
  id: 1,
  isim: 'Ahmet', // Hata! 'isim' yerine 'ad' olmalı
});
```

2. **Kod Tamamlama:**
   Interface'ler IDE'nizin kod tamamlama özelliğini güçlendirir. Bir objenin özelliklerine erişirken, IDE size mevcut tüm özellikleri gösterir.

3. **Dokümantasyon:**
   Interface'ler aynı zamanda bir dokümantasyon görevi görür. Kodunuzu okuyan diğer geliştiriciler, bir objenin yapısını hızlıca anlayabilir.

## Interface vs Type: Farkları Anlamak

Interface ve Type arasındaki farkları gerçek örneklerle inceleyelim:

### 1. Declaration Merging (Bildirimleri Birleştirme)

```typescript
// Interface'de bildirim birleştirme mümkün
interface Araba {
  marka: string;
}

interface Araba {
  // Aynı isimle yeni özellikler ekleyebiliriz
  model: string;
}

const tesla: Araba = {
  marka: 'Tesla', // Her iki interface'den gelen
  model: 'Model 3', // özellikler gerekli
};

// Type'da bu mümkün değil
type Bisiklet = {
  marka: string;
};

// Hata! 'Bisiklet' identifier'ı zaten tanımlı
type Bisiklet = {
  model: string;
};
```

Bu özellik özellikle kütüphane geliştirirken çok faydalıdır. Kullanıcılar mevcut interface'lere yeni özellikler ekleyebilir.

### 2. Extends ve Implements

Interface'ler, nesne yönelimli programlamada çok kullanışlıdır:

```typescript
// Temel interface
interface Hayvan {
  isim: string;
  tur: string;
  sesCikar(): void;
}

// Interface'den türetme
interface Kedi extends Hayvan {
  patiliMi: boolean;
  tirmanabilir: boolean;
}

// Class'larda kullanım
class TekirkKedisi implements Kedi {
  // Tüm özellikleri ve metodları implemente etmeliyiz
  isim: string;
  tur: string = 'Kedi';
  patiliMi: boolean = true;
  tirmanabilir: boolean = true;

  constructor(isim: string) {
    this.isim = isim;
  }

  sesCikar() {
    console.log('Miyav!');
  }
}
```

## Interface Metodları ve Özellikler

Interface'ler sadece veri yapısını değil, nesnelerin davranışlarını da tanımlayabilir. Gerçek bir örnek üzerinden ilerleyelim:

```typescript
// E-ticaret sisteminde ürün yönetimi için interface
interface UrunYonetimi {
  // Temel CRUD operasyonları
  urunEkle(urun: Urun): Promise<boolean>;
  urunGuncelle(id: string, urun: Urun): Promise<boolean>;
  urunSil(id: string): Promise<boolean>;
  urunGetir(id: string): Promise<Urun | null>;

  // Stok yönetimi metodları
  stokGuncelle(urunId: string, miktar: number): Promise<void>;
  stokKontrol(urunId: string): Promise<number>;

  // İstatistik metodları
  satisRaporuOlustur(baslangic: Date, bitis: Date): Promise<SatisRaporu>;
}

// Bu interface'i kullanan bir class
class PostgreSQLUrunYonetimi implements UrunYonetimi {
  constructor(private db: Database) {}

  async urunEkle(urun: Urun): Promise<boolean> {
    try {
      await this.db.query('INSERT INTO urunler (id, ad, fiyat, stok) VALUES ($1, $2, $3, $4)', [
        urun.id,
        urun.ad,
        urun.fiyat,
        urun.stok,
      ]);
      return true;
    } catch (error) {
      console.error('Ürün eklenirken hata:', error);
      return false;
    }
  }

  // Diğer metodların implementasyonu...
}
```

### Readonly ve Optional Özellikler

Interface'lerde bazı özellikleri salt okunur (readonly) veya opsiyonel yapabiliriz. İşte gerçek bir senaryo:

```typescript
// Kullanıcı profili için interface
interface KullaniciProfili {
  // Salt okunur özellikler (değiştirilemez)
  readonly id: string; // Kullanıcı ID'si asla değişmez
  readonly kayitTarihi: Date; // Kayıt tarihi değiştirilemez

  // Zorunlu özellikler
  email: string; // E-posta adresi
  ad: string; // Kullanıcı adı

  // Opsiyonel özellikler
  telefon?: string; // Telefon numarası
  adres?: {
    // Adres bilgileri
    sehir: string;
    ilce: string;
    postaKodu?: string;
  };
  profilResmi?: string; // Profil resmi URL'i

  // Sosyal medya bilgileri (hepsi opsiyonel)
  sosyalMedya?: {
    twitter?: string;
    linkedin?: string;
    github?: string;
  };
}

// Kullanım örneği
const yeniProfil: KullaniciProfili = {
  id: 'usr_123', // readonly olduğu için sadece ilk atamada değer verebiliriz
  kayitTarihi: new Date(), // readonly olduğu için sadece ilk atamada değer verebiliriz
  email: 'ali@ornek.com',
  ad: 'Ali Yılmaz',
  // Opsiyonel alanları eklemek zorunda değiliz
};

// HATALI KULLANIM - readonly özellikleri değiştiremeyiz
yeniProfil.id = 'usr_456'; // Hata! readonly özellik değiştirilemez
yeniProfil.kayitTarihi = new Date(); // Hata! readonly özellik değiştirilemez

// Opsiyonel özellikleri sonradan ekleyebiliriz
yeniProfil.telefon = '555-0123';
yeniProfil.adres = {
  sehir: 'İstanbul',
  ilce: 'Kadıköy',
};
```

## Generic Interface'ler

Generic'ler, interface'leri daha esnek ve yeniden kullanılabilir hale getirir. Örneğin, bir API yanıt yapısı için generic interface:

```typescript
// Generic API yanıt interface'i
interface APIResponse<T> {
  success: boolean; // İşlem başarılı mı?
  data: T; // Generic veri tipi
  timestamp: number; // İşlem zamanı
  statusCode: number; // HTTP durum kodu
  message?: string; // Opsiyonel mesaj
  errors?: string[]; // Varsa hata mesajları
}

// Farklı veri tipleri için kullanım
interface Kullanici {
  id: number;
  ad: string;
  email: string;
}

interface Urun {
  id: number;
  ad: string;
  fiyat: number;
}

// Kullanıcı listesi için API yanıtı
const kullanicilarYanit: APIResponse<Kullanici[]> = {
  success: true,
  data: [
    { id: 1, ad: 'Ahmet', email: 'ahmet@ornek.com' },
    { id: 2, ad: 'Mehmet', email: 'mehmet@ornek.com' },
  ],
  timestamp: Date.now(),
  statusCode: 200,
};

// Tek bir ürün için API yanıtı
const urunYanit: APIResponse<Urun> = {
  success: false,
  data: { id: 0, ad: '', fiyat: 0 }, // Boş ürün
  timestamp: Date.now(),
  statusCode: 404,
  message: 'Ürün bulunamadı',
  errors: ["Belirtilen ID'ye sahip ürün mevcut değil"],
};
```

## Interface Inheritance (Kalıtım)

Interface'ler birbirinden türeyebilir. Bu özellik, kod tekrarını önler ve modüler bir yapı oluşturmamızı sağlar:

```typescript
// Temel varlık özellikleri
interface Varlik {
  id: string; // Benzersiz tanımlayıcı
  olusturmaTarihi: Date; // Oluşturulma tarihi
  guncellemeTarihi: Date; // Son güncelleme tarihi
  silindi: boolean; // Silinme durumu
}

// Temel kişi bilgileri
interface Kisi extends Varlik {
  ad: string;
  soyad: string;
  email: string;
  telefon?: string;
}

// Öğrenci bilgileri
interface Ogrenci extends Kisi {
  ogrenciNo: string;
  bolum: string;
  sinif: number;
  dersler: string[];
  notOrtalamasi?: number;
}

// Öğretmen bilgileri
interface Ogretmen extends Kisi {
  sicilNo: string;
  brans: string;
  verdigiDersler: string[];
  maas: number;
}

// Kullanım örneği
const yeniOgrenci: Ogrenci = {
  // Varlik'ten gelen özellikler
  id: 'ogr_123',
  olusturmaTarihi: new Date(),
  guncellemeTarihi: new Date(),
  silindi: false,

  // Kisi'den gelen özellikler
  ad: 'Ali',
  soyad: 'Yılmaz',
  email: 'ali@okul.edu.tr',

  // Ogrenci'ye özel özellikler
  ogrenciNo: '2024001',
  bolum: 'Bilgisayar Mühendisliği',
  sinif: 2,
  dersler: ['Algoritma', 'Veri Yapıları', 'TypeScript 101'],
};
```

## Interface'lerin Yaygın Kullanım Alanları

Interface'ler yazılım geliştirmenin birçok alanında karşımıza çıkar. İşte en yaygın kullanım alanları:

### 1. API İletişimi

API'lerle çalışırken, gelen ve giden veri yapılarını tanımlamak için interface'leri kullanırız. Örneğin:

```typescript
// API'den gelecek kullanıcı verisinin yapısı
interface KullaniciYaniti {
  id: number;
  ad: string;
  email: string;
  rol: 'admin' | 'kullanici';
  sonGiris?: Date;
}

// API'ye gönderilecek giriş bilgileri
interface GirisBilgileri {
  email: string;
  sifre: string;
  hatirla?: boolean;
}
```

### 2. Veritabanı İşlemleri

Veritabanı işlemlerinde, tablo yapılarını ve sorgu sonuçlarını modellemek için interface'leri kullanırız:

```typescript
interface Urun {
  id: number; // Ürün benzersiz numarası
  ad: string; // Ürün adı
  fiyat: number; // Ürün fiyatı
  stok: number; // Stok miktarı
  kategori: string; // Ürün kategorisi
}
```

### 3. Form Yönetimi

Form verilerinin yapısını ve doğrulama kurallarını tanımlarken interface'ler işimizi kolaylaştırır:

```typescript
interface KayitFormu {
  email: string; // Kullanıcı e-postası
  sifre: string; // Şifre
  sifreTekrar: string; // Şifre tekrarı
  kullaniciAdi: string; // Kullanıcı adı
}
```

### 4. Yapılandırma Yönetimi

Uygulama ayarlarını ve yapılandırmalarını tanımlarken interface'leri kullanırız:

```typescript
interface UygulamaAyarlari {
  apiUrl: string; // API sunucu adresi
  maxDeneme: number; // Maximum deneme sayısı
  timeout: number; // Zaman aşımı süresi
  debug: boolean; // Debug modu açık/kapalı
}
```

### 5. Veri Modelleri

Uygulamamızdaki temel veri yapılarını tanımlarken interface'lerden faydalanırız:

```typescript
interface Siparis {
  id: string; // Sipariş numarası
  musteriId: string; // Müşteri numarası
  urunler: string[]; // Siparişteki ürünler
  toplamTutar: number; // Toplam tutar
  durum: 'beklemede' | 'onaylandi' | 'iptal'; // Sipariş durumu
}
```

Bu kullanım alanları, interface'lerin tip güvenliği ve kod organizasyonu açısından ne kadar önemli olduğunu gösterir. Her bir alanda interface'ler, kodumuzun daha güvenli ve bakımı kolay olmasını sağlar.

## Best Practices ve İpuçları

TypeScript interface'lerini kullanırken dikkat etmemiz gereken bazı önemli noktalar var. Bu pratikleri uygulayarak daha okunabilir ve bakımı kolay kod yazabiliriz.

### 1. İsimlendirme Kuralları

Interface isimleri, ne iş yaptıklarını açıkça belirtmeli ve bazı standartlara uymalı:

```typescript
// ✅ İyi İsimlendirme Örnekleri
interface KullaniciServisi {
  kullaniciGetir(id: string): Promise<Kullanici>;
}

interface UrunDeposu {
  stokGuncelle(urunId: string, miktar: number): void;
}

// ❌ Kaçınılması Gereken İsimlendirmeler
interface IKullanici {
  // 'I' öneki kullanmayın
  // ...
}

interface veriYoneticisi {
  // PascalCase kullanın
  // ...
}

interface DATA_SERVICE {
  // BÜYÜK HARF kullanmayın
  // ...
}
```

### 2. Tek Sorumluluk Prensibi

Her interface tek bir işe odaklanmalı ve o işi iyi yapmalı:

```typescript
// ✅ İyi Tasarlanmış Interface'ler
interface KimlikBilgileri {
  id: string;
  tcKimlikNo: string;
  pasaportNo?: string;
}

interface IletisimBilgileri {
  email: string;
  telefon?: string;
  adres?: {
    il: string;
    ilce: string;
  };
}

// İhtiyaç halinde birleştirin
interface Kullanici extends KimlikBilgileri, IletisimBilgileri {
  ad: string;
  soyad: string;
}

// ❌ Kaçınılması Gereken Durum: Her şeyi tek interface'de toplamak
interface KocamanInterface {
  // Kimlik bilgileri
  id: string;
  tcKimlikNo: string;

  // İletişim bilgileri
  email: string;
  telefon: string;

  // Adres bilgileri
  il: string;
  ilce: string;

  // Kullanıcı bilgileri
  ad: string;
  soyad: string;

  // Diğer bilgiler...
  // ... ve daha birçok özellik
}
```

### 3. Açıklayıcı Yorumlar Ekleme

Interface'lerin nasıl kullanılacağını açıkça belirten yorumlar ekleyin:

```typescript
/**
 * Ödeme işlemi sırasında kullanılacak kart bilgileri.
 * Bu interface sadece ödeme işlemi sırasında kullanılmalı ve
 * hassas bilgiler işlem sonrası bellekten temizlenmelidir.
 */
interface OdemeBilgileri {
  /** Ödenecek tutar (TL cinsinden, kuruş hassasiyetinde) */
  tutar: number;

  /** 16 haneli kart numarası */
  kartNo: string;

  /** AA/YY formatında son kullanma tarihi (örnek: 12/25) */
  sonKullanmaTarihi: string;

  /** Kartın arkasındaki 3 haneli güvenlik kodu */
  guvenlikKodu: string;
}
```

### 4. Opsiyonel Özellikleri Doğru Kullanma

Opsiyonel özellikleri kullanırken dikkatli olun ve belgelendirin:

```typescript
interface KullaniciProfili {
  // Zorunlu alanlar
  id: string;
  ad: string;
  email: string;

  // Opsiyonel alanlar - neden opsiyonel olduklarını açıklayın
  /** Kullanıcı telefon vermek istemeyebilir */
  telefon?: string;

  /** Profil fotoğrafı yüklenmemişse varsayılan kullanılır */
  profilFotoUrl?: string;

  /** Kullanıcı henüz konum izni vermemiş olabilir */
  konum?: {
    lat: number;
    lng: number;
  };
}
```

## Sonuç

Interface'ler, TypeScript'in en güçlü özelliklerinden biridir ve doğru kullanıldığında:

- Kodunuzu daha okunabilir hale getirir
- Tip güvenliği sağlar
- Bakımı kolaylaştırır
- Takım çalışmasını iyileştirir
- Dokümantasyon görevi görür

Bu yazıda gördüğümüz örnekler ve best practice'ler, günlük TypeScript geliştirmelerinizde size yol gösterecektir. Bir sonraki yazıda görüşeceğiz.

Sorularınız varsa yorum bırakabilirsiniz. İyi kodlamalar! 🚀
