
# Saldırganlar Neden Windows Event Logs Kayıtlarını Temizler?

Bir saldırgan bir sisteme erişim sağladıktan sonra çoğu zaman ilk hedef yalnızca persistence elde etmek ya da privilege escalation sağlamak olmaz. En az bunun kadar kritik olan bir diğer hedef, gerçekleştirdiği faaliyetlerin mümkün olduğunca az iz bırakmasını sağlamaktır. Windows ortamlarda bu izlerin en önemli kaynaklarından biri **Windows Event Logs** yapısıdır. Çünkü bir sistem üzerinde gerçekleşen authentication denemeleri, process execution, service activity, system errors ve security-relevant ile birlikte birçok davranış bu kayıt mekanizması üzerinden görünür hale gelir. Bu nedenle bir adversary açısından **Windows Event Logs**, yalnızca pasif bir kayıt alanı değil; aynı zamanda tespit edilme ihtimalini artıran doğrudan bir forensic evidence kaynağıdır. [1]  [2]  [3] 
![](Pasted%20image%2020260404112619.png)

**MITRE ATT&CK** çerçevesinde bu davranış, **Indicator Removal: Clear Windows Event Logs (T1070.001)** alt tekniği altında ele alınmaktadır ve **Defense Evasion** taktiği kapsamında sınıflandırılmaktadır. 
# Indicator Removal: Clear Windows Event Logs

Adversary’ler bir intrusion faaliyetinin izlerini gizlemek amacıyla **Windows Event Log** kayıtlarını temizleyebilir. [1]

Bu çerçeve önemli bir şeyi açıkça gösterir: burada saldırgan yeni bir foothold elde etmeye çalışmaktan çok, daha önce gerçekleştirdiği intrusion activity’yi görünmez kılmaya çalışmaktadır. Başka bir ifadeyle bu teknik çoğu zaman saldırının başlangıcı değil, saldırının izlerini bastırma aşamasıdır. [1][2][3]

Benim açımdan bu tekniği kritik yapan nokta şudur: log temizleme yalnızca birkaç satırın silinmesi değildir. Aslında defender tarafının olayları zaman sıralı biçimde ilişkilendirme, hangi account’un ne zaman devreye girdiğini anlama ve saldırı zincirini geriye dönük inşa etme yeteneğine doğrudan müdahaledir. Bu yüzden **Clear Windows Event Logs**, küçük bir sistemsel işlem gibi görünse de etkisi çoğu zaman olay müdahalesinin merkezine dokunur.
### Windows Event Logs neden bu kadar kritiktir?

**Windows Event Logs**, bir bilgisayar üzerindeki uyarıların ve bildirimlerin kaydını tutan yapılardır.  

Mitre Attack'da belirtildiği üzere sistem tarafından tanımlanan üç temel olay kaynağı bulunmaktadır: **System, Application ve Security.** 
Bunun yanında olaylar; **Error**, **Warning**, **Information**, **Success Audit** ve **Failure Audit** gibi farklı türlerde kayıt altına alınır.
Bu yapı, yalnızca bir olayın gerçekleşip gerçekleşmediğini değil; olayın sistemsel mi, uygulama bazlı mı yoksa doğrudan security context içinde mi değerlendirilmesi gerektiğini de anlamamızı sağlar.

Özellikle **Security log**, blue team ve incident response ekipleri için yüksek analitik değere sahiptir. Authentication girişimleri, audit records, object access davranışları ve çeşitli security-relevant olaylar burada görünür hale gelir. Bu nedenle saldırgan log temizlediğinde yalnızca bir dosyaya değil, doğrudan savunma tarafının gözlem yeteneğine saldırmış olur. Bence bu tekniği doğru okumak için onu “kanıt silme”den biraz daha geniş görmek gerekir; burada asıl hedef, olayların anlamlı bir hikâye oluşturmasını engellemektir.

### Clear Windows Event Logs Nasıl Çalışır?

Bu tekniğin pratikte nasıl işlediğini anlamak, savunma tarafı açısından yalnızca teorik bilgi değildir; doğrudan detection engineering perspektifi sağlar. Saldırganlar bu tekniği birkaç farklı yol üzerinden uygulayabilir. [2]

#### Built-in utilities ile log temizleme 

En bilinen yöntem, Windows’un yerleşik aracı olan **wevtutil** kullanımıdır. 

Microsoft’un komut dokümantasyonu da `wevtutil` aracının log’ları listeleme, dışa aktarma ve **clear** etme amacıyla kullanılabildiğini doğrular. (https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil?utm_source=chatgpt.com)

**Administrator privileges** elde etmiş bir adversary, aşağıdaki gibi komutlarla ilgili log’ları temizleyebilir: [1]  [2]

```powershell
wevtutil cl system
wevtutil cl application
wevtutil cl security
```

Bu komutların tehlikeli tarafı, ek bir araç ya da dışarıdan indirilen bir binary gerektirmemesidir. Saldırgan, doğrudan işletim sisteminin sunduğu native bir utility kullanır. Bu da davranışı daha az dikkat çekici hale getirebilir. Özellikle **Living off the Land** yaklaşımı benimseyen saldırganlar için bu tip native utilities oldukça değerlidir.

Saldırganların yalnızca tek tek log’ları değil, sistemdeki tüm log’ları otomasyonla temizleyebildiğini de göstermektedir. Örneğin PowerShell ile:

```Powershell
wevtutil el | Foreach-Object {wevtutil cl $_}
```

ve cmd üzerinden:

```cmd
for /F "tokens=*" %1 in ('wevtutil.exe el') DO wevtutil.exe cl "%1"
```
kullanımı, sistemde listelenen log’ların topluca temizlenmesini sağlar. (https://viperone.gitbook.io/pentest-everything/everything/everything-active-directory/defense-evasion/indicator-removal/clear-windows-event-logs)

![](Pasted%20image%2020260405154220.png)

Mitre Attack, bu kullanımın yalnızca teorik olmadığını; **APT28** gibi grupların lateral movement sonrasında tam olarak bu komutları kullandığının gözlemlendiğini aktarmaktadır. [1]

Benzer şekilde **APT41** ve **FIN8**’in de post-compromise cleanup sürecinde **Security** ve **System** log’larını temizlediği belirtilmektedir. **NotPetya** ise destrüktif operasyonlarının bir parçası olarak event log’ları silmiştir. [2]

Not : burayı ve mitre attack procudere exapmle at kullananlar hakkında bir yazı yazdır buraya. 

Burada dikkat çekici olan nokta şudur: log temizleme davranışı farklı saldırı tiplerinde ortaklaşabilmektedir. Nation-state actor, ransomware operator ya da daha genel cybercriminal profilleri arasında amaçlar farklı olsa bile, izleri azaltma ihtiyacı aynıdır.

#### Powershell Tabanlı Log Temizleme

Log temizleme yalnızca **wevtutil** ile sınırlı değildir.  Mitre Attack'a göre log’lar **Event Viewer GUI** veya **PowerShell** üzerinden de temizlenebilir. 


Saldırganlar, **PowerShell** üzerinden daha hedefli ya da daha esnek işlemler için Powershell cmdlet'lerini kullanabilir.  

Örneğin aşağıdaki komut, belirli log’ların içeriğini temizler: [1][3]

```Powershell
Clear-EventLog -LogName Application,Security,System
```

Buna karşılık adversary'ler, **Security EventLog** kaydını silmek ve yeniden başlatma sonrasında gelecekteki loglamayı devre dışı bırakmak amacıyla **PowerShell** üzerinde aşağıdaki komutu kullanabilir:

```powershell
Remove-EventLog -LogName Security
```
Bu komut, ilgili log'un silinmesine ve sistem reboot edilene kadar future logging davranışının etkilenmesine, devre dışı bırakılmasına yol açabilir. 

**Not:** Komutun çalıştırıldığı an ile sistemin yeniden başlatıldığı an arasındaki süreçte, olaylar yine de üretilebilir ve **.evtx** dosyası içerisine kaydedilebilir.[1]

Bu ayrıntı savunma açısından kritik bir fırsat penceresi yaratır. Yani saldırgan log temizleme girişiminde bulunmuş olsa bile, olayların tamamı her zaman anında yok olmaz. Özellikle merkezi log toplama veya host-level telemetry mevcutsa, log temizleme girişiminin kendisi bile güçlü bir detection sinyaline dönüşebilir.

İkinci kaynağa göre **ShrinkLocker** ransomware, özellikle **Windows PowerShell** ve **Microsoft-Windows-PowerShell/Operational** log’larını temizleyerek PowerShell tabanlı execution trail’i gizlemeye çalışmıştır. Bu örnek, saldırganın yalnızca genel log’ları değil, kendi kullandığı execution path ile en doğrudan ilişkili log kaynaklarını da hedef alabildiğini göstermesi açısından önemlidir. [2]

#### Doğrudan .evtx Dosyalarını Silme veya Bozma

Bazı adversary’ler Windows API üzerinden log temizlemek yerine, doğrudan fiziksel kayıtlı log dosyalarını doğrudan aşağıdaki dizin içerisinden silerek de logları temizlemeye çalışabilir: [1]

```text
C:\Windows\System32\winevt\logs\
```

Bu yaklaşım, API tabanlı temizlemeye kıyasla daha “sert” bir anti-forensics davranışıdır. Çünkü burada yalnızca log içeriği değil, bizzat dosyanın kendisi hedef alınır.

İkinci kaynakta **LockBit 3.0**’ın log dosyalarını sildiği, **RansomHub**’ın **Security**, **System** ve **Application** log’larından olayları kaldırdığı, **Mafalda** malware’inin ise doğrudan **OpenEventLogW** ve **ClearEventLogW** Windows API fonksiyonlarını kullandığı ifade edilmektedir. [2]

Benim çıkarımıma göre bu fark önemlidir: bazı saldırganlar işletim sistemiyle “uyumlu” görünen yöntemler kullanırken, bazıları ise daha agresif ve doğrudan disk üzerindeki artifact’leri hedef alır. Bu ayrım, detection stratejisini de değiştirir. Çünkü birinde process/command-line telemetry öne çıkarken, diğerinde file system monitoring ve API-level visibility daha kritik hale gelir.

#### Metasploit ve diğer post-exploitation araçları

Bu teknik her zaman yalnızca native administration komutlarıyla uygulanmaz. **Metasploit Meterpreter** bağlamında geçen `clearev` komutu, **Application**, **System** ve **Security** log’larını temizlemek için kullanılan doğrudan bir post-exploitation yeteneğidir. (https://viperone.gitbook.io/pentest-everything/everything/everything-active-directory/defense-evasion/indicator-removal/clear-windows-event-logs)

![](Pasted%20image%2020260405154151.png)




Bu ayrıntı yazının akışı açısından kritik bir yere oturuyor. Çünkü bu bize şunu gösterir: log temizleme, kimi zaman sistem yöneticisi araçlarının kötüye kullanılması, kimi zaman da saldırı framework’lerinin hazır fonksiyonları üzerinden gerçekleşir. Yani defender tarafı yalnızca `wevtutil` aramakla yetinmemeli; genel post-exploitation behavior zinciri içinde bu tekniği de değerlendirmelidir.

### Selective log manipulation

İkinci kaynağın en dikkat çekici katkılarından biri, her saldırganın tüm log’u silmediğini vurgulamasıdır. Örneğin **Volt Typhoon**, bazı durumlarda tüm log’u tamamen boşaltmak yerine belirli intrusion activity ile ilişkili log girdilerini seçici biçimde kaldırmıştır. Bu; failed logons, service installation events ya da process creation records gibi adversary activity ile bağlantılı kayıtların silinmesi, buna karşılık benign görünen kayıtların bırakılması anlamına gelir. [2]

Bu yaklaşım çok daha sofistike görünmektedir. Çünkü tamamen boşalmış bir log, savunma tarafında doğrudan şüphe yaratır. Buna karşılık seçici silme, yüzeyde “normal görünen” ama forensic açıdan bozulmuş bir veri kümesi üretir. Bence bu nokta çok önemlidir: modern adversary behavior yalnızca iz bırakmamak değil, bazen “makul derecede normal görünen” bir iz seti bırakmaktır. Bu da analyst’in işini belirgin biçimde zorlaştırır.

### Malware rutinleri içinde otomatik cleanup

Birçok malware family, log temizlemeyi manuel bir operatör eylemi olarak değil, post-execution routine’in parçası olarak uygular. İkinci kaynağa göre **Meteor** wiper, **Security**, **System** ve **Application** log’larını **wevtutil** ile kaldırabilmektedir. **Lucifer** botnet malware, event log’ları operasyonel güvenlik amacıyla temizlemekte; **SynAck** ise encryption işlemlerinin ardından log’ları silmektedir. [2]

Bu durum şu açıdan önemlidir: saldırgan interactive access’i kaybetse bile anti-forensics davranışı devam edebilir. Yani log silme her zaman klavye başında bir operatörün yaptığı manuel bir işlem olarak düşünülmemelidir; bazen saldırı zincirinin sonunda otomatik çalışan bir kod bloğudur.


## Gerçek dünya örnekleri ne anlatıyor?

Bu tekniğin gerçek saldırılarda tekrar tekrar görülmüş olması, onun yalnızca teorik bir **ATT&CK** maddesi olmadığını; sahada yerleşik bir **anti-forensics** pratiği haline geldiğini göstermektedir.

**MITRE ATT&CK Procedure Examples** bölümü de bunu açık biçimde doğrular. İlgili bölümde yalnızca birkaç örnek değil, farklı operasyonel profillere ait geniş bir küme yer alır: **APT28**, **APT32**, **APT38**, **APT41**, **Aquatic Panda**, **Chimera**, **Dragonfly**, **FIN5**, **FIN8**, **HAFNIUM**, **Volt Typhoon** gibi threat group’ların yanı sıra; **Apostle**, **BlackCat**, **BlackEnergy/KillDisk**, **HermeticWiper**, **LockBit 2.0**, **LockBit 3.0**, **Lucifer**, **Mafalda**, **Meteor**, **NotPetya**, **Olympic Destroyer**, **Qilin**, **RansomHub**, **ShrinkLocker** ve **SynAck** gibi malware, ransomware ve wiper örnekleri de bu teknikle ilişkilendirilmektedir. Bu tablo, **Clear Windows Event Logs (T1070.001)** davranışının tek bir saldırı sınıfına özgü olmadığını açıkça ortaya koyar.

MITRE’nin verdiği örnekler ayrıca yöntemin nasıl çeşitlendiğini de gösterir. Bazı aktörler doğrudan **wevtutil** kullanarak **System** ve **Security** log’larını temizler; örneğin **APT28** için `wevtutil cl System` ve `wevtutil cl Security` açıkça belirtilmiştir. **APT41** için Windows **security** ve **system events** temizlenerek activity’nin izlerinin kaldırılmaya çalışıldığı, **FIN8** için ise log temizleme davranışının **post-compromise cleanup** aşamasında kullanıldığı ifade edilmektedir. **NotPetya** örneğinde ise **wevtutil** ile Windows event log’larının silinmesi, daha destrüktif bir operasyon akışının parçası olarak karşımıza çıkar.

Daha da önemlisi, Procedure Examples listesi tekniğin yalnızca tek bir araçla uygulanmadığını gösterir. Bazı örneklerde **wevtutil** öne çıkarken, bazı vakalarda **PowerShell** modülleri, bazı vakalarda **OpenEventLog/ClearEventLog** gibi Windows API çağrıları, bazı vakalarda ise doğrudan log dosyalarının silinmesi veya overwrite edilmesi kullanılmıştır. Örneğin **Mafalda** `OpenEventLogW` ve `ClearEventLogW` fonksiyonlarını çağırabilir, 
**ShrinkLocker** özellikle **Windows PowerShell** ve **Microsoft-Windows-PowerShell/Operational** log’larını temizler, **HermeticWiper** ise `C:\Windows\System32\winevt\Logs` içeriğini overwrite edebilir. Bu da defender tarafında tek bir komut kalıbına odaklanmanın yeterli olmayacağını, davranışın farklı araç zincirleriyle yeniden üretilebildiğini göstermektedir.

Burada dikkat çekici olan nokta şudur: log temizleme davranışı farklı saldırı tiplerinde ortaklaşabilmektedir. **Nation-state actor**, **ransomware operator** ya da daha genel **cybercriminal** profilleri arasında amaçlar farklı olsa bile, izleri azaltma ve incident response sürecini zorlaştırma ihtiyacı aynıdır. Bu nedenle **T1070.001**, niş bir davranıştan çok, farklı saldırı ekosistemlerinde tekrar tekrar karşımıza çıkan yaygın bir **Defense Evasion** tekniği olarak değerlendirilmelidir.

## Bu teknik neden kurumlar için kritik bir risktir?

Log temizleme girişimi, çoğu zaman asıl olaydan daha büyük bir sorunun habercisidir. Çünkü saldırganın log’ları silmeye çalışması, genellikle ondan önce gizlemek istediği bir activity bulunduğunu gösterir. Bu activity; credential access, lateral movement, persistence, ransomware deployment ya da destructive operation olabilir.

İkinci kaynakta bu davranışın kurumlara etkisi üç başlıkta özetlenmektedir: **extended dwell time**, güvenlik yatırımlarının etkisinin azaltılması ve incident response sürecinin gecikmesi. [2] Gerçekten de bir saldırgan detection’dan kaçabildiği ölçüde ağ içinde daha uzun süre kalabilir; bu da yalnızca daha fazla sistemin etkilenmesine değil, daha fazla veri, hesap ve iş sürecinin risk altına girmesine yol açar.

Benim burada özellikle önemli bulduğum nokta, log temizlemenin yalnızca “bir log problemi” olmadığıdır. Bu, doğrudan **EDR**, **SIEM** ve **AV** gibi güvenlik çözümlerinin ürettiği görünürlüğün anlamını azaltabilir. Başka bir deyişle kurum log üretiyor olabilir; fakat adversary bu log’ların güvenilirliğini ya da sürekliliğini bozabiliyorsa, güvenlik yatırımı teknik olarak mevcuttur ama operasyonel etkisi zayıflamıştır.

## Detection açısından hangi işaretlere bakılmalı?

Bu tekniğin tespitinde en kritik yaklaşım, yalnızca “log silindi mi?” sorusunu sormak değildir. Asıl önemli olan, log temizleme davranışına giden süreci, kullanılan process'i, commad-line içeriğini, oluşan meta-events'i ve temizleme sonrasında oluşan anormallikleri(telemetry boşluklarını) bir arada değerlendirmektir. 

### Event ID 1102 ve Event ID 104

Windows, log temizleme işlemleri sırasında bazı meta-olaylar üretir. **Security Event ID 1102**, audit log’un temizlendiğini; **System Event ID 104** ise bir event log’un silindiğini gösterir ve ilgili log adı ile temizleme işlemini gerçekleştiren account bilgisini içerebilir. Kaynağın özellikle vurguladığı nokta, bu olayların temizleme işlemi tamamlanmadan önce yazılabilmesidir. Bu nedenle oldukça güçlü tamper-evidence göstergeleri olarak değerlendirilebilirler. [2]

Bence burada savunma ekiplerinin yapması gereken en temel şey, bu Event ID’leri yalnızca lokal makinede bırakmamak; mümkünse gerçek zamanlı olarak merkezi bir toplama altyapısına iletmektir. Çünkü lokal log’un kendisi temizlenebilir; ancak olay merkezi platforma zamanında taşınmışsa delil korunmuş olur.

![](Pasted%20image%2020260405154823.png)

**Logon ID**, bu event’in aynı **Logon ID** değerini içeren diğer event’lerle ilişkilendirilmesini sağlayan hexadecimal bir değerdir. Örneğin, bu değer **4624: An account was successfully logged on** gibi event’lerle korelasyon kurmak için kullanılabilir.

Normal koşullarda bu event’in görülmesi beklenmez. Çünkü çoğu durumda **Security event log**’un manuel olarak temizlenmesine ihtiyaç yoktur. Bu nedenle bu event’in izlenmesi ve ilgili aksiyonun neden gerçekleştirildiğinin araştırılması önerilir.


Kural >= https://lantern.splunk.com/Security_Use_Cases/Security_Monitoring/Detecting_a_ransomware_attack/Windows_event_log_cleared 

![[Pasted image 20260408145827.png]]

![[Pasted image 20260408145854.png]]

https://research.splunk.com/endpoint/ad517544-aff9-4c96-bd99-d6eb43bfbb6a/


### Process ve command-line monitoring

Kaynak, **Sysmon Event ID 1** ve **Windows Event ID 4688** üzerinden process creation ve command-line activity’nin izlenmesini önermektedir. Özellikle aşağıdaki davranışlar kritik sinyaller üretir: [2]

- **wevtutil.exe** çağrılarında **cl** veya **clear-log** parametresi kullanılması
- **powershell.exe** üzerinden **Clear-EventLog** veya **Remove-EventLog** cmdlet’lerinin çalıştırılması
- PowerShell script block içinde log temizlemeye yönelik komutların yer alması
- **ClearEventLogW** API çağrılarının gözlemlenmesi

Bu mantık bence son derece değerlidir. Çünkü log temizleme başarılı olduktan sonra yalnızca log sonucu üzerinden analiz yapmak zor olabilir; ancak o işlemi başlatan **process execution** çoğu zaman ayrı telemetry kaynaklarında görülebilir. Bu nedenle güvenilir detection, yalnızca “silinmiş veri”ye değil, o silmeyi gerçekleştiren **behavior chain**’e bakmalıdır. [3]  [4]


https://research.splunk.com/endpoint/fdb829a8-db84-4832-b64b-3e964cd44f01/


#### Parent process chain analizi

Dördüncü kaynakta geçen en değerli operasyonel katkılardan biri, alert triage sırasında **parent process tree**’nin incelenmesi gerektiğinin vurgulanmasıdır. Bir `wevtutil.exe` veya `powershell.exe` çağrısı tek başına anlamlı olsa da, onu başlatan üst süreç çok daha büyük resmi gösterebilir. [4]

Örneğin bu çağrı beklenen bir administrative console’dan mı geldi, yoksa beklenmeyen bir dropper, script runner, remote execution aracı veya şüpheli bir binary tarafından mı tetiklendi? İşte bu fark, alert’in gerçek anlamını belirler. Benim görüşüme göre log clear detection’larında parent-child process ilişkisi çoğu zaman komutun kendisi kadar önemlidir.
### File system monitoring

Eğer saldırgan doğrudan **.evtx** dosyalarını hedef alıyorsa, process monitoring tek başına yeterli olmayabilir. Bu nedenle, **C:\Windows\System32\winevt\Logs\** dizini üzerinde **Sysmon Event ID 23 (FileDelete)** ve gerektiğinde **Event ID 11 (FileCreate)** gibi file activity telemetry’lerinin izlenmesi önerilmektedir. [2]

Özellikle **SYSTEM** dışındaki process’lerin bu dizindeki **.evtx** dosyalarına erişmesi, dosya silmesi ya da anormal file size değişiklikleri oluşturması dikkat çekici olabilir. Ayrıca **file integrity monitoring** yaklaşımı ile log dizininde yetkisiz değişiklik, truncation ya da beklenmedik replacement davranışları da izlenebilir. Bu, özellikle API tabanlı değil, dosya tabanlı anti-forensics girişimlerine karşı önemlidir.

### Centralized log forwarding ve gap analysis

İkinci kaynağın en güçlü önerilerinden biri, log’ların gerçek zamanlı ya da gerçeğe çok yakın zamanlı olarak merkezi bir platforma iletilmesidir. Bunun için **Windows Event Forwarding (WEF)**, **syslog**, SIEM agent’ları veya benzeri merkezi toplama mekanizmaları kullanılabilir. [2]

Bence bu yaklaşım, bu tekniğe karşı en kritik savunma katmanlarından biridir. Çünkü lokal host üzerindeki log her zaman manipüle edilebilir; fakat ayrı bir log collector veya SIEM üzerinde tutulan veri daha dayanıklıdır.

Benim bu konudaki en güçlü önerim, merkezi log toplama ile birlikte **telemetry continuity** yaklaşımının kurulmasıdır. Yani yalnızca olayları toplamak değil, aynı zamanda olay akışındaki kırılmaları da izlemek gerekir. [2]  [3]

Bir sistemin event volume’unun aniden sıfıra düşmesi, beklenen periyodik event’lerin kesilmesi ya da log sequence içinde anormal boşluklar oluşması, özellikle **selective log manipulation** senaryolarında çok kritik sinyaller üretebilir. Bu nedenle **Clear Windows Event Logs** detection’ı yalnızca bir Event ID konusu değil, aynı zamanda bir **görünürlük sürekliliği** problemidir.


## SOC analyst açısından triage nasıl yapılmalı?

Dördüncü kaynak, bu tekniği yalnızca teorik bir davranış olarak değil, gerçek bir SOC alert’i gibi nasıl ele almak gerektiğini de gösteriyor. 

Buna göre bir alert geldiğinde **Possible investigation steps** kapsamında, öncelikle unknown process’lere ait **process execution chain**’in, yani **parent process tree** yapısının ayrıntılı biçimde incelenmesi gerekir. Bu süreçte ilgili executable dosyalarının ortamdaki yaygınlığı (**prevalence**), beklenen dizinlerde bulunup bulunmadığı ve geçerli **digital signature** ile imzalanıp imzalanmadığı değerlendirilmelidir.

Bununla birlikte, ilgili aksiyonu gerçekleştiren **user account** tespit edilmeli ve bu hesabın söz konusu davranışı gerçekleştirmesinin normal operasyonel kapsam içinde olup olmadığı analiz edilmelidir. Ardından, hesap sahibine ulaşılarak bu aktiviteden haberdar olup olmadığı doğrulanmalıdır.

Ek olarak, son **48 saat** içerisinde aynı **user/host** ile ilişkilendirilen diğer alert’ler incelenmeli ve olayın tekil bir aktivite mi yoksa daha geniş bir saldırı zincirinin parçası mı olduğu anlaşılmaya çalışılmalıdır. Aynı bağlamda, başka **anti-forensics** davranışlarının gözlemlenip gözlemlenmediği de araştırılmalıdır. Son olarak, gerçekleştirilen aksiyon öncesindeki **event log** kayıtları detaylı şekilde analiz edilerek, bir adversary’nin hangi şüpheli aktiviteleri gizlemeye çalışmış olabileceği değerlendirilmelidir.

Bence bu yaklaşım çok olgundur. Çünkü log temizleme davranışı çoğu zaman “ana olay” değil, daha önce gerçekleşmiş olayların izini kapatma girişimidir. Bu yüzden alert’i tek başına değerlendirmek çoğu zaman eksik kalır; öncesindeki davranışların korelasyonu şarttır. [4]

## False positive değerlendirmesi neden önemlidir?


**False positive analysis** açısından değerlendirildiğinde, bu mekanizmanın bazı durumlarda meşru amaçlarla kullanılabileceği göz önünde bulundurulmalıdır. Eğer ilgili aktivite sistem yöneticisi tarafından biliniyor, onaylanıyor ve bu aksiyon için geçerli bir operasyonel gerekçe bulunuyorsa, analistler söz konusu alert’i false positive olarak değerlendirebilir.

Bununla birlikte, temizlenen **event log** kaydının güvenlik izleme (**security monitoring**) ve genel sistem izleme (**general monitoring**) açısından kritik olup olmadığı ayrıca analiz edilmelidir. Çünkü yöneticiler, bazı durumlarda güvenlik açısından anlamlı olmayan ya da operasyonel değeri düşük **event log** kayıtlarını bu mekanizma aracılığıyla temizleyebilir.

Ancak burada kritik olan şey, işlemin bağlamıdır. Temizlenen log **security-relevant** bir log mu? İşlemi gerçekleştiren user gerçekten bunu yapması beklenen biri mi? İşlem change management veya bakım faaliyeti kapsamında mı? Bu sorulara olumlu ve doğrulanabilir cevaplar verilemiyorsa, alert’in benign olduğu kolayca varsayılmamalıdır.

Eğer bu aktivite ilgili ortamda beklenen bir davranış niteliği taşıyor ve yüksek düzeyde gürültüye (**noise**) neden oluyorsa, uygun **exception** tanımlarının eklenmesi değerlendirilebilir. Ancak bu istisnaların mümkün olduğunca yalnızca tek bir kritere değil, tercihen **user** ve **command-line** koşullarının birlikte kullanıldığı daha kontrollü bir yapı üzerinden tanımlanması önerilir. Bu yaklaşım, yanlış pozitifleri azaltırken gerçek kötüye kullanım senaryolarının gözden kaçırılması riskini de minimize edecektir.   (https://detection.fyi/elastic/detection-rules/windows/defense_evasion_clearing_windows_event_logs/ )


## Incident response ve remediation neden burada kritik?

**Response and remediation** süreci, gerçekleştirilen **triage** sonucuna göre uygun **incident response** sürecinin başlatılmasıyla yürütülmelidir. 

Bu aktivitenin, adversary’nin ilgili host üzerindeki hedeflerine ulaştıktan sonra gerçekleştirilmiş olabileceği göz önünde bulundurulmalıdır. Bu nedenle log temizleme alert'i geldiğinde yalnızca te bir komutu incelemek yetmez; bu olaydan önce gerçekleşmiş olabilecek diğer aksiyonlar da ilgili **response playbook**’lar doğrultusunda kapsamlı biçimde araştırılmalıdır.

Ayrıca, daha ileri düzeyde **post-compromise** davranışların önüne geçebilmek amacıyla ilgili host izole edilmelidir. Bununla birlikte, saldırgan tarafından compromise edilmiş ya da kullanılmış sistemlerde olası **credential exposure** durumu ayrıntılı şekilde incelenmeli; ele geçirilmiş olabilecek tüm hesaplar tespit edilmelidir. Bu kapsamda, söz konusu hesapların parolaları ile birlikte e-posta hesapları, kurumsal iş sistemleri ve web servisleri gibi diğer potansiyel olarak compromise olmuş kimlik bilgilerinin de reset edilmesi gerekmektedir.

Sistem üzerinde tam kapsamlı bir **antimalware scan** çalıştırılması da önemlidir. Bu işlem, ortama bırakılmış ek artifact’lerin, persistence mechanism’lerinin ve malware bileşenlerinin ortaya çıkarılmasına yardımcı olabilir. Buna ek olarak, saldırgan tarafından kötüye kullanılan **initial vector** belirlenmeli ve aynı yöntem üzerinden yeniden enfeksiyon (**reinfection**) oluşmasını engelleyecek önlemler alınmalıdır.

Son olarak, elde edilen **incident response** verileri doğrultusunda mevcut **logging** ve **audit policy** yapıları gözden geçirilmeli ve güncellenmelidir. Bu iyileştirmelerin amacı, hem **mean time to detect (MTTD)** hem de **mean time to respond (MTTR)** metriklerini geliştirmek ve benzer olaylara karşı organizasyonun savunma kapasitesini artırmaktır.

Benim bu bölümde en önemli bulduğum nokta, log temizleme davranışının “tek başına kapatılacak düşük seviyeli bir alert” olarak görülmemesi gerektiğidir. Vendor rule içinde **severity = low** olarak işaretlenmiş olsa bile, bu durum davranışın önemsiz olduğu anlamına gelmez. Aksine bu tür alert’lerin gerçek önemi, bağlamla birlikte ortaya çıkar. Özellikle öncesinde credential abuse, lateral movement veya suspicious execution activity varsa, düşük severity görünen bu alert çok yüksek değerli bir compromise göstergesine dönüşebilir. [4]

## Mitigation ve prevention nasıl ele alınmalı?

Kaynağın aktardığı **MITRE recommended mitigations** başlıkları içinde özellikle üç nokta öne çıkmaktadır: **Restrict File and Directory Permissions (M1022)**, **Remote Data Storage (M1029)** ve **Encrypt Sensitive Information (M1041)**. [2]

### Restrict File and Directory Permissions

Log dosyalarının ve ilgili dizinlerin uygun permission modeliyle korunması gerekir. Bu, yalnızca mevcut dosyaları korumak için değil; saldırganın privilege escalation fırsatlarını azaltmak için de önemlidir. Pratikte en sık yapılan hatalardan biri, log’ları koruyan güvenlik modelini yalnızca “okuma-yazma” düzeyinde düşünmektir. Oysa burada esas mesele, kimlerin log yönetimi ve temizleme yetkisine sahip olduğunun çok sıkı sınırlandırılmasıdır. [2]

### Remote Data Storage

Log’ların ayrı bir log server, collector ya da cloud tabanlı SIEM altyapısına otomatik olarak iletilmesi, bu tekniğe karşı en etkili kontrollerden biridir. Çünkü saldırgan lokal makinede ne yaparsa yapsın, verinin bir kopyası merkezi sistemde korunuyorsa forensic görünürlük tamamen kaybolmaz. [2]

Benim görüşüme göre bu, “opsiyonel iyi uygulama” değil; özellikle kritik Windows ortamlarında temel güvenlik gereksinimi olarak değerlendirilmelidir. Mümkün olduğunda, event reporting sürecindeki zaman gecikmesini en aza indirerek local sistem üzerindeki uzun süreli depolamayı azaltın. Lokal log’a güvenmek, modern tehdit modelinde tek başına yeterli değildir.


### Encrypt Sensitive Information

Kaynağın önerdiği bir diğer önemli mitigation başlığı **Encrypt Sensitive Information (M1041)**’dir. Bu kapsamda, **event files**’ın hem lokal sistem üzerinde hem de iletim sırasında **obfuscate** ya da **encrypt** edilmesi önerilmektedir. Bu yaklaşımın amacı, adversary’nin log içeriğinden doğrudan geri bildirim (**feedback**) elde etmesini zorlaştırmaktır. [2]

Bu kontrol özellikle iki açıdan önem taşır. Birincisi, log verisinin açık biçimde tutulduğu ortamlarda saldırgan, hangi aktivitelerin kayda geçtiğini anlayarak kendi davranışını buna göre uyarlayabilir. İkincisi ise, log’ların ağ üzerinden merkezi sistemlere taşındığı yapılarda, iletim güvenliği sağlanmadığında bu kayıtlar gözlemlenebilir, manipüle edilebilir ya da istihbarat kaynağı olarak kullanılabilir. Bu nedenle, event log verisinin hem **at rest** hem de **in transit** korunması, yalnızca veri gizliliği açısından değil, aynı zamanda savunma görünürlüğünün adversary tarafından istismar edilmesini önlemek açısından da değerlendirilmelidir.



### Ek savunma pratikleri

İkinci kaynak ayrıca **principle of least privilege**, **defense in depth**, düzenli güncellemeler ve kullanıcı farkındalığı gibi ek önlemleri de önermektedir. [2] Bu öneriler ilk bakışta genel güvenlik tavsiyesi gibi görünse de aslında bu teknikle doğrudan ilişkilidir. Çünkü log temizleme genellikle elevated privileges gerektirir; dolayısıyla yetkilerin sınırlandırılması ve katmanlı savunma uygulanması, bu tekniğin başarılı biçimde kullanılmasını zorlaştırır.

Kaynağın ayrıca merkezi forwarding olmayan ortamlarda telafi edici kontroller önerdiği görülmektedir. Bunlar arasında **FIM**, **Sysmon**, **Event ID 4663** üzerinden object access auditing, periyodik log size/record count kontrolü ve process-level API monitoring gibi yöntemler yer almaktadır. [2] 

Bu öneri önemli; çünkü her kurumun olgun bir WEF ya da SIEM altyapısı olmayabilir. Böyle durumlarda bile log temizleme davranışını tamamen görünmez bırakmamak için alternatif telemetri katmanları kurulabilir.


## Sonuç

**Indicator Removal: Clear Windows Event Logs (T1070.001)**, yüzeyde birkaç komutla uygulanabilen basit bir teknik gibi görünebilir. Ancak gerçekte etkisi bundan çok daha büyüktür. Çünkü burada amaç yalnızca log içeriğini silmek değil; aynı zamanda savunma ekibinin olayları anlamlandırma, bir timeline oluşturma, attacker behavior’ı geriye dönük olarak izleme ve compromise kapsamını belirleme kapasitesini zayıflatmaktır.

Bu teknik, yalnızca Windows üzerinde gerçekleştirilen teknik bir işlem olarak değerlendirilmemelidir. Aynı zamanda SOC perspektifinden doğru şekilde triage edilmesi gereken, false positive bağlamı dikkatle analiz edilmesi gereken ve gerekli durumlarda incident response sürecini tetikleyebilecek bir alert davranışı olarak ele alınmalıdır.

Bu nedenle bu tekniği sıradan bir administration işlemi olarak değil, yüksek öneme sahip bir **Defense Evasion** göstergesi olarak değerlendirmek daha doğru olacaktır. Özellikle bir ortamda **wevtutil**, **Clear-EventLog**, **Meterpreter clearev**, **.evtx deletion**, **Event ID 1102**, **Event ID 104**, **process tree** anormallikleri, **telemetry gaps** ve beklenmeyen **anti-forensics** davranışları bir arada görülüyorsa, bu durum çoğu zaman daha geniş kapsamlı bir **compromise** sonrasında gerçekleştirilen iz gizleme safhasına işaret eder.

Kısacası, bir saldırganın log temizlemesi yalnızca geçmişe ait kayıtları silme girişimi değildir; aynı zamanda savunma ekibinin olayın gerçeğini yeniden inşa etme kapasitesine yönelik bilinçli bir müdahaledir. Bu nedenle **Clear Windows Event Logs**, Windows güvenlik görünürlüğü içerisinde özel öncelik verilmesi gereken tekniklerden biri olarak ele alınmalıdır.

---



## Kaynaklar

1. https://attack.mitre.org/techniques/T1070/001/ 

2. https://www.startupdefense.io/mitre-attack-techniques/t1070-001-clear-windows-event-logs 
3. https://viperone.gitbook.io/pentest-everything/everything/everything-active-directory/defense-evasion/indicator-removal/clear-windows-event-logs
4. https://github.com/elastic/detection-rules/blob/main/rules/windows/defense_evasion_clearing_windows_event_logs.toml
5. 





https://unprotect.it/technique/indicator-removal-clear-windows-event-logs/ 
https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-1102 