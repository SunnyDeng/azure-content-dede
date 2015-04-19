<properties 
	pageTitle="Vergleich von Azure-Websites, Cloud Services und Virtual Machines" 
	description="Erfahren Sie, wann Sie Azure-Websites, Cloud Services und Virtual Machines zum Hosten von Webanwendungen einsetzen." 
	services="web-sites, virtual-machines, cloud-services" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="tdykstra"/>

# Vergleich von Azure-Websites, Cloud Services und Virtual Machines

## Übersicht

Azure bietet verschiedene Möglichkeiten zum Hosten von Websites: [Azure-Websites][], [Cloud-Dienste][] und [Virtual Machines][]. Dieser Artikel unterstützt Sie dabei, die entsprechenden Optionen erfassen und die richtige Wahl für Ihre Webanwendung treffen zu können.

Azure-Websites sind die beste Wahl für die meisten Web-Apps. Die Bereitstellungs- und die Verwaltungsfunktionen sind in die Plattform integriert. Sites können rasch skaliert werden, um umfangreiche Datenaufkommen zu bewältigen. Außerdem bieten der integrierte Lastenausgleich und der Traffic Manager eine hohe Verfügbarkeit. Sie können bestehende Sites mit einem [Online-Migrationstool](https://www.migratetoazure.net/) problemlos in Azure-Websites verschieben, eine Open Source-App aus der Web App Gallery verwenden oder eine neue Site mithilfe des Frameworks und der Tools Ihrer Wahl erstellen. Die [Webauftragsfunktion][] erleichtert das Hinzufügen der Hintergrund-Auftragsverarbeitung zur App. 

Wenn Sie mehr Kontrolle über die Webserver-Umgebung benötigen, wie etwa die Fähigkeit eines Fernzugriffs auf den Server oder der Konfiguration von Server-Startaufgaben, sind Azure-Cloud-Dienste üblicherweise die beste Option.

Wenn Sie über eine vorhandene Anwendung verfügen, die für die Ausführung unter Azure-Websites oder Azure Cloud Services erhebliche Änderungen erforderlich macht, können Sie Azure Virtual Machines wählen, um die Migration in die Cloud zu vereinfachen. Eine korrekte Konfiguration, Sicherung und Wartung von VMs erfordert im Vergleich zu Azure-Websites oder Cloud Services jedoch weitaus mehr Zeit und IT-Kenntnisse. Wenn Sie die Nutzung von Azure Virtual Machines in Erwägung ziehen, müssen Sie den fortlaufenden Wartungsaufwand berücksichtigen, der für das Anpassen, Aktualisieren oder Verwalten der VM-Umgebung erforderlich ist.  

Das folgende Diagramm veranschaulicht den relativen Grad der Steuerung im Gegensatz zur leichten Anwendbarkeit bei jeder dieser Webhostingoptionen unter Azure. 

![ChoicesDiagram][ChoicesDiagram]

## <a name="scenarios"></a>Szenarien und Empfehlungen

Im Folgenden sind einige Anwendungsszenarien mit Empfehlungen dahingehend aufgeführt, welche Azure-Webhostingoption jeweils am geeignetsten ist.

- [Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um mit lokalen Medienobjekten integrierte Geschäftsanwendungen auszuführen.](#onprem)
- [Ich benötige eine zuverlässige Möglichkeit, um meine Unternehmenswebsite zu hosten, die optimal skaliert und global ausgerichtet ist.](#corp)
- [Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.](#iis6)
- [Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.](#smallbusiness)
- [Ich bin ein Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.](#designer)
- [Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.](#multitier)
- [Meine Anwendung hängt von umfassend benutzerspezifisch angepassten Windows- oder Linux-Umgebungen ab und ich möchte die Anwendung in die Cloud verschieben.](#custom)
- [Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.](#oss)
- [Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.](#lob)
- [Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.](#mobile)


### <a id="onprem"></a> Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um mit lokalen Medienobjekten integrierte Geschäftsanwendungen auszuführen.

Azure-Websites stellen eine hervorragende Lösung für komplexe Geschäftsanwendungen dar. Mit ihnen können Sie Apps entwickeln, die automatisch auf einer Lastenausgleichsplattform skaliert, mit Active Directory gesichert und mit Ihren lokalen Ressourcen verbunden werden können. Dadurch wird das Verwalten dieser Apps mithilfe eines erstklassigen Verwaltungsportals und mithilfe von APIs erleichtert. Sie erhalten außerdem Einblicke dahingehend, wie Kunden die Apps mithilfe von App Insight Tools verwenden. Mit der neuen [Webauftragsfunktion][] können Sie Hintergrundprozesse und -aufgaben als Teil der Webebene ausführen, während Hybridkonnektivität und [VNET-Funktionen](../fundamentals-introduction-to-azure/#networking/) eine Rückverbindung zu lokalen Ressourcen erleichtern. Azure-Websites stellen drei 9's-SLAs bereit und ermöglichen es Ihnen, folgende Aktionen vorzunehmen:

* Zuverlässiges Ausführen Ihrer Anwendungen auf einer selbst-reparierenden, automatisch patchenden Cloud-Plattform. 
* Automatisches Skalieren in einem globalen Netzwerk von Datencentern.
* Sichern und Wiederherstellen für die Notfallwiederherstellung. 
* Erlangen einer ISO-, SOC2- und PCI-Konformität.
* Integrieren in Active Directory

### <a id="corp"></a> Ich benötige eine zuverlässige Möglichkeit, um meine Unternehmenswebsite zu hosten, die optimal skaliert und global ausgerichtet ist.

Azure-Websites stellen eine hervorragende Lösung zum Hosten von Unternehmens-Websites dar. Sie ermöglichen ein rasches und problemloses Skalieren der Websites, um die Anforderungen in einem globalen Netzwerk von Datencentern zu erfüllen. Sie bieten lokalen Zugriff, eine entsprechende Fehlertoleranz und eine intelligente Traffic-Verwaltung. Erstklassige Verwaltungstools stehen Ihnen auf einer Plattform zur Verfügung und ermöglichen Ihnen rasch und problemlos Einblicke in den Zustand und das Datenaufkommen der Website. Azure-Websites stellen drei 9's-SLAs bereit und ermöglichen es Ihnen, folgende Aktionen vorzunehmen:

* Zuverlässiges Ausführen Ihrer Websites auf einer selbst-reparierenden, automatisch patchenden Cloud-Plattform. 
* Automatisches Skalieren in einem globalen Netzwerk von Datencentern.
* Sichern und Wiederherstellen für die Notfallwiederherstellung. 
* Verwalten der Protokolle und des Datenaufkommens mit integrierten Tools. 
* Erlangen einer ISO-, SOC2- und PCI-Konformität.
* Integrieren in Active Directory

### <a id="iis6"></a> Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.

Mit Azure-Websites lassen sich die mit dem Migrieren von älteren IIS6-Anwendungen verbundenen Infrastrukturkosten ohne Weiteres vermeiden. Microsoft hat [einfach zu verwendende Migrationstools und eine detaillierte Migrationsanleitung](https://www.movemetowebsites.net/) erstellt, mit deren Hilfe Sie die Kompatibilität überprüfen und etwaige vorzunehmende Änderungen identifizieren können. Die Integration in Visual Studio, TFS oder allgemeine CMS-Tools erleichtert die Bereitstellung von IIS6-Anwendungen direkt in der Cloud. Sobald eine Bereitstellung erfolgt ist, stellt das Azure-Verwaltungsportal stabile Verwaltungstools zur Verfügung. Mit diesen können Sie eine Skalierung nach unten oder oben vornehmen, um je nach Bedarf Kosten zu verwalten oder Anforderungen zu erfüllen. Mit dem Migrationstool können Sie folgende Aktionen vornehmen:

* Schnelles und einfaches Migrieren der Windows Server 2003 Legacy-Webanwendungen zur Cloud.
* Optionales Verlassen der beigefügten lokalen SQL-Datenbank, um eine Hybridanwendung zu erstellen. 
* Automatisches Verschieben der SQL-Datenbank zusammen mit der Legacyanwendung. 

### <a id="smallbusiness"></a>Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.

Azure-Websites sind eine tolle Lösung für dieses Szenario, da Sie mit einer kostenlosen Nutzung beginnen und später bei Bedarf weitere Funktionen hinzufügen können. Jede freie Website geht mit einer Domäne einher, die von Azure bereitgestellt wird (*Ihr_Unternehmen.azurewebsites.net*). Die Plattform beinhaltet integrierte Bereitstellungs- und Verwaltungstools sowie eine Anwendungsgalerie, welche die ersten Schritte erleichtert. Es gibt viele weitere Dienste und Skalierungsoptionen, dank derer Sie die Website mit steigenden Benutzeranforderungen weiterentwickeln können. Azure-Websites ermöglichen Ihnen Folgendes:

- Beginnen mit einer kostenlosen Ebene und nach oben Skalieren dieser je nach Anforderungen
- Verwenden der Web App Gallery, um schnell häufig genutzte Webanwendungen wie WordPress einzurichten
- Hinzufügen zusätzlicher Dienste und Funktionen von Azure zu Ihrer Anwendung, je nach Bedarf
- Sichern der Website mit HTTPS

### <a id="designer"></a> Ich bin Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.

Für Webentwickler und -designer lassen sich Azure-Websites leicht in eine Reihe von Frameworks und Tools integrieren. Sie umfassen eine Bereitstellungsunterstützung für Git und FTP und bieten eine nahtlose Integration in Tools und Dienste wie Visual Studio oder SQL-Datenbank. Websites bieten folgende Möglichkeiten:

- Verwenden von Befehlszeilentools für [automatisierte Aufgaben][scripting].
- Arbeiten mit gängigen Sprachen wie [.Net][dotnet], [PHP][], [Node.js][nodejs] und [Python][].
- Wählen drei verschiedener Skalierungsebenen für die Skalierung auf sehr hohe Kapazitäten
- Integrieren in andere Azure-Dienste, wie [SQL-Datenbank][sqldatabase], [Service Bus][servicebus] und [Storage][], oder Partnerangebote aus dem [Azure Store][azurestore], beispielsweise MySQL und MongoDB.
- Integrieren in Tools wie Visual Studio, Git, WebMatrix, WebDeploy, TFS und FTP

### <a id="multitier"></a>Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.

Wenn Sie eine Webanwendung mit mehreren Ebenen ausführen, wie etwa einen Webserver, der mit einer Datenbank verbunden ist, stellen Azure-Websites eine gute Option dar, welche eine nahtlose Integration in die Azure SQL-Datenbank ermöglicht. Außerdem können Sie die Webauftragsfunktion zum Ausführen von Hintergrundprozessen verwenden.

Wählen Sie den Cloud-Dienst für eine oder mehrere der Ebenen, wenn Sie mehr Kontrolle über die Serverumgebung benötigen. Dies kann beispielsweise die Fähigkeit betreffen, von der Ferne aus auf Ihren Server zuzugreifen oder die Serverstartaufgaben zu konfigurieren.

Wählen Sie die virtuellen Computer für eine oder mehrere der Ebenen, wenn Sie Ihr eigenes Computer-Image verwenden oder Serversoftware oder Dienste ausführen möchten, die Sie nicht in den Cloud-Diensten konfigurieren können. 

### <a id="custom"></a>Meine Anwendung hängt von umfassend benutzerspezifisch angepassten Windows- oder Linux-Umgebungen ab und ich möchte die Anwendung in die Cloud verschieben.

Wenn Ihre Anwendung komplexe Installation oder Konfiguration von Software und Betriebssystem erfordert, ist Virtual Machines die beste Lösung. Virtual Machines ermöglichen Ihnen Folgendes:

- Verwenden des Virtual Machines-Katalogs, um mit einem Betriebssystem wie Windows oder Linux zu beginnen und dieses später an Ihre Anwendungsanforderungen anzupassen 
- Erstellen und Hochladen eines maßgeschneiderten Image eines bereits vorhandenen lokalen Servers für die Ausführung auf einem virtuellen Computer in Azure 

### <a id="oss"></a>Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.

Wenn Ihr Open Source-Framework auf den Websites unterstützt wird, werden die von Ihrer Anwendung benötigten Sprachen und Frameworks automatisch für Sie konfiguriert. Websites bieten die folgenden Funktionen:

- Verwenden vieler gängiger Open Source-Sprachen wie [.NET][dotnet], [PHP][], [Node.js][nodejs] und [Python][]. 
- Einrichten von WordPress, Drupal, Umbraco, DNN und vielen weiteren Webanwendungen von Drittanbietern 
- Migrieren vorhandener Anwendungen oder Erstellen einer neuen mit der Web App Gallery 

Wenn Ihr Open Source-Framework nicht auf den Websites unterstützt wird, können Sie dieses unter einer der beiden anderen Azure-Webhostingoptionen ausführen. Bei Cloud-Diensten verwenden Sie Startaufgaben, um erforderliche Open-Source-Software, die unter Windows ausgeführt wird, zu installieren und zu konfigurieren. Mit Virtual Machines können Sie die Software auf dem Image eines virtuellen Computers installieren und konfigurieren. Dieser kann Windows- oder Linux-basiert sein. 

### <a id="lob"></a>Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.

Wenn Sie eine Branchenanwendung erstellen möchten, benötigt Ihre Website möglicherweise direkten Zugriff auf Dienste oder Daten in Ihrem Unternehmensnetzwerk. Dies ist auf Websites, in Cloud-Diensten oder auf virtuellen Computern möglich, die den [virtuellen Azure-Netzwerkdienst](/services/virtual-network/) verwenden. Auf Websites können Sie die neue [VNET-Integrationsfunktion](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) verwenden. Die Funktion ermöglicht eine Ausführung Ihrer Azure-Anwendungen, als würden sich diese in Ihrem Unternehmensnetzwerk befinden.

### <a id="mobile"></a>Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.

HTTP-basierte Webdienste ermöglichen die Unterstützung vieler unterschiedlicher Clients, einschließlich mobiler Clients. Frameworks wie ASP.NET-Web-API können in Visual Studio integriert werden. Dies erleichtert die Erstellung und Verwendung von REST-Diensten.  Diese Dienste sind von einem Webendpunkt aus exponiert, sodass es möglich ist, jede beliebige Webhostingtechnik in Azure anzuwenden, um dieses Szenario zu unterstützen. Websites sind jedoch auch eine gute Wahl für das Hosten von REST-APIs. Websites bieten folgende Möglichkeiten:

- Schnelles Erstellen einer Website für das Hosten des HTTP-Webdiensts in einem der global verteilten Datencenter von Azure
- Migrieren von vorhandenen Diensten oder Erstellen von neuen
- Erreichen von SLA zur Verfügbarkeit mit einer einzelnen Instanz oder horizontale Skalierung auf mehrere dedizierte Computer 
- Verwenden der veröffentlichten Website, um für HTTP-Clients REST-APIs bereitzustellen, auch für mobile Clients

## <a name="features"></a>Funktionsvergleiche

In der folgenden Tabelle werden die Funktionen von Websites, Cloud Services und Virtual Machines verglichen, um Sie bei Ihrer Wahl zu unterstützen. Aktuelle Informationen über die Vereinbarung zum Servicelevel (SLA) in Bezug auf die jeweilige Option finden Sie unter [Azure-Vereinbarung zum Servicelevel](/support/legal/sla/).

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Funktion</th>
   <th align="left" valign="middle">Websites</th>
   <th align="left" valign="middle">Cloud Services (Webrollen)</th>
   <th align="left" valign="middle">Virtuelle Computer</th>
   <th align="left" valign="middle">Hinweise</th>
</tr>
<tr>
   <td valign="middle"><p>Zeitnahe Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">Die Bereitstellung einer Anwendung oder einer Anwendungsaktualisierung über den Cloud-Dienst oder das Erstellen eines virtuellen Computers nimmt mindestens einige Minuten in Anspruch. Die Bereitstellung einer Anwendung auf einer Website dauert nur Sekunden.</td>
</tr>
<tr>
   <td valign="middle"><p>Skalierung auf leistungsstärkere Computer ohne erneute Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Webserver-Instanzen weisen gemeinsame Inhalte und eine gemeinsame Konfiguration auf. Dies bedeutet, dass Sie bei einer Skalierung nicht neu bereitstellen oder neu konfigurieren müssen.</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Mehrere Bereitstellungsumgebungen (Produktion und Staging)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Automatische Aktualisierungsverwaltung für das Betriebssystem</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Nahtloser Plattformwechsel (einfacher Wechsel zwischen 32 Bit und 64 Bit)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Bereitstellung von Code mit Git, FTP</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Bereitstellung von Code mit Web Deploy</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services unterstützen die Verwendung von Web Deploy, um Updates für einzelne Rolleninstanzen bereitzustellen. Sie können sie allerdings nicht für die Erstbereitstellung einer Rolle verwenden. Wenn Sie außerdem Web Deploy für eine Aktualisierung verwenden, müssen Sie jeweils eine separate Bereitstellung für jede Instanz einer Rolle vornehmen. Mehrfache Instanzen sind erforderlich, um für die Cloud-Dienst-SLA bei Produktionsumgebungen qualifiziert zu sein.</td>
</tr>
<tr>
   <td valign="middle"><p>Unterstützung von WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Zugriff auf Dienste wie Service Bus, Storage und SQL-Datenbank</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Hosten der Web- oder Webdienstebene einer mehrschichtigen Architektur</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Hosten der mittleren Ebene einer mehrschichtigen Architektur</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Websites können problemlos eine mittlere Ebene der REST-API hosten. Die <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a>-Funktion der Websites kann Hintergrundverarbeitungsjobs hosten. Sie können WebJobs auf einer dedizierten Website ausführen, um eine unabhängige Skalierbarkeit für die entsprechende Ebene zu erzielen.</td>
</tr>
<tr>
   <td valign="middle"><p>Integrierte Unterstützung von MySQL-as-a-Service</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services können in MySQL-as-a-Service über Angebote von ClearDB integriert werden, jedoch nicht als Teil des Verwaltungsportal-Workflows.</td>
</tr>
<tr>
   <td valign="middle"><p>Unterstützung von ASP.NET, klassischem ASP, Node.js, PHP, Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Horizontale Skalierung auf mehrere Instanzen ohne erneute Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Bei virtuellen Computern ist eine horizontale Skalierung auf mehrere Instanzen möglich. Die Dienste, die auf diesen Computern ausgeführt werden, müssen jedoch so geschrieben werden, dass sie diese horizontale Skalierung auffangen. Sie müssen einen Lastenausgleich konfigurieren, um Anforderungen über die Computer weiterzuleiten. Zudem müssen Sie eine Affinitätsgruppe erstellen, um gleichzeitigen Neustarts aller Instanzen aufgrund von Wartungs- oder Hardware-Fehlern vorzubeugen.</td>
</tr>
<tr>
   <td valign="middle"><p>SSL-Unterstützung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Bei Websites wird SSL für benutzerdefinierte Domänennamen nur im Basic- und im Standardmodus unterstützt. Informationen zur Verwendung von SSL mit Websites finden Sie unter <a href="../web-sites-configure-ssl-certificate/">Konfigurieren eines SSL-Zertifikats für eine Azure-Website</a>.</td>
</tr>
<tr>
   <td valign="middle"><p>Visual Studio-Integration</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Remotedebuggen</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Bereitstellung von Code mit TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Netzwerkisolation mit <a href="/services/virtual-network/">Azure Virtual Network</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Siehe auch <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Azure Websites Virtual Network Integration</a></td>
</tr>
<tr>
   <td valign="middle"><p>Unterstützung für <a href="/services/traffic-manager/">Azure Traffic Manager</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Integrierte Endpunktüberwachung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Remote-Desktopzugriff auf Server</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Installieren einer beliebigen benutzerdefinierten MSI</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Möglichkeit zum Definieren/Ausführen von Startaufgaben</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Kann ETW-Ereignisse abhören</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>


> [AZURE.NOTE]
> Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


## <a id="nextsteps"></a> Nächste Schritte

Weitere Informationen zu den drei Webhostingoptionen finden Sie in den folgenden Ressourcen:

* [Einführung in Azure](fundamentals-introduction-to-azure.md)
* [Serverhostingoptionen in Azure](fundamentals-application-models.md)

Informationen zu den ersten Schritten hinsichtlich der Option(en), die Sie für Ihre Anwendung wählen, finden Sie in folgenden Ressourcen:

* [Azure-Websites](/documentation/services/websites/)
* [Azure Cloud Services](/documentation/services/cloud-services/)
* [Azure Virtual Machines](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Auswahldiagramm]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Azure-Websites]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Cloud-Dienste]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
  [Virtuelle Computer]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [Webauftragsfunktion]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Konfigurieren eines SSL-Zertifikats für eine Azure-Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [Storage]: http://www.windowsazure.com/documentation/services/storage/
  [Speicher]: http://www.windowsazure.com/documentation/services/storage/

<!--HONumber=47-->
