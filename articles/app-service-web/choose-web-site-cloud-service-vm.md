<properties
	pageTitle="Azure App Service, Cloud Services und Virtual Machines im Vergleich"
	description="Erfahren Sie, wann Sie Azure App Service, Cloud Services und Virtual Machines für das Hosten von Webanwendungen nutzen sollten."
	services="app-service\web, virtual-machines, cloud-services"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/29/2015"
	ms.author="tdykstra"/>

# Azure App Service, Cloud Services und Virtual Machines im Vergleich

## Übersicht

Azure bietet verschiedene Möglichkeiten für das Hosten von Websites: [Azure App Service][], [Cloud Services][] und [Virtual Machines][]. Dieser Artikel unterstützt Sie dabei, die verfügbaren Optionen zu verstehen und die richtige Wahl für Ihre Webanwendung zu treffen.

Azure App Service stellt für die meisten Web-Apps die beste Wahl dar. Die Bereitstellungs- und die Verwaltungsfunktionen sind in die Plattform integriert. Websites können rasch skaliert werden, um hohe Datenaufkommen zu bewältigen. Außerdem bieten der integrierte Lastenausgleich und Traffic Manager eine hohe Verfügbarkeit. Sie können vorhandene Websites mit einem [Online-Migrationstool](https://www.migratetoazure.net/) problemlos in Azure App Service verschieben, eine Open-Source-App aus dem Web-App-Katalog verwenden oder eine neue Website mithilfe des Frameworks und der Tools Ihrer Wahl erstellen. Die Funktion [WebJobs][] erleichtert das Hinzufügen der Hintergrundauftragsverarbeitung zu Ihrer App Service-Web-App.

Wenn Sie mehr Kontrolle über die Webserverumgebung benötigen, wie etwa die Fähigkeit eines Remotezugriffs auf den Server oder eine Konfiguration von Serverstartaufgaben, ist Azure Cloud Services üblicherweise die beste Option.

Wenn Sie über eine vorhandene Anwendung verfügen, für die erhebliche Änderungen erforderlich sind, damit sie in Azure App Service oder Azure Cloud Services ausgeführt werden kann, können Sie Azure Virtual Machines wählen, um die Migration in die Cloud zu vereinfachen. Das richtige Konfigurieren, Schützen und Warten von VMs erfordert im Vergleich zu Azure App Service und Cloud Services jedoch weitaus mehr Zeit und IT-Kenntnisse. Wenn Sie die Nutzung von Azure Virtual Machines in Erwägung ziehen, müssen Sie den fortlaufenden Wartungsaufwand berücksichtigen, der für das Patchen, Aktualisieren oder Verwalten der VM-Umgebung erforderlich ist.

Das folgende Diagramm vergleicht den relativen Grad der Steuerung mit der Benutzerfreundlichkeit für jede der Webhostingoptionen in Azure.

![Auswahldiagramm][ChoicesDiagram]

##<a name="scenarios"></a>Szenarien und Empfehlungen

Im Folgenden sind einige Anwendungsszenarien mit Empfehlungen zur Auswahl der geeigneten Azure-Webhostingoption aufgeführt.

- [Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um Geschäftsanwendungen mit integrierten lokalen Medienobjekten auszuführen.](#onprem)
- [Ich benötige eine zuverlässige Möglichkeit zum Hosten meiner Unternehmenswebsite, die eine problemlose Skalierung und einen globalen Einsatz ermöglicht.](#corp)
- [Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.](#iis6)
- [Ich bin Inhaber eines kleinen Unternehmens und suche nach einem kostengünstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.](#smallbusiness)
- [Ich bin Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.](#designer)
- [Ich migriere meine mehrstufige Webanwendung mit einem Web-Front-End zur Cloud.](#multitier)
- [Meine Anwendung hängt von stark angepassten Windows- oder Linux-Umgebungen ab, und ich möchte die Anwendung in die Cloud verschieben.](#custom)
- [Meine Website verwendet Open-Source-Software, und ich möchte sie in Azure hosten.](#oss)
- [Ich verfüge über eine Branchenanwendung, die eine Verbindung mit dem Unternehmensnetzwerk herstellen muss.](#lob)
- [Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.](#mobile)


### <a id="onprem"></a> Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um Geschäftsanwendungen mit integrierten lokalen Medienobjekten auszuführen.

Azure App Service stellt eine hervorragende Lösung für komplexe Geschäftsanwendungen dar. Mit Azure App Service können Sie Apps entwickeln, die automatisch auf einer Lastenausgleichsplattform skaliert, mit Active Directory geschützt und mit Ihren lokalen Ressourcen verbunden werden können. Das Verwalten dieser Apps wird dank eines erstklassigen Verwaltungsportals und mithilfe von APIs erleichtert. Sie erhalten außerdem mithilfe von App Insight-Tools Einblicke darin, wie Kunden die Apps verwenden. Mit der neuen Funktion [WebJobs][] können Sie Hintergrundprozesse und Aufgaben als Teil der Webebene ausführen, während Hybridkonnektivität und die [VNET-Funktionen](../fundamentals-introduction-to-azure.md#networking/) die Verbindung zurück zu den lokalen Ressourcen ermöglichen. Azure App Service bietet eine SLA mit 99,9 % Verfügbarkeit für Web-Apps und stellt folgende Möglichkeiten bereit:

* Zuverlässige Ausführung Ihrer Anwendungen auf einer Cloudplattform mit Selbstreparatur und automatischem Patching.
* Automatische Skalierung in einem globalen Netzwerk von Datencentern.
* Sicherung und Wiederherstellung für die Notfallwiederherstellung.
* Konformität mit ISO, SOC2 und PCI.
* Integration in Active Directory.

### <a id="corp"></a> Ich benötige eine zuverlässige Möglichkeit zum Hosten meiner Unternehmenswebsites, die eine problemlose Skalierung und einen globalen Einsatz ermöglicht.

Azure App Service stellt eine hervorragende Lösung für das Hosten von Unternehmenswebsites dar. Azure App Service ermöglicht ein rasches und problemloses Skalieren von Web-Apps, um die Anforderungen in einem globalen Netzwerk von Datencentern zu erfüllen. Azure App Service bietet lokalen Zugriff, Fehlertoleranz und eine intelligente Verwaltung des Datenverkehrs. Dies alles auf einer Plattform, die erstklassige Verwaltungstools bereitstellt und es Ihnen ermöglicht, rasch und problemlos Einblicke in den Zustand und das Datenaufkommen der Website zu erhalten. Azure App Service bietet eine SLA mit 99,9 % Verfügbarkeit für Web-Apps und stellt folgende Möglichkeiten bereit:

* Führen Sie Ihre Websites auf einer Cloudplattform mit Selbstreparatur und automatischem Patching zuverlässig aus.
* Nutzen Sie eine automatische Skalierung in einem globalen Netzwerk von Datencentern.
* Profitieren Sie von Sicherung und Wiederherstellung für die Notfallwiederherstellung.
* Verwalten Sie mithilfe integrierter Tools Protokolle und Datenaufkommen.
* Stellen Sie Konformität mit ISO, SOC2 und PCI sicher.
* Profitieren Sie von einer Integration in Active Directory.

### <a id="iis6"></a> Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.

Mit Azure App Service lassen sich die mit dem Migrieren von älteren IIS6-Anwendungen verbundenen Infrastrukturkosten ganz einfach vermeiden. Microsoft hat [einfach zu verwendende Migrationstools und eine detaillierte Migrationsanleitung](https://www.movemetowebsites.net/) erstellt, mit deren Hilfe Sie die Kompatibilität überprüfen und etwaige vorzunehmende Änderungen identifizieren können. Die Integration in Visual Studio, TFS oder allgemeine CMS-Tools erleichtert die Bereitstellung von IIS6-Anwendungen direkt in der Cloud. Sobald eine Bereitstellung erfolgt ist, stellt das Azure-Verwaltungsportal stabile Verwaltungstools zur Verfügung. Mit diesen können Sie eine Skalierung nach unten oder oben vornehmen, um je nach Bedarf Kosten zu verwalten oder Anforderungen zu erfüllen. Mit dem Migrationstool können Sie folgende Aktionen vornehmen:

* Schnelles und einfaches Migrieren der Windows Server 2003 Legacy-Webanwendungen zur Cloud.
* Optionale lokale Konfiguration der angehängten SQL-Datenbank, um eine Hybridanwendung zu erstellen.
* Automatisches Verschieben der SQL-Datenbank zusammen mit der Legacyanwendung.

### <a id="smallbusiness"></a>Ich bin Inhaber eines kleinen Unternehmens und suche nach einem kostengünstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.

Azure App Service ist eine hervorragende Lösung für dieses Szenario, da Sie mit einer kostenlosen Version beginnen und später bei Bedarf weitere Funktionen hinzufügen können. Jede kostenlose Web-App wird mit einer Domäne von Azure bereitgestellt ("Ihr_Unternehmen".azurewebsites.net). Die Plattform umfasst integrierte Bereitstellungs- und Verwaltungstools sowie einen Anwendungskatalog, der Ihnen den Einstieg erleichtert. Es gibt viele weitere Dienste und Skalierungsoptionen, dank derer Sie die Website mit steigenden Benutzeranforderungen weiterentwickeln können. Mit Azure App Service haben Sie folgende Möglichkeiten:

- Starten Sie mit der kostenlosen Version, und führen Sie nach Bedarf eine Skalierung durch.
- Verwenden Sie den Web-App-Katalog, um rasch gängige Webanwendungen wie z. B. WordPress einzurichten.
- Fügen Sie Ihrer Anwendung nach Bedarf zusätzliche Azure-Dienste und -Funktionen hinzu.
- Schützen Sie Ihre Web-App mit HTTPS.

### <a id="designer"></a> Ich bin Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.

Für Webentwickler und -designer lässt sich Azure App Service leicht in eine Reihe von Frameworks und Tools integrieren. Dazu gehören eine Bereitstellungsunterstützung für Git und FTP sowie eine nahtlose Integration in Tools und Dienste wie Visual Studio oder SQL-Datenbank. Mit Azure App Service haben Sie folgende Möglichkeiten:

- Verwenden von Befehlszeilentools für [automatisierte Aufgaben][scripting].
- Arbeiten mit gängigen Sprachen wie [.NET][dotnet], [PHP][], [Node.js][nodejs] und [Python][].
- Wählen Sie aus drei verschiedener Skalierungsebenen, um eine Skalierung für sehr hohe Kapazitäten zu unterstützen.
- Integration in andere Azure-Dienste, wie [SQL-Datenbank][sqldatabase], [Service Bus][servicebus] und [Storage][], oder Partnerangeboten aus dem [Azure Store][azurestore], beispielsweise MySQL und MongoDB.
- Integration in Tools wie Visual Studio, Git, WebMatrix, WebDeploy, TFS und FTP.

### <a id="multitier"></a>Ich migriere meine mehrstufige Webanwendung mit einem Web-Front-End zur Cloud.

Wenn Sie eine mehrstufige Anwendung ausführen, z. B. einen Webserver, der eine Verbindung mit einer Datenbank herstellt, ist Azure App Service eine gute Option, die eine enge Integration in Azure SQL-Datenbank bietet. Außerdem können Sie die WebJob-Funktion zum Ausführen von Hintergrundprozessen verwenden.

Wählen Sie Cloud Services für eine oder mehrere der Ebenen, wenn Sie mehr Kontrolle über die Serverumgebung benötigen. Dies kann beispielsweise die Fähigkeit betreffen, remote auf Ihren Server zuzugreifen oder die Serverstartaufgaben zu konfigurieren.

Wählen Sie Virtual Machines für eine oder mehrere der Ebenen, wenn Sie ein eigenes Image verwenden oder Serversoftware oder Dienste ausführen möchten, die Sie nicht in Cloud Services konfigurieren können.

### <a id="custom"></a>Meine Anwendung hängt von stark angepassten Windows- oder Linux-Umgebungen ab, und ich möchte die Anwendung in die Cloud verschieben.

Wenn Ihre Anwendung eine komplexe Installation oder die Konfiguration von Software und Betriebssystem erfordert, ist Virtual Machines wahrscheinlich die beste Lösung. Virtual Machines ermöglichen Ihnen Folgendes:

- Verwendne Sie den Virtual Machines-Katalog, um mit einem Betriebssystem wie Windows oder Linux zu beginnen und dieses später an Ihre Anwendungsanforderungen anzupassen.
- Erstellen Sie ein maßgeschneiderten Image eines bereits vorhandenen lokalen Servers für die Ausführung auf einem virtuellen Computer in Azure, und laden Sie dieses hoch.

### <a id="oss"></a>Meine Website verwendet Open-Source-Software, und ich möchte sie in Azure hosten.

Wenn Ihr Open-Source-Framework in App Service unterstützt wird, werden die von Ihrer Anwendung benötigten Sprachen und Frameworks automatisch für Sie konfiguriert. App Service ermöglicht Ihnen Folgendes:

- Arbeiten Sie mit vielen gängigen Open-Source-Sprachen wie [.NET][dotnet], [PHP][], [Node.js][nodejs] und [Python][].
- Richten Sie WordPress, Drupal, Umbraco, DNN und viele weitere Webanwendungen von Drittanbietern ein.
- Migrieren Sie vorhandene Anwendungen, oder Erstellen Sie neue Anwendungen über den Web-App-Katalog.

Wenn Ihr Open-Source-Framework in App Service nicht unterstützt wird, ist eine Ausführung über eine der weiteren Azure-Webhostingoptionen möglich. Bei Cloud Services verwenden Sie Startaufgaben für das Installieren und Konfigurieren erforderlicher Open-Source-Software, die unter Windows ausgeführt wird. Mit Virtual Machines können Sie die Software auf dem Image eines virtuellen Computers installieren und konfigurieren. Dieser kann Windows- oder Linux-basiert sein.

### <a id="lob"></a>Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.

Wenn Sie eine Branchenanwendung erstellen möchten, benötigt Ihre Website möglicherweise direkten Zugriff auf Dienste oder Daten in Ihrem Unternehmensnetzwerk. Dies ist in App Service, Cloud Services und Virtual Machines über den [Azure Virtual Network-Dienst](/services/virtual-network/) möglich. In App Service können Sie mithilfe der [VNET-Integrationsfunktion](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) eine Azure-Anwendung so ausführen, als befände sie sich in Ihrem Unternehmensnetzwerk.

### <a id="mobile"></a>Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.

HTTP-basierte Webdienste ermöglichen die Unterstützung vieler unterschiedlicher Clients, einschließlich mobiler Clients. Frameworks wie ASP.NET-Web-API können in Visual Studio integriert werden. Dies erleichtert die Erstellung und Verwendung von REST-Diensten. Diese Dienste werden von einem Webendpunkt verfügbar gemacht, sodass es möglich ist, eine beliebige Webhostingmethode in Azure anzuwenden, um dieses Szenario zu unterstützen. App Service ist jedoch auch eine hervorragende Option für das Hosten von REST-APIs. Mit Azure App Service haben Sie folgende Möglichkeiten:

- Schnelles Erstellen einer Web-App zum Hosten des HTTP-Webdiensts in einem der global verteilten Azure-Datencenter.
- Migrieren von vorhandenen Diensten oder Erstellen von neuen Diensten.
- Erfüllen von SLAs zur Verfügbarkeit mit einer einzelnen Instanz oder durch eine horizontale Skalierung auf mehrere dedizierte Computer.
- Verwenden der veröffentlichten Website, um RESTP-APIs für beliebige HTTP-Clients bereitzustellen, auch für mobile Clients.

Zusätzlich verfügt Azure App Service über eine neue Vorschaufunktion für REST-APIs: API-Apps. Weitere Informationen zu API-Apps finden Sie unter [Was sind API-Apps?](../app-service-api/app-service-api-apps-why-best-platform.md).

##<a name="features"></a>Funktionsvergleich

In der folgenden Tabelle werden die Funktionen von App Service, Cloud Services und Virtual Machines verglichen, um Ihnen die Auswahl der richtigen Option zu erleichtern. Aktuelle Informationen über die Vereinbarung zum Servicelevel (SLA) in Bezug auf die jeweilige Option finden Sie unter [Azure-Vereinbarung zum Servicelevel](/support/legal/sla/).

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Funktion</th>
   <th align="left" valign="middle">App Service (Web-Apps)</th>
   <th align="left" valign="middle">Cloud Services (Webrollen)</th>
   <th align="left" valign="middle">Virtual Machines</th>
   <th align="left" valign="middle">Hinweise</th>
</tr>
<tr>
   <td valign="middle"><p>Zeitnahe Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">Die Bereitstellung einer Anwendung oder einer Anwendungsaktualisierung in Cloud Services oder das Erstellen eines virtuellen Computers nimmt mindestens einige Minuten in Anspruch. Die Bereitstellung einer Anwendung in einer Web-App dauert wenige Sekunden.</td>
</tr>
<tr>
   <td valign="middle"><p>Skalierung auf größere Computer ohne erneute Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Webserverinstanzen weisen gemeinsame Inhalte und eine gemeinsame Konfiguration auf. Dies bedeutet, dass Sie sie bei einer Skalierung nicht neu bereitstellen oder neu konfigurieren müssen.</p></td>
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
   <td valign="middle"><p>Bereitstellung von Code mit GIT, FTP</p></td>
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
   <td valign="middle">Cloud Services unterstützt die Verwendung von Web Deploy, um Updates für einzelne Rolleninstanzen bereitzustellen. Ein Einsatz für die Erstbereitstellung einer Rolle ist jedoch nicht möglich. Wenn Sie Web Deploy für eine Aktualisierung verwenden, müssen Sie jeweils eine separate Bereitstellung für jede Instanz einer Rolle vornehmen. Es sind mehrere Instanzen sind erforderlich, um für die Cloud Services-SLA für Produktionsumgebungen qualifiziert zu sein.</td>
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
   <td valign="middle">App Service kann problemlos für das Hosten einer REST-API der mittleren Ebene genutzt werden, über die Funktion <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> können Hintergrundverarbeitungsaufträge gehostet werden. Sie können WebJobs auf einer dedizierten Website ausführen, um eine unabhängige Skalierbarkeit für die entsprechende Ebene zu erzielen. Die Vorschaufunktion [API-Apps](../app-service-api/app-service-api-apps-why-best-platform.md) bietet sogar noch mehr Funktionen für das Hosten von REST-Diensten.</td>
</tr>
<tr>
   <td valign="middle"><p>Integrierte Unterstützung von MySQL-as-a-Service</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services kann in MySQL-as-a-Service über Angebote von ClearDB integriert werden, jedoch nicht als Bestandteil des Verwaltungsportal-Workflows.</td>
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
   <td valign="middle">Bei virtuellen Computern ist eine horizontale Skalierung auf mehrere Instanzen möglich. Die Dienste, die auf diesen Computern ausgeführt werden, müssen jedoch so geschrieben werden, dass sie diese horizontale Skalierung unterstützen. Sie müssen einen Lastenausgleich konfigurieren, um Anforderungen über die Computer weiterzuleiten. Zudem müssen Sie eine Affinitätsgruppe erstellen, um gleichzeitigen Neustarts aller Instanzen aufgrund von Wartungs- oder Hardware-Fehlern vorzubeugen.</td>
</tr>
<tr>
   <td valign="middle"><p>SSL-Unterstützung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Für App Service-Web-Apps wird SSL für benutzerdefinierte Domänen nur in den Modi "Basic" und "Standard" unterstützt. Weitere Informationen zur Verwendung von SSL mit Web-Apps finden Sie unter <a href="../web-sites-configure-ssl-certificate/">Konfigurieren eines SSL-Zertifikats für eine Azure-Website</a>.</td>
</tr>
<tr>
   <td valign="middle"><p>Visual Studio-Integration</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Remotedebugging</p></td>
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
   <td valign="middle"><p>Netzwerkisolierung mit <a href="/services/virtual-network/">Azure Virtual Network</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Siehe auch <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Virtual Network-Integration in Azure Websites</a></td>
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
   <td valign="middle"><p>Remotedesktopzugriff auf Server</p></td>
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


> [AZURE.NOTE]Wenn Sie Wenn Sie Azure Website ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, wo Sie sofort kostenlos eine kurzlebige ASP.NET-Starter-App in Azure App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.


## <a id="nextsteps"></a> Nächste Schritte

Weitere Informationen zu den drei Webhostingoptionen finden Sie in den folgenden Ressourcen:

* [Einführung in Azure](../fundamentals-introduction-to-azure.md)
* [Compute-Hostingoptionen in Azure](../fundamentals-application-models.md)

Informationen zum Einstieg in die gewählte(n) Optionen für Ihre Anwendungen finden Sie in den folgenden Ressourcen:

* [Azure Websites](/documentation/services/app-service/)
* [Azure Cloud Services](/documentation/services/cloud-services/)
* [Azure Virtual Machines](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Azure App Service]: /services/app-service/
  [Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [Storage]: http://www.windowsazure.com/documentation/services/storage/
 

<!---HONumber=62-->