---
title: "TypeScript'i Anlamak - Bölüm 8: Type Narrowing"
summary: Bu makalede, TypeScript'te type narrowing (tip daraltma) tekniklerini inceleyeceğiz. typeof, instanceof, type predicates gibi tip kontrol yöntemlerini ve discriminated unions yapısını ele alacağız.
date: 2025-02-21T10:30:00+03:00
cardimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum7/images/cover.svg'
featureimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum7/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'type-narrowing', 'type-guards']
translationKey: 'typescript-type-narrowing'
authors:
  - Çağatay: /images/author.jpeg
---

Merhaba! TypeScript serimizin bu bölümünde, tip sisteminin en güçlü özelliklerinden biri olan type narrowing (tip daraltma) konusunu inceleyeceğiz. Type narrowing, bir değişkenin tipini daha spesifik bir tipe daraltmamızı sağlayan tekniklerin genel adıdır. Bu teknikler sayesinde, union type'lar ve karmaşık tip yapıları ile daha güvenli bir şekilde çalışabiliriz.

## Type Narrowing Nedir?

Type narrowing, TypeScript'in bir değişkenin tipini bağlam içinde daha spesifik bir tipe daraltma yeteneğidir. Bu özellik, özellikle union type'lar ile çalışırken çok kullanışlıdır. Örneğin, bir değişken string veya number olabilir, ancak belirli bir kod bloğunda bu değişkenin kesinlikle string olduğunu biliyorsak, TypeScript bu bilgiyi kullanarak tip güvenliğini artırır.

## typeof Type Guards

typeof operatörü, TypeScript'te en temel type narrowing yöntemlerinden biridir:

```typescript
const isTeenager = (age: number | string) => {
  if (typeof age === 'string') {
    // Bu blokta age kesinlikle string
    return age.charAt(0) === '1';
  } else {
    // Bu blokta age kesinlikle number
    return age > 12 && age < 20;
  }
};

isTeenager('20'); // false
isTeenager(13); // true
```

typeof type guard kullanmanın avantajları:

- Tip güvenliği sağlar
- IDE desteği ve kod tamamlama özelliklerini iyileştirir
- Runtime hataları önler
- Kodun okunabilirliğini artırır

## Truthiness Type Guards

JavaScript'in truthiness özelliğini kullanarak da type narrowing yapabiliriz:

```typescript
const printLetters = (word: string | null) => {
  if (!word) {
    console.log('No word was provided.');
    return;
  }

  // Bu noktada word kesinlikle string
  word.split('').forEach((letter) => console.log(letter));
};

printLetters('Hello'); // H, e, l, l, o
printLetters(null); // No word was provided.
```

Truthiness kontrolü şu değerleri false olarak değerlendirir:

- false
- 0
- ""
- null
- undefined
- NaN

## Equality Type Narrowing

Eşitlik karşılaştırmaları da TypeScript'te type narrowing için kullanılır:

```typescript
const someFunc = (x: string | boolean, y: string | number) => {
  if (x === y) {
    // Bu blokta x ve y kesinlikle string
    console.log(x.toUpperCase());
    console.log(y.toLowerCase());
  } else {
    // x: string | boolean
    // y: string | number
    console.log(x);
    console.log(y);
  }
};
```

## in Operator Type Guards

JavaScript'in in operatörü, bir özelliğin bir objede var olup olmadığını kontrol eder. TypeScript bu kontrolü type narrowing için kullanır:

```typescript
type Cat = { meow: () => void };
type Dog = { bark: () => void };

const talk = (creature: Cat | Dog) => {
  if ('meow' in creature) {
    // Bu blokta creature kesinlikle Cat
    creature.meow();
  } else {
    // Bu blokta creature kesinlikle Dog
    creature.bark();
  }
};

const kitty: Cat = { meow: () => console.log('MEOWWW') };
talk(kitty); // MEOWWW
```

## instanceof Narrowing

instanceof operatörü, bir değişkenin belirli bir sınıfın örneği olup olmadığını kontrol eder:

```typescript
const printFullDate = (date: Date | string) => {
  if (date instanceof Date) {
    // Bu blokta date kesinlikle Date
    return date.toUTCString();
  } else {
    // Bu blokta date kesinlikle string
    return new Date(date).toUTCString();
  }
};

console.log(printFullDate(new Date()));
console.log(printFullDate('2025-02-21'));
```

## Type Predicates

TypeScript'te özel type guard fonksiyonları yazabilirsiniz. Bu fonksiyonlar, parameterName is Type formatında bir dönüş tipine sahiptir:

```typescript
interface Cat {
  meow: () => void;
}
interface Dog {
  bark: () => void;
}

// Type predicate function
function isCat(pet: Cat | Dog): pet is Cat {
  return (pet as Cat).meow !== undefined;
}

let pet = getAnimal();
if (isCat(pet)) {
  // Bu blokta pet kesinlikle Cat
  pet.meow();
} else {
  // Bu blokta pet kesinlikle Dog
  pet.bark();
}
```

Type predicate'lerin avantajları:

- Özel type guard mantığı yazabilirsiniz
- Kod tekrarını önler
- Tip kontrollerini merkezileştirir
- Okunabilirliği artırır

## Discriminated Unions

Discriminated unions, ortak bir literal özellik kullanarak birbiriyle ilişkili tipleri ayırt etme tekniğidir:

```typescript
interface Circle {
  kind: 'circle';
  radius: number;
}

interface Square {
  kind: 'square';
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape) {
  switch (shape.kind) {
    case 'circle':
      // Bu blokta shape kesinlikle Circle
      return Math.PI * shape.radius ** 2;
    case 'square':
      // Bu blokta shape kesinlikle Square
      return shape.sideLength ** 2;
  }
}
```

Discriminated unions'ın avantajları:

- Tip güvenliği sağlar
- Switch case ile kullanımı kolaydır
- IDE desteği mükemmeldir
- Yeni tip eklemek kolaydır
- Eksik case'leri derleme zamanında yakalar

## Best Practices

1. **Doğru Type Guard Seçimi**

   ```typescript
   // Basit tipler için typeof
   function processValue(value: string | number) {
     if (typeof value === 'string') {
       return value.toUpperCase();
     }
     return value.toFixed(2);
   }

   // Sınıflar için instanceof
   function processDate(date: Date | string) {
     if (date instanceof Date) {
       return date.toISOString();
     }
     return new Date(date).toISOString();
   }
   ```

2. **Type Predicate'leri Etkili Kullanma**

   ```typescript
   interface User {
     id: number;
     name: string;
   }

   interface Admin extends User {
     role: 'admin';
     permissions: string[];
   }

   function isAdmin(user: User): user is Admin {
     return 'role' in user && user.role === 'admin';
   }
   ```

3. **Discriminated Unions'ı Düzgün Yapılandırma**

   ```typescript
   interface ApiSuccess {
     status: 'success';
     data: any;
   }

   interface ApiError {
     status: 'error';
     error: string;
   }

   type ApiResponse = ApiSuccess | ApiError;

   function handleResponse(response: ApiResponse) {
     if (response.status === 'success') {
       processData(response.data);
     } else {
       handleError(response.error);
     }
   }
   ```

## Sonuç

Type narrowing, TypeScript'in en güçlü özelliklerinden biridir. Bu teknikler sayesinde:

- Daha güvenli kod yazabilirsiniz
- Runtime hatalarını azaltabilirsiniz
- IDE desteğinden maksimum fayda sağlayabilirsiniz
- Karmaşık tip yapılarını daha kolay yönetebilirsiniz

Bir sonraki yazımızda TypeScript'in diğer ileri seviye özelliklerini incelemeye devam edeceğiz. Görüşmek üzere!
