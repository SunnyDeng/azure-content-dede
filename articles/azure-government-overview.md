<properties 
	pageTitle="Azure Government - Übersicht" 
	description="Dieser Artikel bietet einen Überblick über die Funktion der Azure Government Cloud sowie das vertrauenswürdige Design und die Sicherheit, die zur Unterstützung der Konformität für Bundes-, Staats- und lokale Behörden und deren Partner verwendet werden. " 
	services="azure-government" 
	documentationCenter="" 
	authors="joharve2" 
	manager="required"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="1/9/2014" 
	ms.author="jharve"/>

#  Microsoft Azure Government - Übersicht 

<p> Microsoft Azure Government ist eine physische sowie netzwerkisolierte Instanz von Microsoft Azure.  Dieses Entwicklerhandbuch bietet Informationen zu den Unterschieden, die für Entwickler und Administratoren erforderlich sind, um mit diesen unterschiedlichen Bereichen von Azure interagieren und arbeiten zu können.


## In diesem Thema


+ [Übersicht](#Overview)
+ [Derzeit in Microsoft Azure Government verfügbare Funktionen](#Features)
+ [Nächste Schritte](#next)


## <a name="Overview"></a>Übersicht

Azure Government ist eine Community-Cloud für Behörden (Government-Community Cloud, GCC), die entwickelt wurde, um strategische Regierungsszenarien zu unterstützen, die Geschwindigkeit, Skalierung, Sicherheit, Compliance und Wirtschaftlichkeit für US-Regierungsorganisationen erfordern.   Sie wurde basierend auf der langjährigen Erfahrung von Microsoft in Bezug auf die Bereitstellung von Software, Sicherheit, Konformität und Steuerelementen in anderen Microsoft-Clouds wie Azure Public, Office 365, O365 GCC, Microsoft CRM Online usw. entwickelt. 

Darüber hinaus wurde Azure Government entwickelt, um eine höhere Sicherheitsstufe und Konformitätsanforderungen für vertrauliche, dedizierte Arbeitsauslastungen des öffentlichen US-amerikanischen Sektors zu erfüllen, die u.a. in folgenden Vorschriften enthalten sind: United States Federal Risk and Authorization Management Program (FedRAMP), Department of Defense Enterprise Cloud Service Broker (ECSB), Criminal Justice Information Services (CJIS) Security Policy und Health Insurance Portability and Accountability Act (HIPAA).     

Es folgt eine zusammenfassende Darstellung der Infrastruktur, Fabric, Dienste und Frameworks der Azure Government-Cloud, die Regierungsbehörden zur Erstellung von Hybrid-Cloud-Lösungen nutzen können, um ihre Ziele zu erreichen.  Einige der unten aufgeführten Dienste sind nur in der Vorschau verfügbar; eine Liste der aktuellsten, normalerweise verfügbaren Dienste finden Sie in der [Regionsübersicht](http://azure.microsoft.com/regions/#services).

![][2]

Azure Government umfasst die Kernkomponenten von Infrastructure-as-a-Service (IaaS) und Platform-as-a-Service (PaaS).  Dazu gehören Infrastruktur, Netzwerk, Speicher, Datenverwaltung, Identitätsverwaltung und viele andere Dienste.  Azure Government unterstützt die gleichen großartigen Funktionen, die Kunden der öffentlichen Version von Azure nutzen, wie z. B. geografische synchrone Datenreplikation und automatische Skalierung. Microsoft wurde von führenden Branchenanalysten als Marktführer für <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> und <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/> ermittelt.

Neben den stabilen Diensten und Funktionen der öffentlichen Version von Azure bietet Azure Government eine Reihe von Funktionen für US-Regierungseinrichtungen, damit diese sicherstellen können, dass ihre Daten geschützt sind. Dies beinhaltet Folgendes:

- **Physische und netzwerkisolierte Instanz** - Die Umgebung von Azure Government ist eine vollständig separate Instanz von Microsoft Azure Public und wird nur von qualifizierten US-Regierungsbehörden und Lösungsanbietern verwendet.

- **Sicherheit, Datenschutz und Konformität** - Microsoft hat sein Framework aus stabiler Sicherheit, Datenschutz und Konformitätssteuerelementen sowie zusätzliche stringente Steuerelemente implementiert, um die höheren Anforderungen zu erfüllen, die in den Auswirkungsstufen von ECSB und in CJIS enthalten sind.

- **Datenspeicherung** - Die Umgebung von Azure Government verwaltet zwei Rechenzentren, die sich über 500 Meilen voneinander entfernt befinden. Alle vom Kunden verwalteten Daten werden in den Rechenzentren von Continental United States (CONUS) gespeichert.

- **US-Personal** - Alle Azure Government-Operatoren und -Administratoren sind überprüfte US-Bürger.

- **Identitätsverwaltung** - Die Identitätsverwaltung innerhalb der Umgebung von Azure Government ist eine separate Instanz von Azure Active Directory.

- **Konformität** - Microsoft ist kontinuierlich bemüht, die strengen und wechselnden Konformitätsanforderungen von Bundes-, Staats- und lokalen Behörden wie FedRAMP, CJIS, ECSB und HIPAA in Bezug auf Cloud-Lösungen für die US-Regierung zu erfüllen und zu erhalten.

- **Cloud-Integration** - Azure Government bietet eine integrierte Umgebung mit O365 Government, die eine einmalige Anmeldung über Cloud-Dienste und erweiterte Dienste wie z. B. 1 TB Speicherplatz für OneDrive ermöglicht.

Azure Government bietet Organisationen zudem die Möglichkeit, ihre vorhandenen Technologieinvestitionen zu verwalten und die Vorteile von Cloud-Diensten zu nutzen.  Da Azure Government eine interoperable Cloud-Plattform mit Produkten und Technologien ist, können Organisationen von Grund auf offenere Anwendungen erstellen.  Behörden können Tools, Dienste, Betriebssystem, Architektur und Frameworks, einschließlich Windows, Linux, Oracle, SharePoint, .NET, Java, PHP und Node.js für ihre Cloud-Lösungen wählen. Die Flexibilität der Azure Government-Plattform ermöglicht neue Formen der Zusammenarbeit zwischen Agenturen, Anwendungsentwicklung und Integration.  

US-Regierungsbehörden, die an Cloud-Diensten interessiert sind, können darauf vertrauen, dass Azure Government eine enorme Skalierbarkeit und strenge Sicherheitsmaßnahmen bereitstellt, um ihren wachsenden Anforderungen gerecht zu werden. 







## <a name="Features"></a>Funktionen, die derzeit in Microsoft Azure Government verfügbar sind
Azure Government verfügt derzeit in den Regionen US GOV IOWA und US GOV VIRGINIA über die folgenden Dienste:

- Virtuelle Rechner
- Cloud Services
- Storage
- Active Directory
- Scheduler
- Virtuelle Netzwerke
- SQL-Datenbank

Es sind noch andere Dienste verfügbar und es werden zudem kontinuierlich weitere Dienste hinzugefügt.  Die aktuelle Liste der Dienste finden Sie in der [Regionsübersicht](http://azure.microsoft.com/regions/#services), in der die einzelnen verfügbaren Regionen und ihre Dienste hervorgehoben sind.  

Derzeit wird Azure Government von den Rechenzentren US GOV Iowa und US GOV Virginia unterstützt.  Informationen zu aktuell verfügbaren Rechenzentren und Diensten finden Sie in der obigen Regionsübersicht.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>Nächste Schritte

Wenn Sie mehr über Azure Government erfahren möchten, nutzen Sie einige der unten aufgeführten Links.

- **<A href="http://azure.com/gov">Erwerb von und Zugreifen auf Azure Government</a>**

- **<A href="/azure-government-developer-guide">Azure Government-Entwicklerhandbuch</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../virtual-machines-windows-tutorial/
[Link 2 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../web-sites-custom-domain-name/
[Link 3 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../storage-whatis-account/

<!--HONumber=47-->
