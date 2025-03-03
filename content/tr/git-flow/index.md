---
title: 'Git Flow Nedir?'
# subtitle: "Blog post subtitle :zap:"
summary: Git Flow, versiyon kontrol sistemlerinde dal yönetimini sistematikleştiren etkili bir yaklaşımdır. Bu makalede Git Flow'un ne olduğunu, nasıl kullanıldığını ve en iyi uygulama önerilerini detaylı olarak açıklayacağım.
date: 2025-01-30
cardimage: '/blog/tr/git-flow/images/cover.png'
featureimage: 'tr/blog/git-flow/images/cover.png'
featured: true
# caption: Image caption
categories: ['Git']
tags: ['git-flow', 'git']
translationKey: "git-flow-guide"
authors:
  - Çağatay: /images/author.jpeg
---

\
Merhaba. Bu makalede versiyon kontrol sistemlerinden kısaca bahsedip, Git Flow'un ne olduğunu ve nasıl kullanıldığını detaylı olarak anlatacağım. Versiyon kontrol sistemleri, yazılım geliştirme süreçlerinde kod versiyonlarını takip etmek ve yönetmek için kullanılan önemli araçlardır. Git Flow ise bu versiyon kontrol sistemini daha etkili kullanmamızı sağlayan bir dallanma modelidir.

## Versiyon Kontrol Sistemi Nedir?

Versiyon kontrol sistemi, bir veya daha fazla dosya, belge (yazılım projesi, ofis belgeleri vb.) üzerinde yaptığımız değişiklikleri adım adım kaydeden, daha sonra belirli bir versiyona geri dönmemizi sağlayan ve istenirse bunu çevrimiçi bir depoda saklayıp yönetmemize olanak tanıyan bir sistemdir. Git, SVN, BitKeeper ve Mercurial versiyon kontrol sistemlerine örnek olarak verilebilir.

### Neden Versiyon Kontrol Sistemleri Kullanırız?

1. Her dosya için uzun vadeli bir değişiklik geçmişi tutulur.

   - Bu, dosya üzerinde yıllar içinde birden fazla kişi tarafından yapılan her değişikliğin takip edilmesi anlamına gelir.
   - Bu sayede eski ve yeni kodumuzu karşılaştırarak bulunduğumuz noktaya nasıl geldiğimizi anlayabiliriz.

2. Takım üyelerinin aynı kod üzerinde eş zamanlı çalışmasına olanak tanır.

   - Yazılım üzerinde farklı çalışmalar yürütmek için alt versiyonlar oluşturmak ve daha sonra ana yazılıma entegre etmek mümkündür.

3. Yazılıma yapılan her değişikliğin takip edilmesini ve proje yönetimiyle ilişkilendirilmesini sağlar.

   - Yazılım sorunlarının versiyonlarla ilişkilendirilmesini ve takibini sağlar.

4. Projede hatalarla karşılaştığımızda eski kod kayıtlarına dönmemizi sağlar.

### Versiyon Kontrol Sistemleri

**Yerel VKS:** En eski versiyon kontrol sistemi yaklaşımıdır. Projemiz ve yaptığımız değişiklikler kullanıcı makinesindeki bir veritabanında saklanır. Her commit bir versiyon olarak saklanır ve her versiyon commit'e bir hash değeri atanarak ayırt edilir. Ayrıca versiyon görüntüleme özelliği sunar. Ancak bu sistemde sadece bir kullanıcı etkin olarak çalışabilir.

**Merkezi VKS:** Birden fazla kişinin bir proje üzerinde etkin çalışabilmesi için oluşturulmuş bir versiyonlama sistemidir. CVS ve SVN merkezi versiyon kontrol sistemleridir. Bu sistemde proje paylaşılan bir depoda tutulur ve birden fazla geliştirici aynı depo üzerinde checkout ve commit işlemleri gerçekleştirir. Bu yöntem herkesin projeye katkıda bulunmasına olanak sağlarken, bazı ciddi sorunları vardır. Tek merkezi sunucu 1 saat çökerse, kullanıcılar o bir saat boyunca çalışmalarını kaydedemez veya projelerinin versiyonlanmış kopyalarına erişemezler.

**Dağıtık VKS:** Merkezi versiyon sistemlerinin, geliştiricilerin çevrimdışı çalışamaması ve deponun zarar görmesi durumunda kurtarmanın zor olması gibi kısıtlamalarından dolayı oluşturulmuş bir versiyon sistemidir. Git, Mercurial ve BitKeeper dağıtık versiyon sistemlerine örnektir. Bu sistemlerde merkezi bir depo yoktur ve proje üzerinde çalışan her makine projenin bir kopyasını kendi yerel bilgisayarında tutar. Geliştiriciler proje üzerinde değişiklik yapmak veya proje geçmişini görüntülemek istediklerinde uzak depo ile iletişim kurmaları gerekmez. Bir sunucu çökerse ve o sunucu üzerinde işbirliği yapan sistemler varsa, geliştiricilerden birinin projeyi sunucuya geri yüklemesiyle sistem kurtarılabilir. Özetle, aynı proje üzerinde farklı geliştiricilerin farklı iş akışlarıyla farklı şekillerde çalışmasına olanak tanır.

### Git Flow Nedir?

5 Ocak 2020'de nvie, <a href="https://nvie.com/posts/a-successful-git-branching-model/" rel="nofollow">https://nvie.com/posts/a-successful-git-branching-model/</a> adresindeki bir yazıda git depolarını düzenli tutmak için bir model önerdi. Daha sonra bu modeli kullanmayı kolaylaştıran git uzantılarını içeren Git-Flow adlı bir proje yayınladı. GitFlow modeli temelde git versiyon kontrol sistemi üzerine kurulmuştur. Yani tüm model işlemlerini git komutlarıyla gerçekleştirmek mümkündür.

### Git Flow'un Avantajları ve Dezavantajları

**Avantajları:**

- Organize ve öngörülebilir bir geliştirme süreci sağlar
- Büyük takımlar için ideal bir yapı sunar
- Versiyon yönetimini basitleştirir
- Her dalın net bir amacı vardır
- Paralel geliştirmeyi destekler

**Dezavantajları:**

- Küçük projeler için fazla karmaşık olabilir
- Sürekli teslimat için uygun olmayabilir
- Dal yapısı bazen gereksiz karmaşıklığa yol açabilir
- Ek araç kurulumu gerektirir
- Öğrenme eğrisi diğer modellere göre daha yüksektir

### Git Flow Alternatifleri

- **GitHub Flow:** Daha basit bir model, sadece master ve feature dallarını kullanır
- **GitLab Flow:** Git Flow ve GitHub Flow arasında denge kurar
- **Trunk Based Development:** Ana dal üzerinde geliştirmeye odaklanır

### Git Flow Çalışma Prensibi

{{< figure src="./images/diagram.png" alt="git flow">}}

Git Flow modelinde 5 ana dal bulunur:

- **master:** Ana dallardan biri olan Master, proje boyunca var olur. Master dalı her zaman üretime çıkabilecek kodu içerir. İdeal olarak master dalına yapılan her commit bir versiyondur ve "git tag" ile işaretlenmelidir (versiyon numarası verilmelidir). Master dalına direkt commit yapılmaz, sadece hotfix ve release dallarından merge yapılmasına izin verilir.

- **develop:** Develop, proje boyunca var olan diğer ana daldır. Develop dalı bir sonraki versiyon için yapılan değişiklikleri içerir. Tüm feature dalları önce bu dala merge edilir. Bu dal projenin ana geliştirme dalıdır ve sürekli entegrasyon (CI) süreçleri genellikle bu dal üzerinde çalışır.

- **hotfix:** Hotfix dalı, canlıdaki versiyonda kritik bir hata olduğunda ve bu hatanın hemen düzeltilip deploy edilmesi gerektiğinde kullanılır. Hotfix dalı master dalından oluşturulur ve genellikle 'hotfix/[versiyon]-[açıklama]' formatında isimlendirilir. Hotfix dalında hata düzeltmesi tamamlandığında bu dal hem Developer hem de Master ile merge edilir. Master ile merge edildikten sonra değişiklik yeni bir versiyon numarası ile etiketlenir.

- **feature:** Yeni bir özellik eklenirken, bu özellik için bir Feature dalı oluşturulur. Feature dalları her zaman develop dalından oluşturulur ve genellikle 'feature/[özellik-adı]' formatında isimlendirilir. Bunlar özelliklere göre değişiklikler olarak düşünülebilir. Aynı anda birden fazla feature dalı açılabilir. Bu farklı geliştiricilerin farklı özellikler üzerinde çalışabileceği anlamına gelir. Özellikleri ayrı dallarda geliştirmek hem Develop dalının gereksiz commitlerle dolmasını engeller hem de feature dalını silmek suretiyle bir özellikten vazgeçmeyi kolaylaştırır. Özellik tamamlandığında bu dal Develop dalı ile merge edilir ve feature dalı silinir. Yani feature dalları sadece geliştirme süresince yaşar. Tabi bu süreçte kontrol amaçlı arada bir Develop dalından pull almak gerekebilir çünkü başka bir geliştirici feature dalını önce bitirmiş ve Develop dalına version push edilmiş olabilir. Feature dalları master, release, develop veya hotfix içeren isimler içermemelidir.

- **release:** Diyelim ki tüm değişiklikler tamamlandı. Yeni bir versiyon çıkılacağı zaman Develop dalından yeni bir Release dalı oluşturulur. Release dalları genellikle 'release/[versiyon]' formatında isimlendirilir. Versiyondaki son değişiklikler, versiyon numaralarının değiştirilmesi vb. işlemler bu dalda yapılır. Release dalında sadece hata düzeltmeleri yapılmalı, yeni özellikler eklenmemelidir. Gerekli tüm değişiklikler tamamlandığında Release dalında tamamlanan tüm değişiklikler hem Master hem de Develop dallarına merge edilir. Master dalında git tag ile versiyon numarası etiketlenir ve ardından Release dalı silinir.

### Git Flow Örnek Proje

- **/brew install git-flow > git flow init**
  {{< figure src="./images/git-flow-init.png" alt="git flow">}}
  İlk olarak "brew install git-flow" ile kurulum yapıyoruz. GitFlow git ile birlikte gelmiyor. Ayrıca kurulması gerekiyor. Bu da dezavantajlarından biri olarak görülüyor. Git'te projeyi başlatmak için "git init" komutunu kullanıyorduk. Git-flow için git-flow sürecini başlatmak için "git flow init" komutunu giriyoruz. Komut çalıştığında eğer repo yoksa önce repo oluşturuyor. Ardından süreç için kullanılacak dal isimlerini kullanıcıya soruyor. Dal isimleri özelleştirilebilir ancak varsayılan değerlerin korunması önerilir.

- **/git flow feature start performance**
  {{< figure src="./images/git-flow-feature-start.png" alt="git flow">}}
  Bu komut yeni bir feature dalı oluşturur. Feature ismi performance olduğu için varsayılan olarak feature/performance dalı olacaktır. Aynı işlemi mevcut git komutu ile de yapabiliriz. Bunun için girmemiz gereken komut "git checkout -b myFeature feature/performance" olacaktır.

- **/git flow feature finish performance**
  {{< figure src="./images/git-flow-feature-finish.png" alt="git flow">}}
  Bu komut daha önce açılmış bir dalı kapatır. Kapatma işlemi feature dalının develop dalına merge edilmesiyle başlar ve feature dalının silinmesiyle sonlanır. Komut çalıştırıldığında değişiklikler commit edilmemişse hata verecektir. Commit sonrası push yapılmamışsa hata verecektir. Bu işlemi normal git komutlarıyla yapmak için ilgili dalda önce commit yapılır ardından sırasıyla "git checkout develop > git merge --no-ff feature/performance > git branch -D feature/performance" komutları çalıştırılır.

- **/git flow release start 1.0.0**
  {{< figure src="./images/git-flow-release-start.png" alt="git flow">}}
  Bu komut girildiğinde yeni bir versiyon çıkışa hazır hale gelir. Komut çalıştığında Develop dalından yeni bir release/1.0.0 dalı oluşturulur. Bu işlemi mevcut git komutu ile yapmak için "git checkout -b release/1.0.0 develop" komutu çalıştırılır.

- **/git flow release finish 1.0.0**
  {{< figure src="./images/git-flow-release-finish.png" alt="git flow">}}
  Komut girildiğinde tamamlanan versiyon master dalına alınır. Değişiklikler hem develop hem de master dalları ile merge edilir. Master dalındaki son commit versiyon numarası ile etiketlenir. Ardından release dalı otomatik olarak silinir. Aynı işlemi git komutları ile yapmak için sırasıyla aşağıdaki komutlar çalıştırılır:

  ```bash
  git checkout master
  git merge --no-ff release/1.0.0
  git tag -a 1.0.0
  git checkout develop
  git merge --no-ff release/1.0.0
  git branch -d release/1.0.0
  ```

- **/git flow hotfix start 1.0.1**
  {{< figure src="./images/git-flow-hotfix-start.png" alt="git flow">}}
  Bu komut ile yeni bir hotfix başlatılır. Hotfix dalları acil güncellemeler için kullanılır ve master dalından oluşturulur. Komut çalıştığında master dalından dallanan yeni bir hotfix/1.0.1 dalı oluşturulur. İşlemi git komutu ile yapmak için "git checkout -b hotfix/1.0.1 master" komutu çalıştırılır.

- **/git flow hotfix finish 1.0.1**
  {{< figure src="./images/git-flow-hotfix-finish.png" alt="git flow">}}
  Bu komut ile hotfix tamamlanır. Değişiklikler hem Develop hem de Master dallarına alınır. Master dalı 1.0.1 ile etiketlenir ve hotfix dalı silinir. "git tag -l" komutu çalıştırıldığında versiyon numaraları görüntülenir. Aynı işlemi mevcut git komutları ile yapmak için sırasıyla aşağıdaki komutlar çalıştırılır:

  ```bash
  git checkout master
  git merge --no-ff hotfix/1.0.1
  git tag -a 1.0.1
  git checkout develop
  git merge --no-ff hotfix/1.0.1
  git branch -d hotfix/1.0.1
  ```

  ### Git Flow En İyi Uygulama Önerileri

1. **Dal İsimlendirme Kuralları**

   - Feature dalları için açıklayıcı isimler kullanın (örn. feature/kullanici-dogrulama)
   - Hotfix ve release dalları için semantik versiyonlama kullanın

2. **Commit Mesajları**

   - Açıklayıcı commit mesajları yazın
   - Conventional Commits standardını takip edin
   - Her commit'in tek bir amacı olduğundan emin olun

3. **Kod İnceleme Süreci**

   - Feature dallarını merge etmeden önce kod incelemesi yapın
   - Otomatik test süreçlerini kullanın
   - Dokümantasyon güncellemelerini unutmayın

4. **Merge Stratejisi**
   - --no-ff (no fast-forward) parametresini kullanın
   - Merge çakışmalarını hızlıca çözün
   - Squash commit'leri kullanmayı düşünün

### Sonuç

Git Flow, modern yazılım geliştirme süreçlerinde dal yönetimini sistematikleştiren etkili bir yaklaşımdır. Bu iş akışı sayesinde takımlar daha organize çalışabilir, versiyonlamayı daha iyi kontrol edebilir ve kod kalitesini artırabilir. Özellikle büyük projelerde ve takım çalışmalarında, Git Flow'un sunduğu yapılandırılmış dal stratejisi geliştirme süreçlerini önemli ölçüde iyileştirir. Yukarıda bahsedilen en iyi uygulamaları takip ederek, Git Flow'u projenizde başarıyla uygulayabilir ve yazılım geliştirme süreçlerinizi daha verimli hale getirebilirsiniz.
