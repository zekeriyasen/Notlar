## MITRE ATT&CK System Shutdown/Reboot (T1529) nedir?


**System Shutdown/Reboot (T1529)**, MITRE ATT&CK içerisinde **Impact** taktiği altında yer alan ve saldırganın bir sistemi ya da cihazı bilinçli biçimde **shutdown** veya **reboot** ederek erişilebilirliği bozmasını ifade eden bir tekniktir. MITRE’ye göre amaç yalnızca sistemi kapatmak değildir; asıl hedef, meşru kullanıcıların bilgisayar kaynaklarına erişimini kesmek, operasyonel sürekliliği bozmak ve aynı zamanda **incident response** ile **recovery** süreçlerini zorlaştırmaktır. Bu nedenle teknik, doğrudan **Availability** üzerinde etki üreten bir davranış olarak değerlendirilir. [1

MITRE açıklamasında önemli bir nokta, bu davranışın yalnızca yerel işletim sistemi komutlarıyla sınırlı olmamasıdır. Saldırganlar, işletim sistemlerinin sunduğu yerleşik **shutdown/reboot** komutlarını kullanabileceği gibi, bazı durumlarda aynı işlemleri **remote computer** veya **network device** üzerinde de tetikleyebilir. Özellikle **Network Device CLI** üzerinden kullanılan `reload` benzeri komutlar buna örnek olarak verilmektedir. Ayrıca teknik; **hypervisor**, **cloud console** veya ilgili **command line tools** aracılığıyla bir **virtual machine**’in kapatılması ya da yeniden başlatılması şeklinde de uygulanabilir. Bu yönüyle T1529, yalnızca endpoint seviyesinde değil, sanallaştırma ve altyapı katmanında da etkili olabilen bir tekniktir. [1]

Windows tarafında MITRE, komut satırı kullanımının ötesinde daha düşük seviyeli yöntemlere de dikkat çekmektedir. Saldırganlar, **InitializeSystemShutdownExW** veya **ExitWindowsEx** gibi **Windows API** fonksiyonlarını kullanarak sistemi zorla kapatabilir ya da yeniden başlatabilir. Bunun alternatifi olarak **NtRaiseHardError** veya **ZwRaiseHardError** API fonksiyonlarının, `ResponseOption` parametresi **OptionShutdownSystem** olacak şekilde kullanılmasıyla sistemde bir **blue screen of death (BSOD)** tetiklenebilir. MITRE’ye göre bu API’lerden yararlanabilmek için saldırganın bazı durumlarda **SeShutdownPrivilege** elde etmesi gerekebilir; bu ayrıcalık da örneğin **Access Token Manipulation** gibi başka tekniklerle kazanılmış olabilir. [1]

MITRE’nin özellikle vurguladığı kritik ayrıntılardan biri de, bu tür bir shutdown/reboot işleminin bazı senaryolarda geri döndürülebilir olmamasıdır. Başka bir ifadeyle sistem kapatıldıktan veya çöktürüldükten sonra bazı durumlarda yeniden açılmayabilir. Bu da tekniğin, basit bir kesinti üretmenin ötesinde, kalıcı hizmet kaybına yol açabilen yıkıcı bir etki doğurabileceğini göstermektedir. [1]

Ayrıca MITRE’ye göre saldırganlar, sistemi başka yollarla etkiledikten sonra da shutdown/reboot davranışını kullanabilir. Özellikle **Disk Structure Wipe** veya **Inhibit System Recovery** gibi tekniklerin ardından sistemi kapatmak ya da yeniden başlatmak, yaratılan hasarın daha hızlı görünür hale gelmesini ve kurtarma sürecinin daha da zorlaşmasını sağlayabilir. Benim değerlendirmeme göre burada kritik nokta şudur: T1529 çoğu zaman saldırının başlangıç adımı değil, daha önce yürütülen destructive faaliyetlerin etkisini pekiştiren son aşamalardan biridir. Bu nedenle bu tekniği tek başına bir sistem yönetim olayı gibi değil, daha geniş bir saldırı zincirinin parçası olarak okumak gerekir. [1]

MITRE ayrıca daha agresif örneklere de yer verir. **BFG Agonizer**, elevated privileges kullanarak **NtRaiseHardError** çağrısı yapar ve enfekte sistemlerde **BSOD** oluşturur; MITRE’ye göre sistem kapandıktan sonra artık boot edilemeyebilir. **Qilin**, backup server üzerinde reboot başlatarak recovery sürecini sekteye uğratabilir. **ShrinkLocker** hata durumunda sistemi restart edebilir ve encryption sonrası kullanıcıları dışarıda bırakmak için zorla shutdown uygular. **WhisperGate** ise `ExitWindowsEx` ve `EXW_SHUTDOWN` bayrağını kullanarak sistemi kapatabilir. [1]

Diğer malware ve tool örnekleri de tekniğin yaygınlığını gösterir. **Black Basta** kurban sistemi kapatmak ve yeniden başlatmak için **ShellExecuteA** kullanmıştır. **CHIMNEYSWEEP** sistem reboot, shutdown veya user logoff gerçekleştirebilir. **DarkGate** `shutdown` komutunu kullanır. **DCSrv** iki saat bekledikten sonra reboot yapabilir. **KillDisk**, belirli process’leri sonlandırarak makineyi yeniden başlatmayı dener. **Latrodectus** compromise edilmiş host’ları restart edebilir. **LockerGoga** enfekte sistemleri kapatır. **LookBack** hem shutdown hem reboot yapabilir. **Maze**, reboot sonrasında ransomware’in bir VM içinde çalışmasını sağlamak için victim machine’de shutdown komutu uygular. **NotPetya**, enfeksiyondan bir saat sonra sistemi reboot eder. **Olympic Destroyer**, sistem yapılandırmasını değiştirdikten sonra host’u kapatır. **XLoader** da reboot veya shutdown başlatabilir. [1]

Sanallaştırma katmanında da örnekler vardır. **AvosLocker**’ın Linux varyantı **ESXi virtual machine**’lerini sonlandırmıştır. **Medusa Group** ise virtual machine’leri manuel biçimde kapatıp şifrelemiştir. Bu da T1529’un yalnızca endpoint’lerde değil, doğrudan sanal altyapı ve hizmet sürekliliği üzerinde de kullanılabildiğini göstermektedir. [1]

## Mitigations

MITRE ATT&CK’e göre bu teknik, sistemin meşru özelliklerinin kötüye kullanılmasına dayandığı için **preventive controls** ile kolay biçimde engellenebilen bir teknik değildir. Yani shutdown/reboot mekanizmaları işletim sistemlerinin ve altyapı bileşenlerinin doğal yönetim fonksiyonları olduğundan, bunları tamamen ortadan kaldırmak pratik ve güvenli bir savunma yaklaşımı değildir. MITRE bu nedenle doğrudan güçlü bir önleyici mitigation önermemekte ve tekniğin yapısı gereği bunun zor olduğunu açık biçimde belirtmektedir. [1]

Benim değerlendirmeme göre burada savunma yaklaşımı daha çok ayrıcalıkların sınırlandırılması, yetkili kullanıcı bağlamlarının netleştirilmesi ve shutdown/reboot işlemlerinin operasyonel olarak izlenmesi üzerine kurulmalıdır; ancak MITRE’nin doğrudan verdiği ana mesaj, bu tekniğin saf önleyici kontrollerle kolayca bastırılamayacağıdır. [1]


---
## Detection Strategy

MITRE, bu teknik için **DET0559 – Multi-Platform Shutdown or Reboot Detection via Execution and Host Status Events** yaklaşımını önerir. Bu yaklaşımın özü, shutdown/reboot komutlarının ya da çağrılarının tek başına görülmesiyle yetinmemek; bunları **host status change logs**, **identity context**, **administrative context** ve **maintenance window** bilgisiyle ilişkilendirmektir. Bence burada en kritik nokta, T1529’un meşru sistem yönetimiyle çok kolay karışabilmesidir. Dolayısıyla “shutdown komutu çalıştı” tespiti tek başına düşük bağlamlıdır; fakat bu komut beklenmeyen bir kullanıcı, beklenmeyen bir zaman dilimi ve beklenmeyen bir hedef sistem üzerinde görülüyorsa analitik değeri ciddi biçimde yükselir. [1]

MITRE’nin verdiği analitikler platform bazında şu mantığa dayanır:

**AN1538**, Windows ortamında `shutdown.exe`, `restart-computer` gibi process execution olaylarını **Event ID 1074** ve **Event ID 6006** gibi host durum kayıtlarıyla korele etmeyi; ayrıca bu aktivitenin ilgili kullanıcının normal idari rolüyle uyumlu olup olmadığını değerlendirmeyi önerir. Örneğin kullanıcı **Helpdesk** veya yetkili operasyon gruplarında değilse, shutdown davranışı daha anlamlı hâle gelir. [1]

**AN1539**, Linux tarafında `shutdown`, `reboot` veya `systemctl poweroff` gibi yürütmeleri **auditd** veya **syslog** ile görmeyi ve bunları planlı bakım pencereleriyle ya da onaylı kullanıcı bağlamıyla karşılaştırmayı önerir. 

**AN1540**, macOS üzerinde `shutdown`, `reboot` veya `osascript` tabanlı sistem kapatma çağrılarını **unified logs** içinde izlemeyi ve GUI ya da script kaynaklı beklenmeyen shutdown zincirlerini takip etmeyi amaçlar. 

**AN1541**, ESXi tarafında `esxcli system shutdown` veya `vim-cmd vmsvc/power.shutdown` komutlarını, özellikle maintenance window dışında veya sıra dışı kullanıcılar tarafından çalıştırıldığında şüpheli kabul eder; **hostd.log** ve shell log korelasyonu önerir. **AN1542** ise network device ortamında maintenance window dışında verilen `reload` komutlarının **TACACS+/AAA** loglarıyla eşleştirilmesini ve privilege validation yapılmasını önerir. [1]


Detection açısından benim değerlendirmeme göre beş bağlam boyutu özellikle önemlidir. Birincisi **process lineage**’dır; `shutdown.exe`’nin doğrudan interaktif bir admin shell’inden mi, yoksa bir malware sürecinden mi doğduğu fark yaratır. İkincisi **identity context**’tir; işlemi başlatan kullanıcı veya service account’un normal görev alanı sorgulanmalıdır. Üçüncüsü **privilege context**’tir; özellikle Windows API temelli zorlayıcı shutdown senaryolarında **SeShutdownPrivilege** kazanımı veya anormal token kullanımı ayrıca incelenmelidir. Dördüncüsü **time correlation**’dır; shutdown’dan kısa süre önce wiping, backup silme, log temizleme veya recovery engelleme gibi aktiviteler görüldüyse olayın önemi artar. Beşincisi ise **host outcome correlation**’dır; gerçekten 1074, 6006, 6008, service stop veya host offline olayı ile sonuçlanıp sonuçlanmadığı doğrulanmalıdır. [1]


Windows tarafında pratik detection çoğu zaman üç telemetry katmanının birlikte değerlendirilmesiyle daha anlamlı hâle gelir:

1. **process creation telemetry**: `shutdown.exe`, `powershell.exe`, `wmic.exe`, `cmd.exe` gibi süreçler ve bunların command line’ları,
2. **system state telemetry**: **Event ID 1074**, **6006**, **6008** gibi kapanma/yeniden başlama ilişkili kayıtlar,
3. **EDR behavior telemetry**: özellikle **InitializeSystemShutdownExW**, **ExitWindowsEx**, **NtRaiseHardError**, **ZwRaiseHardError** gibi API çağrılarını veya bu çağrılardan önceki privilege escalation/token abuse izlerini görünür kılan veriler.

Önemli bir teknik ayrıntı şudur: native Windows logları çoğu zaman API düzeyindeki her davranışı doğrudan göstermeyebilir. Bu nedenle yalnızca `shutdown.exe` arayan kurallar, **BFG Agonizer** veya **WhisperGate** benzeri davranışların tamamını kapsamayabilir. Bu gibi senaryolarda EDR tabanlı process behavior ve API telemetry daha değerlidir. [1]



---

## Test Ortamında Attack ve Detection Senaryoları

Bu teknik için teorik çerçeveyi MITRE ATT&CK üzerinden değerlendirdikten sonra, davranışın laboratuvar ortamında nasıl üretilebileceğini ve nasıl tespit edilebileceğini göstermek daha anlamlıdır. Benim test ortamımda bir **Domain Controller (DC)** ve buna bağlı birden fazla **Windows client** sistem bulunmaktadır. Domain yapısının merkezi bileşeni olan **Domain Controller**, kimlik doğrulama, merkezi yönetim ve etki alanı servisleri açısından kritik olduğu için, bu bölümdeki senaryolar özellikle **DC üzerinde** ele alınmıştır. Böylece **System Shutdown/Reboot (T1529)** tekniğinin yalnızca teorik değil, operasyonel açıdan neden önemli olduğu da daha görünür hâle gelmektedir.

Bu senaryolarda saldırı simülasyonu için **Atomic Red Team** kullanılmıştır. Atomic Red Team’in sağladığı hazır testler, MITRE ATT&CK tekniklerini kontrollü ve tekrarlanabilir şekilde doğrulamak açısından oldukça faydalıdır. Bu teknik özelinde Windows tarafında doğrudan uygulanabilir iki temel test öne çıkmaktadır: biri sistemin kapatılması, diğeri ise sistemin yeniden başlatılmasıdır. Her iki test de sistem üzerinde doğrudan **Availability impact** oluşturduğu için T1529’un laboratuvar düzeyinde gösterilmesi açısından uygundur. Detection tarafında ise veri kaynağı olarak **Windows Security Event ID 4688** kullanılmıştır. Böylece komutun gerçekten hangi kullanıcı ve hangi süreç bağlamında çalıştırıldığı Splunk üzerinden izlenebilmektedir.

Benim değerlendirmeme göre burada özellikle **DC’nin hedef alınması** yazıya teknik ağırlık kazandırmaktadır. Çünkü sıradan bir istemcinin kapanması yerel etki üretirken, **Domain Controller** üzerinde gerçekleşen shutdown veya reboot davranışı, kimlik doğrulama akışından yönetim süreçlerine kadar daha geniş bir operasyonel bozulma anlamına gelebilir. Bu nedenle senaryoların DC üzerinde kurgulanması, T1529’un gerçek hayattaki etkisini daha iyi yansıtmaktadır.


## 1. Atomic Red Team Attack – Shutdown System on Domain Controller

İlk senaryoda **Atomic Red Team – Atomic Test #1: Shutdown System - Windows** kullanılarak Domain Controller üzerinde sistem kapatma davranışı üretilmiştir. Bu test, Windows sistemini kapatmak için yerleşik `shutdown.exe` aracını kullanır ve aşağıdaki komutu çalıştırır: [2]

```cmd
shutdown /s /t 1
```

Burada `/s` parametresi sistemin kapatılmasını, `/t 1` ise bu işlemin 1 saniyelik gecikmeyle gerçekleştirilmesini ifade etmektedir. Testin **elevated privileges** gerektirmesi, shutdown davranışının pratikte belirli bir yetki seviyesiyle ilişkili olduğunu göstermesi bakımından önemlidir. Domain Controller üzerinde bu komutun çalıştırılması, yalnızca ilgili host’un kapanmasına değil, aynı zamanda etki alanı servislerinin sürekliliğinin de bozulmasına yol açmaktadır. Bu durum, T1529’un neden MITRE ATT&CK içinde doğrudan **Impact** taktiği altında sınıflandırıldığını pratik olarak da doğrulamaktadır. [1][2]

Detection tarafında bu davranış, **Windows Security Event ID 4688** kayıtları üzerinden rahatlıkla izlenebilmektedir. Çünkü `shutdown.exe` sürecinin oluşturulması, eğer process creation logging etkinse, Security log’larda açık biçimde görünmektedir. Bu amaçla kullanılan Splunk sorgusu aşağıdaki gibidir:

Kural İsmi : **Windows System Shutdown CommandLine**

Bu kural, **Windows Security Event ID 4688** kayıtları üzerinden **`shutdown.exe`** sürecinin komut satırı aracılığıyla çalıştırılmasını tespit etmeyi amaçlar. Özellikle komut satırında **`/s`** veya **`-s`** parametreleri ile birlikte **`/t`**, **`-t`**, **`/f`** veya **`-f`** gibi ek parametrelerin kullanılması, sistemin kapatılmasına veya zorla kapatılmasına yönelik bir işlem gerçekleştirildiğini gösterir.

Bu davranış güvenlik açısından önemlidir; çünkü saldırganlar **shutdown** komutunu sistem üzerinde kesinti oluşturmak, izlerini azaltmak, güvenlik kontrollerini dolaylı olarak devre dışı bırakmak veya zararlı değişikliklerin etkili olabilmesi için sistemi kapatmak ya da yeniden başlatmak amacıyla kullanabilir. Eğer aktivite kötü niyetli ise, bu durum **sistem kesintisine**, **denial of service** etkisine veya **Impact** taktiği kapsamında değerlendirilebilecek başka zararlı sonuçlara yol açabilir.

Bu analytic, orijinal Splunk içeriğindeki EDR ve Endpoint datamodel yaklaşımı yerine, doğrudan **Windows Security 4688 process creation** logları üzerinden çalışacak şekilde uyarlanmıştır.

#### Splunk SPL - 4688 üzerinden Shutdown tespiti

![](Ekran%20görüntüsü%202026-04-08%20113325.png)

![](Ekran%20görüntüsü%202026-04-08%20113509.png)


Kaynak => https://research.splunk.com/endpoint/4fee57b8-d825-4bf3-9ea8-bf405cdb614c/


## Known False Positives

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Sistem yöneticileri, bakım süreçleri, güncelleme operasyonları, uzaktan destek çalışmaları veya planlı yeniden başlatma işlemleri sırasında **shutdown.exe** komutunu bilinçli şekilde çalıştırabilir.

Bu nedenle alarm oluştuğunda, işlemi yapan kullanıcının rolü, hedef sistemin kritikliği, komutu başlatan **parent process** ve aktivitenin planlı bir operasyon kapsamında olup olmadığı doğrulanmalıdır.

---

## 2. Atomic Red Team Attack – Restart System on Domain Controller

İkinci senaryoda **Atomic Red Team – Atomic Test #2: Restart System - Windows** kullanılarak Domain Controller üzerinde sistem yeniden başlatma davranışı üretilmiştir. Bu testte kullanılan komut aşağıdaki gibidir: [2]

```cmd
shutdown /r /t 1
```

Burada `/r` parametresi sistemin yeniden başlatılmasını ifade eder. Shutdown senaryosundan farklı olarak bu test, sistemi tamamen kapatmak yerine reboot sürecine sokar. Ancak teknik açıdan bunun etkisi küçümsenmemelidir. Özellikle **Domain Controller** gibi kritik bir sistemde beklenmeyen reboot davranışı, kimlik doğrulama akışında kesinti, oturum problemleri, yönetim gecikmeleri ve bazı durumlarda daha önce uygulanmış destructive değişikliklerin devreye girmesi gibi sonuçlar doğurabilir. Bu nedenle reboot davranışı da T1529 kapsamında güçlü bir **Impact** göstergesidir. [1][2]

Bence burada kritik nokta şudur: shutdown ve reboot ilk bakışta benzer görünse de savunma açısından aynı şey değildir. Shutdown bazı durumlarda doğrudan hizmet kesintisi üretirken, reboot özellikle saldırganın sistem üzerinde yaptığı değişikliklerin yeniden açılış sonrasında etkili olmasını sağlamak için kullanılabilir. MITRE’nin wiper ve destructive malware örneklerinde reboot davranışının sık görülmesinin nedeni de budur. [1]

Kural ismi : Windows System Shutdown CommandLine

Bu kural, **Windows Security Event ID 4688** kayıtlarını kullanarak **`shutdown.exe`** sürecinin komut satırı üzerinden **yeniden başlatma (reboot)** amacıyla çalıştırılmasını tespit etmeyi amaçlar. Özellikle komut satırında **`/r`** veya **`-r`** parametreleri ile birlikte **`/t`**, **`-t`**, **`/f`** veya **`-f`** gibi ek parametrelerin kullanılması, hedef sistemin yeniden başlatılmasına yönelik bir işlem gerçekleştirildiğini gösterir.

Bu davranış güvenlik açısından önemlidir; çünkü saldırganlar sistemi yeniden başlatma komutlarını operasyonel kesinti oluşturmak, zararlı değişikliklerin etkili olmasını sağlamak, savunma mekanizmalarını zayıflatmak veya olay müdahale sürecini zorlaştırmak amacıyla kullanabilir. Özellikle **APT**, **RAT** ve destructive malware senaryolarında sistemin yeniden başlatılması, daha büyük bir saldırı zincirinin parçası olabilir.

Bu kural, işlemi başlatan kullanıcıyı, hedef sistemi, parent process bilgisini ve komut satırı içeriğini birlikte görünür hâle getirerek olayın meşru bir yönetimsel işlem mi yoksa kötü niyetli bir eylem mi olduğunu değerlendirmeye yardımcı olur.


### Splunk SPL – 4688 üzerinden Reboot tespiti
![](Ekran%20görüntüsü%202026-04-08%20114633%201.png)
![](Ekran%20görüntüsü%202026-04-08%20114650.png)

Burada dikkat edilmesi gereken nokta, `process_command_line` alanı içinde **`shutdown.exe /r`** veya **`shutdown.exe -r`** benzeri ifadelerin açık şekilde görülmesidir. Buna ek olarak `parent_process` alanı da önemlidir; çünkü komutun **cmd.exe**, **powershell.exe**, uzak yönetim aracı veya şüpheli bir süreç tarafından başlatılıp başlatılmadığı anlaşılabilir.


## False Positive Durumu

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Sistem yöneticileri bakım, güncelleme, patch yönetimi, uzaktan destek veya planlı yeniden başlatma süreçleri kapsamında **shutdown.exe** komutunu yeniden başlatma parametreleri ile çalıştırabilir.

Bununla birlikte üretim ortamında beklenmeyen kullanıcılar tarafından, sıra dışı saatlerde veya şüpheli parent process’ler üzerinden gerçekleştirilen reboot komutları normal kabul edilmemelidir. Bu nedenle alarm oluştuğunda işlemi yapan kullanıcının rolü, hedef sistemin kritikliği, parent process’in niteliği ve aktivitenin planlı bir operasyon kapsamında olup olmadığı birlikte değerlendirilmelidir.



## Not

Bu kuralın sağlıklı çalışması için **4688 içinde Process Command Line** bilgisinin loglanıyor olması gerekir. Aksi halde yalnızca `shutdown.exe` çalıştığı görülebilir; ancak bunun gerçekten reboot amacıyla mı kullanıldığı net biçimde doğrulanamaz.


Kaynak => https://research.splunk.com/endpoint/97fc2b60-c8eb-4711-93f7-d26fade3686f/

---


## 3. Atomic Red Team Attack – Logoff System

Shutdown ve reboot senaryolarının ardından, bu teknik kapsamında daha hafif ama yine anlamlı bir davranış olarak **logoff** işlemi de ele alınabilir. Atomic Red Team’in T1529 listesinde yer alan **Atomic Test #12: Logoff System - Windows**, tam da bu tür bir davranışı temsil eder. Test, Windows sisteminde kullanıcı oturumunu sonlandırmak için yerleşik shutdown aracını **`shutdown /l`** parametresiyle kullanır; Mandiant örneği olarak DarkGate benzeri araçlarda görülen logoff kabiliyetine referans verir. Kaynağa göre bu test, dcrat gibi bazı arka kapı yazılımlarının gördüğü logoff yeteneğini simüle eder.

Bu davranışın operational bağlamı, shutdown/reboot kadar yıkıcı olmasa da saldırganın ortamda varlığını gizleme, kullanıcının oturumunu kesme, ya da belirli bir süreçten çıkma amaçlarına hizmet edebilir.




Kural İsmi : **Windows System LogOff CommandLine**

Bu kural, **Windows Security Event ID 4688** kayıtlarını kullanarak **`shutdown.exe`** sürecinin komut satırı üzerinden **logoff** amacıyla çalıştırılmasını tespit etmeyi amaçlar. Özellikle komut satırında **`/l`** veya **`-l`** parametreleri ile birlikte **`/t`**, **`-t`**, **`/f`** veya **`-f`** gibi ek parametrelerin kullanılması, hedef sistem üzerindeki oturumun sonlandırılmasına yönelik bir işlem gerçekleştirildiğini gösterir.

Bu davranış güvenlik açısından önemlidir; çünkü saldırganlar sistem üzerindeki aktif kullanıcı oturumlarını sonlandırarak operasyonel kesinti oluşturabilir, kullanıcı müdahalesini engelleyebilir, olay müdahale sürecini zorlaştırabilir veya saldırı sonrası etkilerini artırabilir. Özellikle **APT**, **RAT** ve destructive malware senaryolarında bu tür komutlar, daha büyük bir saldırı zincirinin parçası olarak kullanılabilir.

Bu kural, işlemi başlatan kullanıcıyı, hedef sistemi, parent process bilgisini ve komut satırı içeriğini birlikte görünür hâle getirerek olayın meşru bir yönetimsel işlem mi yoksa kötü niyetli bir müdahale mi olduğunu değerlendirmeye yardımcı olur.



#### Splunk SPL - 4688 üzerinden Logoff tespiti

![](Ekran%20görüntüsü%202026-04-08%20132802.png)

![](Ekran%20görüntüsü%202026-04-08%20132816.png)



Burada dikkat edilmesi gereken nokta, `process_command_line` alanı içinde **`shutdown.exe /l`** veya **`shutdown.exe -l`** benzeri ifadelerin açık şekilde görülmesidir. Buna ek olarak `parent_process` alanı da önemlidir; çünkü komutun **cmd.exe**, **powershell.exe**, uzak yönetim aracı veya şüpheli bir süreç tarafından başlatılıp başlatılmadığı anlaşılabilir.




## False Positive Durumu

Bu kural bazı durumlarda meşru yönetimsel işlemler nedeniyle tetiklenebilir. Sistem yöneticileri, uzaktan destek ekipleri, bakım süreçleri veya oturum temizliği amacıyla çalışan otomasyon mekanizmaları **shutdown.exe** komutunu logoff amacıyla kullanabilir.

Bununla birlikte üretim ortamında beklenmeyen kullanıcılar tarafından, sıra dışı saatlerde veya şüpheli parent process’ler üzerinden gerçekleştirilen logoff komutları normal kabul edilmemelidir. Bu nedenle alarm oluştuğunda işlemi yapan kullanıcının rolü, hedef sistemin kritikliği, parent process’in niteliği ve aktivitenin planlı bir operasyon kapsamında olup olmadığı birlikte değerlendirilmelidir.


Kaynak => https://research.splunk.com/endpoint/74a8133f-93e7-4b71-9bd3-13a66124fd57/





MİTRE ATTACK DA BELİRTİLEN DETECTİON STRATEJİSİNE GÖRE YAZDIM. 

```
(index=winsec host="ABM-DC" source="XmlWinEventLog:Security" EventCode=4688)
OR
(index=winsec host="ABM-DC" source="XmlWinEventLog:System" EventCode IN (1074,6006))
| eval process_name=coalesce(NewProcessName,new_process_name,new_process)
| eval process_command_line=coalesce(Process_Command_Line,process_command_line_arguments)
| eval parent_process=coalesce(parent_process_path,parent_process,ParentProcessName)
| eval user=coalesce(SubjectUserName,user)
| eval dest=coalesce(dest,host,Computer)
| eval event_type=case(
    EventCode=4688 AND like(lower(process_name), "%shutdown.exe"), "shutdown_command",
    EventCode=1074, "shutdown_request_logged",
    EventCode=6006, "eventlog_service_stopped"
)
| where event_type="shutdown_command" OR EventCode IN (1074,6006)
| bin _time span=5m
| stats
    values(event_type) as event_type
    values(process_name) as process_name
    values(process_command_line) as process_command_line
    values(parent_process) as parent_process
    values(user) as user
  by _time dest
| table _time dest user process_name process_command_line parent_process event_type
| sort - _time
```

![](Ekran%20görüntüsü%202026-04-08%20135611.png)

![](Ekran%20görüntüsü%202026-04-08%20135751.png)

### Kural Açıklaması

Bu sorgu, **MITRE ATT&CK T1529 – System Shutdown/Reboot** tekniğinin detection yaklaşımında özellikle vurgulanan **host status change** mantığını Windows ortamında görünür kılmak amacıyla hazırlanmıştır. Sorgu, **Windows Security Event ID 4688** üzerinden `shutdown.exe` sürecinin çalıştırılmasını tespit ederken, aynı zamanda **Windows System log** içindeki **Event ID 1074** ve **Event ID 6006** kayıtlarını da aynı zaman aralığında birlikte değerlendirir. Böylece yalnızca kapatma veya yeniden başlatma niyeti taşıyan komutun çalıştırıldığını göstermekle kalmaz; bu davranışın host üzerinde gerçek bir shutdown/reboot sürecine dönüşüp dönüşmediğine dair ek bağlam da sağlar.

Sorgunun temel mantığı, `shutdown.exe` yürütmesini **process execution** katmanında, **Event ID 1074** ile sistem kapatma/yeniden başlatma isteğinin loglanmasını **host action** katmanında ve **Event ID 6006** ile Event Log servisinin durmasını **shutdown sequence** katmanında birlikte ele almaktır. Benim değerlendirmeme göre bu yaklaşım, sadece komut satırı odaklı bir tespitten daha değerlidir; çünkü saldırganın `shutdown.exe` çalıştırmış olması ile sistemin gerçekten kapanma hattına girmesi arasında analitik olarak anlamlı bir ayrım kurar. Özellikle **Domain Controller** gibi kritik sistemlerde bu tür bir korelasyon, olayın operasyonel önemini daha doğru yansıtır.

Bu sorgu aynı zamanda shutdown/reboot davranışını zaman penceresi içinde **host bazlı** gruplandırarak, aynı sistem üzerinde gerçekleşen ilişkili event’lerin tek bir bağlam içinde görülmesini sağlar. Bu yönüyle, T1529 tekniğinin yalnızca komut çalıştırma boyutunu değil, sistem seviyesindeki etkisini de görünür kılan bağlamsal bir detection yaklaşımı sunmaktadır.

### False Positive Durumu

Bu sorgu her ne kadar saldırgan davranışını görünür kılmak için güçlü bir bağlam sağlasa da, özellikle **sistem yöneticileri**, **Helpdesk ekipleri**, **bakım operasyonları**, **patch yönetimi**, **sunucu yeniden başlatma süreçleri** ve **planlı shutdown işlemleri** sırasında da benzer telemetry üretilebilir. Özellikle kritik sunucular üzerinde gerçekleştirilen planlı bakım faaliyetlerinde `shutdown.exe` çalıştırılması ve bunu izleyen **1074** veya **6006** kayıtlarının görülmesi tamamen meşru olabilir.

Bu nedenle sonuçlar değerlendirilirken yalnızca event’in kendisine değil; işlemi başlatan **kullanıcı hesabına**, **parent process** bağlamına, olayın gerçekleştiği **zaman aralığına**, sistemin o anda planlı bakım kapsamında olup olmadığına ve hedef host’un operasyonel rolüne de bakılmalıdır. Bence burada en kritik nokta, bu sorgunun tek başına “kesin kötü niyetli aktivite” üretmekten çok, **yüksek bağlamlı bir inceleme başlangıç noktası** sağlamasıdır. Özellikle bakım penceresi dışındaki, yetkisiz kullanıcı bağlamında gerçekleşen veya beklenmeyen parent process ilişkileriyle görülen shutdown/reboot olayları daha yüksek önemde ele alınmalıdır.

