---
title: 'Linux Temel Komutları - Bölüm 1'
summary: Bu makale serisinde, her geliştiricinin bilmesi gereken en sık kullanılan Linux komutlarını inceleyeceğiz. İlk bölümde, Linux sisteminizi etkili bir şekilde yönetmenize yardımcı olacak 20 temel komutu ele alacağız.
date: 2025-02-08T18:19:00+03:00
cardimage: '/blog/tr/linux/linux-temel-komutları/images/cover.png'
featureimage: 'tr/blog/linux/linux-temel-komutları/images/cover.png'
featured: true
categories: ['Linux']
tags: ['linux', 'komutlar']
translationKey: "basic-linux-commands"
authors:
  - Çağatay: /images/author.jpeg
---

Herkese merhaba! Bu makale serisinde, her geliştiricinin bilmesi gereken en sık kullanılan Linux komutlarını açıklayacağım. Bu komutlar, Linux sisteminizi etkili bir şekilde yönetmek ve terminaldeki verimliliğinizi artırmak için gereklidir.

## Neden Linux Komutlarını Öğrenmeliyiz?

Linux komutları, terminal aracılığıyla işletim sistemimizle etkileşim kurmamızı sağlayan temel araçlardır. Bu komutları anlamak önemlidir çünkü:

1. Sistem üzerinde daha fazla kontrol sağlarlar
2. Genellikle grafiksel arayüz kullanmaktan daha hızlıdırlar
3. Birçok sunucu Linux üzerinde çalışır ve grafiksel arayüzleri yoktur
4. Otomasyon ve betik yazımı için gereklidirler
5. DevOps ve sistem yönetiminde yaygın olarak kullanılırlar

### En Çok Kullanılan Linux Komutları

En sık kullanılan 20 Linux komutunu inceleyelim:

1. **ls (Listele)**
   - Mevcut dizindeki dosya ve klasörleri listeler
   - Sık kullanılan seçenekler:
     - `ls -l`: Uzun format listeleme
     - `ls -a`: Gizli dosyaları göster
     - `ls -h`: İnsan tarafından okunabilir dosya boyutları

```bash
user@linux:~$ ls -la
total 32
drwxr-xr-x  2 user user 4096 Feb  8 10:00 .
drwxr-xr-x 20 user user 4096 Feb  8 10:00 ..
-rw-r--r--  1 user user  220 Feb  8 10:00 .bash_profile
-rw-r--r--  1 user user 3526 Feb  8 10:00 .bashrc
drwxr-xr-x  2 user user 4096 Feb  8 10:00 Documents
```

2. **cd (Dizin Değiştir)**
   - Bulunduğunuz dizini değiştirir
   - Kullanım örnekleri:
     - `cd /path/to/directory`: Belirli bir dizine git
     - `cd ..`: Bir üst dizine git
     - `cd ~`: Ana dizine git

```bash
user@linux:~$ pwd
/home/user
user@linux:~$ cd Documents
user@linux:~/Documents$ cd ..
user@linux:~$ cd ~
```

3. **pwd (Çalışma Dizinini Yazdır)**
   - Mevcut dizin yolunu gösterir
   - Dosya sisteminde nerede olduğunuzu doğrulamak için kullanışlıdır

```bash
user@linux:~$ pwd
/home/user/Documents/projects
```

4. **mkdir (Dizin Oluştur)**
   - Yeni dizinler oluşturur
   - Seçenekler:
     - `mkdir -p`: Eğer yoksa üst dizinleri oluşturur

```bash
user@linux:~$ mkdir -p projects/new-project
user@linux:~$ ls -l projects/
total 4
drwxr-xr-x 2 user user 4096 Feb 8 10:00 new-project
```

5. **rm (Sil)**
   - Dosya ve dizinleri siler
   - Önemli seçenekler:
     - `rm -r`: Dizinleri özyinelemeli olarak sil
     - `rm -f`: Onay istemeden zorla sil

```bash
user@linux:~$ ls
file1.txt file2.txt test_dir
user@linux:~$ rm file1.txt
user@linux:~$ rm -r test_dir
user@linux:~$ ls
file2.txt
```

6. **cp (Kopyala)**
   - Dosya ve dizinleri kopyalar
   - Yaygın kullanım:
     - `cp file1 file2`: file1'i file2'ye kopyala
     - `cp -r dir1 dir2`: Dizini özyinelemeli olarak kopyala

```bash
user@linux:~$ cp file1.txt backup.txt
user@linux:~$ cp -r projects/ projects_backup/
user@linux:~$ ls
backup.txt file1.txt projects projects_backup
```

7. **mv (Taşı)**
   - Dosya ve dizinleri taşır veya yeniden adlandırır
   - Örnekler:
     - `mv old.txt new.txt`: Dosyayı yeniden adlandır
     - `mv file /path/to/dir`: Dosyayı dizine taşı

```bash
user@linux:~$ ls
old.txt documents/
user@linux:~$ mv old.txt new.txt
user@linux:~$ mv new.txt documents/
user@linux:~$ ls documents/
new.txt
```

8. **cat (Birleştir)**
   - Dosya içeriğini görüntüler
   - Ayrıca dosyaları birleştirmek için kullanılır

```bash
user@linux:~$ cat file.txt
Bu file.txt dosyasının içeriğidir
user@linux:~$ cat file1.txt file2.txt > combined.txt
user@linux:~$ cat combined.txt
file1'den içerik
file2'den içerik
```

9. **grep (Global Regular Expression Print)**
   - Dosyalarda kalıp arar
   - Kullanışlı seçenekler:
     - `grep -i`: Büyük/küçük harf duyarsız arama
     - `grep -r`: Özyinelemeli arama

```bash
user@linux:~$ grep -r "TODO" .
./src/app.js:// TODO: Hata yönetimi eklenecek
./docs/readme.md:TODO: Dokümantasyon güncellenecek
user@linux:~$ grep -i "hata" log.txt
Hata: Bağlantı başarısız
hata: bağlanılamadı
HATA: Sistem hatası
```

10. **chmod (İzinleri Değiştir)**
    - Dosya izinlerini değiştirir
    - Format: `chmod [seçenekler] mod dosya`

```bash
user@linux:~$ ls -l script.sh
-rw-r--r-- 1 user user 256 Feb 8 10:00 script.sh
user@linux:~$ chmod +x script.sh
user@linux:~$ ls -l script.sh
-rwxr-xr-x 1 user user 256 Feb 8 10:00 script.sh
```

11. **sudo (Süper Kullanıcı Olarak Yap)**
    - Komutları süper kullanıcı yetkileriyle çalıştırır
    - Sistem yönetimi görevleri için önemlidir

```bash
user@linux:~$ apt update
E: Could not open lock file - open (13: Permission denied)
user@linux:~$ sudo apt update
[sudo] password for user: 
Reading package lists... Done
Building dependency tree... Done
```

12. **top**
    - Çalışan işlemleri ve sistem kaynaklarını gösterir
    - Etkileşimli işlem görüntüleyici

```bash
user@linux:~$ top
top - 10:00:00 up 2 days, 3:45, 1 user, load average: 0.52, 0.58, 0.59
Tasks: 180 total,   1 running, 179 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.9 us,  3.1 sy,  0.0 ni, 90.6 id,  0.4 wa,  0.0 hi,  0.0 si
MiB Mem :  7861.1 total,   2457.2 free,   3245.5 used,   2158.4 buff/cache
```

13. **ps (İşlem Durumu)**
    - Çalışan işlemleri görüntüler
    - Yaygın seçenekler:
      - `ps aux`: Tüm işlemleri göster

```bash
user@linux:~$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user      2345  0.0  0.1 169512  3252 pts/0    Ss   09:30   0:00 bash
user      2789  0.0  0.2 170284  6432 pts/0    R+   10:00   0:00 ps aux
```

14. **df (Disk Boş Alan)**
    - Disk alanı kullanımını gösterir
    - Kullanışlı seçenekler:
      - `df -h`: İnsan tarafından okunabilir boyutlar

```bash
user@linux:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       234G   67G  156G  31% /
tmpfs           3.9G     0  3.9G   0% /tmp
/dev/sda2       100G   45G   55G  45% /home
```

15. **du (Disk Kullanımı)**
    - Dizin alan kullanımını gösterir
    - Yaygın kullanım:
      - `du -sh *`: Mevcut dizin içeriğinin boyutu

```bash
user@linux:~$ du -sh *
156M    Documents
1.2G    Downloads
42M     Pictures
890M    projects
```

16. **tar**
    - Dosya ve dizinleri arşivler
    - Yaygın işlemler:
      - `tar -czf`: Arşiv oluştur
      - `tar -xzf`: Arşivi çıkart

```bash
user@linux:~$ tar -czf archive.tar.gz Documents/
user@linux:~$ ls -lh archive.tar.gz
-rw-r--r-- 1 user user 145M Feb 8 10:00 archive.tar.gz
user@linux:~$ tar -xzf archive.tar.gz
```

17. **find**
    - Dizin hiyerarşisinde dosya arar
    - Örnekler:
      - `find . -name "*.txt"`: Tüm .txt dosyalarını bul

```bash
user@linux:~$ find . -name "*.txt"
./documents/notes.txt
./projects/readme.txt
./backup/old.txt
user@linux:~$ find . -type d -name "test"
./projects/test
./src/test
```

18. **wget**
    - İnternetten dosya indirir
    - Kullanışlı seçenekler:
      - `wget -c`: Yarıda kalan indirmeye devam et

```bash
user@linux:~$ wget https://example.com/file.zip
--2025-02-08 10:00:00--  https://example.com/file.zip
Resolving example.com... 93.184.216.34
Connecting to example.com... connected.
HTTP request sent, awaiting response... 200 OK
Length: 52890112 (50M) [application/zip]
Saving to: 'file.zip'
```

19. **systemctl**
    - Systemd sistem ve servis yöneticisini kontrol eder
    - Yaygın kullanımlar:
      - `systemctl start/stop/restart/status service`

```bash
user@linux:~$ sudo systemctl status nginx
● nginx.service - Yüksek performanslı web sunucusu ve ters proxy sunucusu
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2025-02-08 09:30:12 UTC; 30min ago
```

20. **history**
    - Komut geçmişini gösterir
    - Kullanışlı özellikler:
      - `!n`: n numaralı komutu çalıştır
      - `!!`: Son komutu çalıştır

```bash
user@linux:~$ history
  1  pwd
  2  cd Documents
  3  ls -la
  4  mkdir projects
  5  cd projects
user@linux:~$ !3
ls -la
total 32
drwxr-xr-x 2 user user 4096 Feb 8 10:00 .
drwxr-xr-x 5 user user 4096 Feb 8 10:00 ..
```

### En İyi Uygulamalar

1. **Her Zaman Tab Tamamlama Kullanın**
   - Zaman kazandırır ve yazım hatalarını önler
   - Mevcut seçenekleri gösterir

2. **Komutları Çalıştırmadan Önce Kontrol Edin**
   - `--help` veya `man` komutunu kullanın
   - `rm` ve `sudo` ile ekstra dikkatli olun

3. **Komut Geçmişini Kullanın**
   - Önceki komutlara göz atmak için yukarı ok tuşuna basın
   - Geriye dönük arama için `Ctrl+R` kullanın

4. **Takma Adlar Oluşturun**
   - Sık kullanılan komutları takma ad olarak kaydedin
   - Bunları `.bashrc` veya `.zshrc` dosyanıza ekleyin

### Sonuç

Bu 20 komut, Linux komut satırı kullanımının temelini oluşturur. Bunları anlamak ve ustalaşmak, Linux sistemleriyle çalışırken verimliliğinizi önemli ölçüde artıracaktır. Bu serinin bir sonraki bölümünde, sistem yönetimi ve otomasyon için daha gelişmiş komutları ve teknikleri inceleyeceğiz.

Bu komutlarda ustalaşmanın anahtarının pratik yapmak olduğunu unutmayın. Günlük çalışmalarınızda bunları düzenli olarak kullanmaya çalışın ve güvenli bir ortamda denemekten çekinmeyin.

Daha gelişmiş Linux komutlarını ele alacağımız 2. Bölüm için takipte kalın!