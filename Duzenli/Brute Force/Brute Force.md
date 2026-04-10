**[1] MITRE ATT&CK – Brute Force (T1110)**  
**[2] MITRE ATT&CK – Password Guessing (T1110.001)**  
**[3] MITRE ATT&CK – Password Cracking (T1110.002)**  
**[4] MITRE ATT&CK – Password Spraying (T1110.003)**  
**[5] MITRE ATT&CK – Credential Stuffing (T1110.004)**  
**[6] Splunk – Brute Force Attack overview**  
**[7] Fortinet – Brute Force Attack overview**  
**[8] Palo Alto Networks – Brute Force operational analysis**  
**[9] IBM – Brute Force technical overview**  
**[10] CrowdStrike – Brute Force Attacks**


## MITRE ATT&CK Brute Force nedir?

MITRE ATT&CK kapsamında **Brute Force (T1110)**, adversary’nin bir hesaba ait parolayı doğrudan bilmediği ya da bazı durumlarda yalnızca **password hash** gibi dolaylı credential material elde ettiği senaryolarda, tekrar eden ve sistematik denemeler yoluyla kimlik doğrulama mekanizmalarını aşmaya çalışmasını ifade eder. MITRE’nin tanımına göre bu süreç, geçerli credential bilgisini canlı bir servis üzerinden doğrulatmaya çalışarak gerçekleşebileceği gibi, daha önce ele geçirilmiş **password hash** veya benzeri credential verileri üzerinde **offline** biçimde de yürütülebilir. Başka bir ifadeyle brute force, yalnızca login ekranına karşı yapılan ardışık parola denemelerinden ibaret değildir; aynı zamanda çalınmış veya dökülmüş credential materyalinin hesap erişimine dönüştürülmesi için kullanılan sistematik bir zorlama modelidir. [1]

Bu teknik, MITRE ATT&CK içinde **Credential Access** taktiği altında sınıflandırılsa da, operasyonel etkisi çoğu zaman bu taktik sınırını aşar. Çünkü brute force ile elde edilen başarılı erişim, saldırgan açısından yalnızca bir kimlik doğrulama başarısı değil; daha sonraki aşamalarda **Initial Access**, **Privilege Escalation**, **Lateral Movement**, **Persistence** ve hatta **Data Exfiltration** için kullanılabilecek geçerli bir başlangıç noktasıdır. MITRE de bu tekniğin tek bir saldırı anına özgü olmadığını, ihlal yaşam döngüsünün farklı evrelerinde tekrar tekrar ortaya çıkabileceğini açıkça göstermektedir. [1]

MITRE’nin özellikle vurguladığı önemli noktalardan biri, brute force faaliyetinin bir ihlal sırasında farklı bağlamlarda devreye girebilmesidir. Örneğin adversary, hedef ortam içinde daha önce yürüttüğü **OS Credential Dumping**, **Account Discovery** veya **Password Policy Discovery** gibi post-compromise faaliyetlerden elde ettiği bilgi ve bağlamı kullanarak, artık hangi hesapların değerli olduğunu, parola politikasının ne kadar güçlü olduğunu veya hangi credential materyalin istismar edilebilir olduğunu daha bilinçli biçimde değerlendirebilir. Bu durumda brute force, rastgele ve kör bir deneme faaliyeti olmaktan çıkar; aksine keşif ve erişim toplama adımlarıyla beslenen daha hedefli bir **Valid Accounts elde etme stratejisine** dönüşür. [1]

MITRE ayrıca brute force’un bazı senaryolarda **External Remote Services** ile birlikte kullanılarak doğrudan **Initial Access** elde etmek amacıyla uygulanabileceğini belirtmektedir. Bu yönüyle teknik, yalnızca iç ağda hesap yayılımı için değil, dışa açık VPN, RDP, webmail, SaaS login paneli veya benzeri uzaktan erişim yüzeylerinde ilk erişim kapısını zorlamak için de kullanılabilir. Modern tehdit ortamında bu durum daha da önemlidir; çünkü saldırganlar brute force’u artık yalnızca kaba kuvvetle çalışan gürültülü denemeler şeklinde değil, **automation**, **botnet**, **residential proxy**, **distributed infrastructure**, **API abuse** ve **stealth tuning** gibi yöntemlerle daha sessiz, dağıtık ve sürdürülebilir kampanyalara dönüştürebilmektedir. Splunk, Fortinet, IBM, CrowdStrike ve Palo Alto Networks tarafından paylaşılan teknik değerlendirmeler de brute force’un günümüzde hâlâ etkili olmasının temel nedeninin bu adaptasyon kabiliyeti olduğunu göstermektedir. [6]  [7]  [8]  [9]  [10]

 
MITRE’nin verdiği bir diğer kritik ayrıntı ise, brute force’un yalnızca doğru parolayı bulmakla sona ermediğidir. Eğer adversary doğru credential’ı tahmin eder ancak **location-based conditional access** politikaları nedeniyle giriş yapamazsa, kullandığı altyapıyı değiştirerek kurbanın coğrafi veya ağ bağlamına daha çok benzeyen bir kaynaktan tekrar deneme yapabilir. Bu detay bence son derece önemlidir; çünkü brute force çoğu zaman “başarılı ya da başarısız login denemeleri” düzeyinde ele alınır. Oysa pratikte saldırgan, kimlik doğrulama kontrolünü aşmak için yalnızca parolayı değil, aynı zamanda erişim bağlamını da uyarlayabilmektedir. Bu da brute force detection yaklaşımının yalnızca failure count’lara değil; **source infrastructure**, **geo-context**, **identity context** ve **conditional access bypass davranışlarına** da bakması gerektiğini gösterir. [1]


Sonuç olarak **Brute Force (T1110)**, ilk bakışta basit görünen ancak aslında hem **online** hem **offline** boyutu olan, ihlal yaşam döngüsünün farklı aşamalarında ortaya çıkabilen ve diğer tekniklerle birleştiğinde etkisi büyüyen bir saldırı tekniğidir. MITRE ATT&CK’in bu tekniği dört alt modele ayırması da boşuna değildir: **Password Guessing**, **Password Cracking**, **Password Spraying** ve **Credential Stuffing**, aynı temel mantığın farklı operasyonel uygulamalarıdır. Endüstri kaynakları brute force’u zaman zaman **dictionary attack**, **hybrid brute force**, **reverse brute force**, **RDP brute force** veya **rainbow table attack** gibi ek kavramlarla da açıklar; ancak bu yazının omurgasında teknik otorite ve sınıflandırma bütünlüğü açısından MITRE ATT&CK’in **T1110** yaklaşımı esas alınmaktadır. Diğer vendor kaynakları ise bu çerçeveyi modern saldırı pratiği, savunma perspektifi ve görünürlük gereksinimleri açısından güçlendiren tamamlayıcı referanslar olarak kullanılmaktadır. [1] [6] [7] [8] [9] [10]


## 1. Sub-technique: Brute Force: Password Guessing (T1110.001)
**Password Guessing (T1110.001)**, MITRE ATT&CK kapsamında **Brute Force (T1110)** tekniğinin bir alt tekniği olarak tanımlanmakta ve **Credential Access** taktiği altında sınıflandırılmaktadır.
Bu alt teknikte adversary, hedef sistem veya ortam içerisindeki meşru credential’lara dair önceden doğrulanmış bir bilgiye sahip olmadan, hesap erişimi elde edebilmek amacıyla parolaları tahmin etmeye çalışır.

MITRE ATT&CK’e göre burada saldırgan, belirli bir hesaba ait parolayı bilmediği için tekrar eden ve sistematik bir deneme mekanizmasıyla farklı parola kombinasyonlarını test eder. 

Bu testler çoğu zaman yaygın kullanılan parolalardan, zayıf parola kalıplarından veya önceden hazırlanmış common password listeleri üzerinden manuel ya da otomat şekilde gerçekleştirilebilir. Başka bir ifadeyle teknik, tamamen kör bir deneme faaliyeti olabileceği gibi, insan davranışına dayalı tahmin edilebilir parola seçim alışkanlıklarından da yararlanabilir. [2]

MITRE’nin tanımında önemli bir nokta, bu alt tekniğin hedef ortamın **password complexity** veya **account use policy** ayrıntıları tam olarak bilinmeden de uygulanabilmesidir. Saldırgan, hedef organizasyonun parola uzunluğu, karmaşıklık gereksinimi veya hesap kilitleme eşiği hakkında net bilgiye sahip olmasa bile guessing saldırısını başlatabilir. Ancak bu durum aynı zamanda saldırı açısından doğal bir risk üretir; çünkü çok sayıda başarısız authentication denemesi, özellikle sıkı account lockout politikalarının uygulandığı ortamlarda hesap kilitlenmesine, alarm oluşmasına ve savunma ekiplerinin dikkatinin çekilmesine neden olabilir. MITRE de bu nedenle Password Guessing’i, potansiyel olarak yüksek görünürlük üretebilen ancak doğru koşullarda hâlâ etkili olabilen bir yöntem olarak ele almaktadır. [2]

MITRE ATT&CK, Password Guessing’in en sık hangi yüzeylerde görüldüğünü de açık biçimde sıralamaktadır. Buna göre saldırganlar çoğu zaman **SSH (22/TCP)**, **Telnet (23/TCP)**, **FTP (21/TCP)**, **NetBIOS / SMB / Samba (139/TCP ve 445/TCP)**, **LDAP (389/TCP)**, **Kerberos (88/TCP)**, **RDP / Terminal Services (3389/TCP)**, **HTTP/HTTP Management Services (80/TCP ve 443/TCP)**, **MSSQL (1433/TCP)**, **Oracle (1521/TCP)**, **MySQL (3306/TCP)**, **VNC (5900/TCP)** ve **SNMP (161/UDP, 162/TCP/UDP)** gibi yaygın servisleri hedef alır. Bu liste bize şunu gösterir: Password Guessing yalnızca Windows logon ekranına karşı yürütülen dar bir saldırı modeli değildir; ağ servisleri, veritabanı servisleri, uzaktan yönetim protokolleri ve altyapı cihazları da bu alt tekniğin doğrudan hedefi olabilir. [2]

MITRE ayrıca bu tekniğin yalnızca klasik management service’lerle sınırlı olmadığını da vurgular. Saldırganlar, **single sign-on (SSO)** mekanizmalarını, **cloud-based applications** kullanan ve **federated authentication protocols** ile çalışan yapıları ve ayrıca **Office 365** gibi dışa açık e-posta uygulamalarını da hedefleyebilir. Bu ayrıntı bence özellikle önemlidir; çünkü birçok savunma yaklaşımı Password Guessing’i hâlâ yalnızca on-prem servisler veya Windows failure event’leri üzerinden okumaya çalışmaktadır. Oysa güncel ortamlarda authentication plane çok daha geniştir ve saldırganın hedefi çoğu zaman domain logon ekranı değil, doğrudan SaaS veya federated identity katmanı olabilir. [2]

MITRE’nin açıklamasında yer alan ve çoğu yazıda atlanan bir diğer önemli boyut, bu tekniğin bazı senaryolarda **network device interface**’ler üzerinden de uygulanabilmesidir. Özellikle **wlanAPI** gibi arayüzler ve **wireless authentication protocols** kullanılarak erişilebilir **wifi-router** yapılarına karşı guessing veya brute force davranışları görülebilir. Bu ayrıntı, T1110.001’in yalnızca kullanıcı hesaplarıyla sınırlı olmadığını; zayıf kimlik doğrulama kontrolüne sahip ağ cihazlarının ve kablosuz erişim yüzeylerinin de aynı mantıkla hedeflenebildiğini göstermektedir. [2]

MITRE’nin detection açısından özellikle dikkat çektiği kritik nokta ise görünürlük farkıdır. Varsayılan ortamlarda **LDAP** ve **Kerberos** bağlantı girişimleri, **SMB** üzerinden oluşan ve çoğu savunma ekibinin alışkın olduğu **Windows logon failure Event ID 4625** kadar görünür event üretmeyebilir. Bu durum doğrudan şu sonuca götürür: Savunma tarafı yalnızca 4625 odaklı izleme yapıyorsa, belirli Password Guessing senaryolarını kısmen ya da tamamen kaçırabilir. Benim değerlendirmeme göre bu, T1110.001’in detection açısından en yanıltıcı yönlerinden biridir. Çünkü saldırı kaba görünse de telemetri izi her zaman kaba değildir; bazen daha düşük görünürlüklü kimlik doğrulama katmanlarında, daha seyrek ama daha anlamlı denemeler şeklinde yürütülür. Bu nedenle detection mantığı yalnızca logon failure count’larına değil, **authentication protocol visibility**, **identity context**, **source behavior** ve **success-after-failure correlation** boyutlarına da dayanmalıdır. [2][6][8][9][10]


## Gerçek hayat kullanım örneği

MITRE örneklerinde **APT28**’in password guessing ve brute force amaçlı tooling kullandığı, bazı kampanyalarda saat başına yüzlerce authentication attempt ürettiği ve zaman içinde dağıtık altyapılarla bu saldırıyı ölçeklendirdiği görülmektedir. **APT29**’un mailbox listelerine karşı password guessing uygulaması, bu tekniğin yalnızca sistem erişimi için değil, doğrudan haberleşme ve bilgi toplama yüzeylerine erişim amacıyla da kullanıldığını göstermektedir. **Emotet**’in hardcoded parola listeleriyle kullanıcı hesaplarını hedeflemesi ve **CrackMapExec**’in belirli kullanıcılar veya ağ genelinde guessing amaçlı kullanılabilmesi de bu alt tekniğin pratik yönünü güçlendirmektedir. [2]

Bu örnek bize şunu gösteriyor: Password Guessing çoğu zaman “amatörce çok deneme yapmak” şeklinde düşünülse de, gelişmiş threat actor’lar bunu hedefin servis yapısına ve gözlemlenebilirlik düzeyine göre uyarlayabilmektedir. Tehdit aktörlerinin kimlik doğrulama yüzeylerini tanıyıp hangi hesapların operasyonel değer taşıdığını bilerek hareket ederler. Özellikle mail, remote access ve yönetim servisleri bu yüzden öncelikli hedef hâline gelir. [2]


### MITRE ATT&CK’e göre detection yaklaşımı

MITRE’nin detection stratejisinde burada temel sinyal, aynı kullanıcıya veya benzer kullanıcı kümelerine karşı zaman içinde tekrarlayan authentication failure desenidir. Ancak olgun bir detection yaklaşımı yalnızca event saymaya dayanmaz; aşağıdaki bağlamların birlikte değerlendirilmesi gerekir: [2]

- **Behavior chain:** Bir kaynaktan aynı hesaba veya benzer hesap adlarına yönelik ardışık başarısız denemeler.
    
- **Time correlation:** Kısa sürede patlayan burst denemeler ile birkaç saate yayılan distributed-low-rate denemelerin ayrı ayrı modellenmesi.
- Coğrafi, zamansal veya çalışma saati dışı login anomalileri
- **Identity context:** Hesabın privileged olup olmadığı, service account mı user account mu olduğu, daha önce hiç giriş yapıp yapmadığı.
    
- **Privilege context:** Hedeflenen account local admin, domain admin, remote management user veya application admin ise risk seviyesi yükseltilmelidir.
    
- **Process / protocol context:** Denemenin SMB, RDP, SSH, OWA, VPN, Kerberos, LDAP veya web-based auth üzerinden gerçekleştiğinin ayrıştırılması gerekir.
    
- **Success-after-failure correlation:** Çok sayıda başarısız denemeden sonra aynı source veya related infrastructure üzerinden gelen başarılı girişler öncelikli incelenmelidir.

Özellikle Windows ortamlarda yalnızca **4625** odaklı analiz eksik kalabilir. Kerberos ve LDAP tabanlı denemeler için **4768**, **4771** ve benzeri kimlik doğrulama olayları; Linux/Unix için **sshd**, PAM veya syslog kayıtları; SaaS için sign-in logs birlikte değerlendirilmelidir. [1][2]

### Sigma kuralı

```yaml
title: Possible Password Guessing Against Single Account on Windows
id: 5f8d8d2e-8d54-4a9a-bf7b-111000100001
status: experimental
description: Detects repeated failed logon attempts against the same account from a single source, which may indicate password guessing.
references:
  - https://attack.mitre.org/techniques/T1110/001/
author: OpenAI
date: 2026-04-07
logsource:
  product: windows
  service: security
detection:
  selection:
    EventID: 4625
    TargetUserName|re: '^(?!.*\$).+'
  filter_machine_accounts:
    TargetUserName|endswith: '$'
  timeframe: 10m
  condition: selection and not filter_machine_accounts
fields:
  - ComputerName
  - IpAddress
  - WorkstationName
  - TargetUserName
  - LogonType
  - Status
  - SubStatus
level: medium
falsepositives:
  - User typing errors
  - Misconfigured service or scheduled task
  - Legacy application using stale credentials
tags:
  - attack.credential_access
  - attack.t1110.001
```

> Not: Sigma tarafında eşik mantığı çoğu SIEM/Sigma backend’inde correlation rule veya SIEM-side aggregation ile tamamlanır. Bu nedenle üretim ortamında “aynı kullanıcı için 10 dakika içinde X sayıda failure” benzeri threshold mantığı backend’e göre ayrıca tanımlanmalıdır.

### Splunk SPL

Aşağıdaki örnek SPL, Windows Security log’larında tek bir hesaba karşı aynı kaynaktan tekrarlayan başarısız denemeleri görünür kılmak için kullanılabilir:

```spl
index=wineventlog sourcetype="WinEventLog:Security" EventCode=4625
TargetUserName!="*$" IpAddress!="-"
| bucket span=10m _time
| stats count as failed_attempts
        values(Status) as status
        values(SubStatus) as substatus
        values(LogonType) as logon_type
        values(WorkstationName) as workstation
        by _time IpAddress TargetUserName ComputerName
| where failed_attempts >= 8
| sort - failed_attempts
```

Eğer ortamda **Splunk Add-on for Microsoft Windows** kullanılıyorsa field isimleri `src`, `user`, `dest`, `signature_id` veya `EventCode` varyasyonlarıyla normalize edilmiş olabilir. Bu nedenle gerçek ortamda field mapping’in doğrulanması gerekir.

### Sorgunun ekran görüntüsünde neyin gösterilmesi gerektiği

Bu bölümde konulacak ekran görüntüsünde en az şu alanlar görünmelidir:

- `_time`
    
- `IpAddress` veya normalize edilmiş `src`
    
- `TargetUserName` veya `user`
    
- `ComputerName` / `dest`
    
- `failed_attempts`
    
- `LogonType`
    
- `Status` ve `SubStatus`
    

Teknik olarak iyi bir ekran görüntüsü, yalnızca event listesini değil, aynı kullanıcıya karşı art arda gelen failure desenini görünür kılmalıdır. Mümkünse aynı IP’den gelen denemelerin belirli bir zaman penceresinde kümelendiği ve belirli bir user üzerinde yoğunlaştığı açık biçimde gösterilmelidir.

### Mitigation

MITRE’ye göre bu alt teknik için temel mitigation başlıkları **Account Use Policies**, **Multi-factor Authentication**, **Password Policies** ve gerektiğinde **Update Software** yaklaşımıdır. Özellikle account lockout eşiği dikkatli ayarlanmalıdır; çok agresif lockout, saldırıyı engellerken operasyonel bir **denial-of-service** etkisi yaratabilir. Bunun yanında dışa açık servislerde MFA etkinleştirilmesi, zayıf veya default password kullanımının önüne geçilmesi ve management interface’lerin güncel sürümde tutulması kritik önemdedir. [2]

---

## 2. Sub-technique: Brute Force: Password Cracking (T1110.002)

**Password Cracking (T1110.002)**, adversary’nin ele geçirdiği **password hash**, credential dump**encrypted credential material** veya benzeri gizli veriler üzerinden kullanılabilir **plaintext credential** elde etmeye çalışmasını ifade eder. MITRE ATT&CK’e göre bu alt teknik çoğunlukla **OS Credential Dumping** sonrasında elde edilen hash’lerle ilişkilidir; ayrıca bazı durumlarda **Data from Configuration Repository** kaynaklarından alınan hashed credential verileri de aynı amaçla kullanılabilir. Özellikle **Pass the Hash** doğrudan mümkün değilse, saldırgan için asıl hedef hash’i çözerek gerçek parolaya ulaşmaktır. Bu süreçte adversary, hash’i üreten parolayı sistematik olarak tahmin etmeye çalışabilir veya önceden hazırlanmış **rainbow table** yapılarından yararlanabilir. MITRE’nin de belirttiği üzere cracking faaliyeti çoğu zaman hedef ağın içinde değil, saldırganın kontrolündeki sistemlerde **offline** olarak yürütülür; başarılı olduğunda elde edilen plaintext parola, ilgili hesabın erişebildiği sistem, servis ve kaynaklarda kullanılabilir. [3]

Bu alt teknik, online brute force’tan farklı olarak çoğu zaman doğrudan authentication failure gürültüsü üretmez. Bu nedenle görünürlük genellikle hash dump izleri, credential artifact erişimi, cracking aracı çalıştırılması ve bunu izleyen beklenmedik **valid authentication** olayları üzerinden oluşur. IBM, Fortinet ve CrowdStrike kaynaklarının da gösterdiği gibi, modern cracking süreçleri artık yüksek işlem gücü, **GPU acceleration**, otomasyon ve önceden hesaplanmış tablolar sayesinde çok daha hızlı yürütülebilmektedir. Bence burada kritik nokta, Password Cracking’in çoğu SOC ekranında doğrudan “parola kırma” olarak görünmemesidir; savunma başarısı, bu tekniğin dağınık izlerini tek bir saldırı zinciri içinde ilişkilendirebilmeye bağlıdır. [7]  [9]  [10]

### Gerçek hayat kullanım örneği

MITRE örneklerinde **APT3**’ün password hash brute force yaptığı, **Dragonfly**’ın **Hydra** ve **CrackMapExec** gibi araçları kullandığı, **FIN6**’nın **ntds.dit** üzerinden hash çıkararak offline cracking yürüttüğü, **Night Dragon** operasyonunda **Cain & Abel** kullanıldığı ve **Salt Typhoon**’un network device configuration dosyalarından elde edilen zayıf şifreleme ile korunan credential’ları kırdığı belirtilmektedir. [3]

Ek olarak vendor kaynaklarında **John the Ripper**, **Hashcat**, **Aircrack-ng**, **L0phtCrack**, **Ophcrack** ve **Rainbow Crack** gibi araçların sıkça anıldığı görülmektedir. Bunların hepsi aynı amaçla kullanılmasa da ortak nokta, offline credential recovery sürecini otomasyon ve hızla desteklemeleridir. [7][9][10]

Bu örnek bize şunu gösteriyor: Password Cracking yalnızca Windows domain hash’lerine karşı çalışan dar bir saldırı modeli değildir. Ağ cihazlarından veritabanlarına, Linux shadow dosyalarından wireless authentication materyallerine kadar uzanabilen çok daha geniş bir credential recovery alanı söz konusudur. [3][7][9][10]

### MITRE ATT&CK’e göre detection yaklaşımı

MITRE’nin önerdiği detection stratejisi, cracking activity’yi doğrudan gözlemekten çok, bu faaliyetin **öncesi** ve **sonrası** üzerinde yoğunlaşır. Olgun bir detection tasarımında aşağıdaki korelasyonlar öne çıkar: [3]

- **Credential access precursor:** `ntds.dit`, `SAM`, `SECURITY`, `/etc/shadow`, config dumps veya exported credential artifacts gibi dosyalara erişim.
    
- **Tool execution context:** `hashcat`, `john`, `john.exe`, `hashcat.exe` gibi cracking araçlarının veya bunları çağıran script’lerin çalıştırılması.
    
- **Process lineage:** Unsigned binary, PowerShell, Python, bash veya cmd zinciri üzerinden cracking tool invocation.
    
- **Resource usage:** Olağandışı CPU/GPU tüketimi, kısa sürede çok yüksek işlem yükü, non-standard working directory’den çalışan cracking binaries.
    
- **Timeline correlation:** Hash dump sonrası aynı account’lardan beklenmedik başarılı girişler.
    
- **Identity reuse indicator:** Daha önce hiç interactive auth yapmamış bir account’un kısa süre sonra valid login üretmesi.
    
Windows ortamlarında Sysmon, EDR telemetry ve process creation log’ları bu alt teknik için kritik görünürlük sağlar. Yalnızca Security Event log’larıyla bu davranışın tamamını yakalamak çoğu zaman mümkün değildir. [3]

### Sigma kuralı

```yaml
title: Possible Password Cracking Tool Execution
id: 9b6a11d3-2c60-44a8-bf7b-111000200002
status: experimental
description: Detects common password cracking tools such as Hashcat or John the Ripper.
references:
  - https://attack.mitre.org/techniques/T1110/002/
author: OpenAI
date: 2026-04-07
logsource:
  category: process_creation
  product: windows
detection:
  selection_image:
    Image|endswith:
      - '\hashcat.exe'
      - '\john.exe'
      - '\john-the-ripper.exe'
  selection_commandline:
    CommandLine|contains:
      - 'hashcat'
      - 'john '
      - ' --wordlist'
      - ' -m '
  condition: selection_image or selection_commandline
fields:
  - Image
  - CommandLine
  - ParentImage
  - User
  - Computer
level: high
falsepositives:
  - Authorized security testing
  - Password audit activities by internal security teams
tags:
  - attack.credential_access
  - attack.t1110.002
```

### Splunk SPL

Aşağıdaki sorgu, process telemetry üzerinden cracking tool execution izlerini aramak için kullanılabilir. Sysmon Event ID 1 veya EDR-normalized process logs ile uyarlanabilir:

```spl
index=sysmon (EventCode=1 OR EventID=1)
(
  Image="*\\hashcat.exe" OR Image="*\\john.exe" OR
  CommandLine="*hashcat*" OR CommandLine="*john *"
)
| stats count
        values(Image) as image
        values(CommandLine) as commandline
        values(ParentImage) as parent_image
        by _time Computer User
| sort - _time
```

Eğer Sysmon yoksa ve yalnızca EDR process telemetry varsa, `process_name`, `process_command_line`, `parent_process_name`, `host`, `actor_process_image_name` gibi alan adlarına göre uyarlama yapılmalıdır.

Ek korelasyon için ikinci aşamada şu mantık kurulabilir: credential dump şüphesi olan host üzerinde cracking tool execution görüldükten sonra aynı account’lardan beklenmedik başarılı authentication var mı?

### Sorgunun ekran görüntüsünde neyin gösterilmesi gerektiği

Ekran görüntüsünde şu alanların görünmesi gerekir:

- `_time`
    
- `Computer`
    
- `User`
    
- `Image`
    
- `CommandLine`
    
- `ParentImage`
    
- Varsa `Hashes`, `CurrentDirectory`, `Signed` veya EDR risk alanları
    

Teknik olarak iyi bir görsel, yalnızca “hashcat.exe çalıştı” bilgisini değil, bunun hangi parent process tarafından tetiklendiğini ve hangi kullanıcı bağlamında gerçekleştiğini göstermelidir. Eğer ikinci bir ekran görüntüsü kullanılacaksa, credential dump sonrası gelen cracking tool execution ile sonrasında görülen successful login korelasyonu ayrıca gösterilebilir.

### Mitigation

MITRE’ye göre bu alt teknik için ana mitigation başlıkları **Multi-factor Authentication** ve **Password Policies**’dir. Güçlü, tahmin edilmesi ve kırılması zor parolalar; modern hashing mekanizmaları; weak encryption kullanımının azaltılması ve credential dumping’e karşı savunma kontrolleri bu alt tekniğin etkisini düşürür. Uygulamada MFA’nın burada özel bir önemi vardır; çünkü hash kırılmış olsa bile ikinci faktör gereksinimi plaintext credential’ın doğrudan istismar edilmesini zorlaştırır. [3]

---

## 3. Sub-technique: Brute Force: Password Spraying (T1110.003)

### MITRE ATT&CK açıklaması

**Password Spraying (T1110.003)**, bir veya birkaç yaygın parolanın çok sayıda farklı kullanıcı hesabı üzerinde denenmesi tekniğidir. Klasik brute force’ta saldırgan tek kullanıcıya çok sayıda parola denerken, password spraying’de mantık tersine çevrilir: aynı veya benzer parolalar çok sayıda kullanıcı üzerinde sınanır. MITRE’ye göre bunun temel amacı, account lockout politikalarını tetiklemeden çoklu kullanıcı havuzu üzerinde geçerli credential elde etmeye çalışmaktır. Bu teknik özellikle **SSH, SMB, LDAP, Kerberos, RDP, SSO, VPN, OWA, Office 365 ve cloud-based authentication yüzeylerinde** yaygın olarak görülür. [4]

Bu alt tekniğin operasyonel gücü, hacimden ziyade dağılım mantığından gelir. Her kullanıcı için failure sayısı düşük olabilir; ancak aynı source IP, aynı device, aynı user-agent veya aynı saldırı altyapısı üzerinden bakıldığında, çok sayıda kullanıcı üzerinde aynı davranış izinin bırakıldığı görülür. MITRE ayrıca adversary’nin detection threshold’larını aşmamak için bu saldırıyı bilinçli biçimde **throttle** edebileceğini vurgular. Yani saldırı bir dakika içinde değil, günler boyunca yavaş ve istikrarlı biçimde yürütülebilir. [4]

Benim değerlendirmeme göre Password Spraying, kurumsal ortamlarda en sık yanlış sınıflandırılan brute force alt tekniğidir. Çünkü bazı SOC ekipleri bu davranışı yalnızca “çoklu failed login” olarak işaretler; ancak asıl ayırt edici unsur, **tek parola mantığının çoklu identity surface üzerinde uygulanmasıdır**. Bu nedenle detection yaklaşımı, “aynı kullanıcıya kaç kez denendi?” sorusundan çok, “aynı source aynı zaman diliminde kaç farklı kullanıcıyı denedi?” sorusuna odaklanmalıdır. [4]

### Gerçek hayat kullanım örneği

MITRE örneklerinde **APT28**, password spraying modunda saat başına düşük sayıda ama günler veya haftalar boyunca süren denemeler gerçekleştirmiş; ayrıca dağıtık altyapı ve Kubernetes cluster kullanarak geniş ölçekli spraying kampanyaları yürütmüştür. **APT29**, **APT33**, **HAFNIUM**, **HEXANE**, **Chimera** ve **Agrius** gibi aktörlerin de password spraying’i initial access veya credential validation amacıyla kullandığı belirtilmektedir. **MailSniper**, **CrackMapExec** ve çeşitli malware örnekleri de bu davranışın tooling tarafını temsil etmektedir. **Quad7 Activity** örneğinde yalnızca 24 saatte bir deneme gibi kasıtlı throttling uygulanması, detection tarafında statik eşiklerin ne kadar kolay aşılabildiğini göstermektedir. [4]

Bu örnek bize şunu gösteriyor: Password Spraying, yalnızca volume-based detection ile güvenilir şekilde yakalanamaz. Davranışın bazı varyantları gürültülü olsa da, olgun threat actor’lar bu tekniği zamana yayarak veya dağıtık IP alanları kullanarak klasik brute force imzasından uzaklaştırabilmektedir. [4]

### MITRE ATT&CK’e göre detection yaklaşımı

MITRE’nin detection stratejisi burada açık biçimde **authentication failures across many accounts within a defined time window** mantığına dayanır. Yani core davranış, aynı source IP, session veya altyapının birden fazla kullanıcı hesabı üzerinde başarısız doğrulama üretmesidir. İyi bir detection modeli şu bağlamları içermelidir: [4]

- Aynı IP veya ilişkili IP havuzundan gelen çoklu kullanıcı failure’ları
- Aynı zaman penceresinde yüksek `dc(user)` veya `dc(TargetUserName)` değeri
- Aynı kullanıcı kümesine karşı günlere yayılan düşük hızlı tekrarlar
- Başarısız denemelerden sonra gelen success event korelasyonu
- Kerberos, LDAP, SSO ve SaaS log’larının birlikte değerlendirilmesi
- Çalışma saati dışı, coğrafi olarak anormal veya riskli IP’lerden gelen denemeler
- MFA prompt anomalisinin eşlik edip etmediği

Windows odaklı ortamlarda **4625**, **4768**, **4771**; mail ve cloud tarafında sign-in telemetry; Linux/Unix tarafında sshd/PAM log’ları birlikte ele alınmalıdır. Yalnızca SMB failure’larına bakmak spraying’in büyük kısmını görünmez bırakabilir. [1][4]

### Sigma kuralı

```yaml
title: Possible Password Spraying Across Multiple Accounts
id: d10a5a8c-1e72-4c84-bf7b-111000300003
status: experimental
description: Detects failed logons from a single source IP across multiple user accounts which may indicate password spraying.
references:
  - https://attack.mitre.org/techniques/T1110/003/
author: OpenAI
date: 2026-04-07
logsource:
  product: windows
  service: security
detection:
  selection:
    EventID:
      - 4625
      - 4771
    TargetUserName|re: '^(?!.*\$).+'
  filter_machine_accounts:
    TargetUserName|endswith: '$'
  timeframe: 15m
  condition: selection and not filter_machine_accounts
fields:
  - IpAddress
  - TargetUserName
  - Status
  - ComputerName
  - WorkstationName
level: high
falsepositives:
  - Password audit or red team testing
  - Misconfigured identity synchronization or legacy applications
tags:
  - attack.credential_access
  - attack.t1110.003
```

> Not: Spray davranışı için asıl değer, SIEM tarafında aynı `IpAddress` altında kaç farklı `TargetUserName` görüldüğünün aggregation ile hesaplanmasındadır.

### Splunk SPL

Aşağıdaki sorgu, Windows ortamında tek bir kaynaktan çok sayıda kullanıcıya yönelen authentication failure desenini tespit etmek için kullanılabilir:

```spl
index=wineventlog sourcetype="WinEventLog:Security" (EventCode=4625 OR EventCode=4771)
TargetUserName!="*$" IpAddress!="-"
| bucket span=15m _time
| stats dc(TargetUserName) as unique_accounts
        values(TargetUserName) as targeted_users
        count as total_failures
        values(EventCode) as event_codes
        values(Status) as status
        by _time IpAddress
| where unique_accounts >= 10
| sort - unique_accounts - total_failures
```

Kerberos odaklı ortamlarda bu sorgu ayrıca `EventCode=4768` ve belirli `Status` kodlarıyla genişletilebilir. Add-on normalizasyonu varsa `src`, `user`, `signature_id`, `failure_reason`, `dest` gibi alan adlarına göre düzenleme yapılmalıdır.

Daha olgun bir sürümde, aynı IP’den gelen spraying pattern’inin ardından herhangi bir hedef kullanıcı için success event korelasyonu da eklenebilir.

### Sorgunun ekran görüntüsünde neyin gösterilmesi gerektiği

Ekran görüntüsünde şu alanlar mutlaka görünmelidir:

- `_time`
    
- `IpAddress` / `src`
    
- `unique_accounts`
    
- `targeted_users`
    
- `total_failures`
    
- `event_codes`
    
- `status`
    

İyi bir görsel, tek bir IP’nin aynı zaman penceresinde çok sayıda farklı hesabı hedeflediğini açık biçimde göstermelidir. Hedef kullanıcı listesinin tamamı çok uzunsa, ilk birkaç hesabı görünür tutup toplam farklı hesap sayısını ayrıca göstermek daha faydalıdır. Eğer ikinci görsel kullanılacaksa, spraying sonrasında başarıya dönen kullanıcı hesabı ayrıca gösterilmelidir.

### Mitigation

MITRE’ye göre **Account Use Policies**, **Multi-factor Authentication** ve **Password Policies** bu alt teknik için temel mitigation katmanlarıdır. Özellikle password spraying’de MFA’nın etkisi çok yüksektir; çünkü tek parola ile çok sayıda hesap test edilse bile ikinci faktör eksikliği nedeniyle başarılı istismar zinciri kırılabilir. Bunun yanında conditional access, risky sign-in blocking, anonymizing proxy trafiğinin engellenmesi ve lockout eşiklerinin operasyonel denge gözetilerek ayarlanması önem taşır. [4]

---

## 4. Sub-technique: Brute Force: Credential Stuffing (T1110.004)

**Credential Stuffing (T1110.004)**, adversary’nin başka platformlarda yaşanan veri ihlallerinden veya breach dump'lardan elde ettiği **username/password pair**’leri, hedef ortamdaki hesaplara karşı deneyerek yetkisiz erişim sağlamaya çalışmasını ifade eder. 

MITRE ATT&CK’e göre bu alt tekniğin temelinde **credential overlap** vardır; yani kullanıcıların kişisel ve kurumsal hesaplarında aynı ya da benzer parolaları yeniden kullanma eğilimi, saldırganın elindeki sızdırılmış credential’ları farklı servislerde geçerli hâle getirebilir. Bu nedenle saldırgan burada yeni parola tahmin etmekten çok, daha önce açığa çıkmış **username/password pair**’leri farklı hedeflerde doğrulamaya çalışır. Bu sızdırılmış bilgiler çoğu zaman internete düşen veri setlerinden, ele geçirilmiş web sitelerinden veya farklı servis ihlallerinden elde edilir. [5]

MITRE’nin vurguladığı önemli noktalardan biri, bu yöntemin operasyonel olarak riskli olmasıdır. Çünkü çok sayıda credential çifti denendiğinde, ortamda yoğun **authentication failure** izleri ve buna bağlı **account lockout** olayları oluşabilir. Buna rağmen teknik, özellikle parola tekrar kullanımının yaygın olduğu yapılarda etkili olabilir. MITRE’ye göre Credential Stuffing; **SSH, Telnet, FTP, NetBIOS/SMB, LDAP, Kerberos, RDP, HTTP/HTTPS management services, MSSQL, Oracle, MySQL ve VNC** gibi yaygın erişim ve yönetim servislerinde görülebilir. Bu da tekniğin yalnızca web login ekranlarıyla sınırlı olmadığını, ağ servislerinden veritabanı servislerine kadar geniş bir authentication yüzeyini hedefleyebildiğini gösterir. [5]

MITRE ayrıca bu davranışın yalnızca geleneksel servislerde değil, **single sign-on (SSO)** yapılarında, **cloud-based applications** üzerinde, **federated authentication** kullanan ortamlarda ve **Office 365** gibi dışa açık e-posta servislerinde de uygulanabildiğini belirtir. Bence burada kritik nokta, Credential Stuffing’in basit bir parola deneme yöntemi değil; dış veri ihlallerini doğrudan kurumsal kimlik altyapısına taşıyan pratik bir **Credential Access** tekniği olmasıdır. Eğer kullanıcı aynı credential bilgisini birden fazla platformda kullanıyorsa, saldırgan açısından başka bir servisteki veri sızıntısı hedef ortamda doğrudan hesap ele geçirmeye dönüşebilir. Bu yönüyle Credential Stuffing, parola zafiyetinden çok **credential reuse** problemine dayanan, kimlik güvenliği açısından son derece kritik bir alt tekniktir. [5]

### Gerçek hayat kullanım örneği

MITRE örneklerinde **Chimera**’nın victim remote services üzerinde credential stuffing kullandığı, **TrickBot**’un ise RDP yüzeyinde brute-force/stuffing benzeri erişim denemelerinde bulunduğu görülmektedir. Her ne kadar MITRE örnekleri sınırlı sayıda olsa da, vendor kaynakları bu tekniğin çok daha geniş bir pratik alana sahip olduğunu göstermektedir. CrowdStrike’ın da belirttiği üzere çalınmış credential listeleri dark web’de düşük maliyetle dolaşmakta, otomasyon araçlarıyla birlikte kullanıldığında credential stuffing giriş eşiği ciddi biçimde düşmektedir. [5][10]

Bu örnek bize şunu gösteriyor: Credential Stuffing yalnızca hedef kurumun kendi parola kalitesine bağlı bir risk değildir. Kurumun kullanıcıları başka bir platformdaki ihlalden etkilenmişse ve aynı credential’ları kurumsal hizmetlerde yeniden kullanıyorsa, saldırganın işi büyük ölçüde kolaylaşır. Dolayısıyla bu risk, yalnızca iç politika meselesi değil; aynı zamanda dış veri ihlallerinin kuruma yansıyan etkisidir. [5]  [6]  [7]  [10]

### MITRE ATT&CK’e göre detection yaklaşımı

MITRE’nin detection stratejisine göre burada öne çıkan davranış, **aynı source IP veya session üzerinden çok sayıda distinct username/password pair kullanılarak yapılan başarısız login denemeleri**dir. Detection mantığında şu bağlamlar kritik önemdedir: [5]
 
 * Aynı source IP veya session üzerinden çok sayıda farklı kullanıcı denemesi
   
- **Cross-identity failure pattern:** Çok sayıda user üzerinde hızlı veya yarı hızlı failure üretimi.
    
- **Cloud / SaaS visibility:** Azure AD, Okta, Duo, O365, Exchange, OWA ve benzeri platformların sign-in logs’ları kritik veri kaynağıdır.
    
- **Success-after-failure correlation:** Sızmış credential çiftlerinden biri geçerliyse, kısa süre sonra aynı source’tan success görülebilir.
    
- **Threat intel enrichment:** Breached credential exposure veya risky IP reputation ile enrichment yapılması detection kalitesini ciddi biçimde artırır.
    
- **User overlap analysis:** Denenen kullanıcıların aynı e-mail domain’e, aynı business unit’e veya aynı identity provider’a ait olup olmadığı incelenmelidir.

Credential Stuffing detection’da yalnızca failure sayısı değil, **farklı kullanıcıların kısa sürede aynı kaynak tarafından taranması** ve bunu izleyen login success’lerin korelasyonu önemlidir. Özellikle internet-facing SSO, mail ve VPN yüzeylerinde bu davranış daha belirgin olur. [5]
### Sigma kuralı

```yaml
title: Possible Credential Stuffing Against External Authentication Surface
id: 0c4de994-7b39-43dc-bf7b-111000400004
status: experimental
description: Detects multiple failed logon attempts from a single source against many users, consistent with credential stuffing or large-scale credential validation.
references:
  - https://attack.mitre.org/techniques/T1110/004/
author: OpenAI
date: 2026-04-07
logsource:
  product: windows
  service: security
detection:
  selection:
    EventID: 4625
    TargetUserName|re: '^(?!.*\$).+'
  filter_machine_accounts:
    TargetUserName|endswith: '$'
  timeframe: 10m
  condition: selection and not filter_machine_accounts
fields:
  - IpAddress
  - TargetUserName
  - WorkstationName
  - ComputerName
  - Status
  - SubStatus
level: high
falsepositives:
  - Internet-facing portal health checks with bad credential cache
  - Security validation activity by authorized teams
tags:
  - attack.credential_access
  - attack.t1110.004
```

> Not: Credential Stuffing ile Password Spraying telemetry açısından birbirine benzeyebilir. Ayırıcı nokta çoğu zaman ek bağlamdır: breached credential intelligence, farklı password denemeleri, cloud sign-in pattern’leri ve hedeflenen hizmetin niteliği.

### Splunk SPL

Aşağıdaki sorgu, internet-facing authentication yüzeylerinde çok sayıda kullanıcıya karşı tek kaynaktan failure deseni aramak için başlangıç noktası olabilir:

```spl
index=wineventlog sourcetype="WinEventLog:Security" EventCode=4625
TargetUserName!="*$" IpAddress!="-"
| bucket span=10m _time
| stats dc(TargetUserName) as unique_users
        values(TargetUserName) as targeted_users
        count as total_failures
        values(WorkstationName) as workstation
        values(Status) as status
        by _time IpAddress
| where unique_users >= 12 AND total_failures >= 20
| sort - total_failures
```

Cloud veya IdP log’ları varsa, detection çok daha anlamlı biçimde aşağıdaki mantıkla genişletilmelidir: aynı IP’nin kısa sürede çok sayıda kullanıcı için sign-in failure üretmesi ve bunlardan bazılarının success ile sonuçlanması.

### Sorgunun ekran görüntüsünde neyin gösterilmesi gerektiği

Ekran görüntüsünde şu alanlar yer almalıdır:

- `_time`
    
- `IpAddress`
    
- `unique_users`
    
- `targeted_users`
    
- `total_failures`
    
- `Status`
    
- Hedef sistem veya uygulamayı gösteren alan (`ComputerName`, `Application`, `ResourceDisplayName`, `ClientApp` vb.)
    

Teknik olarak en faydalı ekran görüntüsü, tek bir kaynaktan çok sayıda kullanıcıya doğrulama denemesi yapıldığını, bunun internet-facing bir authentication surface’e yöneldiğini ve mümkünse sonrasında aynı kaynaktan bir success oluşup oluşmadığını gösterebilmelidir.

### Mitigation

MITRE’ye göre bu alt teknik için temel mitigation başlıkları **Account Use Policies**, **Multi-factor Authentication**, **Password Policies** ve **User Account Management**’dir. Özellikle ihlal edilmiş credential’ların proaktif reset edilmesi burada çok önemlidir. Fortinet, IBM ve Splunk kaynaklarının ortak vurgusu da bu yöndedir: password reuse engellenmeli, MFA zorunlu tutulmalı, kullanılmayan hesaplar temizlenmeli ve kullanıcılar veri sızıntılarının kurumsal etkisi konusunda bilinçlendirilmelidir. MFA’nın burada özel bir değeri vardır; çünkü saldırgan doğru kullanıcı adı ve parolaya sahip olsa bile ikinci faktör erişimi yoksa doğrudan oturum açması zorlaşır. [5][6][7][9]

---

## Genel çıkarım / Son değerlendirme

**Brute Force (T1110)**, ilk bakışta tek boyutlu ve “gürültülü” bir saldırı tekniği gibi görünse de, MITRE ATT&CK’in alt tekniklerine ayrıldığında bunun aslında oldukça farklı operasyonel modeller içerdiği açıkça görülmektedir. **Password Guessing**, belirli kullanıcılar veya servisler üzerinde doğrudan tahmin temelli denemeleri ifade eder. **Password Cracking**, elde edilen hash veya encrypted credential material üzerinden offline recovery mantığıyla çalışır. **Password Spraying**, aynı parolayı çok sayıda kullanıcı üzerinde dağıtarak lockout eşiklerinden kaçmayı hedefler. **Credential Stuffing** ise başka ihlallerden sızmış kullanıcı adı-parola çiftlerini kurumsal yüzeylerde tekrar doğrulamaya çalışır. [1][2][3][4][5]

Vendor kaynaklarının ortak çizgisi, brute force’un artık “eski ama kaba” bir saldırı olmaktan çıktığını göstermektedir. Splunk ve CrowdStrike otomasyon, botnet ve credential reuse boyutuna dikkat çekerken; Fortinet parola hijyeninin zayıflığının saldırının başarısında ne kadar etkili olduğunu; IBM işlem gücü, offline cracking ve modern dağıtık saldırı ölçeğini; Palo Alto Networks ise brute force’un çok aşamalı kampanyalarda kimlik altyapısını hedef alan sessiz ama ısrarlı bir erişim aracı hâline geldiğini ortaya koymaktadır. [6][7][8][9][10]

Bence savunma tarafındaki en yaygın hata, bu dört alt tekniği tek bir “failed login spike” mantığında toplamaktır. Oysa detection engineering açısından her birinin ayrı entity modeli, ayrı zaman penceresi, ayrı görünürlük gereksinimi ve ayrı korelasyon mantığı vardır. Password Guessing için tek kullanıcıya odaklı failure zinciri öne çıkarken, Password Spraying’de aynı source altında yüksek `dc(user)` değeri kritikleşir. Password Cracking’de process telemetry ve credential artifact erişimi belirleyicidir. Credential Stuffing ise ancak cloud sign-in, breach intelligence ve application-level authentication bağlamı ile anlamlı biçimde ayrıştırılabilir. [3][4][5][8]

Sonuç olarak etkili savunma, yalnızca güçlü parola politikasıyla sınırlı olamaz. **MFA**, **adaptive rate limiting**, **conditional access**, **dormant account cleanup**, **password manager adoption**, **secure password storage**, **identity telemetry centralization**, **behavior-based detection** ve **post-incident credential reset discipline** birlikte ele alınmalıdır. Brute force saldırıları çoğu zaman kapıyı kırmaz; kapının açık bırakıldığı, zayıf korunduğu veya kimlik bağlamının iyi izlenmediği yerlerden içeri girer. Bu nedenle gerçek olgunluk, brute force’u yalnızca bir giriş denemesi olarak değil, modern identity attack surface’in sürekli ve değişen bir baskı mekanizması olarak ele alabilmektir. [1][6][7][8][9][10]
