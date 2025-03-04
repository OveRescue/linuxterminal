## 1. Kullanıcılar ve Gruplar
Herhangi bir geleneksel işletim sisteminde kullanıcılar ve gruplar bulunur. Bunlar sadece erişim ve izinler için vardır. Bir işlemi çalıştırırken işlemin sahibi ister Jane ve ya Bob olsun, işlemin sahibi olarak çalışacaktır. Dosya erişimi ve sahipliği de izne bağlıdır. Jane'nin Bob'un belgelerini görmesini ve tam tersini istemezsiniz. 

Her kullanıcının, kullanıcıya özgü dosyalarının saklandığı kendi ana dizini vardır; bu genellikle /home/username dizininde bulunur, ancak farklı dağıtımlarda değişiklik gösterebilir.

Sistem kullanıcıları yönetmek için kullanıcı kimliklerini (UID) kullanır, kullanıcı adları kullanıcıları tanımlama ile ilişkilendirmenin kolay bir yoludur, ancak sistem kullanıcıları UID'leri ile tanımlar. Sistem ayrıca izinleri yönetmek için grupları kullanır, gruplar yalnızca o grup tarafından belirlenen izinlere sahip kullanıcı kümeleridir ve sistem tarafından grup kimliğiyle (GID) tanımlar.

Linux'ta sistemi kullanan normal insanlara ek olarak kullanıcılarda kullanıcılarda bulunacak. Bazen bu kullanıcılar, sistemin çalışır durumda kalmasını sağlamak için sürekli olarak süreçleri çalıştıran sistem yardımcı yazılımlardır. En önemli kullanıcılardan biri root veya superuser , root sistemdeki en güçlü kullanıcıdır, root herhangi bir dosyaya erişebilir, herhangi bir işlemi başlatabilir veya sonlandırabilir. Bu nedenle, her zaman root olarak çalışmak tehlikeli olabilir, potansiyel olarak sistem açısından kritik dosyaları kaldırabilirsiniz. Neyse ki, root erişimi gerekiyorsa ve bir kullanıcı root erişimine sahipse, sudo komutuyla root olarak bir komut çalıştırabilirler. Sudo komutu (superuser do), bir komutu root erişimiyle çalıştırmak için kullanılır. Bir kullanıcının root erişimini nasıl elde ettiğini daha sonraki dersimizde daha ayrıntılı olarak ele alacağız.

Devam edin ve /etc/shadow gibi korumalı bir dosyayı görüntülemeyi deneyin:

    $ cat /etc/shadow

İzin reddedildi hatası aldığınızı fark edin, izinlere şu şekilde bakın:

    $ ls -la /etc/shadow
    
    -rw-r----- 1 root shadow 1134 Dec 1 11:45 /etc/shadow

Henüz izinleri incelemedik, ancak burada olan şey, dosyanın sahibinin root olması ve içerikleri okumak için root erişimine veya shadow grubunun bir parçası olmanız gerekmesidir. Şimdi komutu sudo ile çalıştırın:

    $ sudo cat /etc/shadow

Artık dosyanın içeriğini görebileceksiniz!

Çeviren: Muhammed Cankan

## 2. root
Sudo komutunu kullanarak superuser erişimi elde etmenin bir yoluna baktık. Ayrıca su komutuyla superuser olarak da komut çalıştırabilirsiniz. Bu komut, kullanıcı adı belirtilmemişse "kullanıcıları değiştirecek" ve bir root shell açacaktır.  Şifreyi bildiğiniz sürece herhangi bir kullanıcının yerini almak için bu komutu kullanabilirsiniz.

    $ su

Bu yöntemi kullanmanın bazı dezavantajları da var: her şeyi Root'da çalıştırdığınızda kritik bir hata yapmak çok daha kolay, sistem yapılandırmalarını değiştirmek için kullandığınız komutların kayıtları olmayacak, vb. Temel olarak, komutları superuser olarak çalıştırmanız gerekiyorsa, sadece sudo'ya bağlı kalın. Artık süper kullanıcı olarak hangi komutları çalıştıracağınızı bildiğinize göre, şimdi soru şu: Bunu yapma yetkisine kimin sahip olduğunu nasıl bileceksiniz? Sistem her Joe Schmoe'nun süper kullanıcı olarak komut çalıştırmasına izin vermiyor, peki bunu nasıl biliyor? /etc/sudoers dosyası adında bir dosya var, bu dosya sudo çalıştırabilen kullanıcıları listeliyor. Bu dosyayı visudo komutuyla düzenleyebilirsiniz.

Çeviren: Muhammed Cankan

## 3./etc/passwd
Kullanıcı adlarının kullanıcılar için gerçek bir tanımlama olmadığını unutmayın. Sistem bir kullanıcıyı tanımlamak için bir kullanıcı kimliği (UID) kullanır. Hangi kullanıcıların hangi ID ile eşleştirildiğini öğrenmek için /etc/passwd dosyasına bakılmalıdır.

    $ cat /etc/passwd

Bu dosya size kullanıcıların bir listesini ve listelenen kullanıcılar hakkında ayrıntılı bilgileri gösterir. Örneğin, bu dosyadaki ilk satır büyük olasılıkla böyle görünecektir:  

    root:x:0:0:root:/root:/bin/bash

Her satır, tek bir kullanıcıya ait bilgileri içerir ve genellikle ilk satırda root kullanıcısı bulunur. Kullanıcı hakkında ek bilgiler içeren birçok alan, iki nokta üst üste işareti (:) ile ayrılmıştır. Şimdi bu alanları inceleyelim:

1. Kullanıcı adı
 
 2. Kullanıcının parolası – Gerçek parola bu dosyada saklanmaz,
    genellikle /etc/shadow dosyasında bulunur. Eğer bu alanda "x" varsa, parola /etc/shadow dosyasında saklanıyor demektir. "*" sembolü, kullanıcının giriş yapma izni olmadığını gösterir. Alan boşsa, kullanıcının parolası olmadığını belirtir.

 3. Kullanıcı Kimlik Numarası (UID) – Root kullanıcısının UID’si 0
    olarak tanımlıdır.

 4. Grup Kimlik Numarası (GID)

 5. GECOS alanı – Kullanıcı veya hesap hakkında yorum bırakmak için
    kullanılır. Örneğin, kullanıcının gerçek adı veya telefon numarası
    gibi bilgiler buraya yazılabilir. Alanlar virgül işareti (,) ile
    ayrılır.

 6. Kullanıcının ana dizini

 7. Kullanıcının kabuğu – Genellikle kullanıcıların varsayılan kabuğu
    bash olarak ayarlanmıştır.

Normalde kullanıcı ayarları sayfasında yalnızca insan kullanıcıları görmeyi beklersiniz. Ancak, /etc/passwd dosyasında sistem tarafından kullanılan diğer kullanıcıların da bulunduğunu fark edebilirsiniz. Unutmayın, kullanıcılar esas olarak farklı izinlerle işlemleri çalıştırmak için sistemde bulunur. Bazen, belirli işlemleri önceden tanımlanmış izinlerle çalıştırmak isteyebiliriz. Örneğin, daemon kullanıcısı, sistemde arka planda çalışan süreçler için kullanılır.

  

Ayrıca, /etc/passwd dosyasını *vipw* aracıyla manuel olarak düzenleyebilirsiniz. Ancak, kullanıcı ekleme ve düzenleme gibi işlemler için useradd ve userdel gibi komutların kullanılması daha güvenli ve önerilen bir yöntemdir. Bu komutları ilerleyen derslerde ele alacağız.

  -Murathan ATEŞ tarafından çevirildi.
  -Yunus ARSLAN tarafından kontrol edildi.

# 4./etc/shadow

etc/shadow dosyası kullanıcı kimlik doğrulaması hakkında bilgi depolamak için kullanılır. Süper kullanıcı okuma izinleri gerektirir.

    $ sudo cat /etc/shadow
    root:MyEPTEa$6Nonsense:15000:0:99999:7:::

/etc/shadow dosyasının içeriği, /etc/passwd dosyasına oldukça benzerdir. Ancak, parola alanında şifrelenmiş bir parola bulunur. Alanlar aşağıdaki gibi iki nokta üst üste (:) ile ayrılmıştır:

 1. Kullanıcı adı
 2. Şifrelenmiş parola
 3. Son parola değişiklik tarihi – 1 Ocak 1970’ten itibaren geçen gün
    sayısı olarak ifade edilir. Eğer bu değer 0 ise, kullanıcı bir
    sonraki girişinde parolasını değiştirmelidir.
 4. Minimum parola yaşı – Kullanıcının parolasını tekrar
    değiştirebilmesi için beklemesi gereken gün sayısı.
 5. Maksimum parola yaşı – Kullanıcının parolasını değiştirmesi gereken
    maksimum gün sayısı.
 6. Parola uyarı süresi – Parolanın süresi dolmadan önce kullanıcıya
    gösterilecek uyarı gün sayısı.
 7. Parola geçersiz kalma süresi – Parola süresi dolduktan sonra,
    kullanıcının eski parolasıyla giriş yapabileceği gün sayısı.
 8. Hesap geçerlilik süresi – Kullanıcının giriş yapamayacağı tarih.
 9. Gelecek kullanım için ayrılmış alan

Günümüzde çoğu Linux dağıtımında kullanıcı kimlik doğrulaması yalnızca /etc/shadow dosyasına bağlı değildir. Bunun yerine PAM (Pluggable Authentication Modules) gibi ek mekanizmalar kimlik doğrulamayı yönetmektedir.

-Murathan ATEŞ tarafından çevirildi.
-Yunus ARSLAN tarafından kontrol edildi.

  # 5. /etc/group
Kullanıcı yönetiminde kullanılan bir diğer dosya */etc/group* dosyasıdır. Bu dosya, farklı izinlere sahip farklı grupların oluşturulmasına olanak tanır.

    $ cat /etc/group
    
    root:*:0:pete

*/etc/password* dosyasına çok benzeyen */etc/group* dosyasındaki alanlar şu şekildedir:

1.  *Grup Adı*  
    Grup için kullanılan tanımlayıcı isimdir.
    
2.  *Grup Parolası*  
    Grup parolası ayarlamaya genellikle gerek yoktur, çünkü sudo gibi yükseltilmiş ayrıcalıklar standart olarak kullanılır. Varsayılan değer olarak "*" yerleştirilir.
    
3.  *Grup Kimliği (GID)*  
    Grubu tanımlamak için kullanılan benzersiz bir numaradır.
    
4.  *Kullanıcı Listesi*  
    Belirli bir gruba eklemek istediğiniz kullanıcıları manuel olarak belirtebilirsiniz.

-Yunus ARSLAN tarafından çevirildi.

# 6. Kullanıcı Yönetim Araçları
Çoğu kurumsal ortamda, kullanıcıları, hesapları ve parolaları yönetmek için yönetim sistemleri kullanılmaktadır. Ancak, tek bir bilgisayarda kullanıcıları yönetmek için kullanışlı komutlar mevcuttur.

*Kullanıcı Ekleme*

*adduser* veya *useradd* komutlarını kullanabilirsiniz. *adduser* komutu, ev dizini oluşturma gibi daha fazla yardımcı özelliğe sahiptir. Yeni kullanıcı eklemek için kullanılan yapılandırma dosyaları mevcuttur ve bu dosyalar, varsayılan bir kullanıcıya tahsis etmek istediğiniz öğelere göre özelleştirilebilir.

    $ sudo useradd bob
Yukarıdaki komutun, */etc/passwd* dosyasında bob için bir giriş oluşturduğunu, varsayılan grupları ayarladığını ve */etc/shadow* dosyasına bir giriş eklediğini göreceksiniz.

*Kullanıcı Silme*

Bir kullanıcıyı silmek için *userdel* komutunu kullanabilirsiniz.

    $ sudo userdel bob
Bu komut, temelde *useradd* komutunun yaptığı dosya değişikliklerini geri almaya çalışır.

*Parola Değiştirme*

    $ passwd bob
Bu, kendi parolanızı veya başka bir kullanıcının parolasını (eğer root yetkisine sahipseniz) değiştirmenize olanak tanır.

-Yunus ARSLAN tarafından çevirildi.
