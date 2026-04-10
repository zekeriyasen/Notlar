## MITRE ATT&CK T1098.007 nedir?

**MITRE ATT&CK T1098.007 – Account Manipulation: Additional Local or Domain Groups**, adversary’nin kendi kontrolündeki bir hesabı ek **local** veya **domain group**’lara dahil ederek sistem ya da domain üzerindeki erişimini sürdürmesini veya ayrıcalıklarını artırmasını ifade eder. Bu alt teknik, **T1098 – Account Manipulation** başlığı altında yer almakta ve hem **Persistence** hem de **Privilege Escalation** taktikleriyle ilişkilendirilmektedir. [1] 

Bu davranışın temel mantığı oldukça açıktır: saldırgan her zaman yeni bir hesap oluşturmak zorunda değildir. Bazen mevcut bir hesabı, örneğin daha önce ele geçirilmiş ya da önceden oluşturulmuş bir kullanıcıyı, doğrudan daha yetkili bir grup içine dahil etmek çok daha pratik ve düşük görünürlüklü bir yöntem olabilir. Bu nedenle burada saldırganın amacı yalnızca sisteme erişmek değil; aynı zamanda bu erişimi **grup üyeliği** üzerinden daha kalıcı ve daha etkili hale getirmektir. [1]

MITRE ATT&CK açıklamasında bu davranışın Windows ortamında `net localgroup` ve `net group`, Linux ortamında ise `usermod` ile gerçekleştirilebileceği belirtilmektedir. Bu kapsamda bir hesabın **Administrators**, **Remote Desktop Users**, **VPN user groups** veya benzeri yüksek etkili gruplara eklenmesi, saldırganın yalnızca yetki kazanmasını değil, aynı zamanda gelecekte yeniden kullanılabilecek bir erişim zemini oluşturmasını sağlar. 

Windows ortamlarında makine hesaplarının domain group’lara eklenmesi de mümkündür; bu durum local **SYSTEM** bağlamındaki bir hesabın domain seviyesinde beklenmedik privilege’lar kazanmasına yol açabilir. [1]

Bu alt teknik, üst teknik olan **T1098 – Account Manipulation** içinde değerlendirilmelidir. Bu üst teknik; credential değişiklikleri, permission group değişiklikleri, authentication mekanizmaları üzerindeki oynamalar ve authorization yapılandırmalarının manipüle edilmesi gibi davranışları kapsar. Bu çerçevede group membership değişikliği, hesabın yetki düzeyini doğrudan artıran ve operasyonel etkisi yüksek olan manipülasyon biçimlerinden biridir. [3]

## Teknik açıdan neden önemlidir?

Bir hesabın saldırı açısından değeri, yalnızca varlığından değil; hangi **group memberships**’e sahip olduğundan anlaşılır. Özellikle bir hesabın **Administrators** grubuna eklenmesi, hedef sistem üzerinde geniş yetkiler sağlar. **Remote Desktop Users** grubuna eklenmesi, ileride **RDP** üzerinden etkileşimli erişim kurulmasını mümkün kılar. Linux tarafında **sudoers** grubuna dahil edilen bir hesap ise kalıcı elevated access üretebilir. [1]

Account manipulation davranışı, çoğu zaman tek bir değişiklikten ibaret değildir. Privilege genişletme amacıyla yapılan group membership eklemeleri; password reset, account modification, permission change, account rename ve authentication ayarlarında yapılan değişikliklerle aynı saldırı zinciri içinde görülebilir. Bu nedenle group membership değişikliği, bağımsız bir olaydan çok, hesabın saldırgan lehine yeniden yapılandırıldığı daha geniş bir kötüye kullanım sürecinin parçası olarak ele alınmalıdır. [2]  [3]

Bu tekniğin kritik tarafı, yeni bir hesap oluşturmaya ihtiyaç duymadan mevcut bir hesabın etkisini artırabilmesidir. Bu da davranışı hem daha sessiz hem de daha düşük görünürlüklü hale getirir. Özellikle meşru yönetimsel iş akışlarıyla karışabilmesi nedeniyle, detection açısından dikkatli bir bağlam analizi gerektirir.

 Bu nedenle Additional Local or Domain Groups davranışı yalnızca basit bir yetki güncellemesi değildir. Çoğu durumda bu işlem, daha sonra kullanılacak **Persistence**, **Privilege Escalation** ve hatta **Lateral Movement** adımlarının zeminini hazırlar.

## Gerçek hayat kullanım örnekleri

MITRE ATT&CK procedure examples bölümü, bu davranışın gerçek saldırılarda tekrar tekrar kullanıldığını göstermektedir. **APT3** oluşturduğu hesapları **local admin groups** içine ekleyerek elevated access sağlamıştır. **APT41**, kullanıcı hesaplarını **User** ve **Admin** gruplarına dahil etmiştir. **DarkGate**, oluşturduğu hesapları çalışma sırasında **local administration group** içine yükseltmiştir. 
**Dragonfly**, yeni oluşturulan hesapları **administrators group** içine ekleyerek elevated access sürdürmüştür. **Magic Hound**, `DefaultAccount` isimli kullanıcıyı hem **Administrators** hem de **Remote Desktop Users** gruplarına eklemiştir. **Dragonfly** ve **Magic Hound**, hesapları **Administrators** ve **Remote Desktop Users** gibi gruplara ekleyerek erişim sürekliliği sağlamıştır. **Kimsuky** ise `net localgroup` komutu üzerinden group membership değişiklikleri gerçekleştirmiştir. **ServHelper**, `supportaccount` adlı hesabı **Remote Desktop Users** ve **Administrators** gruplarına dahil etmiştir. [1] 
Daha geniş Account Manipulation örnekleri bu davranışın operasyonel etkisini daha net göstermektedir. **Scattered Spider**, hesapları **ESX Admins** grubuna ekleyerek **vSphere** ortamlarında tam yönetim yetkisi elde etmiştir. **HAFNIUM**, domain accounts üzerinde ek privilege tanımlamış ve default admin accounts’un password’lerini resetlemiştir. **WhiskeyDelta-Two** ise administrator account’un adını değiştirmeye çalışmıştır. Bunun yanında **Mimikatz**, `LSADUMP::ChangeNTLM`, `LSADUMP::SetNTLM` ve **Skeleton Key** yetenekleriyle hesap manipülasyonunu credential ve authentication düzeyine taşımaktadır. [3]

Bu örnekler bize önemli bir şeyi gösteriyor: saldırganın amacı çoğu zaman yalnızca bir hesap oluşturmak değildir; o hesabı operasyonel olarak kullanışlı hale getirmektir. Bir hesabın değerini belirleyen şey yalnızca varlığı değil, hangi gruplara üye olduğudur. Benim değerlendirmeme göre bu yüzden group membership değişiklikleri, birçok ortamda account creation kadar kritik görülmelidir.

## MITRE ATT&CK’e göre detection nasıl yapılmalı?

MITRE ATT&CK detection strategy yaklaşımında bu alt teknik için temel odak, kullanıcı veya makine hesaplarının **privileged local** ya da **domain groups** içine yetkisiz veya beklenmedik biçimde eklenmesinin tespit edilmesidir. Özellikle **Administrators**, **Remote Desktop Users**, **Domain Admins**, **Enterprise Admins** ve ortam özelinde yüksek yetkili diğer gruplar öncelikli izleme kapsamına alınmalıdır. Linux sistemlerde `usermod`, `gpasswd` veya `/etc/group` dosyasına doğrudan müdahale; macOS tarafında `dseditgroup` ve `dscl` kullanımı önemli telemetry kaynaklarıdır. [1]

Windows ortamında bu davranışı doğrudan görünür kılan en kritik event’ler şunlardır:

- **Event ID 4728** → bir üyenin **security-enabled global group** içine eklenmesi
- **Event ID 4732** → bir üyenin **security-enabled local group** içine eklenmesi
- **Event ID 4738** → bir user account üzerinde değişiklik yapılması
- **Event ID 4724** → bir account için password reset girişimi
- **Event ID 4670** → bir nesne üzerindeki permission’ların değiştirilmesi
- **Event ID 4781** → bir account adının değiştirilmesi [2]  [3]

Bu event’ler arasında özellikle **4728** ve **4732**, **Additional Local or Domain Groups** davranışının çekirdek telemetry’sini oluşturur. Ancak detection yaklaşımı yalnızca bu olayların görülmesine dayanmamalıdır. Olayın **kim tarafından**, **hangi sistemde**, **hangi gruba**, **hangi zaman diliminde** ve **hangi ek hesap değişiklikleriyle birlikte** gerçekleştiği değerlendirilmelidir. Mesai dışı saatlerde privileged gruplara yapılan eklemeler, kısa zaman aralığında gelen password reset veya permission change olayları, daha önce düşük profilli olan bir hesabın ani şekilde yüksek ayrıcalıklı gruplara dahil edilmesi yüksek riskli örüntülerdir. [2]  [3]

Olgun bir detection modeli için şu sorular birlikte ele alınmalıdır:
- Hangi hesap hangi gruba eklendi?
- Bu işlemi hangi kullanıcı veya servis gerçekleştirdi?
- Eklenen grup gerçekten privileged bir grup mu?
- Olay öncesinde credential dumping, lateral movement veya suspicious administrative activity var mıydı?
- Aynı hesap üzerinde kısa süre içinde **4738**, **4724**, **4670** veya **4781** gibi ek manipülasyon izleri oluştu mu?

Cloud ve sanallaştırma ortamlarında da benzer mantık uygulanmalıdır. **Azure AD** ve diğer **Identity Provider** log’larında privileged role assignment değişiklikleri, conditional access policy oynamaları ve service account modifications izlenmelidir. **AWS** tarafında trust relationship ve IAM policy attachment olayları önemlidir. **vCenter / vSphere** log’larında ise **ESX Admins** üyelik değişiklikleri kritik telemetry üretir. [2][3]

## Mitigation nasıl olmalı?

MITRE ATT&CK, bu tekniğin meşru sistem işlevlerinin kötüye kullanılmasına dayandığını ve bu nedenle klasik preventive controls ile doğrudan engellenmesinin zor olduğunu belirtmektedir. [1] Bu nedenle savunma yaklaşımı, engellemeden çok **yetki sınırlandırma**, **değişiklik görünürlüğü** ve **erken anomali tespiti** üzerine kurulmalıdır.

Teknik olarak uygulanması gereken başlıca güvenlik kontrolleri şunlardır:

- **Network Segmentation (M1030)** → kritik sistemlere ve domain controllers’a erişimi sınırlandırmak
- **Disable or Remove Feature or Program (M1042)** → gereksiz authentication ve authorization bileşenlerini kaldırmak
- **User Account Management (M1018)** → düşük ayrıcalıklı hesapların account veya policy değiştirme yetkisine sahip olmamasını sağlamak
- **Restrict File and Directory Permissions (M1022)** → authentication ve authorization ile ilişkili hassas dosyalara erişimi sınırlamak
- **Multi-factor Authentication (M1032)** → user ve privileged accounts için MFA kullanmak
- **Privileged Account Management (M1026)** → privileged accounts’un günlük operasyonlarda kullanılmasını engellemek
- **Operating System Configuration (M1028)** → özellikle domain controllers ve kritik sunucuların güvenli yapılandırılmasını sağlamak [3]

Buna ek olarak **principle of least privilege**, **defense in depth**, düzenli log korelasyonu, privileged group membership gözden geçirmeleri ve **SIEM** tabanlı alarm üretimi bu teknik için pratik savunma katmanlarıdır. Compromise olduğu değerlendirilen hesapların password’leri hızla resetlenmeli, grup üyelikleri doğrulanmalı ve anormal yetki kazanımları geri alınmalıdır. [2][3]


Bu aşağıya kaldırıp kaldırmayacağına karar vereceksin => 

Bu ifade bence oldukça önemlidir. Çünkü burada sorun bir exploit değil, yetkili işlemlerin kötüye kullanılmasıdır. Dolayısıyla savunma yaklaşımı tamamen “engelleme” merkezli değil; daha çok **sıkı yetki yönetimi**, **ayrıcalıklı grup üyeliklerinin düzenli gözden geçirilmesi**, **değişiklik izleme**, **audit logging** ve **anomali odaklı detection** mantığıyla kurulmalıdır. Özellikle privileged group’lara kimlerin eklenebileceği sınırlandırılmalı ve bu gruplardaki üyelik değişiklikleri sürekli gözden geçirilmelidir.

## Genel çıkarım

**T1098.007 – Additional Local or Domain Groups**, görünüşte basit bir group membership değişikliği gibi görünse de saldırı perspektifinden bakıldığında doğrudan **Privilege Escalation** ve **Persistence** üreten kritik bir tekniktir. Özellikle bir hesabın **Administrators**, **Remote Desktop Users**, **Domain Admins**, **Enterprise Admins** veya çevreye özgü başka yüksek ayrıcalıklı gruplara eklenmesi, saldırgana hem daha geniş hareket alanı hem de daha kalıcı erişim sağlar. [1][3]

Bu teknik çoğu zaman tek başına gerçekleşmez. Password reset, account rename, permission modification, credential manipulation ve authentication düzeyi değişiklikleriyle birlikte görüldüğünde çok daha anlamlı hale gelir. Bu nedenle detection yaklaşımı tekil event’lere değil, hesabın zaman içindeki değişimlerini gösteren **davranış zincirine** dayanmalıdır. [2][3]

Benim yaklaşımıma göre burada savunma tarafının odaklanması gereken esas nokta, yalnızca “hangi hesap hangi gruba eklendi?” sorusu değildir. Asıl önemli olan, bu değişikliğin saldırganın erişimini ne kadar genişlettiği, hangi diğer hesap manipülasyonlarıyla birlikte geldiği ve ortamın normal idari örüntüsünden ne kadar saptığıdır. Bu çerçevede değerlendirildiğinde **Additional Local or Domain Groups**, yüksek etkili ve dikkatle izlenmesi gereken bir TTP olarak öne çıkmaktadır.

---
## SIEM VE TESPİT KURALLARI

https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/audit-security-group-management

Windows’ta **security-enabled group** üyelik eklemeleri için temel event’ler şunlardır:

- **4728** → Bir kullanıcı **security-enabled global group** içine eklendi
- **4732** → Bir kullanıcı **security-enabled local group** içine eklendi
- **4756** → Bir kullanıcı **security-enabled universal group** içine eklendi

Bu event’lerde mantık üç parçadır:

- **Kim ekledi?** → **Subject**
- **Kimi ekledi?** → **Member**
- **Hangi gruba ekledi?** → **Group / Target**

Bu loglamaları açmak için öncelikle **Audit Security Group Management** policy’i üretildiğini, bu politika ile grup oluşturma/değiştirme/silme ve üyelik ekleme-çıkarma olaylarının denetlendiğini belirtir. ([Microsoft Learn](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/audit-security-group-management))

Buradaki en önemli karar şu:

- Sadece **kritik gruplar** mı izlenecek?
- Yoksa **tüm grup üyelik eklemeleri** mi izlenecek?

Ben burada iki katman için de kural yazmaya çalışacağım:

- **Katman 1:** Her üyelik eklemeyi gören geniş görünürlük kuralı- low
- **Katman 2:** Sadece kritik gruplar için yüksek öncelikli kural - high

Bu yapı sana hem tüm hareketleri görme imkânı verir, hem de gerçekten önemli olanları ayrı alarm olarak yükseltir. Özellikle zamanla tuning yapacaksan bu daha iyi olur.



### Her üyelik eklemeyi gören geniş görünürlük kuralı- low
```

index=winsec EventCode IN (4728, 4732, 4756) 
|  eval actor=coalesce(src_user, src_user_name,SubjectUserName)
|  eval Target_User=coalesce(member_user_name, user_name, added_user)
|  eval Target_Group=coalesce(Group_Name,Target_User_Name, user_group)
|  eval dest=coalesce(dest, Computer, host)
| where isnotnull(actor) AND isnotnull(Target_User) AND isnotnull(Target_Group)
| stats 
    dc(Target_User) as eklenen_kisi_sayisi, 
    values(Target_User) as eklenen_kullanicilar, 
    values(EventCode) as Olay_Kodlari,
    values(dvc) as Hedef_Sistemler
    by _time ,actor, Target_Group
| sort - _time
```



### Windows AD Privileged Group Membership Addition

Bu kural, Windows güvenlik olayları içerisinde **4728, 4732 ve 4756** Event ID’lerini analiz ederek, bir kullanıcının belirli **ayrıcalıklı Active Directory gruplarına** üye eklemesini tespit etmeyi amaçlar. Kural; **Domain Admins, Enterprise Admins, Schema Admins, DnsAdmins, Server Operators, Account Operators, Backup Operators** gibi yüksek yetkili gruplara yapılan üyelik ekleme işlemlerine odaklanır.

Bu davranış güvenlik açısından kritik öneme sahiptir. Çünkü saldırganlar, ele geçirdikleri bir hesabı doğrudan yüksek yetkili gruplara ekleyerek **Privilege Escalation**, **Persistence** veya daha geniş sistem erişimi elde etmeye çalışabilir. Özellikle yönetimsel gruplara yapılan üyelik değişiklikleri, Active Directory ortamının güvenliğini doğrudan etkileyen işlemler arasında yer alır.

Bu kural, işlemi gerçekleştiren kullanıcıyı (**actor**), gruba eklenen hedef kullanıcıyı (**Target_User**) ve hedef grubu (**Target_Group**) ilişkilendirerek görünür hâle getirir. Eğer bu aktivite beklenmeyen bir hesap, anormal zaman dilimi veya kritik bir grup bağlamında görülüyorsa, olay yetkisiz grup üyeliği değişikliği olarak değerlendirilmeli ve detaylı biçimde incelenmelidir. [5]


#### SPL Search

```
index=winsec EventCode IN (4728, 4732, 4756) 
|  eval actor=coalesce(src_user, src_user_name,SubjectUserName)
|  eval Target_User=coalesce(member_user_name, user_name, added_user)
|  eval Target_Group=coalesce(Group_Name,Target_User_Name, user_group)
|  eval dest=coalesce(dest, Computer, host)
| where isnotnull(actor) AND isnotnull(Target_User) AND isnotnull(Target_Group)
| search Target_Group IN ("Admin*", "Domain Admins", "Enterprise Admins", "Backup Admins", "Schema Admins", "DnsAdmins", "Server Operators", "Account Operators", "Backup Operators")
| stats 
    dc(Target_User) as eklenen_kisi_sayisi, 
    values(Target_User) as eklenen_kullanicilar, 
    values(EventCode) as Olay_Kodlari,
    values(dvc) as Hedef_Sistemler
    by _time ,actor, Target_Group
| sort - _time
```

![](Ekran%20görüntüsü%202026-04-07%20111926.png)

![](Ekran%20görüntüsü%202026-04-07%20111951.png)

### Olası İnceleme Adımları

Bir alarm oluştuğunda aşağıdaki inceleme adımları uygulanmalıdır:

- İşlemi gerçekleştiren hesabın, ilgili grubun üye yönetimini yapmaya yetkili olup olmadığı doğrulanmalıdır.
- Hesap sahibi veya ilgili sistem yöneticisi ile iletişime geçilerek bu aktivitenin bilgisi dahilinde gerçekleşip gerçekleşmediği teyit edilmelidir.
- Aynı kullanıcıya veya aynı host’a ait son **48 saat** içindeki diğer alarmlar ve güvenlik olayları incelenmelidir.
- Olay ile ilişkili olarak **account creation**, **password reset**, **account modification**, **privileged logon**, **lateral movement** veya ek **group membership** değişiklikleri bulunup bulunmadığı araştırılmalıdır.
- Değişiklik mesai dışı saatlerde, olağandışı bir yönetim istasyonundan veya beklenmeyen bir yönetim hesabı üzerinden gerçekleşmişse, olay daha yüksek önemde değerlendirilmelidir.  [6]



## False Positive Durumu

Bu teknik, **Active Directory**’nin meşru yönetim mekanizmalarını kötüye kullanır. Bu nedenle her privileged group üyeliği değişikliği doğrudan kötü niyetli kabul edilmemelidir. Bazı durumlarda sistem yöneticileri, IAM ekipleri, help desk personeli veya yetkili otomasyon süreçleri; bakım, rol değişikliği, geçici yetki ataması veya operasyonel ihtiyaçlar nedeniyle kullanıcıları bu gruplara ekleyebilir.

Bununla birlikte burada esas değerlendirilmesi gereken nokta, işlemin teknik olarak mümkün olması değil, **idari bağlamda beklenen ve yetkili bir işlem olup olmamasıdır**. Özellikle düşük profilli bir hesabın aniden ayrıcalıklı bir gruba eklenmesi, daha önce bu tür işlemlerle ilişkilendirilmemiş bir yönetici hesabının grup üyeliği değişikliği yapması veya olayın olağandışı saatlerde gerçekleşmesi, false positive olasılığını azaltır ve şüphe seviyesini yükseltir. [5]  [6]


### Response ve Remediation

Triage sonucunda aktivitenin meşru olmadığı değerlendirilirse, olay doğrudan **incident response** sürecine aktarılmalıdır. İşlemi gerçekleştiren admin hesabının bu değişiklikten haberdar olmadığı doğrulanırsa, **Active Directory incident response planı** devreye alınmalıdır.

Privileged group’a eklenen hesabın bu yetkiye operasyonel olarak ihtiyacı yoksa, ilgili hesap derhal gruptan çıkarılmalıdır. Bunun yanında işlemi gerçekleştiren admin hesabının ayrıcalıkları yeniden gözden geçirilmeli, hesabın compromise edilip edilmediği araştırılmalı ve gerekirse parola sıfırlama, oturum sonlandırma, erişim kısıtlama ve ek adli inceleme adımları uygulanmalıdır.

Olayın kök nedeni de mutlaka araştırılmalıdır. Saldırganın ilk erişimi hangi vektör üzerinden elde ettiği, hangi hesapları kullandığı ve aynı yöntemin yeniden kullanılmasını engellemek için hangi kontrollerin eksik kaldığı belirlenmelidir. Olay müdahalesi sırasında elde edilen bulgular, mevcut **logging**, **audit policy** ve tespit içeriklerinin iyileştirilmesi için kullanılmalıdır. Bu yaklaşım, hem **mean time to detect (MTTD)** hem de **mean time to respond (MTTR)** değerlerinin iyileştirilmesine katkı sağlar. [6]

----

## Kaynaklar

1. Mitre Attack
2. https://www.manageengine.com/products/eventlog/cyber-security/account-manipulation.html
3. https://www.startupdefense.io/mitre-attack-techniques/t1098-account-manipulation
4. https://research.splunk.com/endpoint/065f2701-b7ea-42f5-9ec4-fbc2261165f9/
5. https://community.splunk.com/t5/Getting-Data-In/AD-user-groups/td-p/135558
6. https://www.elastic.co/docs/reference/security/prebuilt-rules/rules/windows/persistence_user_account_added_to_privileged_group_ad



https://research.splunk.com/endpoint/49c0d4d6-c55d-4d3a-b3d5-7709fafed70d/

[https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4728](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4728)  
[https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4732](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4732)  
[https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4756](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4756)  
[https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/audit-security-group-management](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/audit-security-group-management)  
  
  
  
[https://logstail.com/blog/detecting-malicious-user-addition-to-the-local-administrators-group-on-windows/](https://logstail.com/blog/detecting-malicious-user-addition-to-the-local-administrators-group-on-windows/)  
[https://www.manageengine.com/log-management/detection-rules/user-added-to-highly-privileged-group.html](https://www.manageengine.com/log-management/detection-rules/user-added-to-highly-privileged-group.html)   
[https://github.com/P4T12ICK/Sigma-Rule-Repository/blob/master/detection-rules/T1078/win_user_added_to_local_administration.md](https://github.com/P4T12ICK/Sigma-Rule-Repository/blob/master/detection-rules/T1078/win_user_added_to_local_administration.md)  
  
[https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_susp_add_user_local_admin_group/](https://detection.fyi/sigmahq/sigma/windows/process_creation/proc_creation_win_susp_add_user_local_admin_group/)  
  
[https://www.elastic.co/docs/reference/security/prebuilt-rules/rules/windows/persistence_user_account_added_to_privileged_group_ad](https://www.elastic.co/docs/reference/security/prebuilt-rules/rules/windows/persistence_user_account_added_to_privileged_group_ad)  
  
[https://research.splunk.com/endpoint/065f2701-b7ea-42f5-9ec4-fbc2261165f9/](https://research.splunk.com/endpoint/065f2701-b7ea-42f5-9ec4-fbc2261165f9/)  
  
[https://community.splunk.com/t5/Splunk-Search/Active-Directory-security-group-changes/m-p/660670](https://community.splunk.com/t5/Splunk-Search/Active-Directory-security-group-changes/m-p/660670)

