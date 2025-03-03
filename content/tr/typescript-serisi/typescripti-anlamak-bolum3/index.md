---
title: "TypeScript'i Anlamak - Bölüm 3: Obje Tipleri"
summary: Bu makalede, TypeScript'teki obje tiplerini detaylı bir şekilde inceleyeceğiz. Obje tanımlamaları, tip takma adları ve iç içe objelerle çalışmayı öğreneceğiz.
date: 2025-02-09T01:56:00+03:00
cardimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum3/images/cover.svg'
featureimage: '/blog/tr/typescript-serisi/typescripti-anlamak-bolum3/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'javascript', 'object-types']
translationKey: "typescript-object-types"
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! TypeScript serimizin bu bölümünde, object types konusunu detaylı bir şekilde inceleyeceğiz. Özellikle fonksiyonlarda obje tiplerinin nasıl kullanıldığına ve type alias'ların neden önemli olduğuna odaklanacağız.

## Object Annotations ile Çalışmak

TypeScript'te fonksiyonlarda obje parametreleri tanımlarken iki farklı yaklaşım kullanabiliriz. İlk olarak, doğrudan fonksiyon parametresi içinde obje tipini tanımlayabiliriz:

```typescript
// Parametre içinde doğrudan obje tipi tanımlama
const printName = (name: { first: string; last: string }) => {
  return `Name: ${name.first} ${name.last}`;
};

// Kullanımı
printName({ first: 'Will', last: 'Ferrell' });
```

Bu yaklaşım basit objeler için kullanışlı olsa da, karmaşık obje yapılarında okunabilirliği azaltabilir ve kod tekrarına yol açabilir. Özellikle aynı obje tipini birden fazla yerde kullanacaksanız, bu yaklaşım yerine type alias kullanmanız önerilir.

### Süslü Parantezlerin Kullanımı

Fonksiyon parametrelerinde obje tiplerini tanımlarken süslü parantezlerin kullanımı bazen kafa karıştırıcı olabilir:

```typescript
// Kafa karıştırıcı olabilecek syntax
const printPerson = (person: { name: string; age: number }): { info: string } => {
  return { info: `${person.name} is ${person.age} years old` };
};

// Daha okunaklı versiyonu - Type Alias kullanarak
type Person = {
  name: string;
  age: number;
};

type PersonInfo = {
  info: string;
};

const printPerson2 = (person: Person): PersonInfo => {
  return { info: `${person.name} is ${person.age} years old` };
};
```

İkinci yaklaşım daha okunaklıdır çünkü:

1. Tip tanımlamaları fonksiyon tanımından ayrılmıştır
2. Tipler yeniden kullanılabilir
3. Kod daha düzenli ve bakımı daha kolaydır

## Type Alias Kullanımı

Type alias'lar, obje tiplerini ayrı olarak tanımlamamıza ve bu tipleri kodumuzda tekrar tekrar kullanmamıza olanak sağlar:

```typescript
// Type alias tanımlama
type Person = {
  name: string;
  age: number;
};

// Fonksiyonda kullanma
const sayHappyBirthday = (person: Person) => {
  return `Hey ${person.name}, congrats on turning ${person.age}!`;
};

// Değişkende kullanma
const jerry: Person = {
  name: 'Jerry',
  age: 42,
};

sayHappyBirthday(jerry);
```

Type alias kullanmanın avantajları:

1. Kod tekrarını önler
2. Tip tanımlarını merkezi bir yerde tutar
3. Değişiklikleri tek bir yerden yönetmeyi sağlar
4. Kodun okunabilirliğini artırır

## Nested Objects (İç İçe Objeler)

TypeScript'te iç içe obje yapıları tanımlamak oldukça yaygındır. İşte bir örnek:

```typescript
const describePerson = (person: {
  name: string;
  age: number;
  parentNames: {
    mom: string;
    dad: string;
  };
}) => {
  return `Person: ${name}, Age: ${age}, Parents: ${parentNames.mom}, ${parentNames.dad}`;
};

// Kullanımı
describePerson({
  name: 'Jimmy',
  age: 10,
  parentNames: {
    mom: 'Kim',
    dad: 'Steve',
  },
});
```

Bu yapıyı type alias kullanarak daha düzenli hale getirebiliriz:

```typescript
type ParentNames = {
  mom: string;
  dad: string;
};

type PersonWithParents = {
  name: string;
  age: number;
  parentNames: ParentNames;
};

const describePerson2 = (person: PersonWithParents) => {
  const { name, age, parentNames } = person;
  return `Person: ${name}, Age: ${age}, Parents: ${parentNames.mom}, ${parentNames.dad}`;
};
```

## Excess Properties (Fazla Özellikler)

TypeScript, bir obje tipinde tanımlanmamış özellikleri kullanmaya çalıştığınızda sizi uyarır:

```typescript
type BasicPerson = {
  name: string;
  age: number;
};

// Hata verecek
const person: BasicPerson = {
  name: 'John',
  age: 30,
  location: 'New York', // Fazla özellik hatası
};

// Doğru kullanım
const personData = {
  name: 'John',
  age: 30,
  location: 'New York',
};
const person2: BasicPerson = personData; // Bu çalışır
```

## Optional Properties

Bazen obje tipindeki bazı özelliklerin opsiyonel olmasını isteyebiliriz:

```typescript
type OptionalPerson = {
  name: string;
  age: number;
  phone?: string; // Opsiyonel özellik
  email?: string; // Opsiyonel özellik
};

// Her iki kullanım da geçerli
const person1: OptionalPerson = {
  name: 'Alice',
  age: 25,
};

const person2: OptionalPerson = {
  name: 'Bob',
  age: 30,
  phone: '555-0123',
  email: 'bob@email.com',
};
```

## Readonly Modifier

TypeScript'te `readonly` modifier, bir nesnenin özelliklerinin değiştirilmesini engellemek için kullanılır. Bu, veri bütünlüğünü korumak ve istenmeyen değişiklikleri önlemek için oldukça kullanışlıdır:

```typescript
type Person = {
  readonly name: string;
  readonly age: number;
};

const john: Person = {
  name: 'John',
  age: 30,
};

// Aşağıdaki satırlar derleme zamanında hata verecektir
// john.name = "Johnny";  // Hata: Cannot assign to 'name' because it is a read-only property
// john.age = 31;  // Hata: Cannot assign to 'age' because it is a read-only property
```

`readonly` modifier, nesnenin ilk oluşturulması sırasında değer atamasına izin verir, ancak daha sonra bu özelliklerin değiştirilmesini engeller.

### Readonly Array

Diziler için de `readonly` modifier kullanılabilir. Bu, dizinin içeriğinin değiştirilmesini engeller:

```typescript
const numbers: readonly number[] = [1, 2, 3, 4, 5];

// Aşağıdaki metodlar artık kullanılamaz
// numbers.push(6);  // Hata
// numbers.pop();    // Hata
// numbers[2] = 10;  // Hata
```

## Intersection Types

Intersection types, birden fazla tipi birleştirerek yeni bir tip oluşturmamıza olanak sağlar:

```typescript
type Employee = {
  employeeId: number;
  department: string;
};

type Person = {
  name: string;
  age: number;
};

// İki tipi birleştiren yeni bir tip
type EmployeePerson = Employee & Person;

const worker: EmployeePerson = {
  employeeId: 1234,
  department: 'Engineering',
  name: 'Alice',
  age: 30,
};
```

Intersection types, karmaşık nesne yapıları oluştururken oldukça kullanışlıdır. Birden fazla tipin özelliklerini tek bir tipte birleştirebilirsiniz.

### Intersection Type Örneği

Intersection type'ların nasıl kullanılabileceğini gösteren basit bir örneğe bakalım:

```typescript
// Adres bilgilerini içeren tip
type Address = {
  street: string;
  city: string;
  country: string;
};

// İletişim bilgilerini içeren tip
type Contact = {
  email: string;
  phone: string;
};

// İki tipi birleştirerek tam bir kullanıcı profili oluşturma
type UserProfile = Person & Address & Contact;

// Kullanım örneği:
const user: UserProfile = {
  name: 'Ahmet',
  age: 30,
  street: 'Atatürk Caddesi',
  city: 'İstanbul',
  country: 'Türkiye',
  email: 'ahmet@email.com',
  phone: '555-0123'
};

// Fonksiyonda kullanım örneği
function displayUserInfo(user: UserProfile) {
  console.log(`
    Kullanıcı: ${user.name}
    Yaş: ${user.age}
    Adres: ${user.street}, ${user.city}, ${user.country}
    İletişim: ${user.email}, ${user.phone}
  `);
}

displayUserInfo(user);
```

Bu örnek, intersection type'ların nasıl farklı özellikleri birleştirmek için kullanılabileceğini gösteriyor:

1. **Tip Kompozisyonu**: Farklı amaçlar için ayrı tipler tanımlıyoruz (`Person`, `Address`, `Contact`)
2. **Intersection Types**: Bu tipleri `&` operatörü ile birleştirerek daha kapsamlı bir tip oluşturuyoruz
3. **Modülerlik**: Her tip kendi sorumluluğuna sahip ve ayrı ayrı yönetilebilir
4. **Yeniden Kullanılabilirlik**: Bu tipleri başka yerlerde de kullanabiliriz

Bu desen özellikle şunları yapmak istediğinizde kullanışlıdır:
- Farklı veri gruplarını mantıksal olarak ayırmak
- Kodunuzu daha modüler hale getirmek
- Tip tanımlarını yeniden kullanmak
- Karmaşık veri yapılarını organize etmek

## Array Types

TypeScript'te dizi tipleri birkaç farklı şekilde tanımlanabilir:

```typescript
// Birinci yöntem: Köşeli parantez kullanarak
const numbers: number[] = [1, 2, 3, 4, 5];

// İkinci yöntem: Generic Array tipi kullanarak
const strings: Array<string> = ['hello', 'world'];

// Karışık tip dizisi
const mixed: (number | string)[] = [1, 'two', 3, 'four'];

// Tuple (Sabit uzunlukta, farklı tiplerde dizi)
const employee: [number, string] = [1, 'John Doe'];

// Readonly dizi
const readonlyNumbers: readonly number[] = [1, 2, 3];
```

### Dizi Metodları ve Tip Çıkarımı

TypeScript, dizi metodlarında akıllı tip çıkarımı yapar:

```typescript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((x) => x * 2); // doubled tipi number[]

const names = ['Alice', 'Bob', 'Charlie'];
const upperNames = names.map((name) => name.toUpperCase()); // upperNames tipi string[]
```

### Çok Boyutlu Diziler (Multi-Dimensional Arrays)

TypeScript'te çok boyutlu diziler, iç içe diziler veya matris benzeri veri yapıları oluşturmak için kullanılır:

```typescript
// 2 Boyutlu sayı dizisi
const matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9],
];

// 3 Boyutlu dizi örneği
const threeDimensionalArray: number[][][] = [
  [
    [1, 2],
    [3, 4],
  ],
  [
    [5, 6],
    [7, 8],
  ],
  [
    [9, 10],
    [11, 12],
  ],
];

// Karışık tipli 2 boyutlu dizi
const mixedMatrix: (number | string)[][] = [
  [1, 'two', 3],
  ['four', 5, 'six'],
];

// Çok boyutlu dizi üzerinde işlemler
const sumMatrix = (matrix: number[][]): number => {
  return matrix.flat().reduce((sum, num) => sum + num, 0);
};

console.log(sumMatrix(matrix)); // Tüm elemanların toplamını verir

// Dizi boyutunu kontrol etme
const printMatrixInfo = (matrix: number[][]) => {
  console.log(`Matris boyutu: ${matrix.length} x ${matrix[0].length}`);
};

printMatrixInfo(matrix); // "Matris boyutu: 3 x 3" çıktısı verir
```

Çok boyutlu diziler, özellikle görüntü işleme, oyun geliştirme, bilimsel hesaplamalar gibi alanlarda sıkça kullanılır. TypeScript, bu tür karmaşık dizi yapılarında güçlü tip kontrolü sağlar.

## Sonuç

TypeScript'in object types özellikleri, kodunuzun tip güvenliğini artırır ve daha net, hata ayıklaması kolay kod yazmanıza yardımcı olur. `readonly` modifier, intersection types, esnek dizi tipleri ve çok boyutlu diziler, TypeScript'in güçlü tip sisteminin önemli parçalarıdır.

Bir sonraki yazımızda görüşmek üzere!
