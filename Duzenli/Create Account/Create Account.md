# Giriş

Bir saldırganın bir ortama ilk erişimi elde etmesi çoğu zaman olayın sadece başlangıç kısmıdır. Asıl kritik nokta ise bu erişimin nasıl **kalıcı** hale getirildiğidir. Çünkü gerçek saldırı senaryolarında amaç çoğu zaman yalnızca bir kez sisteme girmek değil, mümkün olduğunca uzun süre içeride kalabilmek, gerektiğinde yeniden erişim sağlayabilmek ve bunu mümkün olduğunca düşük görünürlükle sürdürebilmektir.  Bu noktada **MITRE ATT&CK T1136 – Create Account** tekniği, **Persistence** taktiği altında oldukça önemli bir yere sahiptir. [1]

# MITRE ATT&CK Create Account Tekniği Nedir?

MITRE ATT&CK’e göre **Create Account**, adversary’nin victim system üzerinde erişimini sürdürmek amacıyla yeni bir account oluşturmasıdır. Bu account bir **local account**, **domain account** ya da bir **cloud tenant** içerisindeki account olabilir.  Yeterli privilege seviyesine sahip bir saldırgan için bu yöntem, sisteme her seferinde ayrı bir malware ya da persistent remote access tool yerleştirmeden, doğrudan **credential-based secondary access** kanalı oluşturma imkanı sağlar. Bu da tekniği özellikle **Persistence** bağlamında kritik hale getirir. [1] 

Bu tekniğin önemli tarafı, persistence mekanizmasının doğrudan kimlik ve yetki modeli üzerinden kurulmasıdır. Saldırgan yeni bir hesap oluşturduğunda, yalnızca sisteme bir kullanıcı eklemiş olmaz; aynı zamanda ileride tekrar kullanabileceği, gerektiğinde privilege genişletebileceği ve başka sistemlere erişim için değerlendirebileceği meşru görünümlü bir erişim kanalı oluşturmuş olur.

Bana göre bu tekniği önemli yapan temel nokta da tam olarak budur: burada persistence, çoğu savunma ekibinin ilk aşamada aradığı klasik “malware izi” üzerinden değil, çoğu zaman meşru yönetim işlemlerine benzeyen bir davranış üzerinden sağlanır. Yani yeni bir account oluşturulması, ilk bakışta bir scheduled task, suspicious service ya da registry-based persistence kadar dikkat çekmeyebilir. Ancak operasyonel açıdan bakıldığında bu yöntem çok daha temiz, daha sessiz ve bazı durumlarda daha sürdürülebilir bir erişim modeli sağlar.  Bu da **Create Account** tekniğini, görünürde basit ama operasyonel etkisi yüksek bir persistence yöntemi haline getirir.

birçok ortamda hesap oluşturma işlemleri zaten BT operasyonlarının doğal bir parçasıdır. Saldırgan tam da bu normalleşmiş davranış alanını kullanarak kendi persistence mekanizmasını gizlemeye çalışır. 

MITRE ATT&CK bu ana tekniği üç alt başlık altında ele almaktadır:

1. **T1136.001 – Create Account: Local Account**
2. **T1136.002 – Create Account: Domain Account**
3. **T1136.003 – Create Account: Cloud Account** 

## Tekniğin anlaşılması için alt teknikler kritik önemdedir

Create Account tekniğini gerçekten anlamak için onu yalnızca genel tanımıyla değil, alt teknikleriyle birlikte ele almak gerekir. Çünkü **Local Account**, **Domain Account** ve **Cloud Account** oluşturma davranışları aynı ana mantığa dayansa da, operasyonel etkileri, görünürlük seviyeleri ve savunma zorlukları birbirinden farklıdır. [2]  [3]  [4]



# 1. T1136.001 – Create Account: Local Account

## MITRE ATT&CK açıklaması

MITRE ATT&CK’e göre **Create Account: Local Account**, adversary’nin victim system üzerinde yerel kullanım için bir **local account** oluşturmasıdır. Bu hesaplar genellikle tek bir sistem veya servis üzerinde geçerlidir ve kullanıcı, remote support, service ya da local administration amaçlarıyla yapılandırılmış olabilir. Ancak adversary yeterli yetkiye ulaştığında bu meşru hesap mantığını persistence amacıyla kötüye kullanabilir. 
Windows tarafında `net user /add`, Linux tarafında `useradd`, macOS tarafında `dscl -create`, network devices üzerinde yaygın CLI komutları, **ESXi** üzerinde `esxcli system account add`, hatta bazı durumlarda **Kubernetes** tarafında `kubectl` gibi araçlar bu davranışın teknik karşılığı olabilir. MITRE ayrıca zafiyetli firewall management console’lar üzerinde yeni **super-admin** hesapların oluşturulmasının da bu alt teknik kapsamına girdiğini belirtmektedir. [2]


Bu alt tekniğin operasyonel değeri, saldırgana sistem üzerinde doğrudan ikinci bir erişim yolu sağlamasıdır. Özellikle endpoint üzerinde local administrator seviyesinde yeni bir hesap oluşturulması, credential rotation yapılsa bile erişimin belirli bir host üzerinde korunmasına imkan verebilir. Bu yüzden local account creation yalnızca bir kullanıcı yönetimi olayı değil, host seviyesinde kalıcılığın kurulması anlamına gelir. [2]


## Gerçek hayat kullanım örneği

MITRE procedure examples kısmında bu alt tekniğin birçok threat actor ve malware tarafından kullanıldığı görülmektedir. Örneğin **DarkGate**, `net user` komutlarını kullanarak `SafeMode` isimli bir local user account oluşturmuştur. Benzer şekilde **APT5**’in short-cycle credential rotation uygulanan ortamlarda erişimi korumak için **Local Administrator** hesapları oluşturduğu, **Wizard Spider**’ın compromised network içinde local administrator accounts yarattığı ve **Fox Kitten**’ın administrator privilege’lara sahip local users oluşturduğu raporlanmıştır. [2]

Bu örnekler bize şunu gösterir: local account creation çoğu zaman yalnızca “sisteme bir kullanıcı eklemek” değildir; ortamın yönetimsel görünümüne uyumlu, dikkat çekmeyen, fakat gerektiğinde yüksek etkili olacak bir persistence nesnesi üretmektir. Özellikle “support”, “help”, “DefaultAccount” gibi normalleşmiş isimlendirmeler, savunma ekiplerinin dikkatini azaltmak için kullanılabilir. [2]



## MITRE ATT&CK’e göre detection nasıl yapılmalı?

MITRE’nin detection strategy yaklaşımına göre local account creation yalnızca account creation event’inin görülmesiyle değil, bu olayı üreten **process lineage** ile birlikte değerlendirilmelidir. Windows tarafında temel izleme mantığı, **Event ID 4720** ile birlikte `net.exe`, `net1.exe`, `powershell.exe`, `wmic.exe` gibi built-in araçların process creation kayıtlarını ilişkilendirmektir. Linux tarafında `useradd`, `adduser` veya `/etc/passwd` ile `/etc/shadow` değişiklikleri; macOS tarafında `dscl -create`; ESXi tarafında `esxcli`; network device’larda ise **AAA logging**, CLI history ya da API telemetry izlenmelidir. [2]

Savunma açısından burada önemli olan nokta, “hesap oluşturuldu” bilgisinin tek başına yeterli olmamasıdır. Asıl sorulması gereken sorular şunlardır:  
* Bu hesap hangi araçla oluşturuldu?  
* İşlem hangi kullanıcı bağlamında çalıştı?  
* Oluşturulan hesap hangi yetki seviyesine taşındı?  
* Bu davranış ilgili host için normal mi, yoksa anormal mi? [2]

## Mitigation

MITRE’ye göre bu alt teknik için temel mitigation başlıkları **Multi-factor Authentication (M1032)** ve **Privileged Account Management (M1026)**’dır. Özellikle local administrator account’ların günlük işlerde kullanılmaması, hesap oluşturma yetkisine sahip kullanıcı sayısının azaltılması ve local privileged access’in sıkı şekilde sınırlandırılması önemlidir. Bu yaklaşım, saldırganın ele geçirdiği bir hesabı yeni bir local persistence nesnesine dönüştürmesini zorlaştırır. [2]



# 2. T1136.002 – Create Account: Domain Account

MITRE ATT&CK’e göre **Create Account: Domain Account**, adversary’nin **Active Directory Domain Services** tarafından yönetilen bir ortam içinde yeni bir **domain account** oluşturmasıdır. Domain accounts; user, administrator veya service account şeklinde olabilir ve domain’e bağlı sistemler ile servisler genelinde erişim ve yetki sağlayabilir. Windows ortamında yeterli privilege elde eden bir saldırgan, `net user /add /domain` komutuyla yeni bir domain user oluşturabilir. [3]

Bu alt tekniğin local account oluşturmadan farkı, etkisinin tek bir host ile sınırlı olmamasıdır. Domain account oluşturulduğunda saldırgan artık yalnızca bir endpoint üzerinde değil, kurumsal kimlik altyapısının içinde yer alan bir nesne üretmiş olur. Bu durum persistence ile birlikte **Privilege Escalation** ve **Lateral Movement** için de güçlü bir zemin oluşturur. [3]

## Gerçek hayat kullanım örneği

MITRE procedure examples kısmında **Sandworm Team**’in 2015 Ukraine Electric Power Attack sırasında privileged **domain accounts** oluşturduğu, 2016 Ukraine Electric Power Attack sürecinde ise `"admin"` ve `"система"` isimli iki yeni hesabı yaratıp bunlara yeni privilege’lar delege ettiği belirtilmektedir. Aynı şekilde **GALLIUM**, **HAFNIUM**, **BlackByte**, **Medusa Group** ve **Wizard Spider** gibi aktörlerin de victim Active Directory environment içinde yeni domain accounts oluşturduğu raporlanmıştır. [3]

Bu gerçek hayat örnekleri, domain account creation davranışının basit bir user provisioning süreci değil, merkezi kimlik mimarisi üzerinde kalıcı bir iz bırakma girişimi olduğunu göstermektedir. Bir saldırgan domain içine kendi oluşturduğu bir account yerleştirdiğinde, normal kullanıcı davranışına daha çok benzeyen, yönetilebilir ve yeniden kullanılabilir bir erişim nesnesi elde eder. [3]


## MITRE ATT&CK’e göre detection nasıl yapılmalı?

MITRE’nin detection strategy yaklaşımına göre domain account creation, özellikle bir **domain controller** veya domain management yetkisine sahip host üzerinde gerçekleşen suspicious process activity ile **Event ID 4720** ilişkilendirilerek izlenmelidir. Yani detection mantığı yalnızca “yeni hesap oluşturuldu” şeklinde kurulmaz; bunun yerine “bu hesap hangi yönetim aracının çalışmasından sonra, hangi host bağlamında, hangi actor tarafından oluşturuldu?” sorularına cevap aranır. [3]

MITRE ayrıca Windows dışı ya da karma ortamlarda `realmd`, `samba-tool`, `ldapmodify` gibi araçların da domain account creation için kullanılabileceğini belirtmektedir. Bu nedenle yalnızca klasik Windows komutlarına odaklanmak yeterli değildir. AD ile ilişkili LDAP değişiklikleri, yönetim script’leri ve daha önce görülmeyen bir hesabın hemen ardından başlayan authentication activity’leri de detection kapsamına alınmalıdır. [3]


## Mitigation

MITRE’ye göre bu alt teknik için başlıca mitigation başlıkları şunlardır: **Multi-factor Authentication (M1032)**, **Network Segmentation (M1030)**, **Operating System Configuration (M1028)** ve **Privileged Account Management (M1026)**. Özellikle domain controllers’a erişimin sınırlandırılması, account creation yetkilerinin yalnızca belirli yönetim gruplarıyla kısıtlanması ve yüksek yetkili domain administrator accounts’un günlük kullanım için kullanılmaması, bu tekniğin kötüye kullanılmasını zorlaştırır. [3]

# 3. T1136.003 – Create Account: Cloud Account

## MITRE ATT&CK açıklaması

MITRE ATT&CK’e göre **Create Account: Cloud Account**, adversary’nin bir **cloud environment**, **Identity Provider**, **Office Suite** veya **SaaS** platformu içinde yeni bir hesap oluşturmasıdır. Bu hesaplar klasik kullanıcı hesapları olabileceği gibi servisle ilişkili kimlikler de olabilir. MITRE özellikle **Azure** tarafında **service principals** ve **managed identities**, **GCP** tarafında **service accounts**, **AWS** tarafında ise kaynakların belirli **roles**’ü assume etmesine dayalı permission modellerine dikkat çekmektedir. [4]

Bu alt tekniğin modern saldırı yüzeyindeki önemi oldukça büyüktür. Çünkü günümüzde persistence yalnızca endpoint veya on-prem Active Directory seviyesinde kurulmamaktadır. Bir adversary, dar yetkili ama görünürlüğü düşük bir cloud identity oluşturarak environment içinde uzun vadeli ve yeniden kullanılabilir bir erişim modeli yaratabilir. Ayrıca MITRE’nin de belirttiği gibi, oluşturulan bu hesaplar daha sonra **Additional Cloud Credentials** eklenerek ya da **Additional Cloud Roles** atanarak daha güçlü hale getirilebilir. [4]


## Gerçek hayat kullanım örneği

MITRE procedure examples bölümünde **AADInternals** aracının yeni **Azure AD users** oluşturabildiği, **APT29**’un Azure AD üzerinden yeni kullanıcılar yaratabildiği ve **LAPSUS$**’ın hedef organizasyonun cloud instance’larında **global admin accounts** oluşturduğu belirtilmektedir. [4]

Bu örnekler cloud account creation davranışının yalnızca teorik bir API işlemi olmadığını, gerçek saldırı kampanyalarında persistence ve privilege expansion amacıyla kullanıldığını göstermektedir. Özellikle cloud tarafta oluşturulan bir hesabın hemen ardından rol atamaları, credential üretimi veya servis erişimleri ekleniyorsa, bu artık sıradan bir provisioning olayı olmaktan çıkar ve saldırı davranışına yaklaşır. [4]


## MITRE ATT&CK’e göre detection nasıl yapılmalı?

MITRE’nin detection strategy yaklaşımına göre cloud account creation davranışı, **Identity Provider APIs**, admin portal aktiviteleri, **CreateUser** benzeri işlemler, role attachment, credential generation ve bunu izleyen authentication events ile birlikte değerlendirilmelidir. Detection burada tek bir event’e indirgenmez; aksine bir **temporal chain** şeklinde düşünülmelidir. Örneğin yeni kullanıcı oluşturulması, hemen sonrasında bu hesaba rol atanması, ardından login activity görülmesi güçlü bir risk göstergesidir. [4]

MITRE ayrıca **Add User** aktivitelerinin suspicious IP’lerden, automation sources’tan, anormal zaman dilimlerinden veya foreign IP ranges’ten gelip gelmediğinin izlenmesini önermektedir. Aynı şekilde **SaaS** uygulamalarında lifecycle provisioning activity’leri, **M365** veya **Google Workspace** ortamlarında user, service account veya guest account creation sonrası gelen privilege artırımları ve token generation davranışları da önemlidir. [4]

## Mitigation

MITRE’ye göre bu alt teknik için temel mitigation’lar **Multi-factor Authentication (M1032)**, **Network Segmentation (M1030)** ve **Privileged Account Management (M1026)** başlıkları altında toplanmaktadır. Özellikle cloud tarafında hesap oluşturma yetkisi olan admin rollerinin minimize edilmesi, privileged identities’in günlük operasyonlarda kullanılmaması, kritik servislerin ayrı ağ segmentleri veya ayrı yönetim sınırları içinde tutulması ve yeni oluşturulan hesapların otomatik governance kontrollerinden geçirilmesi önemlidir. [4]



## Diğer Gerçek saldırı örnekleri bize ne söylüyor?

MITRE ATT&CK içerisinde paylaşılan procedure examples, bu tekniğin teorik olmaktan çok pratikte karşılığı olan bir davranış olduğunu gösteriyor. Örneğin:

- **Sandworm Team**, 2016 Ukraine Electric Power Attack sırasında bir **SQL Server** üzerinde `sp_addlinkedsrvlogin` kullanarak login eklemiştir. [1]
- **Indrik Spider**, `wmic.exe` aracılığıyla sisteme yeni bir kullanıcı eklemiştir. [1]
- **LockBit 2.0**, persistence amacıyla `"a"` gibi oldukça basit isimlerle account oluştururken gözlemlenmiştir. [1]
- **Salt Typhoon**, compromise ettiği network devices üzerinde `/etc/shadow` ve `/etc/passwd` dosyalarını değiştirerek Linux seviyesinde kullanıcılar oluşturmuştur. [1]
- **Scattered Spider** ise compromised organization içerisinde yeni user identities oluşturmuştur. [1]

Bu örnekler bana göre çok önemli bir noktayı ortaya koyuyor: **Create Account**, tek bir saldırı grubuna ya da tek bir işletim sistemine özgü bir davranış değildir. Hem ransomware operasyonlarında hem de state-linked ya da gelişmiş tehdit aktörlerinin faaliyetlerinde benzer mantıkla kullanılabilmektedir. Bu da savunma tarafında account creation olaylarının “rutin yönetimsel işlem” gözüyle otomatik biçimde normalleştirilmemesi gerektiğini gösterir.

## Sonuç 

Benim değerlendirmeme göre bu tekniğin en tehlikeli yanı, karmaşık olmaya ihtiyaç duymamasıdır. Saldırgan burada çoğu zaman exploit çalıştırmak, yeni bir malware ailesi bırakmak ya da çok gürültülü bir persistence mekanizması kurmak zorunda değildir. Bazen yalnızca yeni bir hesap oluşturmak, daha sonra da bu hesabı yetki, rol veya credential düzeyinde güçlendirmek yeterlidir. Bu da Create Account tekniğini, özellikle olgun görünmeyen izleme mimarilerinde kolayca gözden kaçabilecek ama ciddi sonuçlar üretebilecek bir saldırı davranışı haline getirir. 

Savunma tarafında ise en doğru yaklaşım, yalnızca “hesap oluşturuldu mu?” sorusunu sormak değildir. Bunun yerine şu sorular birlikte sorulmalıdır:  
* Bu hesap neden oluşturuldu?  
* Kim tarafından oluşturuldu?  
* Hangi araç veya API ile oluşturuldu?  
* Hemen ardından hangi yetkiler verildi?  
* Bu davranış gerçekten organizasyonun normal idari modeline uyuyor mu?

Bu sorulara birlikte cevap verebilen bir detection yaklaşımı kurulduğunda, Create Account tekniği yalnızca geçmişe dönük incelenen bir event olmaktan çıkar; aktif olarak izlenen ve erken aşamada müdahale edilebilen bir persistence göstergesine dönüşür.


## Event ID 4720 
----
Windows ortamında bu davranışın ilk görünür olduğu kayıt genellikle **Event ID 4720**’dir. Bu event, sistem üzerinde yeni bir kullanıcı hesabı oluşturulduğunu gösterdiği için, local account creation davranışının tespitinde temel başlangıç noktalarından biridir. Bu nedenle, söz konusu aktivitenin Windows logları üzerindeki ilk yansımasını anlamak açısından **Event ID 4720** kaydı kritik bir öneme sahiptir.

![](Pasted%20image%2020260406201708.png)

**Burada kısa olarak dikkat edilmesi gereken başlıca field’lar şunlardır:**

- **SubjectUserName** → hesabı oluşturan kullanıcı
- **SubjectDomainName** → işlemi gerçekleştiren kullanıcının bulunduğu domain veya sistem
- **TargetUserName** → oluşturulan yeni hesap
- **TargetSid** → oluşturulan hesabın SID bilgisi
- **SubjectLogonId** → işlemi yapan oturumu ilişkilendirmek için önemli alan
- **Computer** / **Hostname** → olayın gerçekleştiği sistem
- **TimeCreated** / **_time** → olayın zamanı

**Kısa not:**  
Eğer oluşturulan hesap bir **domain account** ise, ilgili event çoğunlukla **Domain Controller** üzerinde görülecektir. Eğer oluşturulan hesap bir **local account** ise, log ilgili yerel sistem üzerinde oluşacaktır.
## Event ID 4726
Windows ortamında bir kullanıcı hesabının silindiğini gösteren en temel kayıt genellikle **Event ID 4726**’dır. Bu event, sistem üzerinde mevcut bir kullanıcı hesabının kaldırıldığını gösterir ve özellikle yetkisiz hesap silme, iz temizleme veya kritik hesapların kaldırılması gibi senaryoların tespitinde önemli bir veri kaynağıdır. Bu nedenle, bu davranışın teknik olarak nasıl iz bırakttığını göstermek adına ilgili **Event ID 4726** kaydının incelenmesi önemlidir.

![](Pasted%20image%2020260406215940.png)

**Burada kısa olarak dikkat edilmesi gereken başlıca field’lar şunlardır:**

- **SubjectUserName** → kullanıcı hesabını silen hesap
- **SubjectDomainName** → işlemi gerçekleştiren kullanıcının bulunduğu domain veya sistem
- **TargetUserName** → silinen kullanıcı hesabı
- **TargetSid** → silinen hesabın SID bilgisi
- **SubjectLogonId** → işlemi yapan oturumu diğer event’lerle ilişkilendirmek için önemli alan
- **PrivilegeList** → işlem sırasında kullanılan privilege bilgisi
- **Computer** / **Hostname** → event’in loglandığı sistem
- **TimeCreated** / **_time** → olayın gerçekleştiği zaman

**Kısa not:**  
Bu event, **domain controller**, **member server** ve **workstation** üzerinde üretilebilir. Eğer silinen hesap bir **domain user account** ise, log çoğunlukla **Domain Controller** üzerinde görülür. Eğer silinen hesap bir **local user account** ise, log ilgili yerel sistem üzerinde oluşur.


### Loglama için Gerekli Audit Policy Yapılandırması
----
Windows ortamında **Event ID 4720** ve **Event ID 4726** kayıtlarının üretilebilmesi için ilgili sistemlerde **Audit User Account Management** audit alt kategorisinin etkin olması gerekir. Bu alt kategori, kullanıcı hesabı oluşturma ve silme dahil olmak üzere user account management işlemlerini denetler. Temel audit policy ile benzer ayarlar yapılabilse de, Microsoft daha seçici ve tutarlı bir yapı için **Advanced Audit Policy Configuration** kullanılmasını önerir; ayrıca basic ve advanced audit policy ayarlarının birlikte kullanılmaması gerektiğini belirtir. (https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/audit-user-account-management)

**Domain ortamında GPO ile yapılandırma adımları**

1. **Group Policy Management Console (GPMC)** açılır ve bu ayarın uygulanacağı sistemler için uygun GPO seçilir ya da yeni bir GPO oluşturulur. Audit policy ayarları domain, site veya OU seviyesinde GPMC üzerinden yönetilebilir.
2. GPO içinde şu yol izlenir:  
    **Computer Configuration → Policies → Windows Settings → Security Settings → Advanced Audit Policy Configuration → Audit Policies → Account Management → Audit User Account Management**. Bu alt kategori, kullanıcı hesabı oluşturma ve silme dahil user account management olaylarını üretir.
3. **Audit User Account Management** ayarı açılır ve en azından **Success** işaretlenir. 4720 ve 4726 gibi başarılı hesap oluşturma/silme olaylarının görülebilmesi için pratikte gerekli olan seçenek budur. Microsoft bu alt kategorideki event hacmini **low** olarak tanımlar; yani seçici etkinleştirme açısından makul bir ayardır.
4. Advanced audit policy kullanılıyorsa, şu ayarın da etkin olması gerekir:  
    **Computer Configuration → Windows Settings → Security Settings → Local Policies → Security Options → Audit: Force audit policy subcategory settings (Windows Vista or later) to override audit policy category settings**. Microsoft bu ayarın etkin bırakılmasını ve basic audit policy ile çakışmayı önlemek için kullanılmasını önerir.
5. GPO uygun hedefe bağlanır. Eğer amacın **domain user account** oluşturma/silme olaylarını izlemekse, bu ayarın özellikle **Domain Controllers OU** kapsamındaki sistemlere uygulanması en doğru yaklaşımdır. Çünkü 4720 ve 4726 event’leri domain controller, member server ve workstation üzerinde üretilebilse de, domain user işlemlerinde ana kayıt çoğunlukla domain controller tarafında oluşur; local user işlemleri ise ilgili yerel sistemde görülür.
6. Policy uygulandıktan sonra hedef sistemlerde Event Viewer üzerinden **Security** log’unda 4720 ve 4726 event’leri doğrulanır. İstenirse `auditpol.exe` ile audit policy’nin etkin durumu da kontrol edilebilir; Microsoft bu aracın audit policy görüntüleme ve yönetiminde kullanılabildiğini belirtir.


# SIEM VE DETECTİON KURALLARI
---
### Detection: Windows Create Local Account

Bu kural, **Windows Security Event ID 4720** kayıtları üzerinden bir sistem üzerinde yeni bir kullanıcı hesabı oluşturulmasını tespit etmeyi amaçlar. Yeni bir local account oluşturulması, özellikle saldırganların sisteme kalıcılık sağlama, ek erişim noktası oluşturma veya daha sonraki aşamalarda yetki yükseltme amacıyla başvurabildiği bir davranıştır.

SOC perspektifinden bakıldığında bu olay önemlidir; çünkü ele geçirilmiş bir sistemde saldırgan, fark edilmeden yeniden erişim sağlayabilmek için kendisine yeni bir hesap açabilir. Özellikle sunucu sistemlerde, yönetimsel olmayan saatlerde veya beklenmeyen kullanıcılar tarafından gerçekleştirilen hesap oluşturma işlemleri şüpheli kabul edilmelidir.

Ancak burada önemli bir nokta vardır: **Event ID 4720 yalnızca local account creation için değil, Domain Controller üzerinde oluştuğunda domain user creation için de görülebilir.** Bu nedenle alarm değerlendirilirken olayın hangi sistemde oluştuğu mutlaka kontrol edilmelidir. Workstation veya member server üzerinde görülmesi local account açısından daha anlamlıdır; Domain Controller üzerinde görülmesi ise domain account creation ile ilişkili olabilir.

![](Ekran%20görüntüsü%202026-04-06%20181407.png)

![](Ekran%20görüntüsü%202026-04-06%20181500.png)
 

#### False Positive Durumları

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Sistem yöneticileri, help desk ekipleri, otomasyon süreçleri veya kurulum senaryoları kapsamında yeni kullanıcı hesapları oluşturulabilir. Ayrıca **Domain Controller** üzerinde oluşan 4720 event’leri, local account değil **domain account creation** anlamına gelebilir; bu nedenle yanlış yorumlamamak gerekir.

Bununla birlikte workstation veya member server üzerinde beklenmeyen bir kullanıcı tarafından yeni hesap oluşturulması normal kullanıcı davranışı değildir. Bu nedenle alarm oluştuğunda hesabı oluşturan kullanıcının rolü, işlemin gerçekleştiği sistem, oluşturulan hesabın daha sonra hangi gruplara eklendiği ve olayın planlı bir yönetim faaliyeti olup olmadığı mutlaka doğrulanmalıdır.


SPL Kaynağı => https://research.splunk.com/endpoint/3fb2e8e3-7bc0-4567-9722-c5ab9f8595eb/

---

### Detection: Short Lived Windows Accounts

Bu kural, bir Windows hesabının **kısa süre içinde oluşturulup ardından silinmesini** tespit etmeyi amaçlar. Özellikle **Event ID 4720** ile hesap oluşturma ve **Event ID 4726** ile hesap silme olayları birlikte değerlendirildiğinde, aynı hesabın yaklaşık **1 saat içinde** yaşam döngüsünü tamamlaması şüpheli bir davranış olarak ele alınabilir.

Bu davranış güvenlik açısından önemlidir çünkü saldırganlar bazen kalıcı görünmeden işlem yapmak, kısa süreli erişim sağlamak, belirli bir görevi yerine getirdikten sonra izlerini azaltmak veya savunma ekiplerinin dikkatinden kaçmak amacıyla geçici hesaplar oluşturabilir. Hesabın hızlı şekilde silinmesi, olayın yönetimsel bir işlem değil saldırı zincirinin parçası olabileceğini düşündürür.

Bu nedenle bu kural, yalnızca hesap oluşturmayı değil, aynı hesabın kısa zaman içinde ortadan kaldırılmasını da ilişkilendirerek inceler. Doğrulanması hâlinde bu davranış, savunmadan kaçınma, yetkisiz erişim veya lateral movement hazırlığı ile ilişkili olabilir.

```
index=winsec (EventCode=4720 OR EventCode=4726)
| eval account_name=coalesce(SamAccountName, Target_User_Name, TargetUserName, user_name, TargetSid)
| eval actor=coalesce(SubjectUserName, src_user, src_user_name, user, SubjectUserSid)
| eval dest=coalesce(Computer, ComputerName, host)
| where isnotnull(account_name)
| stats count
    min(eval(if(EventCode=4720,_time,null()))) as firstTime
    min(eval(if(EventCode=4726,_time,null()))) as lastTime
    values(eval(if(EventCode=4720,actor,null()))) as create_actor
    values(eval(if(EventCode=4726,actor,null()))) as delete_actor
    values(eval(if(EventCode=4720,dest,null()))) as create_dest
    values(eval(if(EventCode=4726,dest,null()))) as delete_dest
    values(eval(if(EventCode=4720,EventCode,null()))) as create_result_id
    values(eval(if(EventCode=4726,EventCode,null()))) as delete_result_id
    by account_name
| where isnotnull(firstTime) AND isnotnull(lastTime) AND lastTime>=firstTime AND (lastTime-firstTime)<=86400
| eval firstTime=strftime(firstTime,"%Y-%m-%d %H:%M:%S")
| eval lastTime=strftime(lastTime,"%Y-%m-%d %H:%M:%S")
| rename account_name as user
| table firstTime lastTime count user create_actor delete_actor create_dest delete_dest create_result_id delete_result_id
| sort - lastTime
```
![](Pasted%20image%2020260406212656.png)

![](Pasted%20image%2020260406212816.png)

#### False Positive Durumları

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Sistem yöneticileri test amacıyla hesap açıp kısa süre sonra silebilir, yanlış oluşturulan hesaplar geri alınabilir veya otomasyon süreçleri geçici hesap üretip temizleyebilir. Laboratuvar ortamlarında da benzer davranışlar doğal olarak görülebilir.

Bununla birlikte bir hesabın kısa süre içinde oluşturulup silinmesi üretim ortamında sık beklenen bir davranış değildir. Bu nedenle alarm oluştuğunda hesabı oluşturan kullanıcının kim olduğu, işlemin hangi sistemde gerçekleştiği, hesabın bu kısa süre içinde hangi gruplara eklendiği ve olayın planlı bir yönetim faaliyeti kapsamında olup olmadığı mutlaka doğrulanmalıdır.

SPL Kaynağı => https://research.splunk.com/endpoint/b25f6f62-0782-43c1-b403-083231ffd97d/ 

----

#### Kısa Süre İçinde Yüksek Sayıda Windows Kullanıcısı Oluşturulması

Bu kural, kısa bir zaman aralığında aynı kullanıcı veya aynı sistem bağlamında birden fazla Windows kullanıcı hesabının oluşturulmasını tespit etmek amacıyla hazırlanmıştır. Kural, Windows Security loglarında yer alan **Event ID 4720** kayıtlarını temel alır ve belirlenen süre penceresi içerisinde oluşturulan benzersiz hesap sayısını analiz eder. Bu davranış, saldırganların persistence sağlamak, yetki genişletmek, daha sonra kullanmak üzere geçici hesaplar hazırlamak veya yönetimsel erişim elde etmek amacıyla toplu kullanıcı oluşturma girişimlerinde bulunduğu senaryolarda anlamlı bir göstergedir. Özellikle kısa süre içinde normal operasyonel davranışın üzerinde sayıda hesap oluşturulması, olayın detaylı incelenmesini gerektiren şüpheli bir durum olarak değerlendirilmelidir.

```
index=winsec EventCode=4720
| eval actor=coalesce(SubjectUserName, src_user, src_user_name, user, SubjectUserSid)
| eval account_name=coalesce(SamAccountName, Target_User_Name, TargetUserName, user_name, TargetSid)
| eval dest=coalesce(Computer, ComputerName, host)
| where isnotnull(account_name)
| bucket span=5m _time
| stats dc(account_name) as unique_created_accounts
        values(account_name) as created_accounts
        count as total_events
  by _time actor dest
| where unique_created_accounts >= 5
| sort - _time

```
![](Pasted%20image%2020260406224956.png)
![](Pasted%20image%2020260406225033.png)
##### False Positive Durumu

Bu kural bazı meşru yönetimsel işlemler sırasında false positive üretebilir. Özellikle sistem yöneticileri tarafından toplu kullanıcı açma işlemlerinin yapıldığı durumlar, onboarding süreçleri, test/lab ortamlarında otomatik kullanıcı üretimi, script ile hesap oluşturma işlemleri veya merkezi kimlik yönetimi araçları üzerinden yürütülen toplu provisioning faaliyetleri bu kurala takılabilir. Bu nedenle alarm değerlendirilirken işlemi yapan kullanıcı, zaman bilgisi, hedef sistem ve oluşturulan hesapların iş amacıyla ilişkili olup olmadığı birlikte incelenmelidir


### Kısa Süre İçinde Yüksek Sayıda Windows Kullanıcısının Silinmesi

Bu kural, kısa bir süre içerisinde birden fazla Windows kullanıcı hesabının silinmesi davranışını tespit etmek amacıyla oluşturulmuştur. Kural, Windows Security loglarında üretilen **Event ID 4726** kayıtlarını izler ve belirlenen zaman aralığı içinde silinen benzersiz hesap sayısını hesaplar. Bu tür bir davranış; saldırganın iz temizleme girişiminde bulunması, daha önce oluşturduğu geçici hesapları kaldırması, kritik hesapları devre dışı bırakmaya yönelik kötü niyetli faaliyet yürütmesi veya ortam üzerinde yıkıcı etki oluşturmaya çalışması gibi senaryolarla ilişkili olabilir. Normal kullanıcı yaşam döngüsünde yüksek hacimli hesap silme işlemleri sık görülmediğinden, bu davranış güvenlik açısından dikkatle değerlendirilmelidir.

```
index=winsec EventCode=4726
| eval actor=coalesce(SubjectUserName, src_user, src_user_name, user, SubjectUserSid)
| eval account_name=coalesce(Target_User_Name, TargetUserName, SamAccountName, user_name, TargetSid)
| eval dest=coalesce(Computer, ComputerName, host)
| where isnotnull(account_name)
| bucket span=5m _time
| stats dc(account_name) as unique_deleted_accounts
        values(account_name) as deleted_accounts
        count as total_events
  by _time actor dest
| where unique_deleted_accounts >= 5
| sort - _time
```

![](Pasted%20image%2020260406225525.png)

![](Pasted%20image%2020260406225557.png)

#### False Positive Durumu

Bu kural, bazı meşru bakım ve temizlik işlemleri sırasında false positive üretebilir. Özellikle eski hesapların toplu olarak kaldırıldığı kullanıcı yaşam döngüsü çalışmaları, deprovisioning süreçleri, test amaçlı açılmış kullanıcıların toplu şekilde silinmesi, otomasyon sistemleri tarafından yapılan temizlik işlemleri veya domain/hesap düzenleme çalışmaları bu alarmı tetikleyebilir. Bu nedenle alarm oluştuğunda silinen hesapların niteliği, işlemi yapan yönetici hesabı, işlemin planlı bir değişiklik sürecine ait olup olmadığı ve ortamın operasyonel bağlamı birlikte değerlendirilmelidir.

### Kısa Süre İçinde Yüksek Sayıda Windows Kullanıcısı Oluşturma ve Silme Aktivitesi

Bu kural, kısa bir zaman aralığında hem kullanıcı oluşturma hem de kullanıcı silme aktivitelerinde anormal yoğunluk oluşmasını tespit etmek amacıyla hazırlanmıştır. Kural, Windows Security loglarında yer alan **Event ID 4720** ve **Event ID 4726** kayıtlarını birlikte değerlendirir ve belirlenen süre penceresinde kaç farklı hesabın oluşturulduğunu ve kaç farklı hesabın silindiğini analiz eder. Bu yaklaşım, yalnızca tek yönlü hesap yönetimi hareketlerini değil, aynı zamanda saldırganların hesap açıp kısa süre sonra silerek izlerini gizlemeye çalıştığı veya toplu hesap manipülasyonu yaptığı senaryoları da görünür hale getirir. Özellikle aynı actor tarafından kısa sürede yoğun create/delete davranışı görülmesi, persistence hazırlığı, cleanup faaliyeti veya hesap yönetimi üzerinden gerçekleştirilen kötü niyetli operasyonlar açısından önemli bir göstergedir.


```
index=winsec (EventCode=4720 OR EventCode=4726)
| eval actor=coalesce(SubjectUserName, src_user, src_user_name, user, SubjectUserSid)
| eval account_name=coalesce(SamAccountName, Target_User_Name, TargetUserName, user_name, TargetSid)
| eval dest=coalesce(Computer, ComputerName, host)
| where isnotnull(account_name)
| bucket span=5m _time
| stats dc(eval(if(EventCode=4720,account_name,null()))) as created_users
        dc(eval(if(EventCode=4726,account_name,null()))) as deleted_users
        values(eval(if(EventCode=4720,account_name,null()))) as created_account_list
        values(eval(if(EventCode=4726,account_name,null()))) as deleted_account_list
        count as total_events
  by _time actor dest
| eval total_changed_users=created_users+deleted_users
| where created_users >= 5 OR deleted_users >= 5 OR total_changed_users >= 8
| sort - _time
```

![](Pasted%20image%2020260406225812.png)

![](Pasted%20image%2020260406225927.png)


#### False Positive Durumu

Bu kural, kullanıcı yönetimi süreçlerinin yoğun olduğu dönemlerde false positive üretebilir. Örneğin İK süreçlerine bağlı toplu işe giriş-çıkış işlemleri, kimlik yönetimi sistemleri üzerinden yapılan otomatik provisioning ve deprovisioning aktiviteleri, test ortamlarında yürütülen kullanıcı senaryoları, migration çalışmaları veya büyük ölçekli bakım operasyonları bu alarmı tetikleyebilir. Bu nedenle alarm değerlendirilirken create ve delete işlemlerinin aynı zaman aralığında neden gerçekleştiği, işlemi yapan hesabın rolü, hedef kullanıcıların niteliği ve organizasyondaki planlı değişiklik kayıtları dikkate alınmalıdır.



## Kaynakça

[1] MITRE ATT&CK – **T1136: Create Account**  
[2] MITRE ATT&CK – **T1136.001: Create Account: Local Account**  
[3] MITRE ATT&CK – **T1136.002: Create Account: Domain Account**  
[4] MITRE ATT&CK – **T1136.003: Create Account: Cloud Account**
[5] Microsoft - https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4720


