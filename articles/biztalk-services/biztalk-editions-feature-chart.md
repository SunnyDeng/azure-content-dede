<properties 
	pageTitle="Erfahren Sie mehr über die Features in BizTalk Services-Editionen | Azure" 
	description="Vergleichen Sie die Funktionen der BizTalk Services-Editionen: Free, Developer, Basic, Standard und Premium. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>




# BizTalk Services: Editionsübersicht

Azure BizTalk Services sind in folgenden Editionen verfügbar: Free, Developer, Basic, Standard und Premium:

**Free (Vorschau)**: Ermöglicht das Erstellen und Verwalten von Hybridverbindungen. Eine Hybridverbindung stellt eine einfache Methode dar, um eine Azure-Website mit einem lokalen System wie SQL Server zu verbinden. 

**Developer**: Enthält Hybridverbindungen, EAI- und EDI-Nachrichtenverarbeitung mit einem einfach zu bedienenden Verwaltungsportal für Handelspartner sowie Unterstützung für gängige EDI-Schemas und EDI-Verarbeitung über X12 und AS2. Kann gängige EAI-Szenarien erstellen, in denen über eines der Protokolle HTTP/S, REST, FTP, WCF oder SFTP Verbindungen mit Diensten in der Cloud hergestellt werden, um Nachrichten zu lesen und zu schreiben.  Mit den sofort einsatzbereiten SAP-, Oracle eBusiness-, Oracle DB-, Siebel- und SQL Server-Adaptern können Sie die Konnektivität zu lokalen Branchensystemen nutzen. Diese Funktionen sind in einer auf Entwickler ausgerichteten Umgebung mit Visual Studio-Tools verfügbar, um die Entwicklung und Bereitstellung zu erleichtern. Ohne Vereinbarung zum Servicelevel (SLA) auf Entwicklungs- und Testzwecke beschränkt.

**Basic**: Enthält die meisten Funktionen der Developer Edition mit zusätzlichen Hybridverbindungen, EAI-Brücken, EDI-Vereinbarungen und BizTalk Adapter Pack-Verbindungen. Bietet außerdem hohe Verfügbarkeit und eine Skalierungsoption mit einer Vereinbarung zum Servicelevel (SLA).

**Standard**: Enthält alle Funktionen der Basic Edition mit zusätzlichen Hybridverbindungen, EAI-Brücken, EDI-Vereinbarungen und BizTalk Adapter Pack-Verbindungen. Bietet außerdem hohe Verfügbarkeit und eine Skalierungsoption mit einer Vereinbarung zum Servicelevel (SLA).


**Premium**: Enthält alle Funktionen der Standard Edition mit zusätzlichen Hybridverbindungen, EAI-Brücken, EDI-Vereinbarungen und BizTalk Adapter Pack-Verbindungen. Umfasst außerdem Archivierung, hohe Verfügbarkeit und eine Skalierungsoption mit einer Vereinbarung zum Servicelevel (SLA).


In der folgenden Tabelle sind die Unterschiede aufgeführt:

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (Vorschau)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>
<tr>
<td><strong>Startpreis</strong></td>
<td>Nähere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk-Dienste - Preisdetails</a>.</td>
<td>Nähere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk-Dienste - Preisdetails</a>.</td>
<td>Nähere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk-Dienste - Preisdetails</a>.</td>
<td>Nähere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk-Dienste - Preisdetails</a>.</td>
<td>Nähere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk-Dienste - Preisdetails</a>.</td>
</tr>
<tr>
<td><strong>Standardminimalkonfiguration</strong></td>
<td>1 Free-Einheit</td>
<td>1 Developer-Einheit</td>
<td>1 Basic-Einheit</td>
<td>1 Standard-Einheit</td>
<td>1 Premium-Einheit</td>
</tr>
<tr>
<td><strong>Skalieren</strong></td>
<td>Keine Skalierung</td>
<td>Keine Skalierung</td>
<td>Ja, in Schritten von jeweils 1 Basic-Einheit</td>
<td>Ja, in Schritten von jeweils 1 Standard-Einheit</td>
<td>Ja, in Schritten von jeweils 1 Premium-Einheit</td>
</tr>
<tr>
<td><strong>Maximal zulässige Skalierungsgröße</strong></td>
<td>Keine Skalierung</td>
<td>Keine Skalierung</td>
<td>Bis zu 8 Einheiten</td>
<td>Bis zu 8 Einheiten</td>
<td>Bis zu 8 Einheiten</td>
</tr>
<tr>
<td><strong>EAI-Brücken pro Einheit</strong></td>
<td>Nicht enthalten</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Einschließlich TPM-Vereinbarungen</td>
<td>Nicht enthalten</td>
<td>Enthalten. 10 Vereinbarungen pro Einheit.</td>
<td>Enthalten. 50 Vereinbarungen pro Einheit.</td>
<td>Enthalten. 250 Vereinbarungen pro Einheit.</td>
<td>Enthalten. 1000 Vereinbarungen pro Einheit.</td>
</tr>
<tr>
<td><strong>Hybridverbindungen pro Einheit</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Datenübertragung von Hybridverbindungen (GB) pro Einheit</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>BizTalk Adapter Service-Verbindungen zu lokalen Branchensystemen</strong></td>
<td>Nicht enthalten</td>
<td>1 Verbindung</td>
<td>2 Verbindungen</td>
<td>5 Verbindungen</td>
<td>25 Verbindungen</td>
</tr>
<tr>
<td align="left"><strong>Unterstützte Protokolle/Systeme:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure Blob</li>
<li>REST-APIs</li>
</ul>
</td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
</tr>
<tr>
<td><strong>Hohe Verfügbarkeit</strong>
<br/><br/>
Nähere Informationen zur Vereinbarung zum Servicelevel (SLA) finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services - Preisdetails</a>.
</td>
<td>Nicht enthalten</td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
</tr>
<tr>
<td><strong>Sichern und Wiederherstellen</strong></td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
</tr>
<tr>
<td><strong>Nachverfolgung</strong></td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
</tr>
<tr>
<td><strong>Archivierung</strong><br/><br/>
Einschließlich Empfangsnachweis und Herunterladen nachverfolgter Nachrichten</td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Nicht enthalten</td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
</tr>
<tr>
<td><strong>Verwendung von benutzerdefiniertem Code</strong></td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
</tr>
<tr>
<td><strong>Verwendung von Transformationen, einschließlich benutzerdefiniertem XSLT</strong></td>
<td>Nicht enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
<td>Enthalten</td>
</tr>
</table>

**Hinweis**
<br/>Hohe Verfügbarkeit impliziert, dass zum besseren Schutz vor Hardwareausfällen mehrere virtuelle Computer in einer BizTalk-Einheit ausgeführt werden.

## Häufig gestellte Fragen
#### Was ist eine BizTalk-Einheit?
Eine "Einheit" stellt die kleinste Größe einer Azure BizTalk Services-Bereitstellung dar. Den verschiedenen Editionen sind jeweils Einheiten mit unterschiedlicher Rechenkapazität und Speichermenge eigen. Beispielsweise verfügt eine Basic-Einheit über mehr Rechenkapazität als eine Developer-Einheit, eine Standard-Einheit verfügt über mehr Rechenkapazität als eine Basic-Einheit usw. Die Skalierung von BizTalk Services erfolgt in Einheiten.

#### Worin unterscheidet sich BizTalk Services von Azure BizTalk VM?
BizTalk Services stellt eine echte PaaS-Architektur (Platform-as-a-Service) zum Erstellen von Integrationslösungen in der Cloud bereit. Beim PaaS-Modell konzentrieren Sie sich ganz auf die Anwendungslogik und überlassen Microsoft die Infrastrukturverwaltung, die Folgendes beinhaltet:

- Sie müssen keine virtuellen Computer verwalten oder anpassen.
- Microsoft gewährleistet die Verfügbarkeit.
- Sie steuern die bedarfsgerechte Skalierung, indem Sie über das Azure-Verwaltungsportal einfach mehr oder weniger Kapazität anfordern.

BizTalk Server in Azure Virtual Machines stellt eine IaaS-Architektur (Infrastructure-as-a-Service) bereit. Sie können virtuelle Computer erstellen und sie genau wie in Ihrer lokalen Umgebung konfigurieren, sodass es einfacher ist, vorhandene Anwendungen ohne Codeänderungen in der Cloud auszuführen. Beim IaaS-Modell sind Sie nach wie vor für das Konfigurieren der virtuellen Computer, das Verwalten der virtuellen Computer (z. B. Installieren von Software und Betriebssystemupdates) und   das Gestalten der Anwendung zur Bereitstellung hoher Verfügbarkeit verantwortlich. 

Wenn Sie neue Integrationslösungen erstellen möchten, welche Ihren Infrastrukturverwaltungsaufwand minimieren, dann sollten Sie BizTalk Services verwenden. Wenn Sie vorhandene BizTalk-Lösungen schnell migrieren und eine bedarfsgerechte Umgebung zum Entwickeln und Testen von BizTalk Server-Anwendungen möchten, dann sollten Sie BizTalk Server auf einer virtuellen Azure-Maschine verwenden.

#### Worin unterscheidet sich der BizTalk Adapter Service von Hybridverbindungen?
Der BizTalk Adapter Service wird von einem Azure BizTalk Service genutzt. Der BizTalk Adapter Service stellt über das BizTalk Adapter Pack eine Verbindung mit einem lokalen Branchensystem her. Eine Hybridverbindung stellt eine einfache und praktische Möglichkeit dar, um Azure-Anwendungen wie Websites und Mobile Services mit einer lokalen Ressource zu verbinden. 

#### Warum wird die Anzahl der Brücken von eins auf zwei erhöht, wenn ich in BizTalk Services eine Vereinbarung erstelle? 

Jede Vereinbarung umfasst zwei verschiedene Brücken: eine Kommunikationsbrücke auf der Senderseite und eine Kommunikationsbrücke auf der Empfängerseite.

####  Was geschieht, wenn die Höchstzahl von Brücken oder Vereinbarungen erreicht worden ist? 

Sie können dann keine neuen Brücken mehr bereitstellen bzw. keine neuen Vereinbarungen mehr erstellen. Um mehr Brücken oder Vereinbarungen bereitstellen zu können, müssen Sie mehr BizTalk Services-Einheiten oder eine Edition mit mehr Einheiten erwerben.

#### Wie kann ich von einer Ebene von BisTalk Services zu einer anderen Ebene migrieren? 

Verwenden Sie den Workflow zum Sichern und Wiederherstellen, um von einer Ebene zu einer anderen Ebene zu migrieren. Nicht alle Migrationspfade werden unterstützt. Nähere Informationen zu den unterstützten Migrationspfaden finden Sie unter [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873).

#### Ist der BizTalk Adapter Service im Dienst enthalten? Wie erhalte ich die Software?

Ja, BizTalk Adapter Service und BizTalk Adapter Pack sind im Azure BizTalk Services SDK enthalten und als [Download](http://www.microsoft.com/download/details.aspx?id=39087) verfügbar.

## Weiter

Um Azure BizTalk Services im Azure-Verwaltungsportal bereitzustellen, wechseln Sie zu [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280). Wenn Sie mit dem Erstellen von Anwendungen beginnen möchten, wechseln Sie zu [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Weitere Informationen
- [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!--HONumber=46--> 
 