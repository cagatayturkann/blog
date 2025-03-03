---
title: "TypeScript'i Anlamak - Bölüm 7: Generics"
summary: Bu makalede, TypeScript'in en güçlü özelliklerinden biri olan generic yapıları inceleyeceğiz. Tip güvenliğini korurken yeniden kullanılabilir kod yazmanın yollarını öğreneceğiz.
date: 2025-02-12T19:30:00+03:00
cardimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum7/images/cover.svg'
featureimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum7/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'generics', 'type-safety']
translationKey: 'typescript-generics'
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! TypeScript serimizin bu bölümünde, kodunuzu daha esnek ve yeniden kullanılabilir hale getiren generic yapıları inceleyeceğiz. Generics, tip güvenliğinden ödün vermeden farklı veri tipleriyle çalışabilen fonksiyonlar ve sınıflar yazmamızı sağlar.

## Generic Yapılar Nedir?

Generics, bir fonksiyon veya sınıfın farklı tiplerle çalışabilmesini sağlayan bir özelliktir. Burada önemli nokta, tip güvenliğini korurken bu esnekliği sağlayabilmesidir. Basit bir örnekle başlayalım:

```typescript
// Generic olmayan yaklaşım - Her tip için ayrı fonksiyon
function getFirstNumber(arr: number[]): number {
  return arr[0];
}

function getFirstString(arr: string[]): string {
  return arr[0];
}

// Generic yaklaşım - Tek fonksiyon, tüm tipler
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

// Kullanım
const ilkSayi = getFirst<number>([1, 2, 3]); // number tipinde
const ilkMetin = getFirst<string>(['a', 'b', 'c']); // string tipinde
```

Bu örnekte `<T>` bir tip parametresidir ve fonksiyonun hangi tiplerle çalışacağını belirtir. TypeScript çoğu durumda tip çıkarımı yapabildiği için tip parametresini açıkça belirtmek zorunda değiliz:

```typescript
const ilkSayi = getFirst([1, 2, 3]); // number tipini otomatik çıkarır
const ilkMetin = getFirst(['a', 'b', 'c']); // string tipini otomatik çıkarır
```

## Built-in Generic Tipler

TypeScript'te sık kullanılan bazı yerleşik generic tipler vardır:

### Array<T>

```typescript
let sayilar: Array<number> = [1, 2, 3]; // number[]
let metinler: Array<string> = ['a', 'b']; // string[]
```

### Promise<T>

```typescript
async function veriGetir(): Promise<User> {
  const response = await fetch('/api/user');
  return response.json();
}
```

### Record<K,V>

```typescript
type KullaniciRolleri = Record<string, string[]>;

const roller: KullaniciRolleri = {
  admin: ['okuma', 'yazma', 'silme'],
  editor: ['okuma', 'yazma'],
  user: ['okuma'],
};
```

## Generic Fonksiyonlar Yazmak

Generic fonksiyonlar yazarken dikkat etmemiz gereken bazı noktalar var:

```typescript
// Basit generic fonksiyon
function reverse<T>(items: T[]): T[] {
  return items.reverse();
}

// Generic fonksiyon - Ok fonksiyonu syntax
const filter = <T>(arr: T[], fn: (item: T) => boolean): T[] => {
  return arr.filter(fn);
};

// Kullanım örnekleri
const sayilar = reverse<number>([1, 2, 3]);
const metinler = reverse(['a', 'b', 'c']); // Tip çıkarımı

const filtrelenmisArray = filter([1, 2, 3, 4], (n) => n % 2 === 0);
```

### Çoklu Tip Parametreleri

Bazen birden fazla tip parametresine ihtiyaç duyabiliriz:

```typescript
function cift<T, U>(x: T, y: U): [T, U] {
  return [x, y];
}

const sonuc = cift<string, number>('merhaba', 42);
const otomatik = cift('merhaba', 42); // Tip çıkarımı çalışır
```

## Tip Kısıtlamaları (Constraints)

Generic tiplerin hangi özelliklere sahip olması gerektiğini belirtmek için tip kısıtlamaları kullanabiliriz:

```typescript
interface Uzunluk {
  length: number;
}

function uzunlukGoster<T extends Uzunluk>(arg: T): number {
  return arg.length;
}

// Çalışır - string'in length özelliği var
uzunlukGoster('Merhaba');

// Çalışır - array'in length özelliği var
uzunlukGoster([1, 2, 3]);

// Hata - number'ın length özelliği yok
// uzunlukGoster(123);
```

## Generic Sınıflar

Generic yapıları sınıflarda da kullanabiliriz:

```typescript
class DataContainer<T> {
  private data: T[];

  constructor(initialData: T[]) {
    this.data = initialData;
  }

  add(item: T): void {
    this.data.push(item);
  }

  get(index: number): T {
    return this.data[index];
  }

  getAll(): T[] {
    return this.data;
  }
}

// Kullanım
const sayiContainer = new DataContainer<number>([1, 2, 3]);
sayiContainer.add(4);
console.log(sayiContainer.getAll()); // [1, 2, 3, 4]

const metinContainer = new DataContainer<string>(['a', 'b', 'c']);
metinContainer.add('d');
console.log(metinContainer.get(0)); // 'a'
```

## Varsayılan Tip Parametreleri

Generic tiplere varsayılan değerler atayabiliriz:

```typescript
class Queue<T = number> {
  private data: T[] = [];

  push(item: T) {
    this.data.push(item);
  }

  pop(): T | undefined {
    return this.data.shift();
  }
}

// number tipini varsayılan olarak kullanır
const sayiKuyrugu = new Queue();

// string tipi için özelleştirilmiş
const metinKuyrugu = new Queue<string>();
```

## Generic Interface'ler

Interface'lerde de generic yapıları kullanabiliriz:

```typescript
interface APIResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface User {
  id: number;
  name: string;
}

// Kullanım
function fetchUser(): Promise<APIResponse<User>> {
  return fetch('/api/user').then((res) => res.json());
}

// async/await ile kullanım
async function getUser(): Promise<APIResponse<User>> {
  const response = await fetch('/api/user');
  return response.json();
}
```

## Generic Type Inference (Tip Çıkarımı)

TypeScript'in güçlü tip çıkarım sistemi sayesinde çoğu durumda generic tipleri açıkça belirtmemize gerek kalmaz:

```typescript
// Tip parametrelerini açıkça belirtme
const x = getFirst<number>([1, 2, 3]);

// Tip çıkarımına izin verme
const y = getFirst([1, 2, 3]); // number tipini otomatik çıkarır

// Generic sınıf için tip çıkarımı
const container = new DataContainer(['a', 'b', 'c']); // string[] çıkarımı
```

## Best Practices

1. **Generic İsimlendirme Kuralları**

```typescript
// Yaygın kullanılan generic tip isimleri:
// T: Type (Genel tip parametresi)
// K: Key (Anahtar tipi)
// V: Value (Değer tipi)
// E: Element (Eleman tipi)
// P: Properties (Özellik tipi)
```

2. **Tip Kısıtlamalarını Doğru Kullanma**

```typescript
// İyi kullanım
interface HasId {
  id: number;
}

function getById<T extends HasId>(items: T[], id: number): T | undefined {
  return items.find((item) => item.id === id);
}
```

3. **Generic Constraint vs Union Types**

```typescript
// Generic constraint kullanımı
function process<T extends string | number>(value: T): T {
  return value;
}

// Union type kullanımı
function process2(value: string | number): string | number {
  return value;
}

// Generic constraint daha güvenli ve tip bilgisini korur
```

4. **Generic'leri Basit Tutun**

```typescript
// Karmaşık
function processData<T, U, V, W>(
  data: T[],
  transformer: (item: T) => U,
  validator: (transformed: U) => V,
  formatter: (validated: V) => W
): W[] {
  // ...
}

// Daha iyi - Ara tipler için ayrı interface'ler kullan
interface DataProcessor<T, R> {
  transform(item: T): R;
}

function processData<T, R>(data: T[], processor: DataProcessor<T, R>): R[] {
  return data.map((item) => processor.transform(item));
}
```

## Generic'lerle Arrow Functions

TypeScript'te generic'leri arrow function'larla kullanırken dikkat etmemiz gereken bazı syntax özellikleri vardır:

```typescript
// Standart generic arrow function
const identity = (arg: T): T => arg;

// React ile kullanırken TSX çakışmasını önlemek için
const identity = (arg: T): T => arg;

// Çoklu tip parametreli arrow function
const pair = (first: T, second: U): [T, U] => [first, second];
```

## Generic'lerle Asenkron İşlemler

Modern web uygulamalarında asenkron işlemler çok yaygın. Generic'ler bu konuda da yardımcımız olabilir:

```typescript
// Generic async fonksiyon
async function fetchData(url: string): Promise {
  const response = await fetch(url);
  return response.json();
}

// Kullanım
interface User {
  id: number;
  name: string;
  email: string;
}

// Tip güvenli API çağrısı
const user = await fetchData('/api/user/1');
console.log(user.name); // Tip güvenli erişim

// Generic error handling
interface APIError {
  code: number;
  message: string;
}

async function fetchWithError(): Promise {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw (await response.json()) as APIError;
    }
    return response.json();
  } catch (error) {
    throw error as APIError;
  }
}
```

## Generics ile Utility Types

TypeScript'in yerleşik utility type'ları da generic yapıları kullanır. İşte sık kullanılan örnekler:

```typescript
// Partial - Tüm özellikleri opsiyonel yapar
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial;
// { id?: number; name?: string; email?: string; }

// Pick - Belirli özellikleri seçer
type UserBasicInfo = Pick;
// { name: string; email: string; }

// Omit - Belirli özellikleri çıkarır
type UserWithoutId = Omit;
// { name: string; email: string; }

// Record - Key-value yapısı oluşturur
type UserRoles = Record;
// { [key: string]: string[] }
```

## Generic Tip Çıkarımında İleri Konular

TypeScript'in tip çıkarım sistemi oldukça gelişmiştir. İşte bazı ileri seviye örnekler:

```typescript
// Return type çıkarımı
function createPair(first: T) {
  return {
    first,
    second: first,
  };
}
// TypeScript otomatik olarak { first: T, second: T } tipini çıkarır

// Generic constraints ile tip çıkarımı
interface HasLength {
  length: number;
}

function longest(a: T, b: T): T {
  return a.length >= b.length ? a : b;
}

// TypeScript string[] ve string için ayrı çıkarımlar yapar
const longerArray = longest([1, 2], [1, 2, 3]); // type: number[]
const longerString = longest('123', '12345'); // type: string
```

## Generic Type Alias vs Interface

Generic yapıları type alias ve interface ile kullanırken bazı farklılıklar vardır:

```typescript
// Generic type alias
type Container = {
  value: T;
  tag: string;
};

// Generic interface
interface Box {
  value: T;
  tag: string;
}

// İkisi de benzer şekilde kullanılır
const numberContainer: Container = { value: 42, tag: 'sayı' };
const stringBox: Box = { value: 'test', tag: 'metin' };

// Interface'ler extends edilebilir
interface LabeledBox extends Box {
  label: string;
}

// Type alias'lar intersection type ile genişletilebilir
type LabeledContainer = Container & {
  label: string;
};
```

## Önemli Noktalar ve İpuçları

1. **Generic Constraint'leri İyi Düşünün**

   - Çok geniş constraint'ler tip güvenliğini azaltır
   - Çok dar constraint'ler yeniden kullanılabilirliği azaltır

2. **Tip İnference'a Güvenin**

   - TypeScript çoğu durumda doğru tipi çıkarabilir
   - Generic tipleri gereksiz yere açıkça belirtmeyin

3. **Okunabilirliği Koruyun**
   - Generic tip isimleri anlamlı olsun
   - Çok fazla tip parametresinden kaçının
   - Karmaşık generic yapıları daha küçük parçalara bölün

## Sonuç

Generic'ler, TypeScript'in en güçlü özelliklerinden biridir. Doğru kullanıldığında:

- Kod tekrarını azaltır
- Tip güvenliğini artırır
- Yeniden kullanılabilir ve esnek kod yazmanızı sağlar
- Daha iyi IDE desteği sunar

Özellikle büyük projelerde ve kütüphane geliştirirken generic'lerin gücünden maksimum fayda sağlayabilirsiniz. Generic'leri anlamak ve etkili kullanmak, TypeScript ile geliştirme yaparken vazgeçilmez bir beceridir.

Bir sonraki yazımızda TypeScript'in diğer ileri seviye özelliklerini inceleyeceğiz. Görüşmek üzere!
