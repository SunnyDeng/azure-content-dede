<properties 
	pageTitle="Azure App Service-Web-Apps-Angebote für Unternehmen" 
	description="Zeigt, wie Sie mithilfe von Azure App Service-Web-Apps Unternehmens-Website-Lösungen für Ihr Unternehmen erstellen." 
	services="app-service\web" 
	documentationCenter="" 
	authors="apwestgarth" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="anwestg"/>

# Azure App Service-Web-Apps-Angebote für Enterprise Whitepaper #

Die Notwendigkeit, in einem schnell wachsenden Umfeld Kosten zu senken und IT-Lösungen schneller bereitzustellen bedeutet neue Herausforderungen für Entwickler, IT-Experten und Manager. Benutzer fordern immer öfter, dass ihre Line of Business (LOB)-Webanwendungen schnell, reaktionsfähig und auf jedem Gerät verfügbar sein sollen. Gleichzeitig versuchen Unternehmen von der Produktivität und Effizienz zu profitieren, die durch die Integration mit Cloud und mobilen Services entsteht. Dies kann etwas so Einfaches sein wie einmaliges Anmelden auf allen Geräten mithilfe von Active Directory, bis hin zur die Zusammenarbeit in Office 365, bei der aus einer internen LOB-Anwendung extrahierte Daten verwendet wreden, die wiederum Daten aus der Implementierung von Salesforce im Unternehmen extrahiert. [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) ist ein Clouddienst auf Unternehmensebene zum Entwickeln, Testen und Ausführen von Web- und Mobilanwendungen, Web-APIs und generischen Websites. Er kann zum Ausführen von Unternehmenswebsites, Geschäftsanwendungen und Durchführen von digitalen Marketingkampagnen auf einem globalen Netzwerk von Datencentern verwendet werden. Dieses ist für Skalierbarkeit und Verfügbarkeit optimiert und bietet Unterstützung für kontinuierliche Integration und moderne DevOps-Methoden.

In diesem Whitepaper werden die Funktionen des [Web-Apps](/services/app-service/web/)-Dienstes erläutert. Vor allem die Ausführung von LOB-Webanwendungen, die die Migration bestehender Webanwendungen und die Bereitstellung von neuen LOB-Webanwendungen auf der Plattform umfassen, werden besonders hervorgehoben.

## Zielgruppe ##

IT-Experten, Architekten und Manager, die Web-Arbeitslasten, die derzeit in der lokalen Umgebung ausgeführt werden, in die Cloud migrieren möchten. Web-Arbeitsauslastungen können Webanwendungen mit Interaktion zwischen Unternehmen und Mitarbeitern oder Unternehmen und Geschäftspartnern umfassen.

## Einführung ##

App Service-Web-Apps ist eine ideale Plattform für das Hosten externer und interner Webanwendungen und Dienste, da sie eine kostengünstige, in hohem Maße skalierbare, verwaltete Lösung darstellt, mit der Sie sich auf die Bereitstellung von geschäftlichem Nutzen für Ihre Benutzer konzentrieren können, anstatt viel Zeit und Geld in die Pflege und Unterstützung von separaten Umgebungen zu investieren. Web-Apps bietet eine flexible Plattform für die Bereitstellung von Webanwendungen auf Unternehmensebene sowie die Möglichkeit der weiteren Unterstützung der Authentifizierung über das lokale Active Directory via Integration mit Microsoft Azure Active Directory. Unterstützt werden außerdem einfache und schnelle Bereitstellungen durch die Nutzung von internen kontinuierlichen Integrations- und Bereitstellungsmethoden, während durch Skalieren automatisch das Unternehmenswachstum berücksichtigt wird. Und dies alles auf einer verwalteten Plattform, die Ihnen ermöglicht, sich auf Ihre Anwendung zu konzentrieren anstatt auf Ihre Infrastruktur.

## Problemdefinition ##

Die IT-Landschaft ändert sich mit rasanter Geschwindigkeit, wobei eine Verschiebung weg vom Hosting auf herkömmlichen Servern mit hohen Kapitalkosten bei langen Vorlaufzeiten hin zum Hosting mit bedarfsgerechter Nutzung von Diensten mit automatischer Skalierung zur Bewältigung von Arbeitslasten. IT-Abteilungen stehen vor der Herausforderung, Kosten und Umfang der Infrastruktur und Wartung und insbesondere den Investitionsaufwand zu reduzieren, dabei jedoch gleichzeitig die Flexibilität zu erhöhen. Das Ende der Lebensdauer älterer Infrastruktur-Plattformen wie Windows Server 2003 bringt IT-Abteilungen dazu, die Cloud-Migration als potenzielle Möglichkeit, neue langfristige Kapitalkosten zu vermeiden, zu überdenken. In der Vergangenheit trafen CIOs Kaufentscheidungen für andere Abteilungen. Zunehmend nehmen jedoch CMOs und andere Bereichsleiter eine aktivere Rolle bei der Verwendung ihres Budgets und der Bestimmung des ROI ein. Zunehmend erwarten Unternehmen von ihren Mitarbeitern ein immer höheres Maß an Mobilität, und Mitarbeiter, die nicht vor Ort arbeiten und viel Zeit mit Kunden verbringen, benötigen problemlosen Zugriff auf Systeme.

Geschäftliche Anforderungen ändern sich monatlich, wöchentlich, täglich. Unternehmen wollen direkt global einsteigen, mit regelmäßig aktualisierten Diensten mit einer Vielzahl von neuen Funktionen, die von Drittanbietern oder intern bereitgestellt werden. Benutzer haben höhere Erwartungen, viele nutzen die Dienste im privaten Leben, wie z. B. Office 365, und erwarten Zugriff auf ähnliche, aktuelle, funktionsreiche Dienste im Arbeitsalltag. Zur Bewältigung dieser Anforderungen musst die IT dem Unternehmen helfen, dies zu erreichen, und zwar durch Auswahl und Integration von Drittanbieterdiensten sowie die sorgfältige Auswahl von Plattformen, die sich an die geschäftlichen Anforderungen anpassen können und auch bei reduzierten Gesamtbetriebskosten zuverlässig sind.

Entwicklungsteams sind bestrebt, sofortigen geschäftlichen Nutzen zu liefern, indem sie in regelmäßigen Abständen neue Funktionen bereitstellen. Sie möchten eine kostengünstige, zuverlässige Plattform, die sich in die vorhandenen Tools und Methoden integrieren lässt – Entwicklung, Test, Veröffentlichung. Außerdem führt die Zusammenarbeit mit IT-Abteilungen zur Automatisierung von Bereitstellung, Verwaltung und Alarmgebung – alles mit dem Ziel der Vermeidung von Ausfallzeiten.

<a href="highlevel" />
## High-Level-Lösung ##

Webplattformen und Frameworks werden zunehmend zum Entwickeln, Testen und Hosten von LOB-Anwendungen verwendet. Mit einer typischen LOB-Anwendung, wie z. B. einem internen Mitarbeiterkostensystem, das häufig nur aus einer Web-App mit einer unterstützenden Datenbank zum Speichern der mit der Anwendung verbundenen Daten besteht.

App Service-Web-Apps ist eine Option für das Hosten solcher Anwendungen, denn es bietet eine skalierbare und zuverlässige Infrastruktur, die verwaltet ist und kaum manuelle Eingriffe und Ausfallzeiten mit sich bringt. Die Microsoft Azure-Plattform bietet viele Optionen für die Datenspeicherung zur Unterstützung von Webanwendungen, die in Web-Apps in der Microsoft Azure SQL-Datenbank gehostet sind, eine verwaltete, skalierbare, relationale Database-as-a-Service, bis hin zu beliebten Diensten von unseren Partnern, wie z. B. ClearDB MySQL-Datenbank und MongoDB.

Eine alternative Lösung besteht darin, Ihre vorhandenen lokalen Investitionen zu verwenden. In diesem Beispielszenario, einem Mitarbeiterkostensystem, können Sie Ihren Datenspeicher innerhalb Ihrer eigenen internen Infrastruktur verwalten. Dies kann für die Integration mit internen Systemen (Reporting, Lohnbuchhaltung, Abrechnung usw.) oder zur Erfüllung von IT-Governance-Anforderungen verwendet werden. Web-Apps bietet zwei Methoden zur Aktivierung der Verbindung mit Ihrer lokalen Infrastruktur:

- [Hybridverbindungen](../integration-hybrid-connection-overview.md) – Hybridverbindungen sind ein Feature von Microsoft Azure BizTalk Services. Damit kann Web-Apps eine Verbindung zu lokalen Ressourcen herstellen, wie z. B. zu SQL Server, MySQL, Web-APIs und benutzerdefinierten Webdiensten. 
- [Virtuelle Netzwerkintegration](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) – Durch Web-Apps-Integration mit Azure Virtual Network können Sie Ihre Web-App mit einem virtuellen Azure-Netzwerk verbinden, das wiederum mit Ihrer lokalen Infrastruktur über ein Site-to-Site-VPN verbunden werden kann. 

Im folgende Diagramm ist eine beispielhafte Lösung auf hoher Ebene mit Konnektivitätsoptionen für lokale Ressourcen zu sehen.

![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png)

## Geschäftliche Vorteile ##

App Service-Web-Apps bietet eine Vielzahl von geschäftlichen Vorteilen, die Ihre Funktion kostengünstiger und agiler bei der Bereitstellung für geschäftliche Anforderungen machen.

### PaaS-Modell ###

App Service-Web-Apps basiert auf einem PaaS (Platform-as-a-Service)-Modell, das viele Kosten- und Effizienzeinsparungen ermöglicht. Sie müssen nicht mehr Stunden mit Verwalten von VMs, Patchen von Betriebssystemen und Frameworks verbringen. Web-Apps wird automatisch mit Patches versehen. Dadurch können Sie sich auf das Verwalten Ihrer Webanwendungen konzentrieren anstatt auf VMs, und Teams haben die Möglichkeit, zusätzlichen Unternehmenswert zu schaffen.

Das PaaS-Modell hinter Web-Apps ermöglicht Anwendern der DevOps-Methodologie, ihre Ziele zu erreichen. Als Unternehmen bedeutet dies vollständige Verwaltung und Integration im gesamten Anwendungslebenszyklus, einschließlich Entwicklung, Tests, Veröffentlichung, Überwachung und Verwaltung sowie Support.

Für Entwicklungsteams können fortlaufende Integrations- und Bereitstellungsarbeitsabläufe über Visual Studio Online, GitHub, TeamCity, Hudson oder BitBucket konfiguriert werden. Diesw ermöglicht automatisiertes Erstellen von Builds, Tests und Bereitstellung, was schnellere Versionszyklen ermöglicht und Probleme bei Veröffentlichung in der vorhandenen Infrastruktur verringert. Web-Apps unterstützt auch die Erstellung mehrerer Test- und Stagingumgebungen für den Freigabe-Workflow. Sie müssen für diese Zwecke also keine Hardware mehr reservieren oder zuweisen, sondern können beliebig viele Umgebungen erstellen und Ihre eigene Unterstützung für den Release-Workflow definieren. Als Unternehmen können Sie einen Test-Steckplatz aus der Quellcodeverwaltung freigeben, eine Reihe von Tests durchführen und nach dem erfolgreichen Abschluss auf einen Staging-Steckplatz heraufstufen. Abschließend können Sie zur Produktion ohne Ausfallzeiten wechseln, was den zusätzlichen Vorteil hat, dass Webanwendungen, die auf Web-Apps gehostet werden, vorab geladen und bereit sind, die bestmögliche Kundenerfahrung zu bieten.

Operations-Teams können sicher sein, dass sie in der bestmöglichen Position sind, auf Probleme mit ihren Webanwendungen zu reagieren, die auf Web-Apps gehostet werden, und zwar dank der integrierten Überwachungs- und Alarmfunktionen. Operations-Teams haben bereits in Analyse- und Überwachungslösungen wie Microsoft Visual Studio Application Insights, New Relic und AppDynamics investiert. Diese werden außerdem vollständig auf Web-Apps unterstützt. Das ermöglicht Kontinuität und eine vertraute Umgebung, von der aus Ihre Webanwendungen überwacht werden können.

Schließlich ermöglicht Web-Apps die automatische Sicherung Ihrer App(s) und gehosteten Datenbank(en) direkt in einem Azure Blob-Speichercontainer. Dies bietet Ihnen eine einfache und äußerst kostengünstige Methode der Wiederherstellung nach einem Notfall und reduziert die Notwendigkeit für komplexe lokale Hardware und Software.

### Einfache Migration ###

Hardware-Wartung und Auswechslung ist ein Hauptanliegen von Unternehmen, da sich Versionszyklen für Hardware und Betriebssysteme beschleunigen. Vielleicht haben Sie Windows Server 2003 R2-Server, die das Ende des Supports 2015 erreichen, aber weiterhin wichtige Webanwendungen für Ihr Unternehmen hosten? App Service-Web-Apps ist ein guter Kandidat für das Hosten dieser Webanwendungen, und für Sie, um Platz für Business-Hardware einzusparen. Web-Apps ermöglicht Ihnen den Zugriff auf eine Vielzahl von Hardwarespezifikationen, die verwaltet und als Teil des Dienstes gewartet werden. Dadurch entfällt die Notwendigkeit, Ersetzungs- und Verwaltungskosten im Rahmen Ihres Infrastruktur-Budgets zu berücksichtigen. Migration kann so einfach sein wie ein Copy-and-Paste-Vorgang aus der vorhandenen Bereitstellung auf Web-Apps, oder eine komplexere Migration, bei der die Verwendung des Web-Apps-Migrationsassistenten Mehrwert schafft. Migrierte Webanwendungen bieten das gesamte Spektrum der Azure-Dienste und integrieren zusätzliche Dienste in die Webanwendungen. Beispielsweise können Sie erwägen, Azure Active Directory zu verwenden, zur Steuerung des Zugriffs auf Ihre Anwendung basierend auf der Zuordnung von Benutzern zu Sicherheitsgruppen. Ein weiteres Beispiel sind Cache Services zum Verbessern der Leistung und Verringerung der Latenz, die allgemein mehr Benutzerfreundlichkeit bieten.

### Enterprise-Class Hosting ###

App Service-Web-Apps bietet eine stabile, zuverlässige Plattform, die nachweislich eine Vielzahl von Unternehmensanforderungen erfüllen kann, und zwar von kleinen internen Entwicklungs- und Testarbeitslasten bis hin zu stark in hohem Maße skalierten Websites mit hohen Verkehrsaufkommen. Mit Web-Apps nutzen Sie die gleiche Hosting-Plattform der Enterprise-Klasse, die Microsoft als Unternehmen für hochwertige Arbeitslasten verwendet. Web-Apps sowie alle Dienste auf der Azure-Plattform beinhalten Sicherheit und die Einhaltung gesetzlicher Bestimmungen, wie z. B. ISO (ISO/IEC 27001:2005), SOC1 und SOC2 SSAE 16/ISAE 3402-Bescheinigungen, HIPAA BAA, PCI und Fedramp im Kern jedes Elements und Features. Weitere Informationen finden Sie unter [http://aka.ms/azurecompliance](/support/trust-center/compliance/).

Die Microsoft Azure-Plattform ermöglicht rollenbasierte Zugriffssteuerung auf Organisationsebene für Ressourcen in Web-Apps. RBAC bietet Unternehmen die Möglichkeit, ihre eigenen Zugriffsmanagementrichtlinien für alle ihre Ressourcen in der Azure-Umgebung zu implementieren, und zwar durch Zuweisen von Benutzern zu Gruppen und wiederum Zuweisen der erforderlichen Berechtigungen zu diesen Gruppen für eine Ressource, wie z. B. eine Web-App. Weitere Informationen zu RBAC in Azure finden Sie unter [http://aka.ms/azurerbac](../role-based-access-control-configure/). Durch Nutzung von Web-Apps können Sie sicher sein, dass Ihre Webanwendungen in einer sicheren und zuverlässigen Umgebung bereitgestellt werden, und verfügen über die vollständige Kontrolle, in welcher Region Ihrer Ressourcen bereitgestellt werden.

Darüber hinaus kann Web-Apps auch vollständig Ihre lokalen Investitionen nutzen, denn es ermöglicht die Herstellung einer Verbindung zurück zu Ihren internen Ressourcen, wie z. B. Ihrem Data Warehouse oder Ihrer SharePoint-Umgebung. Wie bereits in [High-Level-Lösung] erwähnt, können Sie mithilfe von Hybridverbindungen und virtueller Netzwerkkonnektivität Verbindungen mit einer lokaler Infrastruktur und lokalen Diensten herstellen.

### Globale Skalierbarkeit ###

App Service-Web-Apps ist eine globale und skalierbare Plattform, die es Ihren Webanwendungen ermöglicht, zu wachsen und sich an die Anforderungen eines wachsenden Unternehmens schnell und bei einem Mindestmaß an Langzeitplanung und Kosten anzupassen. In typischen Szenarien mit lokaler Infrastruktur, Erweiterung und Nachfrage, vor Ort und weltweit, würden eine große Menge an Verwaltung, Planung und Ausgaben für die Bereitstellung und Verwaltung zusätzlicher Infrastruktur erfordern. Web-Apps bietet die Möglichkeit, Ihre Webanwendungen nach wechselnden Anforderungen zu skalieren. Ein Beispiel ist die Verwendung der Spesenanwendung. Den größten Teil des Monats wird die Anwendung von Benutzern kaum genutzt. Wenn sich jedoch der monatliche Stichtag für die Übermittlung von Ausgaben nähert und die Anwendung häufiger genutzt wird, verfügt Web-Apps über die Fähigkeit, automatisch mehr Infrastruktur für die Anwendung bereitzustellen. Wenn die Nutzung dann wieder zurück geht, kann eine Skalierung zurück zur Basis-Infrastruktur erfolgen, die von Ihnen definiert wird.

Web-Apps ist in 17 Rechenzentren weltweit verfügbar, und es werden mehr. Die aktuellste Liste von Regionen und Orten finden Sie unter [http://aka.ms/azlocations](http://aka.ms/azlocations). Mit Web-Apps, kann Ihr Unternehmen ohne großen Aufwand globale Reichweite und Skalierbarkeit erreichen. Wenn Ihr Unternehmen in neue Regionen expandiert, können die Dashboards für Berichtsanwendungen, die auf Web-Apps verwendet und gehostet werden, problemlos in zusätzlichen Rechenzentren bereitgestellt und durch die Kombination von Web-Apps und Azure Traffic Manager von lokalen Benutzern wesentlich schneller genutzt werden. Dies alles ermöglicht der Zusatznutzen der zugrunde liegenden, skalierbaren Infrastruktur, die sich verkleinern und erweitern kann, wenn sich die Anforderungen der regionalen Niederlassungen ändern.
 
## Lösungsdetails ##

Sehen wir uns ein Beispiel für ein Anwendungsmigrationsszenario an. Hier lässt sich erkennen, wie App Service-Web-Apps-Funktionen zusammenwirken, um eine gute Lösung zu bieten und Geschäftswert zu schaffen.
 
In diesem Beispiel wird die Branchenanwendung, die wir erläutern eine Spesenabrechnunngsanwendung sein, mit der Mitarbeiter ihre Ausgaben für die Rückerstattung übermitteln können. Die Anwendung wird auf einem Windows Server 2003 R2 mit IIS6 gehostet und die Datenbank ist eine SQL Server 2005-Datenbank. Der Grund, warum ein älterer Server gewählt wurde, ist das bevorstehende Support-Ende für Windows Server 2003 R2 und SQL Server 2005. Und es stehen [Tools](http://aka.ms/websitesmigration) und [Anleitungen](http://aka.ms/websitesmigrationresources) für die automatische Migration von Arbeitslasten nach Azure zur Verfügung. Denken Sie daran, das Muster in diesem Beispiel gilt für eine große Bandbreite von Migrationsszenarien.

### Migrieren von vorhandenen Anwendungen ###

Schritt 1 der Gesamtlösung für das Verschieben einer Line-of-Business-Anwendung nach Web-Apps besteht in der Identifizierung der vorhandenen Anwendungsressourcen und -architektur. Das Beispiel in diesem Artikel ist eine ASP.NET-Webanwendung, gehostet auf einem IIS-Server, mit der Datenbank auf einem separaten SQL Server gehostet, wie in der folgenden Abbildung dargestellt. Anmeldung für Mitarbeiter am System geschieht über eine Kombination aus Benutzername und Kennwort. Sie geben die Details der Ausgaben an und scannen Kopien von Empfangsbestätigungen in die Datenbank, für jedes Element der Ausgaben.
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### Zu berücksichtigende Punkte ####

Bei der Migration einer Anwendung aus einer lokalen Umgebung sollten Sie bedenken, dass für Web-Apps Beschränkungen bestehen. Einige wichtige Themen, die bei der Migration von Webanwendungen nach Web-Apps beachtet werden sollten, finden Sie unter: ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

-	Portbindungen – Web-Apps unterstützt nur Port 80 für HTTP- und Port 443 für HTTPS-Datenverkehr. Wenn Ihre Anwendung einen anderen Anschluss verwendet, möchte die Anwendung nach erfolgter Migration Port 80 für HTTP und Port 443 für HTTPS-Datenverkehr nutzen. Dies ist häufig ein harmloses Problem, da lokale Bereitstellungen häufig verschiedene Ports verwenden,um die Verwendung von Domänennamen zu umgehen, vor allem in Entwicklungs- und Testumgebungen.
-	Authentifizierung – Web-Apps unterstützt standardmäßig die anonyme Authentifizierung sowie Formularauthentifizierung, sofern von einer Anwendung angegeben. Web-Apps kann Windows-Authentifizierung bieten, wenn die Anwendung nur mit Azure Active Directory und AD FS integriert ist. Diese Funktion wird [hier](http://aka.ms/azurebizapp) ausführlicher erläutert. 
-	GAC-basierte Assemblys – Web-Apps unterstützt nicht die Bereitstellung von Assemblys für den globalen Assemblycache (GAC). Wenn daher die zu migrierende Anwendung diese lokale Funktion nutzt, sollten Sie die Assemblys in den Papierkorb der Anwendung verschieben.
-	IIS5-Kompatibilitätsmodus – Web-Apps unterstützt nicht den IIS5-Kompatibilitätsmodus. Daher wird jede Web-Apps-Instanz und jede Webanwendung unter der übergeordneten Web-Apps-Instanz im selben Arbeitsprozess in einem einzelnen Anwendungspool ausgeführt.
-	Verwendung von COM-Bibliotheken – Web-Apps erlaubt nicht die Registrierung von COM-Komponenten auf der Plattform. Wenn daher die Anwendung COM-Komponenten verwendet, müssen diese in verwaltetem Code neu geschrieben und mit der Anwendung bereitgestellt werden.
-	ISAPI-Filter – ISAPI-Filter können auf Web-Apps unterstützt werden. Sie müssen als Teil der Anwendung bereitgestellt und in der web.config-Datei der Webanwendung registriert werden. Weitere Informationen finden Sie unter [http://aka.ms/azurewebsitesxdt](../web-sites-transform-extend/). 

Nachdem diese Punkte berücksichtigt wurden, müsste Ihre Webanwendung bereit für die Cloud sein. Und keine Sorge, wenn einige Themen nicht vollständig beachtet werden, bringt das Migrationstool die bestmögliche Migrationsleistung.

Die nächsten Schritte beim Migrationsvorgang bestehen darin, eine App Service-Web-App und eine Azure SQL-Datenbank zu erstellen. Es sind Web-Apps-Instanzen in verschiedenen Größen und mit einer unterschiedlichen Anzahl von CPU-Kernen und RAM-Mengen verfügbar, die Sie basierend auf Ihrer Webanwendungsanforderung auswählen können. Weitere Informationen und Preisangaben finden Sie unter [http://aka.ms/azurewebsitesskus](/pricing/details/websites/). Ebenso bedient Microsoft Azure SQL-Datenbank alle Anforderungen eines Unternehmens mit verschiedenen Dienstebenen und Leistungsstufen, um Anforderungen zu erfüllen. Weitere Informationen finden Sie unter [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/) Nach der Erstellung wird die Anwendung entweder über FTP oder WebDeploy auf App Service-Web-Apps hochgeladen und dann in die Datenbank übertragen.

Bei dieser Migration verwendet die Lösung Azure SQL-Datenbank, aber das ist nicht die einzige Datenbank, die auf Azure unterstützt wird. Unternehmen können auch MySQL, MongoDB, Azure DocumentDB und vieles mehr über Add-Ons nutzen, die im [Azure Store](/marketplace/partner-program/) erworben werden können.

Beim Erstellen einer Azure SQL-Datenbank stehen Ihnen eine Reihe von Optionen zum Importieren einer vorhandenen Datenbank von einem lokalen Server zur Verfügung, vom Generieren eines Skripts für eine vorhandene Datenbank bis zur Verwendung von [Export und Import von Datenebenenanwendungen](http://aka.ms/dacpac).

Die Ausgabenanwendungsdatenbank wurde durch Erstellen einer neuen Azure SQL-Datenbank erstellt, wobei eine Verbindung mit der Datenbank mithilfe von SQL Server Management Studio hergestellt und anschließend ein Skript ausgeführt wurd, um das Datenbankschema zu erstellen und mit Daten aus der lokalen Datenbank zu füllen.

Der letzte Schritt in dieser ersten Phase der Migration erfordert die Aktualisierung der Verbindungszeichenfolgen für die Datenbank für die Anwendung. Dies kann über das Azure-Portal erfolgen. Für jede Web-App können Sie anwendungsspezifische Einstellungen ändern, einschließlich aller Verbindungszeichenfolgen, die von der Anwendung für die Verbindung mit einer Datenbank verwendet werden.

### Alternativen zur Verwendung von Azure SQL-Datenbank ###

Die Azure-Plattform bietet eine Reihe von Alternativen zur Verwendung von Azure SQL-Datenbank als primäre Webdatenbank-Anwendungen. Dadurch wird es möglich, verschiedene Arbeitslasten zu bewältigen, d. h. eine NoSQL-Lösung zu verwenden, oder die Plattform an die Datenanforderungen eines Unternehmens anzupassen. Beispielsweise kann ein Unternehmen über Daten verfügen, die nicht standortfern oder in einer öffentlichen Cloud-Umgebung gespeichert werden dürfen, und strebt daher an, seine lokale Datenbank weiterzuverwenden.

#### Verbindung zu lokalen Ressourcen ####
App Service-Web-Apps bietet zwei Methoden zum Herstellen einer Verbindung zu lokalen Ressourcen, wie z. B. Datenbanken, welche die Wiederverwendung von vorhandener hochwertiger Infrastruktur ermöglichen. Die beiden Methoden sind die Virtual Network-Integration von Web-Apps und Hybridverbindungen:

- Die Virtual Network-Integration von Web-Apps unterstützt die Integration zwischen Web-Apps und Azure Virtual Network und ermöglicht Ihnen den Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, was bei Verbindung mit Ihrem lokalen Netzwerk mit Site-to-Site-VPN-Verbindung die direkte Konnektivität zu Ihren lokalen Systemen erlaubt.
- Hybridverbindungen sind ein Feature von Azure BizTalk Services und bieten eine einfache Möglichkeit zum Herstellen einer Verbindung zu einzelnen Ressourcen wie z. B. SQL Server, MySQL, HTTP-Web-APIs und viele benutzerdefinierte Webdienste.

#### Skalierbarkeit und Flexibilität ####

Wächst ein Unternehmen und seine Mitarbeiterzahl, über Akquisitionen oder natürliches Wachstum, müssen auch die Anwendungen skaliert werden, um diese neuen Anforderungen zu erfüllen. Heute ist es in der Tat üblich, noch mehr örtlich getrennt arbeitenden Teams und Mitarbeiter an entfernten Standorten einzusetzen. Beispiele hierfür sind Unternehmen mit Sitz in den USA, Europa und Asien, mit mobilem Vertriebspersonal in vielen weiteren Gebieten. Web-Apps verfügt über die Fähigkeit, unterschiedliche Änderungen in der Skalierung problemlos und automatisch zu bewältigen.

App Service-Web-Apps ermöglicht es, Webanwendungen so zu konfigurieren, dass sie automatisch über das Azure-Portal skaliert werden, und zwar abhängig von zwei Vektoren – Planzeiten oder CPU-Auslastung. Die automatische Skalierung von Web-Apps bietet eine kostengünstige und äußerst flexible Möglichkeit zur Bewältigung größerer Änderungen bei der Nutzung aller Geschäftsanwendungen, von Webanwendungen wie unserem Spesenabrechnungssystem bis zu Marketing-Websites, bei denen ein starker Anstieg des Datenverkehrs für eine kurze Zeitspanne der Heraufstufung auftritt. Weitere Informationen und Anleitungen zum Skalieren von Webanwendungen mit Web-Apps finden Sie unter [Skalieren von Websites](web-sites-scale.md).

Neben der Skalierungsflexibilität von Web-Apps ermöglicht die Gesamtplattform Business Continuity und Resilience durch die mögliche Verteilung von Webanwendungen und der zugehörigen Ressourcen über mehrere Rechenzentren und geografische Regionen.

## Zusammenfassung ##
App Service-Web-Apps bietet eine flexible, kostengünstige und reaktionsfähige Lösung für die dynamischen Anforderungen eines Unternehmens in einer sich rasant entwickelnden Umgebung. Web-Apps hilft Unternehmen, die Produktivität und Effizienz zu steigern, durch Nutzung einer verwalteten Plattform mit modernen DevOps-Funktionen und reduzierter manueller Verwaltung und der gleichzeitigen Bereitstellung von Unternehmensfunktionen für Skalierung, Resilience, Sicherheit und Integration bei lokalen Ressourcen.

## Call-to-Action ##
Weitere Informationen zu Azure App Service-Web-Apps finden sie unter [http://aka.ms/enterprisewebsites](/sservices/websites/enterprise/). Oder registrieren Sie sich für eine kostenlose Testversion unter [http://aka.ms/azuretrial](/pricing/free-trial/), um den Dienst zu bewerten und die Vorteile für Ihr Unternehmen zu entdecken.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=Oct15_HO3-->