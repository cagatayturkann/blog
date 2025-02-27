---
title: "TypeScript'i Anlamak - Bölüm 9: Örnek Proje"
summary: Bu makalede, TypeScript ve Node.js kullanarak JWT ve Google OAuth kimlik doğrulaması içeren bir Todo uygulaması geliştirme sürecini inceleyeceğiz. TypeScript'in tip güvenliği, MongoDB entegrasyonu ve clean architecture prensiplerini uygulayarak modern bir REST API tasarlama deneyimini ele alacağız.
date: 2025-02-25T09:00:00+03:00
cardimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum9/images/cover.svg'
featureimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum9/images/cover.svg'
featured: true
categories: ['TypeScript', 'Node.js', 'Backend']
tags: ['typescript', 'nodejs', 'microservices']
translationKey: 'typescript-file-handler-service'
authors:
  - Çağatay: /images/author.jpeg
---

# TypeScript ile Node.js Authentication API Geliştirme: Kapsamlı Rehber

## Giriş

Modern web uygulamaları geliştirirken tip güvenliği ve kod kalitesi giderek daha önemli hale geliyor. Bu eğitimde, TypeScript'in sunduğu güçlü özellikleri gerçek bir proje üzerinde öğreneceğiz. JWT ve Google OAuth kimlik doğrulaması içeren bir Todo uygulaması geliştirirken, TypeScript'in temel kavramlarını ve best practice'lerini uygulayacağız. Uygulamanın tüm haline [GitHub repository](https://github.com/cagatayturkann/blogProjects/tree/main/nodejs-typescript-auth) adresinden ulaşabilirsiniz.

Bu proje size şunları kazandıracak:

- TypeScript ile güvenli kod yazma pratiği
- Modern bir REST API tasarlama deneyimi
- Authentication ve Authorization implementasyonu
- MongoDB ile TypeScript kullanımı
- Clean Architecture prensiplerini uygulama

## TypeScript Özellikleri ve Proje Yapısı

Bu projede TypeScript'in temel özelliklerini kullanarak güvenli ve ölçeklenebilir bir API geliştireceğiz. Her özelliği gerçek proje kodlarımızdan örneklerle açıklayalım:

### 1. TypeScript Temelleri (Basics)

TypeScript'in temel yapı taşlarını projemizde şu şekilde kullanıyoruz:

```typescript
// src/config/env.ts'de Tip Tanımlamaları
const PORT: number = Number(process.env.PORT) || 3000;
const JWT_EXPIRES_IN: string = '1d';

// src/middleware/auth.middleware.ts'de Type Assertion
const decoded = jwt.verify(token, JWT_SECRET) as IJwtPayload;
// Burada JWT'den gelen veriyi IJwtPayload tipine dönüştürüyoruz

// src/interfaces/user.interface.ts'de Literal Types
type UserRole = 'user' | 'admin';
// User modelinde kullanıcı rollerini sadece bu iki değerle sınırlıyoruz
```

**Projedeki Kullanım Örnekleri:**

- `PORT` tanımı `src/index.ts`'de server başlatırken kullanılıyor
- Type assertion `auth.middleware.ts`'de JWT doğrulamasında kullanılıyor
- UserRole tipi `IUser` interface'inde kullanıcı rolünü kısıtlamak için kullanılıyor

### 2. Fonksiyonlar (Functions)

TypeScript'te fonksiyonları tip güvenli şekilde tanımlıyoruz:

```typescript
// src/services/auth.service.ts'de Method Signatures
interface IAuthService {
  login(credentials: IUserLogin): Promise<{ user: IUser; token: string }>;
  register(userData: IUserRegistration): Promise<IUser>;
}

// src/controllers/auth.controller.ts'de Implementation
public async login(req: Request, res: Response): Promise<void> {
  const { email, password } = req.body;
  const result = await this.authService.login({ email, password });
  // ...
}
```

**Projedeki Kullanım Örnekleri:**

- `IAuthService` interface'i `auth.service.ts`'de servis implementasyonunu tanımlıyor
- Controller'lardaki tüm handler fonksiyonları Request ve Response tiplerini kullanıyor
- Tüm async fonksiyonlar Promise return type'ı ile tanımlanıyor

### 3. Nesne Tipleri (Object Types)

Projemizde karmaşık veri yapılarını nesne tipleriyle modelliyoruz:

```typescript
// src/config/database.ts'de Configuration Types
type DatabaseConfig = {
  uri: string;
  options: {
    useNewUrlParser: boolean;
    useUnifiedTopology: boolean;
  };
};

// src/controllers/todo.controller.ts'de Request Types
interface ITodoCreate {
  title: string;
  description?: string; // Optional property örneği
}
```

**Projedeki Kullanım Örnekleri:**

- `DatabaseConfig` tipi MongoDB bağlantı ayarlarını tanımlıyor
- `ITodoCreate` interface'i todo oluşturma endpoint'inde request body validasyonu için kullanılıyor
- Optional property'ler todo güncellemelerinde kısmi güncellemeye izin veriyor

### 4. Interfaces

Interface'leri projemizde hem tip tanımı hem de sözleşme olarak kullanıyoruz:

```typescript
// src/interfaces/base.interface.ts'de Base Interface
interface IBaseEntity {
  _id: string;
  createdAt: Date;
  updatedAt: Date;
}

// src/interfaces/user.interface.ts'de Interface Extension
interface IUser extends IBaseEntity {
  email: string;
  password?: string;
  name: string;
  role: UserRole;
}
```

**Projedeki Kullanım Örnekleri:**

- `IBaseEntity` tüm MongoDB modellerimizin temel alanlarını tanımlıyor
- `IUser` interface'i User modelinin şemasını ve metodlarını tanımlıyor
- Interface'ler mongoose model tanımlarında tip güvenliği sağlıyor

### 5. TypeScript Compiler

TypeScript derleyicisini projemiz için özel olarak yapılandırıyoruz:

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

**Compiler Ayarlarının Önemi:**

- `strict`: Katı tip kontrolü sağlıyor
- `target`: Modern JavaScript özelliklerini kullanmamızı sağlıyor
- `module`: Node.js ile uyumlu modül sistemi kullanıyoruz

### 6. Classes

OOP prensiplerini TypeScript classes ile uyguluyoruz:

```typescript
// src/services/base.service.ts'de Abstract Base Class
abstract class BaseService<T extends IBaseEntity> {
  constructor(protected model: Model<T>) {}

  abstract create(data: Partial<T>): Promise<T>;

  async findById(id: string): Promise<T | null> {
    return this.model.findById(id);
  }
}

// src/services/todo.service.ts'de Class Implementation
class TodoService extends BaseService<ITodo> {
  async create(data: ICreateTodo): Promise<ITodo> {
    return this.model.create(data);
  }

  async markAsCompleted(id: string): Promise<ITodo | null> {
    return this.model.findByIdAndUpdate(id, { completed: true });
  }
}
```

**Neden Bu Özellik?**

- Abstract class'lar ile ortak davranışları zorunlu kılıyoruz
- Inheritance ile kod tekrarını önlüyoruz
- Service katmanını organize ediyoruz

**Projedeki Kullanım Örnekleri:**

- `BaseService` tüm servisler için temel CRUD operasyonlarını tanımlıyor
- `TodoService` ve `AuthService` bu base class'ı extend ederek kendi özel metodlarını ekliyor
- Abstract metodlar sayesinde her servisin kendi create metodunu implemente etmesi zorunlu

### 7. Generics

Generic tipleri projemizde şu şekilde kullanıyoruz:

```typescript
// src/services/base.service.ts'de Generic Service
class CrudService<T extends IBaseEntity> {
  async findOne(filter: FilterQuery<T>): Promise<T | null> {
    return this.model.findOne(filter);
  }
}

// src/utils/response.ts'de Generic Response Handler
function createResponse<T>(success: boolean, message: string, data?: T): IApiResponse<T> {
  return { success, message, data };
}

// src/utils/error.ts'de Generic Error Handler
class ApiError<T = unknown> extends Error {
  constructor(public statusCode: number, message: string, public data?: T) {
    super(message);
  }
}
```

**Neden Bu Özellik?**

- Tip güvenliğini koruyarak yeniden kullanılabilir kod yazıyoruz
- Farklı veri tipleriyle çalışabilen fonksiyonlar oluşturuyoruz
- Tip parametreleri ile esnek yapılar kuruyoruz

**Projedeki Kullanım Örnekleri:**

- `CrudService` farklı model tipleriyle çalışabiliyor (User, Todo vb.)
- `createResponse` her türlü veri yapısı için tutarlı API yanıtları oluşturuyor
- `ApiError` farklı hata tipleri için özelleştirilebilir error handling sağlıyor

### 8. Type Narrowing

Runtime'da tip kontrolü ve daraltma işlemlerini güvenli şekilde yapıyoruz:

```typescript
// src/utils/error.ts'de Type Guards
function isError(error: unknown): error is Error {
  return error instanceof Error;
}

// src/middleware/error.middleware.ts'de Error Handling
function handleError(error: unknown): IApiResponse<null> {
  if (isError(error)) {
    return createResponse(false, error.message);
  }

  if (typeof error === 'string') {
    return createResponse(false, error);
  }

  return createResponse(false, 'Unknown error occurred');
}

// src/types/error.types.ts'de Discriminated Unions
type ValidationError = {
  type: 'validation';
  fields: { [key: string]: string };
};

type AuthError = {
  type: 'auth';
  message: string;
};

type AppError = ValidationError | AuthError;

// src/utils/error-handler.ts'de Error Type Handling
function handleAppError(error: AppError) {
  switch (error.type) {
    case 'validation':
      return error.fields;
    case 'auth':
      return error.message;
  }
}
```

**Neden Bu Özellik?**

- Runtime'da tip güvenliğini sağlıyoruz
- Hata yönetimini daha güvenli yapıyoruz
- Union type'ları doğru şekilde işliyoruz

**Projedeki Kullanım Örnekleri:**

- `isError` type guard'ı middleware'lerde hata tipini doğru şekilde belirlememizi sağlıyor
- Error handling middleware'de farklı hata tiplerini ayırt edebiliyoruz
- Discriminated union'lar ile validation ve auth hatalarını ayrı ayrı işleyebiliyoruz

## Proje Özeti

Geliştireceğimiz API aşağıdaki özellikleri içerecek:

1. **Kullanıcı Yönetimi**

   - Kayıt ve Giriş
   - JWT Authentication
   - Google OAuth Entegrasyonu
   - Rol tabanlı yetkilendirme

2. **Todo İşlemleri**

   - Todo oluşturma, okuma, güncelleme, silme
   - Kullanıcıya özel todo'lar
   - Todo durumu değiştirme

3. **Güvenlik ve Validasyon**
   - Input validasyonu
   - Route koruması
   - Error handling

## Proje Yapısı

Projemiz şu şekilde organize edilmiştir:

```
src/
├── config/         # Konfigürasyon dosyaları
├── controllers/    # HTTP request handlers
├── interfaces/     # TypeScript interfaces
├── middleware/     # Express middleware
├── models/        # Mongoose modelleri
├── routes/        # API routes
├── services/      # İş mantığı
├── utils/         # Yardımcı fonksiyonlar
└── index.ts       # Uygulama giriş noktası
```

## TypeScript ile Proje Geliştirme

### 1. Proje Kurulumu ve TypeScript Konfigürasyonu

İlk adım olarak TypeScript'i projemize entegre edelim:

```bash
mkdir nodejs-typescript-auth
cd nodejs-typescript-auth
npm init -y
npm install typescript ts-node @types/node --save-dev
```

#### Bağımlılıklar

Projemiz için gerekli paketleri yükleyelim:

```bash
# Ana bağımlılıklar
npm install express mongoose dotenv jsonwebtoken bcrypt passport passport-google-oauth20 passport-jwt cors

# Tip tanımlamaları
npm install @types/express @types/mongoose @types/jsonwebtoken @types/bcrypt @types/passport @types/passport-google-oauth20 @types/passport-jwt @types/cors --save-dev
```

#### TypeScript Konfigürasyonu

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

### 2. Veri Modellerini Tanımlama

#### User Modeli

TypeScript interface'leri ile kullanıcı modelimizi tanımlayalım:

```typescript
// src/interfaces/user.interface.ts

// Base interface - temel kullanıcı özellikleri
interface IBaseUser {
  email: string;
  name: string;
}

// Ana kullanıcı interface'i - tüm özellikleri içerir
interface IUser extends IBaseUser {
  password?: string; // Optional: Google OAuth kullanıcıları için şifre olmayabilir
  googleId?: string; // Optional: Sadece Google ile giriş yapanlar için
  role: 'user' | 'admin'; // Union type ile rol kısıtlaması
  comparePassword(candidatePassword: string): Promise<boolean>;
}

// Kayıt için gerekli alanlar
interface IUserRegistration {
  email: string;
  password: string;
  name: string;
}

// Giriş için gerekli alanlar
interface IUserLogin {
  email: string;
  password: string;
}
```

#### Todo Modeli

Todo işlemleri için gerekli interface'leri tanımlayalım:

```typescript
// src/interfaces/todo.interface.ts

interface ITodo {
  title: string;
  description?: string;
  completed: boolean;
  user: string; // Referans: User ID
  createdAt: Date;
  updatedAt: Date;
}

// Todo oluşturma için gerekli alanlar
interface ICreateTodo {
  title: string;
  description?: string;
}

// Todo güncelleme için opsiyonel alanlar
interface IUpdateTodo {
  title?: string;
  description?: string;
  completed?: boolean;
}
```

### 3. Servis Katmanı Implementasyonu

#### Base Service

Generic bir base service oluşturarak kod tekrarını önleyelim:

```typescript
// src/services/base.service.ts

abstract class BaseService<T> {
  constructor(protected model: Model<T>) {}

  async findById(id: string): Promise<T | null> {
    return this.model.findById(id);
  }

  async findOne(filter: FilterQuery<T>): Promise<T | null> {
    return this.model.findOne(filter);
  }

  async find(filter: FilterQuery<T>): Promise<T[]> {
    return this.model.find(filter);
  }
}
```

#### Auth Service

Kimlik doğrulama işlemlerini yöneten servis:

```typescript
// src/services/auth.service.ts

class AuthService extends BaseService<IUser> {
  public async register(userData: IUserRegistration): Promise<IUser> {
    const existingUser = await this.findOne({ email: userData.email });
    if (existingUser) {
      throw new Error('Bu email adresi zaten kullanımda');
    }

    const user = await this.model.create(userData);
    return user;
  }

  public async login(loginData: IUserLogin): Promise<{ user: IUser; token: string }> {
    const user = await this.findOne({ email: loginData.email });
    if (!user || !(await user.comparePassword(loginData.password))) {
      throw new Error('Geçersiz kimlik bilgileri');
    }

    return {
      user,
      token: this.generateToken(user),
    };
  }

  private generateToken(user: IUser): string {
    return jwt.sign({ id: user._id, email: user.email, role: user.role }, process.env.JWT_SECRET!, { expiresIn: '1d' });
  }
}
```

#### Todo Service

Todo işlemlerini yöneten servis:

```typescript
// src/services/todo.service.ts

class TodoService extends BaseService<ITodo> {
  public async getAllTodos(userId: string): Promise<ITodo[]> {
    return this.find({ user: userId });
  }

  public async createTodo(todoData: ICreateTodo, userId: string): Promise<ITodo> {
    return this.model.create({
      ...todoData,
      user: userId,
      completed: false,
    });
  }

  public async updateTodo(todoId: string, todoData: Partial<ITodo>, userId: string): Promise<ITodo | null> {
    return this.model.findOneAndUpdate({ _id: todoId, user: userId }, todoData, { new: true });
  }
}
```

### 4. Middleware Implementasyonu

TypeScript ile güvenli middleware yazımı:

```typescript
// src/middleware/auth.middleware.ts

// Request tipini genişletme
declare global {
  namespace Express {
    interface Request {
      user?: IUser;
    }
  }
}

export const isAuthenticated = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) {
      throw new Error('Token bulunamadı');
    }

    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as IJwtPayload;
    const user = await UserModel.findById(decoded.id);

    if (!user) {
      throw new Error('Kullanıcı bulunamadı');
    }

    req.user = user;
    next();
  } catch (error) {
    res.status(401).json({
      success: false,
      message: 'Yetkilendirme hatası',
      error: error instanceof Error ? error.message : 'Bilinmeyen hata',
    });
  }
};
```

### 5. Controller Katmanı

TypeScript ile tip güvenli controller'lar:

```typescript
// src/controllers/todo.controller.ts

class TodoController {
  constructor(private todoService: TodoService) {}

  public async getAllTodos(req: Request, res: Response): Promise<void> {
    try {
      const todos = await this.todoService.getAllTodos(req.user!._id);

      res.status(200).json({
        success: true,
        message: 'Todolar başarıyla getirildi',
        data: todos,
      });
    } catch (error) {
      res.status(500).json({
        success: false,
        message: 'Todolar getirilirken hata oluştu',
        error: error instanceof Error ? error.message : 'Bilinmeyen hata',
      });
    }
  }
}
```

## API Endpoint'leri

### Auth Endpoints

```typescript
POST /api/auth/register
- Request Body: { email: string, password: string, name: string }
- Response: { success: boolean, message: string, data: { user: IUser, token: string } }

POST /api/auth/login
- Request Body: { email: string, password: string }
- Response: { success: boolean, message: string, data: { user: IUser, token: string } }

GET /api/auth/google
- Google OAuth başlatma endpoint'i

GET /api/auth/google/callback
- Google OAuth callback endpoint'i
```

### Todo Endpoints

```typescript
GET /api/todos
- Headers: { Authorization: "Bearer ${token}" }
- Response: { success: boolean, message: string, data: ITodo[] }

POST /api/todos
- Headers: { Authorization: "Bearer ${token}" }
- Request Body: { title: string, description?: string }
- Response: { success: boolean, message: string, data: ITodo }

PUT /api/todos/:id
- Headers: { Authorization: "Bearer ${token}" }
- Request Body: { title?: string, description?: string, completed?: boolean }
- Response: { success: boolean, message: string, data: ITodo }

DELETE /api/todos/:id
- Headers: { Authorization: "Bearer ${token}" }
- Response: { success: boolean, message: string }
```

## Best Practices

1. **Tip Güvenliği**

   - Her zaman spesifik tipler kullanın
   - `any` tipinden kaçının
   - Union types ile değer kümelerini sınırlayın
   - Generic tipler ile yeniden kullanılabilir kod yazın

2. **Kod Organizasyonu**

   - Her bir katman için ayrı klasör kullanın
   - Interface'leri ilgili domain klasöründe tutun
   - Servis katmanını abstract class'lar ile genelleştirin

3. **Error Handling**

   - Custom error sınıfları oluşturun
   - Global error handler kullanın
   - Hata mesajlarını standardize edin

4. **Güvenlik**
   - Hassas bilgileri environment variable'larda saklayın
   - Input validasyonu yapın
   - Rate limiting uygulayın
   - CORS politikalarını doğru yapılandırın

## Sonuç

Bu projede:

- TypeScript'in tip sistemi ile güvenli kod yazmayı
- OOP prensiplerini TypeScript ile uygulamayı
- Modern bir API mimarisi tasarlamayı
- Authentication ve authorization implementasyonunu
  öğrendik.

TypeScript, projemize:

- Derleme zamanında hata yakalama
- Daha iyi IDE desteği
- Self-documenting kod
- Maintainability
  gibi önemli avantajlar sağladı.
