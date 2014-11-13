<properties pageTitle="Vergleich von Azure-Websites, Cloud Services und Virtual Machines" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Erfahren Sie, wann Sie Azure-Websites, Cloud-Dienste oder virtuelle Computer zum Hosten von Webanwendungen einsetzen." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Vergleich von Azure-Websites, Cloud Services und Virtual Machines

## Übersicht

Azure bietet verschiedene Möglichkeiten zum Hosten von Websites: [Azure-Websites][Azure-Websites], [Cloud Services][Cloud Services] und [Virtual Machines][Virtual Machines]. Dieser Artikel unterstützt Sie dabei, die entsprechenden Optionen erfassen und die richtige Wahl für Ihre Webanwendung treffen zu können.

Azure-Websites sind die beste Wahl für die meisten Web-Apps. Die Bereitstellungs- und die Verwaltungsfunktionen sind in die Plattform integriert. Sites können rasch skaliert werden, um umfangreiche Datenaufkommen zu bewältigen. Außerdem bieten der integrierte Lastenausgleich und der Traffic Manager eine hohe Verfügbarkeit. Sie können bestehende Sites mit einem [Online-Migrationstool][Online-Migrationstool] problemlos in Azure-Websites verschieben, eine Open Source-App aus der Web App Gallery verwenden oder eine neue Site mithilfe des Frameworks und der Tools Ihrer Wahl erstellen. Die [WebJobs][WebJobs]-Funktion (aktuell in der Vorschau) erleichtert das Hinzufügen der Hintergrund-Auftragsverarbeitung zur App.

Wenn Sie mehr Kontrolle über die Webserver-Umgebung benötigen, wie etwa die Fähigkeit eines Fernzugriffs auf den Server oder der Konfiguration von Server-Startaufgaben, sind Azure Cloud Services üblicherweise die beste Option.

Wenn Sie über eine vorhandene Anwendung verfügen, die für die Ausführung unter Azure-Websites oder Azure Cloud Services erhebliche Änderungen erforderlich macht, können Sie Azure Virtual Machines wählen, um die Migration in die Cloud zu vereinfachen. Eine korrekte Konfiguration, Sicherung und Wartung von VMs erfordert im Vergleich zu Azure-Websites oder Cloud Services jedoch weitaus mehr Zeit und IT-Kenntnisse. Wenn Sie die Nutzung von Azure Virtual Machines in Erwägung ziehen, müssen Sie den fortlaufenden Wartungsaufwand berücksichtigen, der für das Anpassen, Aktualisieren oder Verwalten der VM-Umgebung erforderlich ist.

Das folgende Diagramm veranschaulicht den relativen Grad der Steuerung im Gegensatz zur leichten Anwendbarkeit bei jeder dieser Webhostingoptionen unter Azure.

![Auswahldiagramm][Auswahldiagramm]

## Inhaltsverzeichnis

-   [Szenarien und Empfehlungen][Szenarien und Empfehlungen]
-   [Funktionsvergleiche][Funktionsvergleiche]
-   [Nächste Schritte][Nächste Schritte]

## <a name="scenarios"></a>Szenarien und Empfehlungen

Im Folgenden sind einige Anwendungsszenarien mit Empfehlungen dahingehend aufgeführt, welche Azure-Webhostingoption jeweils am geeignetsten ist.

-   [Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um mit lokalen Medienobjekten integrierte Geschäftsanwendungen auszuführen.][Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um mit lokalen Medienobjekten integrierte Geschäftsanwendungen auszuführen.]
-   [Ich benötige eine zuverlässige Möglichkeit, um meine Unternehmenswebsite zu hosten, die optimal skaliert und global ausgerichtet ist.][Ich benötige eine zuverlässige Möglichkeit, um meine Unternehmenswebsite zu hosten, die optimal skaliert und global ausgerichtet ist.]
-   [Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.][Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.]
-   [Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.][Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.]
-   [Ich bin ein Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.][Ich bin ein Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.]
-   [Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.][Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.]
-   [Meine Anwendung hängt von umfassend benutzerspezifisch angepassten Windows- oder Linux-Umgebungen ab und ich möchte die Anwendung in die Cloud verschieben.][Meine Anwendung hängt von umfassend benutzerspezifisch angepassten Windows- oder Linux-Umgebungen ab und ich möchte die Anwendung in die Cloud verschieben.]
-   [Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.][Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.]
-   [Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.][Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.]
-   [Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.][Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.]

### <span id="onprem"></span></a> Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um mit lokalen Medienobjekten integrierte Geschäftsanwendungen auszuführen.

Azure-Websites stellen eine hervorragende Lösung für komplexe Geschäftsanwendungen dar. Mit ihnen können Sie Apps entwickeln, die automatisch auf einer Lastenausgleichsplattform skaliert, mit Active Directory gesichert und mit Ihren lokalen Ressourcen verbunden werden können. Dadurch wird das Verwalten dieser Apps mithilfe eines erstklassigen Verwaltungsportals und mithilfe von APIs erleichtert. Sie erhalten außerdem Einblicke dahingehend, wie Kunden die Apps mithilfe von App Insight Tools verwenden. Mit der neuen Funktion [WebJobs][WebJobs] können Sie Hintergrundprozesse und Aufgaben als Teil der Webebene ausführen, während die Hybridkonnektivität und die [VNET-Funktionen][VNET-Funktionen] die Verbindung zurück zu den lokalen Ressourcen ermöglichen. Azure-Websites stellen drei 9's-SLAs bereit und ermöglichen es Ihnen, folgende Aktionen vorzunehmen:

-   Zuverlässiges Ausführen Ihrer Anwendungen auf einer selbst-reparierenden, automatisch patchenden Cloud-Plattform.
-   Automatisches Skalieren in einem globalen Netzwerk von Datencentern.
-   Sichern und Wiederherstellen für die Notfallwiederherstellung.
-   Erlangen einer ISO-, SOC2- und PCI-Konformität.
-   Integrieren in Active Directory

### <span id="corp"></span></a> Ich benötige eine zuverlässige Möglichkeit, um meine Unternehmenswebsite zu hosten, die optimal skaliert und global ausgerichtet ist.

Azure-Websites stellen eine hervorragende Lösung zum Hosten von Unternehmens-Websites dar. Sie ermöglichen ein rasches und problemloses Skalieren der Websites, um die Anforderungen in einem globalen Netzwerk von Datencentern zu erfüllen. Sie bieten lokalen Zugriff, eine entsprechende Fehlertoleranz und eine intelligente Traffic-Verwaltung. Erstklassige Verwaltungstools stehen Ihnen auf einer Plattform zur Verfügung und ermöglichen Ihnen rasch und problemlos Einblicke in den Zustand und das Datenaufkommen der Website. Azure-Websites stellen drei 9's-SLAs bereit und ermöglichen es Ihnen, folgende Aktionen vorzunehmen:

-   Zuverlässiges Ausführen Ihrer Websites auf einer selbst-reparierenden, automatisch patchenden Cloud-Plattform.
-   Automatisches Skalieren in einem globalen Netzwerk von Datencentern.
-   Sichern und Wiederherstellen für die Notfallwiederherstellung.
-   Verwalten der Protokolle und des Datenaufkommens mit integrierten Tools.
-   Erlangen einer ISO-, SOC2- und PCI-Konformität.
-   Integrieren in Active Directory

### <span id="iis6"></span></a> Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.

Mit Azure-Websites lassen sich die mit dem Migrieren von älteren IIS6-Anwendungen verbundenen Infrastrukturkosten ohne Weiteres vermeiden. Microsoft hat [einfach zu verwendende Migrationstools und eine detaillierte Migrationsanleitung][einfach zu verwendende Migrationstools und eine detaillierte Migrationsanleitung] erstellt, mit deren Hilfe Sie die Kompatibilität überprüfen und etwaige vorzunehmende Änderungen identifizieren können. Die Integration in Visual Studio, TFS oder allgemeine CMS-Tools erleichtert die Bereitstellung von IIS6-Anwendungen direkt in der Cloud. Sobald eine Bereitstellung erfolgt ist, stellt das Azure-Verwaltungsportal stabile Verwaltungstools zur Verfügung. Mit diesen können Sie eine Skalierung nach unten oder oben vornehmen, um je nach Bedarf Kosten zu verwalten oder Anforderungen zu erfüllen. Mit dem Migrationstool können Sie folgende Aktionen vornehmen:

-   Schnelles und einfaches Migrieren der Windows Server 2003 Legacy-Webanwendungen zur Cloud.
-   Optionales Verlassen der beigefügten lokalen SQL-Datenbank, um eine Hybridanwendung zu erstellen.
-   Automatisches Verschieben der SQL-Datenbank zusammen mit der Legacyanwendung.

### <span id="smallbusiness"></span></a>Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.

Azure-Websites sind eine tolle Lösung für dieses Szenario, da Sie mit einer kostenlosen Nutzung beginnen und später bei Bedarf weitere Funktionen hinzufügen können. Jede freie Website geht mit einer Domäne einher, die von Azure bereitgestellt wird (*Ihr\_Unternehmen*.azurewebsites.net). Die Plattform beinhaltet integrierte Bereitstellungs- und Verwaltungstools sowie eine Anwendungsgalerie, welche die ersten Schritte erleichtert. Es gibt viele weitere Dienste und Skalierungsoptionen, dank derer Sie die Website mit steigenden Benutzeranforderungen weiterentwickeln können. Azure-Websites ermöglichen Ihnen Folgendes:

-   Beginnen mit einer kostenlosen Ebene und nach oben Skalieren dieser je nach Anforderungen
-   Verwenden der Web App Gallery, um schnell häufig genutzte Webanwendungen wie WordPress einzurichten
-   Hinzufügen zusätzlicher Dienste und Funktionen von Azure zu Ihrer Anwendung, je nach Bedarf
-   Sichern der Website mit HTTPS

### <span id="designer"></span></a> Ich bin Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.

Für Webentwickler und -designer lassen sich Azure-Websites leicht in eine Reihe von Frameworks und Tools integrieren. Sie umfassen eine Bereitstellungsunterstützung für Git und FTP und bieten eine nahtlose Integration in Tools und Dienste wie Visual Studio oder SQL-Datenbank. Websites bieten folgende Möglichkeiten:

-   Verwenden von Befehlszeilentools für [automatisierte Aufgaben][automatisierte Aufgaben]
-   Arbeiten mit gängigen Sprachen wie [.Net][.Net], [PHP][PHP], [Node.js][Node.js] und [Python][Python]
-   Wählen drei verschiedener Skalierungsebenen für die Skalierung auf sehr hohe Kapazitäten
-   Integrieren in andere Azure-Dienste, wie [SQL-Datenbank][SQL-Datenbank], [Service Bus][Service Bus] und [Storage][Storage], oder Partnerangeboten aus dem [Azure Store][Azure Store], beispielsweise MySQL und MongoDB
-   Integrieren in Tools wie Visual Studio, Git, WebMatrix, WebDeploy, TFS und FTP

### <span id="multitier"></span></a>Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.

Wenn Sie eine Webanwendung mit mehreren Ebenen ausführen, wie etwa einen Webserver, der mit einer Datenbank verbunden ist, stellen Azure-Websites eine gute Option dar, welche eine nahtlose Integration in die Azure SQL-Datenbank ermöglicht. Außerdem können Sie die neue WebJobs-Funktion (in der Vorschau) zum Ausführen von Hintergrundprozessen verwenden.

Wählen Sie den Cloud-Dienst für eine oder mehrere der Ebenen, wenn Sie mehr Kontrolle über die Serverumgebung benötigen. Dies kann beispielsweise die Fähigkeit betreffen, von der Ferne aus auf Ihren Server zuzugreifen oder die Serverstartaufgaben zu konfigurieren.

Wählen Sie die virtuellen Computer für eine oder mehrere der Ebenen, wenn Sie Ihr eigenes Computer-Image verwenden oder Serversoftware oder Dienste ausführen möchten, die Sie nicht in den Cloud-Diensten konfigurieren können.

### <span id="custom"></span></a>Meine Anwendung hängt von umfassend benutzerspezifisch angepassten Windows- oder Linux-Umgebungen ab und ich möchte die Anwendung in die Cloud verschieben.

Wenn Ihre Anwendung komplexe Installation oder Konfiguration von Software und Betriebssystem erfordert, ist Virtual Machines die beste Lösung. Virtual Machines ermöglichen Ihnen Folgendes:

-   Verwenden des Virtual Machines-Katalogs, um mit einem Betriebssystem wie Windows oder Linux zu beginnen und dieses später an Ihre Anwendungsanforderungen anzupassen
-   Erstellen und Hochladen eines maßgeschneiderten Image eines bereits vorhandenen lokalen Servers für die Ausführung auf einem virtuellen Computer in Azure

### <span id="oss"></span></a>Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.

Wenn Ihr Open Source-Framework auf den Websites unterstützt wird, werden die von Ihrer Anwendung benötigten Sprachen und Frameworks automatisch für Sie konfiguriert. Websites bieten die folgenden Funktionen:

-   Verwenden vieler gängiger Open-Source-Sprachen wie [.NET][.Net], [PHP][PHP], [Node.js][Node.js] und [Python][Python]
-   Einrichten von WordPress, Drupal, Umbraco, DNN und vielen weiteren Webanwendungen von Drittanbietern
-   Migrieren vorhandener Anwendungen oder Erstellen einer neuen mit der Web App Gallery

Wenn Ihr Open Source-Framework nicht auf den Websites unterstützt wird, können Sie dieses unter einer der beiden anderen Azure-Webhostingoptionen ausführen. Bei Cloud-Diensten verwenden Sie Startaufgaben, um erforderliche Open-Source-Software, die unter Windows ausgeführt wird, zu installieren und zu konfigurieren. Mit Virtual Machines können Sie die Software auf dem Image eines virtuellen Computers installieren und konfigurieren. Dieser kann Windows- oder Linux-basiert sein.

### <span id="lob"></span></a>Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.

Wenn Sie eine Branchenanwendung erstellen möchten, benötigt Ihre Website möglicherweise direkten Zugriff auf Dienste oder Daten in Ihrem Unternehmensnetzwerk. Dies ist auf Websites, in Cloud-Diensten oder auf virtuellen Computern möglich, die den [virtuellen Azure-Netzwerkdienst][virtuellen Azure-Netzwerkdienst] verwenden. Auf Websites können Sie die neue [VNET-Integrationsfunktion][VNET-Integrationsfunktion] verwenden. Die Funktion ermöglicht eine Ausführung Ihrer Azure-Anwendungen, als würden sich diese in Ihrem Unternehmensnetzwerk befinden.

### <span id="mobile"></span></a>Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.

HTTP-basierte Webdienste ermöglichen die Unterstützung vieler unterschiedlicher Clients, einschließlich mobiler Clients. Frameworks wie ASP.NET-Web-API können in Visual Studio integriert werden. Dies erleichtert die Erstellung und Verwendung von REST-Diensten. Diese Dienste sind von einem Webendpunkt aus exponiert, sodass es möglich ist, jede beliebige Webhostingtechnik in Azure anzuwenden, um dieses Szenario zu unterstützen. Websites sind jedoch auch eine gute Wahl für das Hosten von REST-APIs. Websites bieten folgende Möglichkeiten:

-   Schnelles Erstellen einer Website für das Hosten des HTTP-Webdiensts in einem der global verteilten Datencenter von Azure
-   Migrieren von vorhandenen Diensten oder Erstellen von neuen
-   Erreichen von SLA zur Verfügbarkeit mit einer einzelnen Instanz oder horizontale Skalierung auf mehrere dedizierte Computer
-   Verwenden der veröffentlichten Website, um für HTTP-Clients REST-APIs bereitzustellen, auch für mobile Clients

## <a name="features"></a>Funktionsvergleiche

In der folgenden Tabelle werden die Funktionen von Websites, Cloud Services und Virtual Machines verglichen, um Sie bei Ihrer Wahl zu unterstützen. Aktuelle Informationen über die Vereinbarung zum Servicelevel (SLA) in Bezug auf die jeweilige Option finden Sie unter [Azure-Vereinbarung zum Servicelevel][Azure-Vereinbarung zum Servicelevel].

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Funktion</th>
<th align="left">Websites</th>
<th align="left">Cloud Services (Webrollen)</th>
<th align="left">Virtuelle Computer</th>
<th align="left">Hinweise</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Zeitnahe Bereitstellung</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">Die Bereitstellung einer Anwendung oder einer Anwendungsaktualisierung über den Cloud-Dienst oder das Erstellen eines virtuellen Computers nimmt mindestens einige Minuten in Anspruch. Die Bereitstellung einer Anwendung auf einer Website dauert nur Sekunden.</td>
</tr>
<tr class="even">
<td align="left"><p>Skalierung auf leistungsstärkere Computer ohne erneute Bereitstellung</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Webserver-Instanzen weisen gemeinsame Inhalte und eine gemeinsame Konfiguration auf. Dies bedeutet, dass Sie bei einer Skalierung nicht neu bereitstellen oder neu konfigurieren müssen.</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Mehrere Bereitstellungsumgebungen (Produktion und Staging)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Automatische Aktualisierungsverwaltung für das Betriebssystem</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Nahtloser Plattformwechsel (einfacher Wechsel zwischen 32 Bit und 64 Bit)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Bereitstellung von Code mit Git, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Bereitstellung von Code mit Web Deploy</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">Cloud Services unterstützen die Verwendung von Web Deploy, um Updates für einzelne Rolleninstanzen bereitzustellen. Sie können sie allerdings nicht für die Erstbereitstellung einer Rolle verwenden. Wenn Sie außerdem Web Deploy für eine Aktualisierung verwenden, müssen Sie jeweils eine separate Bereitstellung für jede Instanz einer Rolle vornehmen. Mehrfache Instanzen sind erforderlich, um für die Cloud-Dienst-SLA bei Produktionsumgebungen qualifiziert zu sein.</td>
</tr>
<tr class="odd">
<td align="left"><p>Unterstützung von WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Zugriff auf Dienste wie Service Bus, Storage und SQL-Datenbank</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Hosten der Web- oder Webdienstebene einer mehrschichtigen Architektur</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Hosten der mittleren Ebene einer mehrschichtigen Architektur</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Websites können problemlos eine mittlere Ebene der REST-API hosten. Die <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a>-Funktion der Websites (aktuell in der Vorschau) kann Hintergrundverarbeitungsjobs hosten. Sie können WebJobs auf einer dedizierten Website ausführen, um eine unabhängige Skalierbarkeit für die entsprechende Ebene zu erzielen.</td>
</tr>
<tr class="odd">
<td align="left"><p>Integrierte Unterstützung von MySQL-as-a-Service</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Cloud Services können in MySQL-as-a-Service über Angebote von ClearDB integriert werden, jedoch nicht als Teil des Verwaltungsportal-Workflows.</td>
</tr>
<tr class="even">
<td align="left"><p>Unterstützung von ASP.NET, klassischem ASP, Node.js, PHP, Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Horizontale Skalierung auf mehrere Instanzen ohne erneute Bereitstellung</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Bei virtuellen Computern ist eine horizontale Skalierung auf mehrere Instanzen möglich. Die Dienste, die auf diesen Computern ausgeführt werden, müssen jedoch so geschrieben werden, dass sie diese horizontale Skalierung auffangen. Sie müssen einen Lastenausgleich konfigurieren, um Anforderungen über die Computer weiterzuleiten. Zudem müssen Sie eine Affinitätsgruppe erstellen, um gleichzeitigen Neustarts aller Instanzen aufgrund von Wartungs- oder Hardware-Fehlern vorzubeugen.</td>
</tr>
<tr class="even">
<td align="left"><p>SSL-Unterstützung</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Bei Websites wird SSL für benutzerdefinierte Domänennamen nur im Basic- und im Standardmodus unterstützt. Informationen zur Verwendung von SSL mit Websites finden Sie unter <a href="../web-sites-configure-ssl-certificate/">Configuring an SSL certificate for an Azure Website</a> (Konfigurieren eines SSL-Zertifikats für eine Azure-Website, in englischer Sprache).</td>
</tr>
<tr class="odd">
<td align="left"><p>Visual Studio-Integration</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Remote-Debuggen</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Bereitstellung von Code mit TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Netzwerkisolation mit <a href="/de-de/services/virtual-network/">Azure Virtual Network</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Siehe auch <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Azure Websites Virtual Network Integration</a></td>
</tr>
<tr class="odd">
<td align="left"><p>Unterstützung für <a href="/de-de/services/traffic-manager/">Azure Traffic Manager</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Integrierte Endpunktüberwachung</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Remote-Desktopzugriff auf Server</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Installieren einer beliebigen benutzerdefinierten MSI</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Möglichkeit zum Definieren/Ausführen von Startaufgaben</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Für Nachverfolgung und Debugging können benutzerdefinierte ETW-Ereignisse verwendet werden.</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a>Nächste Schritte

Weitere Informationen zu den drei Webhostingoptionen finden Sie in den folgenden Ressourcen:

-   [Einführung in Azure][Einführung in Azure]
-   [Azure-Ausführungsmodelle][Azure-Ausführungsmodelle]

Informationen zu den ersten Schritten hinsichtlich der Option(en), die Sie für Ihre Anwendung wählen, finden Sie in folgenden Ressourcen:

-   [Azure-Websites][2]
-   [Azure Cloud Services][Azure Cloud Services]
-   [Azure Virtual Machines][Azure Virtual Machines]

  [Azure-Websites]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
  [Online-Migrationstool]: https://www.migratetoazure.net/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Auswahldiagramm]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Szenarien und Empfehlungen]: #scenarios
  [Funktionsvergleiche]: #features
  [Nächste Schritte]: #nextsteps
  [Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um mit lokalen Medienobjekten integrierte Geschäftsanwendungen auszuführen.]: #onprem
  [Ich benötige eine zuverlässige Möglichkeit, um meine Unternehmenswebsite zu hosten, die optimal skaliert und global ausgerichtet ist.]: #corp
  [Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.]: #iis6
  [Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.]: #smallbusiness
  [Ich bin ein Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.]: #designer
  [Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.]: #multitier
  [Meine Anwendung hängt von umfassend benutzerspezifisch angepassten Windows- oder Linux-Umgebungen ab und ich möchte die Anwendung in die Cloud verschieben.]: #custom
  [Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.]: #oss
  [Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.]: #lob
  [Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.]: #mobile
  [VNET-Funktionen]: ../fundamentals-networking/
  [einfach zu verwendende Migrationstools und eine detaillierte Migrationsanleitung]: https://www.movemetowebsites.net/
  [automatisierte Aufgaben]: http://www.windowsazure.com/de-de/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/de-de/develop/net/
  [PHP]: http://www.windowsazure.com/de-de/develop/php/
  [Node.js]: http://www.windowsazure.com/de-de/develop/nodejs/
  [Python]: http://www.windowsazure.com/de-de/develop/python/
  [SQL-Datenbank]: http://www.windowsazure.com/de-de/documentation/services/sql-database/
  [Service Bus]: http://www.windowsazure.com/de-de/documentation/services/service-bus/
  [Storage]: http://www.windowsazure.com/de-de/documentation/services/storage/
  [Azure Store]: http://www.windowsazure.com/de-de/gallery/store/
  [virtuellen Azure-Netzwerkdienst]: /de-de/services/virtual-network/
  [VNET-Integrationsfunktion]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
  [Azure-Vereinbarung zum Servicelevel]: /de-de/support/legal/sla/
  [Einführung in Azure]: ../fundamentals-introduction-to-azure/
  [Azure-Ausführungsmodelle]: ../fundamentals-application-models/
  [2]: /de-de/documentation/services/websites/
  [Azure Cloud Services]: /de-de/documentation/services/cloud-services/
  [Azure Virtual Machines]: /de-de/documentation/services/virtual-machines/
