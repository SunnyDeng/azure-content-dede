<properties umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="robb" manager="johndaw" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb"></tags>

# Azure-Ausführungsmodelle

Azure bietet verschiedene Ausführungsmodelle für die Ausführung von Anwendungen. Die Modelle bieten unterschiedliche Dienste an, und daher hängt Ihre Auswahl davon ab, was Sie vorhaben. Dieser Artikel betrachtet drei Modelle, beschreibt die einzelnen Technologien und liefert Anwendungsbeispiele.

## Inhaltsverzeichnis

-   [Virtual Machines][Virtual Machines]
-   [Websites][Websites]
-   [Cloud Services][Cloud Services]
-   [Welches Modell sollte ich verwenden? Die Auswahl][Welches Modell sollte ich verwenden? Die Auswahl]

## <span id="VMachine"></span></a>Virtual Machines

Mit Azure Virtual Machines können Entwickler, Mitarbeiter der IT-Abteilung und andere Personen virtuelle Computer in der Cloud erstellen und nutzen. Diese auch *Infrastructure as a Service (IaaS)* genannte Technologie kann auf unterschiedliche Arten genutzt werden. [Abbildung 1][Abbildung 1] zeigt die Basiskomponenten.

<a name="Fig1"></a>![01\_CreatingVMs][01\_CreatingVMs]

**Abbildung 1: Azure Virtual Machines bietet Infrastructure as a Service.**

Wie Sie in der Abbildung sehen, können Sie virtuelle Computer entweder im Azure-Verwaltungsportal oder über die REST-basierte Azure-Dienstverwaltungs-API erstellen. Sie können das Verwaltungsportal mit allen gängigen Browsern öffnen, inklusive Internet Explorer, Mozilla und Chrome. Für die REST-API bietet Microsoft Clientskriptingtools für Windows, Linux und Macintosh-Systeme an. Auch andere Anwendungen können diese Schnittstelle verwenden.

Für all diese Nutzungsformen müssen Sie bei der Erstellung eines neuen virtuellen Computers zunächst eine virtuelle Festplatte (VHD) für das Image des virtuellen Computers auswählen. Diese VHDs werden in Azure-Blobs gespeichert.

Sie können auf zwei verschiedene Arten beginnen

1.  Laden Sie Ihre eigene VHD hochladen
2.  Verwenden Sie die von Microsoft und deren Partnern in der Galerie für virtuelle Azure-Computer oder auf der Open-Source-Website [VMDepot][VMDepot] von Microsoft angebotenen VHDs.

Die VHDs in der Galerie und auf VMDepot beinhalten frische Microsoft- und Linux-Betriebssystem-Images sowie Images mit vorinstallierten Produkten von Microsoft und von Drittanbietern. Die Anzahl der Optionen wächst ständig. Erhältlich sind verschiedene Versionen, Editionen und Konfigurationen von:

-   Windows Server
-   Linux-Server wie z. B. Suse, Ubuntu und CentOS
-   SQL Server
-   BizTalk Server
-   SharePoint Server

Geben Sie zusätzlich zur VHD noch die Größe Ihres neuen virtuellen Computers an. Die kompletten Merkmale der einzelnen Größen finden Sie in der [Azure-Bibliothek][Azure-Bibliothek].

-   **Sehr klein**, mit einem freigegebenen CPU-Kern und 768 MB Arbeitsspeicher.
-   **Klein**, mit 1 CPU-Kern und 1,75GB Arbeitsspeicher.
-   **Mittel**, mit 2 CPU-Kernen und 3,5GB Arbeitsspeicher.
-   **Groß**, mit 4 CPU-Kernen und 7GB Arbeitsspeicher.
-   **Sehr groß**, mit 8 CPU-Kernen und 14GB Arbeitsspeicher.
-   **A6**, mit 4 CPU-Kernen und 28GB Arbeitsspeicher.
-   **A7**, mit 8 CPU-Kernen und 56GB Arbeitsspeicher.

Wählen Sie zuletzt aus, in welchem Azure-Datencenter Sie Ihren neuen virtuellen Computer einrichten möchten: USA, Europa oder Asien.

Sobald der virtuelle Computer läuft, werden Ihnen dessen Betriebsstunden in Rechnung gestellt, und wenn Sie den Computer entfernen, müssen Sie auch nicht mehr für ihn bezahlen. Der in Rechnung gestellte Betrag hängt nicht von der Auslastung Ihres virtuellen Computers ab, sondern ausschließlich von der tatsächlichen Zeit. Ein virtueller Computer im Leerlauf kostet pro Stunde genauso viel wie unter Volllast.

Zu jedem virtuellen Computer gehört ein *Betriebssystemdatenträger*, der in einem Blob gespeichert wird. Wenn Sie einen virtuellen Computer mit einer VHD aus der Galerie erstellen, wird diese VHD auf Ihren Betriebssystemdatenträger kopiert. Alle Änderungen am Betriebssystem Ihres laufenden virtuellen Computers werden hier gespeichert. Wenn Sie z. B. eine Anwendung installieren, welche die Windows-Registrierung verändert, dann werden diese Änderungen auf dem Betriebssystemdatenträger Ihres virtuellen Computers gespeichert. Wenn Sie anschließend wieder einen virtuellen Computer von diesem Betriebssystemdatenträger erstellen, wird dieser in dem geänderten Zustand fortgesetzt. Für die VHDs in der Galerie führt Microsoft bei Bedarf Updates aus, z. B. Betriebssystem-Patches. Für die VHDs in Ihren eigenen Betriebssystemdatenträgern sind Sie jedoch selbst für die Ausführung dieser Updates verantwortlich (Windows Update ist jedoch standardmäßig aktiviert).

Laufende virtuelle Computer können auch verändert und anschließend mit dem Sysprep-Tool erfasst werden. Sysprep entfernt spezifische Angaben wie den Computernamen, sodass ein VHD-Image für die Erstellung mehrerer virtueller Computer mit derselben allgemeinen Konfiguration verwendet werden kann. Diese Images werden zusammen mit Ihren hochgeladenen Images im Verwaltungsportal gespeichert und können als Ausgangspunkt für zusätzliche virtuelle Computer verwendet werden.

Virtual Machines überwacht außerdem die Hardware, die Ihre virtuellen Computer hostet. Wenn der physische Server eines virtuellen Computers ausfällt, erkennt die Plattform das Problem und startet denselben virtuellen Computer auf einem anderen Server. Mit der richtigen Lizenz können Sie eine geänderte VHD von Ihrem Betriebssystemdatenträger an einen anderen Ort kopieren und dort ausführen, z. B. in Ihrem lokalen Datencenter oder bei einem anderen Cloud-Anbieter.

Neben dem Betriebssystemdatenträger verfügt Ihr virtueller Computer über ein oder mehrere Datenträger. Diese Datenträger sehen wie gewöhnliche Laufwerke in Ihrem virtuellen Computer aus, ihre Inhalte sind jedoch in Blobs gespeichert. Alle Änderungen an Datenträgern werden persistent im zugrunde liegenden Blob gespeichert. Azure repliziert diese Blobs wie auch alle anderen Blobs als Ausfallschutz sowohl innerhalb eines Datencenters als auch in andere Datencenter.

Laufende virtuelle Computer können mithilfe des Verwaltungsportals, der PowerShell und anderen Skript-Tools oder direkt über die REST-API verwaltet werden. (Alle im Verwaltungsportal verfügbaren Vorgänge können auch programmgesteuert über diese API ausgeführt werden.) Microsoft-Partner wie RightScale und ScaleXtreme bieten ebenfalls Verwaltungsdienste auf Basis der REST-API an.

Azure Virtual Machines kann auf vielerlei Arten genutzt werden. Es folgt eine Liste der wichtigsten Szenarien für Microsoft:

-   **Virtuelle Computer für Entwicklung und Tests.** Entwicklungsgruppen benötigen oft virtuelle Computer mit speziellen Konfigurationen für die Erstellung von Anwendungen. Azure Virtual Machines bietet einen einfachen und wirtschaftlichen Weg, um diese virtuellen Computer zu erstellen, zu verwenden und anschließend wieder zu entfernen, wenn sie nicht mehr benötigt werden.
-   **Ausführen von Anwendungen in der Cloud.** Für manche Anwendungen macht es aus wirtschaftlichen Gesichtspunkten Sinn, diese in der öffentlichen Cloud auszuführen. Ein Beispiel sind Anwendungen mit extremen Anforderungsspitzen. Sie können jederzeit genügend Computer kaufen, um diese Anwendungen in Ihrem eigenen Datencenter auszuführen, allerdings werden diese Computer die meiste Zeit ungenutzt bleiben. Wenn Sie diese Anwendung stattdessen auf Azure ausführen, bezahlen Sie nur für die zusätzlichen virtuellen Computer, wenn Sie diese benötigen, und können Sie nach Ende der Anforderungsspitze herunterfahren. Ein weiteres Beispiel sind Start-Up-Unternehmen, die kurzfristig und unverbindlich On-Demand-Ressourcen benötigen. Auch in diesem Fall ist Azure die richtige Wahl.
-   **Erweitern Ihres eigenen Datencenters in die öffentliche Cloud.** Mit Azure Virtual Network können Sie ein virtuelles Netzwerk (VNET) für Ihr Unternehmen erstellen, mit dem eine Gruppe von virtuellen Azure-Computern in Ihr lokales Netzwerk integriert werden. Auf diese Weise können Sie SharePoint und andere Anwendungen auf Azure ausführen, was im Vergleich zur lokalen Ausführung einfacher bereitzustellen und/oder günstiger zu betreiben ist.
-   **Notfallwiederherstellung.** Anstatt fortlaufend für ein selten genutztes Sicherungsdatencenter zu bezahlen, bezahlen Sie mit Iaas-basierter Notfallwiederherstellung nur für die tatsächlich genutzten Ressourcen, wenn Sie diese wirklich benötigen. Wenn Ihr primäres Datencenter ausfällt, können Sie virtuelle Azure-Computer für die Ausführung wichtiger Anwendungen erstellen und anschließend abschalten, wenn diese nicht mehr benötigt werden.

Dies sind nicht die einzigen Möglichkeiten, dienen aber als gute Beispiele für den Einsatz von Azure Virtual Machines.

### Gruppieren von virtuellen Computern: Cloud Services

Bei der Erstellung eines neuen virtuellen Computers mit Azure Virtual Machines haben Sie die Auswahl, diesen eigenständig oder als Teil einer Gruppe von virtuellen Computern in einem *Cloud-Dienst* anzulegen. (Verwechseln Sie dies trotz der Namensgleichheit nicht mit Cloud Services, der Azure-Paas-Technologie.) Jedem eigenständigen virtuellen Computer wird eine öffentliche IP-Adresse zugewiesen, während alle virtuellen Computer in einem Cloud-Dienst über dieselbe öffentliche IP-Adresse erreichbar sind. [Abbildung 2][Abbildung 2] zeigt, wie dies aussieht.

<a name="Fig2"></a>![02\_CloudServices][02\_CloudServices]

**Abbildung 2: Jeder eigenständige virtuelle Computer hat eine eigene öffentliche IP-Adresse, während virtuelle Computer in einem Cloud-Dienst über dieselbe öffentliche IP-Adresse erreichbar sind.**

Wenn Sie z. B. einen virtuellen Computer für Erstellung und Test einer einfachen Anwendung erstellen, werden Sie vermutlich einen eigenständigen virtuellen Computer erstellen. Für Anwendungen mit mehreren Ebenen (Web-Front-End, Datenbank und evtl. sogar eine mittlere Ebene) würden Sie dagegen mehrere virtuelle Computer in einem Cloud-Dienst zusammenfassen (siehe Abbildung 2).

Für gruppierte virtuelle Computer in Cloud-Diensten stehen außerdem weitere Optionen zur Auswahl. Azure bietet Lastenausgleich für virtuelle Computer innerhalb eines Cloud-Diensts und verteilt Benutzeranforderungen über diese Computer. Die auf diese Art verbundenen virtuellen Computer können außerdem direkt über das lokale Netzwerk innerhalb eines Azure-Datencenters miteinander kommunizieren.

Virtuelle Computer innerhalb eines Cloud-Diensts können außerdem in eine oder mehrere *Verfügbarkeitsgruppen* gruppiert werden. Stellen Sie sich z. B. eine Webanwendung vor, deren Front-End auf mehreren virtuellen Computern läuft. Wenn all diese virtuellen Computer auf demselben physischen Computer oder auch nur im gleichen Regal laufen, besteht die Gefahr, dass sie alle durch einen einzigen Hardwarefehler gleichzeitig ausfallen. Wenn Diese virtuellen Computer jedoch in einer Verfügbarkeitsgruppe gruppiert sind, verteilt Azure sie über das Datencenter, sodass sie keine einzelne Fehlerquelle teilen.

### Szenario: Ausführen von Anwendungen mit SQL Server

Wir werden nun einige Szenarien im Detail betrachten, um Ihnen ein besseres Gefühl für die Funktionsweise von Azure Virtual Machines zu vermitteln. Nehmen wir z. B. an, Sie möchten eine zuverlässige und skalierbare Webanwendung unter Azure erstellen. Dazu kann z. B. die Anwendungslogik in einer oder mehreren virtuellen Azure-Computern ausgeführt und ein SQL Server für die Datenverwaltung verwendet werden. [Abbildung 3][Abbildung 3] zeigt, wie dies aussieht.

<a name="Fig3"></a>![03\_AppUsingSQLServer][03\_AppUsingSQLServer]

**Abbildung 3: Anwendungen auf virtuellen Azure-Computern können SQL Server als Datenspeicher verwenden.**

Beide Arten von virtuellen Computern in diesem Beispiel wurden mit Standard-VHDs aus der Galerie erstellt. Die Anwendungslogik läuft unter Windows Server 2008 R2. Der Entwickler erstellt also drei virtuelle Computer mit dieser VHD und installiert seine Anwendung auf allen dreien. Da sich all diese virtuellen Computer in demselben Cloud-Dienst befinden, kann er Hardware-Lastenausgleich für sie konfigurieren, um Anforderungen gleichmäßig zu verteilen. Der Entwickler erstellt außerdem zwei virtuelle Computer mit einer VHD aus der Galerie, die SQL Server 2012 enthält. Sobald diese Computer laufen, konfiguriert er SQL Server für die einzelnen Instanzen, sodass diese Datenbankspiegelung mit automatischem Failover verwenden. Dies ist nicht zwingend notwendig. Die Anwendung könnte auch auf einer einzigen SQL Server-Instanz laufen, allerdings wird mit diesem Ansatz die Zuverlässigkeit verbessert.

### Szenario: Betrieb einer SharePoint-Farm

Nehmen wir an, ein Unternehmen benötigt eine SharePoint-Farm, möchte diese jedoch nicht im eigenen Datencenter ausführen. Möglicherweise sind die Ressourcen im lokalen Datencenter knapp, oder die entsprechende Unternehmenseinheit möchte nicht mit der internen IT-Gruppe zusammenarbeiten. In diesem Fall macht es Sinn, SharePoint auf Azure Virtual Machines auszuführen. [Abbildung 4][Abbildung 4] zeigt, wie dies aussieht.

<a name="Fig4"></a>![04\_SharePointFarm][04\_SharePointFarm]

**Abbildung 4: Mit Azure Virtual Machines kann eine SharePoint-Farm in der Cloud ausgeführt werden.**

SharePoint-Farmen bestehen aus mehreren Komponenten, von denen jede auf einem virtuellen Azure-Computer aus einer eigenen VHD läuft. Folgende Komponenten werden benötigt:

-   Microsoft SharePoint. Die Galerie enthält Probeversionen, oder das Unternehmen verwendet eine eigene VHD.
-   Microsoft SQL Server. SharePoint benötigt SQL Server, also erstellt das Unternehmen virtuelle Computer mit SQL Server 2012 mit einer Standard-VHD aus der Galerie.
-   Windows Server Active Directory. SharePoint benötigt außerdem Active Directory, und das Unternehmen muss Domänencontroller in der Cloud mit einem Windows Server-Image aus der Galerie erstellen. Dies ist nicht zwingend erforderlich. Es ist auch möglich, lokale Domänencontroller zu verwenden, allerdings interagiert SharePoint häufig mit Active Directory, und der hier gezeigte Ansatz bietet eine bessere Leistung.

Obwohl dies nicht in der Abbildung gezeigt wird, ist diese SharePoint-Farm vermutlich über Azure Virtual Network mit einem lokalen Netzwerk verbunden. Auf diese Weise werden die virtuellen Computer und die enthaltenen Anwendungen für Benutzer in diesem Netzwerk als lokal angezeigt.

An diesen Beispielen sehen Sie, dass Sie mit Azure Virtual Machines neue Anwendungen in der Cloud erstellen und ausführen oder bestehende Anwendungen auf unterschiedliche Arten ausführen können. Das Ziel der Technologie ist für alle Anwendungsarten dasselbe: eine allgemeingültige Basis für öffentliches Cloud Computing.

## <span id="WebSites"></span></a>Websites

Web-Technologien werden auf unterschiedlichste Arten eingesetzt. Unternehmen können z. B. eine Präsenz-Website migrieren oder einrichten, die Millionen von Aufrufen pro Woche verarbeitet und auf verschiedene Datencenter weltweit verteilt ist. Eine Webdesign-Agentur kann gemeinsam mit einem Team von Entwicklern eine Webanwendung erstellen, die Tausende von Benutzern verarbeiten kann. Entwickler in Firmen können Anwendungen erstellen, um Spesenabrechnungen in der Cloud für authentifizierte Benutzer aus dem Active Directory des Unternehmens zu überwachen. IT-Berater können mithilfe von beliebten Open Source-Anwendungen Content Management-Systeme für kleinere Unternehmen erstellen.
All diese Dinge können mit Azure Virtual Machines umgesetzt werden. Erstellung und Verwaltung virtueller Computer erfordern jedoch einige Fähigkeiten und einen gewissen Aufwand. Für die Umsetzung von Websites oder Webanwendungen gibt es eine einfachere (und günstigere) Lösung: der auch als Platform as a Service (PaaS) bekannte Ansatz. In Abbildung 5 sehen Sie, dass Azure dies mit Websites unterstützt.

<a name="Fig5"></a>![05\_Websites][05\_Websites]

**Abbildung 5: Azure-Websites unterstützt statische Websites, beliebte Webanwendungen und spezielle Webanwendungen unter Verwendung unterschiedlicher Technologien.**

Azure-Websites bildet gemeinsam mit Azure Cloud Services eine für die Ausführung von Webanwendungen optimierte Platform as a Service-Lösung. In der Abbildung sehen Sie, dass Websites entweder auf mehreren eigenständigen virtuellen Computern mit unterschiedlichen Websites verschiedener Benutzer oder auch auf virtuellen Standard-Computern einzelner Benutzer laufen kann. Die virtuellen Computer sind Teil eines von Azure-Websites verwalteten Ressourcen-Pools und bieten daher hohe Verfügbarkeit und Fehlertoleranz.
Der Anfang ist denkbar einfach. Mit Azure-Websites können Benutzer aus einer breiten Palette an Anwendungen, Frameworks und Vorlagen auswählen und innerhalb von Sekunden eine Website erstellen. Anschließend können sie ihre Lieblings-Entwicklungswerkzeuge (WebMatrix, Visual Studio oder andere Editoren) und Quellcodeverwaltungsoptionen verwenden, um die fortlaufende Integration einzurichten und im Team zu entwickeln. Anwendungen mit MySQL-Datenbanken können einen MySQL-Service konsumieren, der für Azure von dem Microsoft-Partner ClearDB angeboten wird.
Entwickler können mit Websites große und skalierbare Webanwendungen entwickeln. Die Technologie unterstützt die Erstellung von Anwendungen in ASP.NET, PHP, Node.js und Python. Anwendungen können z. B. persistente Sitzungen verwenden, und existierende Webanwendungen können ohne Änderungen in diese Cloud-Plattform verschoben werden. Anwendungen unter Websites können jederzeit andere Aspekte von Azure verwenden, z. B. Servicebus, SQL-Datenbank und Blobspeicher. Außerdem können Sie mehrere Kopien einer Anwendung in unterschiedlichen virtuellen Computern ausführen, und Websites führt einen automatischen Lastenausgleich zwischen diesen Computern durch. Und da Websites-Instanzen in bereits existierenden virtuellen Computern erstellt werden, erfolgt der Start neuer Anwendungsinstanzen sehr schnell im Vergleich zur Wartezeit bei der Erstellung neuer virtueller Computer.
In [Abbildung 5][Abbildung 5] sehen Sie, dass Sie Code und andere Webinhalte auf verschiedene Arten in Websites veröffentlichen können. Sie können FTP, FTPS oder die WebDeploy-Technologie von Microsoft verwenden. Websites unterstützt außerdem die Veröffentlichung von Code aus Quellcodeverwaltungssystemen, darunter Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server und dem cloudbasierten Team Foundation Service.

## <span id="CloudServices"></span></a>Cloud Services

Azure Virtual Machines bietet IaaS, während Azure-Websites Webhosting anbietet. Die dritte Option heißt Cloud Services und bietet *Platform as a Service (PaaS)*. Diese Technologie unterstützt skalierbare und zuverlässige Anwendungen mit niedrigen Betriebskosten. Außerdem müssen sich Entwickler keine Gedanken mehr über die Verwaltung ihrer Plattform machen und können sich ausschließlich auf ihre Anwendungen konzentrieren. [Abbildung 6][Abbildung 6] illustriert diesen Gedanken.

<a name="Fig6"></a>![06\_CloudServices2][06\_CloudServices2]

**Abbildung 6: Azure Cloud Services bietet Platform as a Service.**

Wie auch die anderen Azure-Optionen baut Azure Cloud Services auf virtuellen Computern auf. Diese Technologie bietet zwei leicht abweichende Optionen für virtuelle Computer: Auf Instanzen von *Webrollen* läuft eine Variante von Windows Server mit IIS, während auf Instanzen von *Workerrollen* dieselbe Windows Server-Variante ohne IIS läuft. Cloud Services-Anwendungen verwenden dieselbe Kombination dieser zwei Optionen.

Einfache Anwendungen verwenden z. B. nur eine Webrolle, während komplexere Anwendungen eine Webrolle für die Verarbeitung eingehender Benutzeranforderungen verwenden und die aus diesen Anforderungen entstandene Arbeit an Workerrollen abgeben. (Diese Kommunikation erfolgt über einen Servicebus oder über Azure-Warteschlangen.)

Die Abbildung zeigt, das alle virtuellen Computer in einer einzigen Anwendung im gleichen Cloud-Dienst laufen, wie zuvor für Azure Virtual Machines beschrieben. Aus diesem Grund greifen die Benutzer über eine einzige öffentliche IP-Adresse auf die Anwendung zu, und die Anforderungen werden automatisch auf die einzelnen virtuellen Computer verteilt. Und wie auch bei Cloud-Diensten mit Azure Virtual Machines stellt die Plattform die virtuellen Computer in einer Cloud Services-Anwendung so bereit, dass einzelne Fehlerquellen vermieden werden.

Obwohl die Anwendungen in virtuellen Computern laufen, ist es wichtig, zu betonen, dass Cloud Services PaaS und nicht Iaas bietet. Sie können sich dies auf folgende Weise vorstellen: Mit IaaS, z. B. mit Azure Virtual Machines, erstellen und konfigurieren Sie zunächst die Umgebung für Ihre Anwendung und stellen die Anwendung anschließend in Ihrer Umgebung bereit. Sie sind für die Verwaltung dieser Umgebung verantwortlich und müssen z. B. neue gepatchte Versionen des Betriebssystems in den einzelnen virtuellen Computern installieren. Mit PaaS dagegen existiert diese Umgebung bereits. Sie müssen nur noch Ihre Anwendung bereitstellen. Sie brauchen sich nicht um die Verwaltung der Plattform kümmern, inklusive neuer Versionen des Betriebssystems.

Mit Cloud Services erstellen Sie keine virtuellen Computer. Stattdessen teilen Sie Azure über eine Konfigurationsdatei mit, wie viele Instanzen Sie benötigen, z. B. drei Webrolleninstanzen und zwei Workerrolleninstanzen, und die Plattform erstellt diese Instanzen für Sie. Für die Größe dieser virtuellen Computer haben Sie dieselbe Auswahl wie bei virtuellen Azure-Computern, aber Sie erstellen diese Computer nicht selbst. Wenn Ihre Anwendung eine größere Last verarbeiten muss, können Sie von Azure weitere virtuelle Computer anfordern. Wenn die Last abnimmt, können Sie diese Instanzen abschalten und müssen diese nicht länger bezahlen.

Cloud Services-Anwendungen werden normalerweise in zwei Schritten bereitgestellt. Zunächst lädt ein Entwickler die Anwendung in den Stagingbereich der Plattform hoch. Wenn der Entwickler die Anwendung live schalten möchte, kann er diese über das Azure-Verwaltungsportal in den Produktionsmodus umschalten. Der Wechsel zwischen Staging und Produktion erfolgt ohne Ausfallzeit. Daher können laufende Anwendungen ohne Beeinträchtigung der Benutzer auf neue Versionen aktualisiert werden.

Cloud Services bietet außerdem Überwachungsfunktionen. Wie auch bei Azure Virtual Machines werden Ausfälle von physischen Servern erkannt und die entsprechenden virtuellen Computer auf einem anderen Server neu gestartet. Cloud Services erkennt jedoch neben Hardwarefehlern auch Ausfälle von virtuellen Computern und Anwendungen. Im Gegensatz zu Virtual Machines läuft in jeder Web- und Workerrolle ein Agent. Daher können im Fehlerfall neue virtuelle Computer und Anwendungsinstanzen gestartet werden.

Die PaaS-Funktionsweise von Cloud Services hat auch noch andere Auswirkungen. Zum Beispiel sollten Anwendungen für diese Technologie so entwickelt werden, dass sie auch dann korrekt funktionieren, wenn eine beliebige Web- oder Workerrolle ausfällt. Aus diesem Grund sollten Cloud Services-Anwendungen keinen Status im Dateisystem der eigenen virtuellen Computer speichern. Im Gegensatz zu virtuellen Computern mit Azure Virtual Machines sind Schreibvorgänge in virtuellen Cloud Services-Computern nicht persistent. Diese virtuellen Computer haben keine eigenen Datenträger. Cloud Services-Anwendungen sollten daher ihren Status in SQL-Datenbanken, Blobs, Tabellen oder andere externe Speichermedien schreiben. Die auf diese Weise erstellten Anwendungen sind skalierbarer und fehlerresistenter; zwei wichtige Ziele von Cloud Services.

## <span id="WhatShouldIUse"></span></a>Welches Modell sollte ich verwenden? Die Auswahl

Alle drei Azure-Ausführungsmodelle eignen sich für die Erstellung skalierbarer und zuverlässiger Anwendungen in der Cloud. Welches Modell sollten Sie angesichts dieser scheinbaren Ähnlichkeit verwenden? Die Antwort hängt von Ihren Anforderungen ab.

Virtual Machines ist die vielseitigste Lösung. Dies ist die beste Option, falls Sie möglichst viel Kontrolle oder generische virtuelle Computer benötigen, z. B. für Entwicklung und Tests. Virtual Machines ist außerdem die beste Lösung für lokale Standardanwendungen in der Cloud, wie Sie am SharePoint-Beispiel erfahren haben. Und da die mit dieser Technologie erstellten virtuellen Computer genau wie Ihre lokalen virtuellen Computer aussehen können, ist dies auch die erste Wahl für Ihre Notfallwiederherstellung. Allerdings bringen all diese Möglichkeiten auch mehr Verantwortung mit sich. Mit dem IaaS-Modell müssen Sie einige der Verwaltungsaufgaben selbst übernehmen.

Websites ist die erste Wahl für die Erstellung einfacher Websites. Dies gilt insbesondere dann, wenn Ihre Website auf existierende Anwendungen wie Joomla, WordPress oder Drupal aufbaut. Websites eignet sich ebenfalls für die Erstellung wartungsarmer Webanwendungen, selbst wenn diese gut skalierbar sein müssen, oder zum Verschieben existierender IIS-Webanwendungen in die öffentliche Cloud. Schnelle Bereitstellung ist ein weiteres Merkmal. Neue Instanzen Ihrer Anwendung können fast ohne Verzögerung gestartet werden, während die Einrichtung neuer virtueller Computer mit Virtual Machines oder Cloud Services mehrere Minuten dauern kann.

Cloud Services ist das ursprüngliche Ausführungsmodell von Azure und verfolgt einen reinen PaaS-Ansatz. Während die Grenze zwischen PaaS und Webhosting nicht immer eindeutig ist, unterscheidet sich Cloud Services in einigen wichtigen Punkten von Websites:

-   Cloud Services bietet im Gegensatz zu Websites administrativen Zugang zu den virtuellen Computern Ihrer Anwendung. Daher können Sie beliebige zusätzliche Software für Ihre Anwendung installieren, was mit Websites nicht möglich ist.
-   Cloud Services bietet sowohl Web- als auch Workerrollen an und eignet sich daher besser als Websites für Anwendungen mit mehreren Ebenen, die separate virtuelle Computer für ihre Geschäftslogik benötigen.
-   Cloud Services bietet separate Staging- und Produktionsumgebungen und erleichtert auf diese Weise die Aktualisierung von Anwendungen im Vergleich zu Websites.
-   Im Gegensatz zu Websites können Sie Netzwerktechnologien wie Azure Virtual Network und Azure Connect verwenden, um lokale Computer mit Cloud Services-Anwendungen zu verbinden.
-   Mit Cloud Services können Sie Remotedesktop verwenden, um sich direkt mit den virtuellen Computern der Anwendung zu verbinden. Dies ist mit Websites nicht möglich.

Cloud Services bietet dank der PaaS-Funktionsweise verschiedene Vorzüge im Vergleich zu Azure Virtual Machines. Verschiedene Wartungsaufgaben wie die Installation von Betriebssystem-Updates werden Ihnen abgenommen. Daher sind Ihre Betriebskosten vermutlich niedriger im Vergleich zum IaaS-Ansatz von Azure Virtual Machines.

Alle drei Azure-Ausführungsmodelle haben Vor- und Nachteile. Für eine optimale Auswahl müssen Sie diese Vor- und Nachteile kennen, Ihre Ziele definieren und anschließend die beste Lösung auswählen.

Manchmal kann es sein, dass sich keines der Ausführungsmodelle perfekt eignet. In diesen Fällen können Sie eine Kombination der unterschiedlichen Optionen verwenden. Nehmen wir an, Sie möchten eine Anwendung erstellen und die Verwaltungsvorzüge von Cloud Services-Webrollen nutzen, müssen aber aus Kompatibilitäts- oder Leistungsgründen SQL Server verwenden. In diesem Fall können Sie die Ausführungsmodelle kombinieren, wie in [Abbildung 7][Abbildung 7] gezeigt.

<a name="Fig7"></a>![07\_CombineTechnologies][07\_CombineTechnologies]

**Abbildung 7: Eine einzige Anwendung kann mehrere Ausführungsmodelle verwenden.**

Wie die Abbildung zeigt, laufen die virtuellen Cloud Services-Computer in einem anderen Cloud-Dienst als die virtuellen Virtual Machines-Computer. Die beiden können dennoch effizient miteinander kommunizieren. Daher ist dieser Ansatz oft die beste Option.

Azure bietet verschiedene Ausführungsmodelle an, da Cloud-Plattformen viele verschiedene Szenarien unterstützen müssen. Wer diese Plattform effektiv einsetzen möchte, und wenn Sie bis hier gelesen haben, dann gehören Sie vermutlich zu diesen Personen, muss diese Modelle verstehen.

  [Virtual Machines]: #VMachine
  [Websites]: #WebSites
  [Cloud Services]: #CloudServices
  [Welches Modell sollte ich verwenden? Die Auswahl]: #WhatShouldIUse
  [Abbildung 1]: #Fig1
  [01\_CreatingVMs]: ./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png
  [VMDepot]: http://vmdepot.msopentech.com/
  [Azure-Bibliothek]: http://msdn.microsoft.com/de-de/library/windowsazure/dn197896.aspx
  [Abbildung 2]: #Fig2
  [02\_CloudServices]: ./media/fundamentals-application-models/ExecModels_02_CloudServices.png
  [Abbildung 3]: #Fig3
  [03\_AppUsingSQLServer]: ./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png
  [Abbildung 4]: #Fig4
  [04\_SharePointFarm]: ./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png
  [05\_Websites]: ./media/fundamentals-application-models/ExecModels_05_Websites.png
  [Abbildung 5]: #Fig5
  [Abbildung 6]: #Fig6
  [06\_CloudServices2]: ./media/fundamentals-application-models/ExecModels_06_CloudServices2.png
  [Abbildung 7]: #Fig7
  [07\_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
