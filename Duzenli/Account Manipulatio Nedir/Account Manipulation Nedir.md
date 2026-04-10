## MITRE ATT&CK Account Manipulation nedir?

MITRE ATT&CK’e göre **Account Manipulation (T1098)**, saldırganların kurban ortamdaki hesapları manipüle ederek erişimlerini sürdürmesi ve/veya genişletmesi için kullandığı bir tekniktir. Bu teknik kapsamında saldırgan, ele geçirilmiş bir hesabın **credentials**, **permissions**, **permission groups** veya diğer erişim belirleyicileri üzerinde değişiklik yaparak mevcut foothold’unu daha kalıcı, daha esnek ve çoğu zaman daha ayrıcalıklı hâle getirebilir. MITRE’nin tanımında önemli olan nokta, burada yalnızca yeni bir hesap oluşturulmasından değil, mevcut bir hesabın saldırgan lehine yeniden yapılandırılmasından söz edilmesidir [1].

Bu manipülasyon; hesabın parolasının değiştirilmesi, ilgili hesabın yeni **permission groups** içine eklenmesi, belirli **account attributes** değerlerinin güncellenmesi ya da saldırgan erişimini koruyacak alternatif authentication ilişkilerinin tanımlanması gibi farklı biçimlerde ortaya çıkabilir. MITRE ayrıca bu davranışların, yalnızca mevcut erişimi korumaya değil, güvenlik politikalarını dolaylı biçimde baypas etmeye de hizmet edebileceğini vurgular. Örneğin saldırgan, ele geçirdiği **credentials**’ın geçerliliğini uzatmak amacıyla ardışık parola değişiklikleri gerçekleştirerek **password duration policies** benzeri kontrolleri etkisizleştirmeye çalışabilir. Başka bir ifadeyle T1098, bazı senaryolarda doğrudan bir hesap değişikliği tekniği olmanın ötesinde, kurumsal **identity** ve **access control** modelinin saldırgan lehine yeniden düzenlenmesi anlamına gelir [1].

Bu tekniğin uygulanabilmesi için saldırganın sistem, uygulama, servis veya domain üzerinde belirli bir yetki seviyesine zaten sahip olması gerekir. Ancak MITRE’nin özellikle belirttiği üzere, hesap üzerinde yapılan bu değişiklikler yalnızca mevcut erişimi korumakla kalmayabilir; aynı zamanda saldırgana ek **roles**, daha yüksek **permissions** veya daha ayrıcalıklı **Valid Accounts** üzerinden yeni erişim yolları da kazandırabilir. Bu nedenle **Account Manipulation**, ATT&CK matrisi içinde hem **Persistence** hem de **Privilege Escalation** taktikleri altında yer alır. Benim değerlendirmeme göre burada kritik nokta şudur: saldırgan çoğu zaman sisteme yalnızca “girmekle” yetinmez; içerideki erişim modelini kendi operasyonuna uygun olacak şekilde sessizce stabilize eder. T1098 tam olarak bu stabilizasyon evresini temsil eder [1].

MITRE’nin güncel sınıflandırmasına göre bu teknik; **Containers, ESXi, IaaS, Identity Provider, Linux, Network Devices, Office Suite, SaaS, Windows ve macOS** dahil olmak üzere geniş bir platform yelpazesinde gözlemlenebilir. Bu ayrıntı, Account Manipulation’ın yalnızca Windows veya Active Directory bağlamında düşünülmemesi gerektiğini göstermesi açısından önemlidir. Modern ortamlarda hesap manipülasyonu; bir **Identity Provider** üzerinde rol değişikliği, bir SaaS platformunda yönetici delegasyonu, bir hypervisor ortamında ayrıcalıklı grup üyeliği ya da yerel/domain düzeyinde hesap yetkilendirmesi şeklinde de görülebilir. Bu yüzden teknik, klasik uç nokta görünürlüğünün ötesinde, kimlik ve yetkilendirme katmanını da kapsayan geniş bir telemetry yaklaşımı gerektirir [1].

MITRE, T1098 altında yedi ayrı **sub-technique** tanımlar: **T1098.001 Additional Cloud Credentials**, **T1098.002 Additional Email Delegate Permissions**, **T1098.003 Additional Cloud Roles**, **T1098.004 SSH Authorized Keys**, **T1098.005 Device Registration**, **T1098.006 Additional Container Cluster Roles** ve **T1098.007 Additional Local or Domain Groups**. Bu alt tekniklerin ortak mantığı, saldırganın mevcut bir kimlik nesnesine yeni bir erişim mekanizması, yeni bir ayrıcalık katmanı veya yeni bir güven ilişkisi eklemesidir. Bence bu çerçeve, T1098’i anlamak için son derece değerlidir; çünkü teknik özünde “hesabı değiştirmekten” çok, hesabın etrafındaki **authorization surface**’ı saldırgan lehine genişletmeyi anlatır [1].


## Gerçek hayat kullanım örnekleri

MITRE’nin verdiği **procedure examples**, bu tekniğin gerçek operasyonlarda ne kadar farklı biçimlerde uygulanabildiğini açık biçimde göstermektedir. **Sandworm Team**, 2016 Ukraine Electric Power Attack sırasında MS-SQL üzerindeki `sp_addlinkedsrvlogin` komutunu kullanarak oluşturulan bir hesabı ağdaki diğer sunucularla ilişkilendirmiştir. Buradaki önemli nokta, hesap manipülasyonunun yalnızca kullanıcı parolası veya grup üyeliği değişikliği şeklinde değil, authentication ilişkilerini genişleten sunucu tarafı yapılandırmalar üzerinden de gerçekleşebilmesidir [1].

**Calisto**, kullanıcılara ek izinler ve uzaktan oturum açma yetkileri vererek erişim modelini genişletmiştir. **HAFNIUM**, domain hesaplarına ek ayrıcalıklar tanımlamış ve varsayılan admin hesaplarının parolalarını sıfırlamıştır. **Lazarus Group** ile ilişkilendirilen WhiskeyDelta-Two zararlısının administrator hesabını yeniden adlandırmaya çalışan bir fonksiyon içermesi ise, Account Manipulation’ın bazı durumlarda savunma ekiplerinin beklediği “parola değiştirme” veya “grup ekleme” desenlerinden daha farklı görünebileceğini göstermektedir [1].

MITRE’nin verdiği en dikkat çekici örneklerden biri **Mimikatz**’tır. Araç yalnızca credential dumping amacıyla değil, aynı zamanda **Skeleton Key** benzeri domain controller authentication bypass senaryolarında ve `LSADUMP::ChangeNTLM` ile `LSADUMP::SetNTLM` modülleri üzerinden bir hesabın parola hash’ini, açık parola değeri bilinmeden manipüle edebilecek şekilde de kullanılabilmektedir. Bu örnek bize şunu gösterir: T1098 bazen yönetim konsolu veya native admin aracından, bazen de doğrudan kimlik doğrulama altyapısının iç mantığını istismar eden araçlarla uygulanabilir [1].

Benzer şekilde **Scattered Spider**’ın hesapları **ESX Admins** grubuna ekleyerek vSphere üzerinde tam admin hakları elde etmesi, teknik ile **privileged group assignment** arasındaki ilişkiyi net biçimde ortaya koymaktadır. Başka bir ifadeyle, Account Manipulation çoğu zaman saldırganın yeni bir ayrıcalıklı kimlik yaratmasıyla değil, var olan hesapları daha ayrıcalıklı bir **authorization boundary** içine taşımasıyla gerçekleşir [1].

## MITRE ATT&CK’e göre detection yaklaşımı

MITRE, bu teknik için **DET0096 – Account Manipulation Behavior Chain Detection** yaklaşımını önermektedir. Bu stratejinin en önemli yönü, tek bir hesap değişikliği olayını izole biçimde değerlendirmek yerine, onu çevreleyen davranış zinciriyle birlikte ele almasıdır. Buna göre savunma tarafı; parola set edilmesi, grup üyeliğinin değiştirilmesi, `servicePrincipalName` güncellenmesi, **logon hours** değiştirilmesi veya benzeri **account attribute** değişikliklerini; olağandışı **process lineage**, beklenmeyen zamanlama, şüpheli oturum açma bağlamı veya ayrıcalık artışı ile korele etmelidir [1].

MITRE’nin bu başlık altında sunduğu analitikler de aynı düşünceyi destekler. **AN0265**, parola set etme, grup üyeliği güncelleme, `servicePrincipalName` değişikliği ve **logon hours** modifikasyonu gibi hareketleri; beklenmeyen süreç zinciri veya zamanlama anomalileri ile ilişkilendirir. Bu, özellikle bir hesap değişikliğinin normal yönetim araçları yerine olağandışı bir parent-child process ilişkisi içinden gelmesi durumunda önem kazanır. Örneğin bir kullanıcı hesabına ilişkin kritik değişikliklerin etkileşimli admin konsolu yerine beklenmedik bir script interpreter veya LOLBin üzerinden tetiklenmesi, yüksek öncelikli inceleme gerektirir [1].

**AN0266**, Linux tarafında `usermod`, `passwd` ve `groupmod` gibi native araçların kullanımını login veya process event’leri ile ilişkilendirerek ayrıcalık artırımı ya da kalıcılık amaçlı kullanıcı değişikliklerini saptamayı hedefler. **AN0267**, macOS üzerinde `dscl`, `pwpolicy` veya `sysadminctl` aracılığıyla yapılan kullanıcı/grup/root değişikliklerine odaklanır. **AN0268**, SSO/SAML tabanlı ortamlarda `isAdmin`, `role`, **MFA bypass** veya uygulama ataması gibi kullanıcı özniteliklerinde yapılan değişiklikleri CLI, API veya rogue IdP application bağlamında ele alır. **AN0269**, API veya vSphere Client üzerinden yeni kullanıcı eklenmesi ya da rol izinlerinin yükseltilmesini, özellikle beklenmeyen kaynak IP’lerden gelmesi durumunda anlamlı kabul eder. **AN0270** ise Google Workspace, O365 veya dosya paylaşım uygulamalarında **Editor → Owner** gibi rol yükseltmelerini takip eder [1].

Benim değerlendirmeme göre MITRE’nin burada verdiği en önemli mesaj şudur: T1098 için detection yalnızca “hangi Event ID oluştu?” sorusuna indirgenmemelidir. Asıl önemli olan; değişikliğin **hangi identity context** içinde yapıldığı, değişikliği yapan hesabın normalde bu aksiyonu gerçekleştirip gerçekleştirmediği, işlemin **privilege context** bakımından anormal olup olmadığı, olayın hemen öncesinde veya sonrasında şüpheli login activity bulunup bulunmadığı ve bu hareketin olağan bakım pencereleri dışına çıkıp çıkmadığıdır. Dolayısıyla bu teknik için etkili detection, tekil log aramaktan çok **time correlation**, **process lineage**, **role/permission transition** ve **source-host trust model** analizi gerektirir [1].



## MITRE ATT&CK’e göre mitigation yaklaşımı

MITRE, Account Manipulation’a karşı birden fazla mitigation önermektedir. **M1042 Disable or Remove Feature or Program**, kötüye kullanılabilecek gereksiz authentication ve authorization mekanizmalarının kaldırılmasını tavsiye eder. Bu öneri özellikle tarihsel olarak açık bırakılmış, ancak operasyonel olarak artık gerekmeyen trust relation, legacy auth feature veya yetki devri mekanizmaları açısından önemlidir [1].

**M1032 Multi-factor Authentication**, hem kullanıcı hem de ayrıcalıklı hesaplarda **MFA** kullanımını önerir. Tek başına MFA, tüm hesap manipülasyonlarını durdurmaz; ancak saldırganın hesap üzerinde yaptığı değişikliklerin pratik değerini azaltabilir ve bazı senaryolarda ek doğrulama katmanı sayesinde istismarı zorlaştırabilir. **M1030 Network Segmentation**, kritik sistemlere ve özellikle **domain controllers**’a erişimin segmentasyon ve firewall politikalarıyla kısıtlanmasını öngörür. MITRE’nin burada bulut ortamlar için ayrı **VPC** segmentleri vurgulaması da dikkate değerdir; çünkü hesap manipülasyonu birçok zaman merkezi yönetim sistemlerine erişim elde edebilen ağ konumlarından gerçekleştirilir [1].

**M1028 Operating System Configuration**, kritik sunucuların uygun güvenlik yapılandırmasıyla korunmasını ve SMB file sharing gibi gereksiz protokol ya da servislerin sınırlandırılmasını önerir. **M1026 Privileged Account Management**, **domain administrator** hesaplarının günlük işlerde kullanılmamasını vurgular; çünkü yüksek ayrıcalıklı hesapların sıradan uç noktalarda kullanılması, saldırganın bu hesapları manipüle etmesini çok daha olası hâle getirir. **M1022 Restrict File and Directory Permissions**, authentication ve authorization ile ilişkili hassas dosyalara erişimin kısıtlanmasını ister. **M1018 User Account Management** ise düşük ayrıcalıklı kullanıcıların hesapları veya hesap politikalarını değiştirecek permission’lara sahip olmamasını şart koşar [1].

Bence mitigation tarafında en kritik gerçek, T1098’in yalnızca teknik bir log problemi olmadığıdır. Bu teknik çoğu zaman zayıf **privileged access governance**, gevşek **role delegation**, yetersiz **identity hygiene** ve aşırı geniş bırakılmış yönetim yüzeylerinin doğal sonucudur. Dolayısıyla savunma yaklaşımı da yalnızca alarm üretmekle sınırlı kalmamalı; hesap değişikliği yapabilen kimliklerin daraltılması, ayrıcalıklı işlemlerin ayrıştırılması ve kritik yönetim yollarının minimize edilmesiyle desteklenmelidir [1].




## SIEM ve Tespit Kuralları

Bu bölümde yer alan **SIEM** ve **tespit kurallarına** geçmeden önce, ilgili loglama altyapısının doğru şekilde yapılandırılmış olması gerekir. Bu nedenle öncelikle ayrı olarak ele aldığım **Audit User Account Management** bölümünün incelenmesi önerilir. Çünkü aşağıdaki kuralların çalışabilmesi ve anlamlı sonuçlar üretebilmesi, hesap yönetimine ilişkin olayların uygun **audit policy** ayarları ile loglanmasına bağlıdır.

### Örnek Kural 1 : **Windows Multiple Account Passwords Changed** [2]

Bu kural, **Windows Security Event ID 4724** kayıtlarını kullanarak kısa süre içerisinde birden fazla farklı kullanıcı hesabının parolasının değiştirilmesi ya da sıfırlanması davranışını tespit etmeyi amaçlar. Kural, **10 dakikalık** zaman aralığında aynı aktör tarafından hedef alınan **benzersiz kullanıcı hesaplarını** sayar ve bu sayı **5’ten büyükse** alarm üretir.

Bu davranış güvenlik açısından önemlidir; çünkü kısa süre içinde birden fazla hesabın parolasının değiştirilmesi normal kullanıcı akışında beklenen bir durum değildir. Özellikle bir hesabın art arda çok sayıda kullanıcı parolasını resetlemesi, yetkisiz erişim, iç tehdit, ele geçirilmiş yönetici hesabı veya otomasyona benzetilmeye çalışılan kötü niyetli bir faaliyet göstergesi olabilir. Doğrulanması hâlinde saldırgan, çok sayıda hesaba erişim sağlayabilir, yetkisini genişletebilir ve ortamda kalıcılık oluşturabilir.

Burada önemli bir teknik not da vardır: **4724** olayı pratikte çoğu zaman “başka bir hesabın parolasını resetleme” davranışını görünür kılar. Bu nedenle bu kural, son kullanıcıların kendi parolasını değiştirmesinden(4723) çok, bir hesabın başka hesaplar üzerinde toplu parola işlemleri yapmasını yakalamak açısından daha değerlidir.


#### Kural SPL'i


![](Ekran%20görüntüsü%202026-04-08%20162025.png)
![](Ekran%20görüntüsü%202026-04-08%20162039.png)


Burada dikkat edilmesi gereken nokta, aynı **actor** hesabının kısa süre içinde birden fazla farklı **target_user** hesabı üzerinde parola işlemi gerçekleştirmesidir. Özellikle `changed_users` alanında çok sayıda farklı hesabın listelenmesi, olayın sıradan bir yönetimsel işlemden çok toplu ve dikkat çekici bir hesap manipülasyonu olabileceğini gösterir.

#### False Positive Durumu

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Özellikle **service account**’lar, kimlik yönetimi otomasyonları, help desk ekipleri, IAM çözümleri veya toplu kullanıcı bakım süreçleri kısa süre içinde birden fazla hesabın parolasını resetleyebilir. Kullanıcı onboarding/offboarding süreçleri veya güvenlik politikası gereği yapılan toplu parola yenilemeleri de benzer kayıtlar üretebilir.

Bununla birlikte üretim ortamında sıradan bir kullanıcı hesabının kısa zaman diliminde çok sayıda farklı hesabın parolası üzerinde işlem yapması normal bir davranış değildir. Bu nedenle alarm oluştuğunda işlemi yapan hesabın rolü, hesabın yetkileri, hedef alınan kullanıcıların niteliği ve işlemin planlı bir operasyon kapsamında olup olmadığı mutlaka doğrulanmalıdır. 

## Ek Not

Bu kural, önceki bazı tekil hesap kurallarından farklı olarak **davranışsal yoğunluğa** bakar. Yani tek bir parola reset olayını değil, kısa süre içinde birden fazla hesaba yönelik toplu parola işlemlerini öne çıkarır. Bu yüzden özellikle ele geçirilmiş yönetici hesabı veya kötüye kullanılan servis hesabı senaryolarında daha anlamlı sonuç üretir.




### Örnek Kural 2 : **Windows Multiple Accounts Disabled** [3]

Bu kural, **Windows Security Event ID 4725** kayıtlarını kullanarak kısa süre içerisinde birden fazla Windows hesabının devre dışı bırakılması davranışını tespit etmeyi amaçlar. Kural, **10 dakikalık** zaman aralığında aynı aktör tarafından devre dışı bırakılan **benzersiz kullanıcı hesaplarını** sayar ve bu sayı **5’ten büyükse** alarm üretir.

Bu davranış güvenlik açısından önemlidir; çünkü kısa süre içinde çok sayıda hesabın devre dışı bırakılması normal kullanıcı veya standart yönetim akışında sık beklenen bir durum değildir. Bu tür bir aktivite, ele geçirilmiş bir yönetici hesabı, kötüye kullanılan servis hesabı, iç tehdit veya operasyonları bozmayı amaçlayan dış saldırgan davranışı ile ilişkili olabilir. Özellikle kritik kullanıcı hesaplarının toplu şekilde devre dışı bırakılması, erişim kesintisine, iş sürekliliğinin bozulmasına ve olay müdahale sürecinin zorlaşmasına neden olabilir.

Bu nedenle bu kural, tekil bir hesap devre dışı bırakma olayından ziyade kısa zaman aralığında gerçekleştirilen **toplu hesap manipülasyonu** davranışını görünür kılar. Doğrulanması hâlinde bu aktivite, **Account Manipulation** ve **Valid Accounts** bağlamında değerlendirilmelidir.



## Kural SPL'i

```
index=winsec EventCode=4725 status=success
  
| bucket span=10m _time
  
| stats count dc(user) as unique_users values(user) as user values(dest) as dest
    BY EventCode signature _time
       src_user SubjectDomainName TargetDomainName
       Logon_ID
  
| where unique_users > 5
```
![](Ekran%20görüntüsü%202026-04-08%20163457.png)


![](Ekran%20görüntüsü%202026-04-08%20163512.png)



Burada dikkat edilmesi gereken nokta, aynı **actor** hesabının kısa süre içinde birden fazla farklı **target_user** hesabını devre dışı bırakmasıdır. Özellikle `disabled_users` alanında çok sayıda farklı hesabın listelenmesi, bunun sıradan bir yönetimsel işlem değil toplu ve dikkat çekici bir hesap manipülasyonu olabileceğini gösterir.



### False Positive Durumu

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Özellikle **service account**’lar, IAM otomasyonları, help desk ekipleri, kullanıcı yaşam döngüsü yönetimi süreçleri veya güvenlik politikası gereği yapılan toplu hesap kapatma işlemleri benzer kayıtlar üretebilir. İşten ayrılma, rol değişikliği veya geçici erişim temizliği gibi senaryolarda da kısa süre içinde birden fazla hesabın devre dışı bırakılması mümkündür.

Bununla birlikte üretim ortamında sıradan bir kullanıcı hesabının kısa zaman diliminde çok sayıda hesabı devre dışı bırakması normal bir davranış değildir. Bu nedenle alarm oluştuğunda işlemi yapan hesabın rolü, yetki seviyesi, hedef alınan kullanıcıların niteliği ve işlemin planlı bir operasyon kapsamında olup olmadığı mutlaka doğrulanmalıdır.

## Ek Not

Bu kural, tek bir hesabın devre dışı bırakılmasını değil; kısa süre içinde birden fazla hesaba yönelik **toplu disable işlemini** öne çıkarır. Bu nedenle özellikle ele geçirilmiş yönetici hesabı, kötüye kullanılan servis hesabı veya operasyonel bozma amacı taşıyan saldırı senaryolarında daha anlamlı sonuç üretir.


### Örnek Kural 3 : **Windows Multiple Accounts Disabled** [4]

Bu kural, **Windows Security Event ID 4725** kayıtlarını kullanarak kısa süre içerisinde birden fazla Windows hesabının devre dışı bırakılması davranışını tespit etmeyi amaçlar. Kural, **10 dakikalık** zaman aralığında aynı aktör tarafından devre dışı bırakılan **benzersiz kullanıcı hesaplarını** sayar ve bu sayı **5’ten büyükse** alarm üretir.

Bu davranış güvenlik açısından önemlidir; çünkü kısa süre içinde çok sayıda hesabın devre dışı bırakılması normal kullanıcı veya standart yönetim akışında sık beklenen bir durum değildir. Bu tür bir aktivite, ele geçirilmiş bir yönetici hesabı, kötüye kullanılan servis hesabı, iç tehdit veya operasyonları bozmayı amaçlayan dış saldırgan davranışı ile ilişkili olabilir. Özellikle kritik kullanıcı hesaplarının toplu şekilde devre dışı bırakılması, erişim kesintisine, iş sürekliliğinin bozulmasına ve olay müdahale sürecinin zorlaşmasına neden olabilir.

Bu nedenle bu kural, tekil bir hesap devre dışı bırakma olayından ziyade kısa zaman aralığında gerçekleştirilen **toplu hesap manipülasyonu** davranışını görünür kılar. Doğrulanması hâlinde bu aktivite, **Account Manipulation** ve **Valid Accounts** bağlamında değerlendirilmelidir.


## Kural SPL'i



```
index=winsec EventCode=4726 status=success
  
| bucket span=10m _time
  
| stats count dc(user) as unique_users values(user) as user values(dest) as dest
    BY EventCode signature _time
       src_user SubjectDomainName TargetDomainName
       Logon_ID
  
| where unique_users > 5
```

![](Ekran%20görüntüsü%202026-04-08%20163944.png)

![](Ekran%20görüntüsü%202026-04-08%20164001.png)



## False Positive Durumu

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Özellikle **service account**’lar, IAM otomasyonları, help desk ekipleri, kullanıcı yaşam döngüsü yönetimi süreçleri veya güvenlik politikası gereği yapılan toplu hesap kapatma işlemleri benzer kayıtlar üretebilir. İşten ayrılma, rol değişikliği veya geçici erişim temizliği gibi senaryolarda da kısa süre içinde birden fazla hesabın devre dışı bırakılması mümkündür.

Bununla birlikte üretim ortamında sıradan bir kullanıcı hesabının kısa zaman diliminde çok sayıda hesabı devre dışı bırakması normal bir davranış değildir. Bu nedenle alarm oluştuğunda işlemi yapan hesabın rolü, yetki seviyesi, hedef alınan kullanıcıların niteliği ve işlemin planlı bir operasyon kapsamında olup olmadığı mutlaka doğrulanmalıdır.

---

## Ek Not

Bu kural, tek bir hesabın devre dışı bırakılmasını değil; kısa süre içinde birden fazla hesaba yönelik **toplu disable işlemini** öne çıkarır. Bu nedenle özellikle ele geçirilmiş yönetici hesabı, kötüye kullanılan servis hesabı veya operasyonel bozma amacı taşıyan saldırı senaryolarında daha anlamlı sonuç üretir.






## Genel çıkarım / Son değerlendirme

**Account Manipulation (T1098)**, saldırganın erişim kazanmasından sonra ortam içindeki kimlik ve yetki modelini kendi lehine yeniden şekillendirmesine imkân tanıyan, stratejik değeri yüksek bir tekniktir. Teknik; **credentials**, **permissions**, **permission groups**, **roles** ve çeşitli **account attributes** üzerinden uygulanabilir. Bu nedenle yalnızca bir “hesap değiştirildi” olayı olarak değil, saldırganın **persistence** kazanma ve **privilege escalation** gerçekleştirme sürecinin bir parçası olarak değerlendirilmelidir [1].

Bu teknikte kritik nokta, saldırganın çoğu zaman yeni bir yapı inşa etmemesi, mevcut ve meşru görünen yapıların iç mantığını kendi lehine çevirmesidir. Savunma ekipleri açısından bu da şu anlama gelir: görünürde meşru olan hesap yönetim hareketleri, uygun bağlam analizi yapılmadığında en tehlikeli davranışlardan bazıları olabilir. T1098’i doğru anlamak, yalnızca hesap değişikliklerini izlemek değil; bu değişikliklerin kurumsal yetki modeline, süreç zincirine ve zaman bağlamına ne ölçüde uyduğunu analiz etmekten geçer [1].



## Kaynakça

[1] MITRE ATT&CK, **T1098 – Account Manipulation**.
[2] https://research.splunk.com/endpoint/faefb681-14be-4f0d-9cac-0bc0160c7280/
[3] https://research.splunk.com/endpoint/5d93894e-befa-4429-abde-7fc541020b7b/
[4] https://research.splunk.com/endpoint/49c0d4d6-c55d-4d3a-b3d5-7709fafed70d/ 
