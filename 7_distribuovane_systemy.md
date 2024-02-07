# Distribuované systémy
> Základní pojmy, principy. Rozdíl mezi centralizovanou a distribuovanou architekturou systému, nevýhody obojího a jejich překonávání. Replikace, sdílení dat. Architektura orientovaná na služby (SOA), webové služby. Příklady existujících technologií a jejich využití. Příklady z praxe pro vše výše uvedené. ([PA053](https://is.muni.cz/auth/el/fi/jaro2023/PA053/um/))

## Základní pojmy, principy

### Distribuovaný systém
- DS je kolekcia nezávislých uzlov/výpočetných jednotiek, ktoré spolupracujú na dosiahnutí spoločného cieľa. 
- Uzly sú prepojené počítačovou sieťou, cez ktorú komunikujú a koordinujú medzi sebou tasky na poskytnutie nejakej unifikovanej služby. 
- DS sú navrhnuté pre lepšiu performance, spoľahlivosť a škálovateľnosť na základe distribuovania taskov medzi rôznymi mašinami
- architektúry DS - client-server, P2P, component-based, SOA, microservice
- napr. cloud platform, P2P siete, CDN, distribuované databáze

### Middleware
- middleware je software vystupujúci ako medzivrstva medzi jednolivými komponentami DS
- jeho cieľom je zjednodušenie a zefektívnenie komunukácie a koordinácie jednotlivých uzlov DS
- napr. message queueing system ako RabbitMQ, Remote Procedure Call - CORBA (Common Object Request Broker Architecture)

## Rozdíl mezi centralizovanou a distribuovanou architekturou systému, nevýhody obojího a jejich překonávání. 
### Centralizovaná architektúra
- celý systém (dáta aj funkcionalita) sa nachádza na jednom stroji
- (+) lahšia správa systému
- (-) single point of failure -> môžeme vytvoriť repliky (záložné servery), ktoré budú schopné zastúpiť zlyhaný server
- (-) vysoká previazanosť == nízka flexibilita -> dôraz na kvalitu

### Distribuovaná architektúra
- systém sa skladá z viacerých častí, ktoré sa nachádzajú nezávisle na rôznych strojoch
- (+) lepšia škálovateľnosť -> naškálujem len časť ktorá to potrebuje
- (+) no single point of failure == zlyhaná časť systému neznamená automaticky zlyhanie celého systému
- (-) ťažšie spravovanie a synchronizácia == väčšia komplexita -> treba čo najlepšie navrhnúť architektúru a využitie vhodných nástrojov, ako napr. RabbitMQ/Kafka

Zaistenie konzistentnosti a integrity v DS je ťažká úloha a preto namiesto dodržiavania ACID princípov sa prechádza na BASE:
- **Basically Available** = aj v prípade nefunkčnosti časti systému zostáva zvyšok systému dostupný a funkčný
- **Soft state** = stav systému môže byť DOČASNE nekonzistentný medzi jednotlivými uzlami
- **Eventually consistent** = systém po čase dosiahne konzistentného stavu, ak sa nevyskytnú nové aktualizácie -> dosiahnutie silnej konzistencie v DS v reálnom čase je často nepraktické


## Replikace, sdílení dat
- v DS kde sú jednotlivé komponenty systému rozmiestnené nezávisle po sieti je potrebné zaistiť zdieľanie dát ak potrebujú jednotlivé komponenty k nim pristupovať == spolupráca a koordinácia medzi distribuovanými uzlami
  - treba zabezpečiť:
    - bezpečnosť == riadenie prístupu, 
    - konzistentnosť (súbežný prístup je problém) == uzamykanie,dist. tranzakcie apod.,
    
- replikácia je potrebná aj pre zaistenie rýchlej dostupnosti dát, zvýšenie odolnosti systému (ak mi padne server, mám niekde zálohu) voči výpadkom/chybám, či zlepšenie výkonu
  - *úplná* = všetky dáta sa replikujú do všetkých uzlov
  - *čiastočná* = všetkým len časť dát na základe nejakých kritérii
  - *selektívna* = časť dát sa selektívne replikuje niektorým uzlom
- pri replikácii treba riešiť **konzistentnosť** -> zmena dát v jednom uzle sa musí premietnuť aj do ostatných replík == **synchronizácia**
- **master-slave replikácia** - master uzol zabezpečuje aktualizáciu slave (tí poskytujú len čítanie)
- **P2P replikácia** - každý uzol číta aj zapisuje a následne aktualizuje

- **sharding** = na rôzne uzly sú umiestnené rôzne časti údajov

##  Architektura orientovaná na služby (SOA), webové služby
- SOA je architektúra skladajúca sa z niekoľkých služieb (cca 4-12), ktoré sú nezávisle od seba nasadené/škálované a komunikujú medzi sebou prostredníctvom dobre definovaných rozhraní == ľahké previazanie (loosely coupled) 
- pre komunikáciu môžu využívať architektonické štýly ako SOAP alebo REST (microservisy len REST), ale môžu využívať aj iné mechanizmy
- zvyčajne synchrónna komunikácia cez sieť (microservisy asynchronna/event-driven)
- zvyčajne využívajú spoločnú DB (microservisy majú vlastné)
- tímy pracujú nezávisle na službách avšak môže sa objaviť nejaké prepojenie a výzvy koordinácie (microservisy nemajú)


- *webová služba* je typ služby, ktorá sa riadi princípmi SOA, ale komunikuje pomocou štandardizovaných štýlov ako SOAP/REST cez HTTP, teda komunikuje spôsobom typickým pre komunikáciu cez web
- pre popis služieb aké ponúka iným aplikáciam cez web poskytuje ja **WSDL**(Web Services Description Language) pre SOAP / **OpenAPI specification** pre REST
- v týchto dokumentáciach definuje popis a spôsob volania služieb, ktoré poskutyje - aké parametre, aké typy, aký výsledok očakávať

### SOAP - Simple Object Access Protocol
- serializácia dát - XML - prenos dát medzi službami
- stavová aj bezstavová komunikácia
- nezávislý na transporte (REST využíva len HTTP)
- pevne daná štruktúra - envelope -> header + body
- umožnuje 1 správu cieliť viacerým príjemcom
- použitie - enterprise systémy = SOAP document alebo sa dá využiť aj pre RPC

### REST - REpresentational State Transfer
- serializácia dát - XML alebo JSON
- len bezstavová komunikácia cez HTTP == request obsahuje všetko čo treba pre jeho vyhodnotenie a vrátenie výsledku a teda netreba si uchovávať žiadny stav z predošlej komunikácie
- zaobchádza s dátami ako so **zdrojmi** 
  - každý zdroj je unikátne identifikovateľný = URI (Uniform Resource Identifier)
  - s každým zdrojom sa zaobchádza unifikovane (jednotné rozhranie) prostredníctvom HTTP metód (GET/POST/PUT/DELETE)
- použitie - vývoj API -> škálovateľnosť, výkon a jednoduchosť kvôli HTTP


## Příklady existujících technologií a jejich využití
- vyššie spomenuté SOAP + REST pre web služby

### RPC - Remote Procedure Call
- využíva sa pri komunikácii v DS medzi jednotlivými uzlami, kedy jeden uzol (client) volá procedúru/funkciu ako lokálnu, avšak pre vykonanie danej funkcie je potrebné kontaktovať iný uzol (server), ktorý vlastní implementáciu danej funkcie
- z pohľadu klienta to vyzerá, že daná procedúra/funkcia je lokálna 
- rozhranie, ktoré klient aj server vlastnia je definované prostredníctvom IDL - Interface Definition Language

### CORBA - Common Object Request Broker Architecture
- architektúra pre komunikáciu medzi uzlami DS, kde jeden uzol zaobchádza s objektom ako s lokálnym, ale ten sa nachádza na inom vzdialenom uzle
- je potreba:
1. definícia rozhrania cez IDL
2. generovanie kódu na základe rozhrania pre ďalšiu komunikáciu s objektmi
3. ORB - sprostredkovateľ objektov medzi klientom a serverom = odosiela požiadavky, volá metód a spracovanie výsledkov
4. vzdialené volanie metôd = RPC -> klient používa STUB (proxy) objektu
5. CORBA sa postará o (de)serializáciu dát pre prenos cez sieť

---
 
## Základní pojmy, principy
**Distribuovaný systém** se skládá z komponentů (počítačů) propojených komunikační sítí. Distribuované systémy řeší problémy (výpočty/zpracovávání requestů) spoluprací jednotlivých komponentů (každý dělá něco). Díky tomu se systém snadněji škáluje (posilujeme subsystém, který má problémy).

Architektury popsány v [otázce 1](./1_programovani_a_softwarovy_vyvoj.md#základní-koncepty-softwarových-architektur-z-pohledu-implementace-vícevrstvá-architektura-moderních-informačních-systémů-architektura-model-view-controller), takže jen shrnutí

**Monolitická architektura** - obsahuje vše, co systém potřebuje, je možné pouze vertikální škálování, špatná spolehlivost (pád znamená pád celého systému)

**Úrovňová (tiered) architektura** (nezaměňovat s layered) - jednotlivé úrovně lze distribuovat, paralelizovat, nahradit (komunikace skrz API)
- Klient může být tenký/tlustý dle poskytnuté funkcionality, klasické úrovně bývají klient, server, databáze.

**Hexagonal/Microkernel/component-based** - základní aplikace poskytuje minimální funkcionalitu, zbytek se dodává skrz plug-in komponenty komunikující přes předdefinované api. Komponenty je možné případně zapojovat za běhu systému. Další možnost využití komponentů - pokud potřebujeme používat legacy systém, který si nemůžeme dovolit přepsat, je možné ho zabalit jako komponent a přistupovat k němu přes naše kompatibilní rozhraní. Vývoj komponentových systémů je náročnější (zvlášť problematické je správně určit rozhraní), ale umožňuje větší přizpůsobitelnost/znovupoužitelnost komponentů v budoucích projektech. E.g. extensions ve VSCode, component-based architekturu používá Jakarta Enterprise Edition, kde jednotlivé Java Beans jsou komponenty

**Pipeline architecture** - sekvenční zpracování, každý komponent se stará o relativně transformaci vstupu na výstup (dělej malou věc, ale dělej ji dobře)

**Service-oriented architecture** - popsáno v [samostatné kapitole](./7_distribuovane_systemy.md#architektura-orientovaná-na-služby-soa)

**Microservice architecture** - vysoká koheze, nízká provázanost služeb, systém je tvořen velkým množstvím malých služeb. Důležitá je rychlá komunikace mezi službami (gRPC). Služby nesdílí DB.

**Remote Procedure Call** umožňuje spuštění předem vystavené procedury mezi procesy (i na vzdáleném stroji) tak, jako bychom proceduru volali přímo v kódu. Implementace procedury může být v odlišném programovacím jazyce. Součástí je definice rozhraní, ze kterého je možné vygenerovat odpovídající volatelné funkce/struktury použité pro argumenty pro náš jazyk. De-facto standardem je dnes gRPC. Pro fullstack typescript aplikace je dnes populární používat tRPC. V PA053 se probírala CORBA (primární focus na Javu, ale podporovala i jiné jazyky, nejde jen o RPC, ale o architekturu pro komunikaci mezi objekty v distribuovaném prostředí), ale ta se v nových systémech prakticky nepoužívá kvůli složitosti/lepším alternativám, nahrazena jednodušším SOAP a REST, nebo RPC řešeními (gRPC, funguje na HTTP/2, zprávy binárně serializuje pomocí protocol bufferu).

Pro komunikaci se v distribuovaných systémech kromě RPC používají **message queues** a **event brokers**, kteří umožňují komunikaci typu publisher-subscriber, nebo zpracování jedním z množiny příjemců, a jsou schopny zprávy persistentně uchovávat (hodí se pro transakční zpracování, spolehlivost v případě výpadku). Obecně lze rozlišovat mezi zprávamy určenými do **queue** (klasická fronta, očekáváme zpracování jedním konzumentem) a **topic** (zpráva jde všem poslouchajícím). E.g. Apache Kafka, RabbitMQ.

Alternativně se může pro komunikaci v distribuovaném systému používat e.g. REST, nebo (pokud chceme low level kontrolu a výkon) přímá komunikace mezi sockety.

**Cloud** - výhodou je, že můžeme používat platformu/infrastrukturu jako službu, aniž bychom se o ni museli starat/provádět nákladnou iniciální investici. Výpočetní výkon lze (i automaticky) upravit/škálovat na základě aktuálního vytížení. Fyzické zdroje mohou být sdílené, čímž je možné dosáhnout nižší ceny a je možné distribuovat výpočetní požadavky (peaky různých aplikacích v různých dobách zvládne i jeden stroj). Datová centra lze volit na základě blízkosti k našim zákazníkům.

**Batch vs Stream processing**
![](img/20230602104120.png)

U batch processingu můžeme distribuovat pomocí jednotlivých jobs, řeší se plánování jobs (může stačit obyčejná fronta)

Stream e.g. Apache Kafka

**MapReduce** - k transformaci dat používáme operace MAP (transformace dat 1:1) a REDUCE (sumarizace dat N:1). MAPery lze triviálně paralelizovat (stejné i rozdílné operace), u REDUCErů je to trochu složitější, paralelizujeme rozdílné operace. E.g. Apache Hadoop


## Rozdíl mezi centralizovanou a distribuovanou architekturou systému, nevýhody obojího a jejich překonávání

Hlavním rozdílem je, že centralizovaná architektura shromažďuje data a logiku na jednom místě, distribuovaná architektura rozptyluje logiku do více samostatných komponentů (běžících třeba i na samostatných strojích), které spolu komunikují.

Nevýhody centralizované architektury
- neumožňuje horizontální škálování => škálujeme vertikálně
- selhání části znamená selhání celku => redundance, záložní servery
- nízká flexibilita, vysoká provázanost => důraz na kvalitu kódu

Nevýhody distribuované architektury
- komplexita celkového systému, náročnější správa
- vyžadují více/složitější komunikaci, složitější synchronizace, náchylnost na latenci => použití message queues, gRPC, eventual consistency


Oproti centralizované architektuře distribuované systémy
- nebývají požadavky/transakce ACID, ale BASE
    - **BAsically available** - nefunkčnost části nezpůsobí nefunkčnost celku, zbytek funguje i v případě nefunkční části systému. e.g. na netflixu nemusí fungovat služba hledání, ale vše ostatní běží v cajku. Na každý dotaz dostaneme nějakou odpověď.
    - **Soft state** - změny v systému mohou nastávat i když nepřichází žádné dotazy - systém takto propaguje data, aby dosáhl konzistence
    - **Eventually consistent** - data nemusí být konzistentní okamžitě po získání odpovědi na dotaz, ale až po nějaké chvíli
- selhání (pád) části systému neznamená pád celku
- jsou flexibilnější na modifikace díky nízké provázanosti

## Replikace, sdílení dat

V distribuovaných systémech se používá replikace dat z různých důvodů. U distribuovaných databází(Apache Cassandra)/filesystémů (Apache Hadoop) to může být z důvodu bezpečnosti/dostupnosti/prevence výpadku, obecně se tím ale v systémech snažíme zajistit rychlejší odezvy. Centrální databáze, ve které se sdílí data, se může stát limitujícím bodem -> použijeme buď distribuovanou databázi, která replikaci řeší interně, nebo vícero databází, které mohou být jednodušší (MongoDB), protože se distribucí dat vzdáváme ACID a fungujeme s BASE. Určitá replikace dat vzniká kešováním (Redis). U replikace je potřeba nějakým způsobem řešit invalidaci dat po změně (timeout, nebo CQRS).

Replikace je kýžená u content delivery network (CDN), kde se snažíme mít statické zdroje (web, obrázky) co nejblíže uživateli, aby se dosáhlo rychlého načítání.

NoSQL databáze mají obvykle mechanismy pro automatickou replikaci/distribuci dat mezi různými uzly. Je možné použít sharding (rozbijeme data, uzel se stará o svou doménu) pro distribuci dat, a/nebo master-slave replikaci pro škálování (u aplikací s častým čtením) a prevenci výpadků (spadne master? jeden ze slaves je nový master) - master se při zápisu stará o aktualizaci dat na slaves.

Pro sdílení dat (událostí) je možné použít Apache Kafka, platformu pro streamování dat ukládaných do logů. Pro sdílení informací o službách distribuovaného systému se dá použít Apache ZooKeeper.


## Architektura orientovaná na služby (SOA)

Hybrid mezi microservices a monolitem, poměrně pragmatická architektura pro škálovatelný systém. Systém je tvořen vzájemně nezávislými, samostatně nasaditelnými a vzájemně komunikujícími službami (obvykle 4-12), které obsahují ucelené jednotky funkcionality (e.g. uživatelé, správa produktů, správa objednávek, platební služba, mailová služba...). Každá služba může být nezávisle na ostatních škálována. Nad službami může být pro zajištění jednotného API vrstva fasády. Služby obvykle sdílí databázi, ale je možné ji rozbít na vícero nezávislých celků. Je náročnější na vývoj, ale celkově je systém díky modularizaci lépe udržitelný a škálovatelný.

## Webové služby

Komponenty umožňující komunikaci a interakci prostřednictvím standardizovaných protokolů a formátů. Jsou založeny na Service Oriented Architecture. Web services poskytují abstrakci funkcionalitě služby skrz webové API. Skrz definiční jazyk je formálně popsáno schéma/rozhraní dané služby a je možné generování klientského kódu pro různé programovací jazyky s cílem usnadnit použití webové služby. Schéma může být zároveň generováno přímo ze zdrojového kódu prostřednictvím anotací. 

Dříve se používaly web services založené na **SOAP** (simple object access protocol) a **XML**, definované pomocí **Web Service Definition Language (WSDL)**.

Aktuálně se pro tyto účely spíše používá **REST** (representational state transfer, není to protokol, ale architektonický styl pro definici rozhraní) a **JSON** (byť je možné použít i jiné formáty), definované pomocí **OpenAPI Specification**, případně **GraphQL** (a JSON) se svým **GraphQL Schema** a dotazovacím jazykem. GraphQL používá jeden entrypoint (+1 playground) a umožňuje přesně specifikovat kýžená data (až na úroveň polí) a řešit tak problém s overfetching (1 dotaz obsahuje zbytečná data) a underfetching (v dotazu nemáme dostatek dat, takže děláme vícero různých dotazů).

*SOAP je nezávislý na transportu, REST využívá HTTP. REST je jednodušší, rychlejší a efektivnější. SOAP umožňuje jednu zprávu cílit více příjemcům, přechod zprávy přes prostředníky, kteří mohou zpracovávat hlavičku (tělo je určeno jen příjemci). SOAP umožňuje výměnu strukturovaných a typovaných XML dat SOAP hlavička (nepovinná) může obsahovat metadata, QoS, bezpečnostní informace, SAML data, session identifikátor (a.k.a. cookie)..., SOAP obálka je root XML prvek zprávy, obsahuje namespace (určunící verzi protokolu), styl kódování dat, SOAP tělo obsahuje samotný obsah zprávy. REST umožňuje provázanost (díky hyperlinkům) a je možné se pomocí něj dostat na úplně jinou stránku mimo náš systém.*

*REST se dívá na web jako na zdroje adresovatelné URL, které vrací reprezentaci dat (HTML, XML, PNG, JSON...). Příjem dat uvede klienta do stavu, který může být transformován přístupem na jiný zdroj. Je bezstavový, každá zpráva obsahuje vše, co je nutné pro její interpretaci (správně by zpráva neměla obsahovat cookie, ale třeba JWT), dotazy jsou kešovatelné.*

## Notes

**Middleware** - vrstva softwaru poskytující rozhraní pro interakci s různými službami/systémy, abstrakce k často používané funkcionalitě, případně vrstva propojující existující systémy.
- e.g. CORBA, Web Services, REST, message queue systémy, event brokeři.
