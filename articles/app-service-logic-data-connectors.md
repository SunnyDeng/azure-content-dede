<properties 
	pageTitle="Microsoft Azure-API-Apps - Datenconnectors | API-Apps-Microservice" 
	description="Erfahren Sie, wie Sie Microsoft Azure-Datenconnector-API-Apps erstellen und die API-App Ihrer Logik-App hinzufügen; Microservices" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/>


# Datenconnectors in Microsoft Azure App Service


## Was ist ein App- und Data Services-API-App-Connector?
App- und Data Services-Connectors sind "API-Apps", die eine Verbindung mit einer breiten Palette von Datendienstanwendungen herstellen und mehrere  *Actions* für den authentifizierten Benutzer ausführen können. Die meisten dieser Connectors können auch mit einem  *Trigger* konfiguriert werden. Trigger sind Ereignisse (ähnlich wie Ereignisse in .NET Framework), die für einige Connectors zum Auslösen eines Workflows konfiguriert werden können. Beispielsweise kann eine Instanz des Dropbox-Connectors mit einem  *new file*-Trigger konfiguriert werden. Dabei wird der Trigger jedes Mal ausgelöst, wenn dem überwachten Dropbox-Konto eine neue Datei hinzugefügt wird. Dieser Trigger kann dann zum Auslösen einer  *Action* konfiguriert werden, die einen  *Get*-Vorgang auf die Datei anwendet und sie in eine lokale SharePoint-Liste hochlädt.


Hier eine kurze Übersicht über die einzelnen App- und Data Services-Connectors, die in der Azure-Galerie verfügbar sind. 

<table>
<tr>
<th> Name</th>
<th> Beschreibung</th>
<th> Trigger</th>
<th>Aktionen</th>

<tr>
<td>Azure Media Services
<td>Der Azure Media Services-Connector ermöglicht Ihnen die Erstellung vollständiger Medienworkflows mit flexibler und skalierbarer Codierung, Verpackung und Verteilung. Sie können auch Video- oder Audioinhalte sicher hochladen, speichern, codieren und streamen - sowohl für die bedarfsgesteuerte als auch die Livestreaming-Übermittlung an eine Vielzahl von Endpunkten (TV, PC und mobile Geräte).
<td>Keine
<td>Keine 
</tr>

<tr>
<td>Azure Mobile Services
<td>Ein Azure Mobile Services-Connector ermöglicht das Erstellen und Ändern von Daten und Aufrufen benutzerdefinierter APIs.
<td>Keine
<td><li>Abfragen
<li>Einfügen
<li>Löschen
<li>Aktualisieren
<li>Benutzerdefinierter API-Aufruf

</tr>

<tr>
<td>Azure Service Bus
<td>Mit dem Azure Service Bus-Connector können Sie Nachrichten aus Service Bus-Entitäten wie Warteschlangen und Themen senden und Nachrichten von Service Bus-Entitäten wie Warteschlangen und Abonnements empfangen.
<td>Neue Nachrichten
<td>Nachricht senden
</tr>

<tr>
<td>Azure Storage-Blob
<td>Azure Storage-Blob ermöglicht Ihnen, eine Verbindung mit Blob-Speicher herzustellen und bestimmte Aktionen auszuführen.
<td>Neue Datei hinzugefügt
<td><li>Hochladen <li>Abrufen <li>Löschen <li>Auflisten <li>Kopieren <li>Momentaufnahme
</tr>

<tr>
<td>Azure Storage-Tabelle
<td>Der Azure Storage-Tabellenconnector ermöglicht das Verbinden mit einer Azure Storage-Tabelle und Ausführen verschiedener Aktionen, wie z. B. Entität abrufen, Entität abfragen, Entität einfügen, Entität aktualisieren, Entität löschen und Trigger zum Abrufen von Daten hinzufügen.
<td>Neue Entität
<td><li>Entität abrufen
	<li>Entität einfügen
	<li>Entität löschen
	<li>Entität aktualisieren
	<li>Abfragen

</tr>

<tr>
<td>Box
<td>Der Box-Connector ermöglicht das Herstellen einer Verbindung mit Box und Anwenden verschiedener Aktionen auf Ihre Dateien. 
<td>Neue Dateien hinzugefügt
<td><li>Datei hochladen
<li>Datei abrufen
<li>Datei löschen
<li>Dateien auflisten
</tr>

<tr>
<td>Dropbox
<td>Mit dem Dropbox-Connector können Sie eine Verbindung mit Dropbox herstellen und bestimmte Aktionen ausführen, wie z. B. Datei hochladen, Datei abrufen, Datei löschen, Dateien auflisten und einen Trigger zum Abrufen von Dateien hinzufügen.
<td>Neue Dateien hinzugefügt
<td><li>Datei hochladen
<li>Datei abrufen
<li>Datei löschen
<li>Dateien auflisten
</tr>

<tr>
<td>HDInsight
<td>Mithilfe eines HDInsight-Connectors können Sie einen Hadoop-Cluster auf Azure erstellen und verschiedene Hadoop-Aufträge übermitteln, wie z. B. Hive, Pig, MapReduce und Streaming MapReduce. Mit diesem Connector können Sie einen Cluster einrichten, einen Auftrag übermitteln und warten, bis der Auftrag abgeschlossen ist.
<td>-
<td><li>Cluster erstellen
		<li>Auf die Erstellung des Clusters warten
		<li>Pig-Auftrag übermitteln
		<li>Hive-Auftrag übermitteln
		<li>MapReduce-Auftrag übermitteln
		<li>Beendigung des Auftrags abwarten
		<li>Cluster löschen
<li>MapReduce-Auftrag übermitteln
<li>Streaming MapReduce-Auftrag übermitteln

</tr>

<tr>
<td>Microsoft SQL
<td>Ein Microsoft SQL-Connector ermöglicht das Erstellen und Ändern von Einträgen in Microsoft SQL Server- und Azure SQL-Datenbanktabellen.
<td>-
<td><li>Nachrichten: senden, auflisten, abrufen und durchsuchen
	<li>Telefonnummern: kaufen, auflisten und Ortsruf- und gebührenfreie Nummern abrufen
</tr>

<tr>
<td>MongoDB
<td>Mit einem MongoDB-Connector können Sie Dokumente in einer MongoDB-Auflistung erstellen, aktualisieren, löschen und abrufen.
<td>Neues Dokument
<td>	<li>Dokument hinzufügen
		<li>Dokument aktualisieren
		<li>Dokumente abrufen
		<li>Dokument einfügen/aktualisieren
		<li>Dokument löschen
</tr>

<tr>
<td>Office 365
<td>Ein Office 365-Connector ermöglicht das Senden und Empfangen von E-Mails und Verwalten von Kalendern und Kontakten.
<td>Neue Nachricht
<td>	<li>E-Mail senden
		<li>E-Mail beantworten
		<li>Ereignis senden
		<li>Kontakt hinzufügen
</tr>

<tr>
<td>OneDrive
<td>Mit dem OneDrive-Connector können Sie eine Verbindung mit Ihrem persönlichen Microsoft OneDrive-Speicherkonto herstellen und verschiedene Aktionen ausführen, wie z. B. Dateien hochladen, abrufen, löschen und auflisten.
<td>Neue Datei
<td><li>Dateien: hochladen, löschen, auflisten, herunterladen
</tr>

<tr>
<td>Oracle
<td>Ein Oracle Database-Connector ermöglicht Ihnen das Erstellen und Ändern von Einträgen in einer Oracle-Datenbanktabelle.
<td>Neue Daten auf Grundlage der Abfrage
<td><li>Tabelle: Daten einfügen, aktualisieren, auswählen, löschen
<li>Gespeicherte Prozedur aufrufen
</tr>

<tr>
<td>SharePoint Online
<td>Mithilfe eines Microsoft SharePoint-Connectors können Sie Dokumente und Listenelemente in SharePoint Server oder Microsoft SharePoint Online erstellen und ändern.
<td><li>Neues Dokument
<li>Neues Listenelement
<td><li>Dokumentbibliothek: Hochladen, löschen und abrufen
<li>Liste: Element einfügen

</tr>

<tr>
<td>SharePoint Server
<td>Mit dem SharePoint Server-Connector können Sie Dokumente und Listenelemente in Ihrem SharePoint-Server verwalten. Standardanmeldeinformationen, die Windows-Authentifizierung und die formularbasierte Authentifizierung werden unterstützt. Sie müssen eine Service Bus-Verbindungszeichenfolge bereitstellen und den lokalen Listener-Agent installieren, bevor Sie eine Verbindung mit dem Server herstellen können.
<td><li>Neues Dokument
<li>Neues Listenelement
<td><li>Dokumentbibliothek: Hochladen, löschen und abrufen
<li>Liste: Element einfügen
</tr>
</table>


##Gründe für die Verwendung von Connectors

Connectors beschleunigen die Anwendungsentwicklung und ermöglichen sogar Nicht-Entwicklern das Erstellen voll funktionsfähiger Unternehmensanwendungen, und zwar ohne eine Programmiersprache lernen oder Code schreiben zu müssen.

Nun, da Sie eine Vorstellung haben, was unsere App- und Data Services-Connectors leisten können, wollen wir einen Blick auf einige einfache Anwendungsfälle für diese Connectors werfen. 

### Überwachung Ihrer Dropbox und Aktualisierung von SharePoint ###
Angenommen, Sie arbeiten in einem Bauunternehmen, das sehr große Dateien mit Bauplänen empfängt. Diese Dateien sind normalerweise zu groß, um per E-Mail übertragen zu werden, weshalb Ihr Unternehmen ein Dropbox-Konto eingerichtet hat und seine Kunden auffordert, die Baupläne in der Dropbox abzulegen. Sie könnten nun die Mitarbeiter bitten, ständig die Dropbox-Ordner auf neue Pläne zu überprüfen und dann auf Ihren SharePoint-Server hochzuladen. Allerdings sind Sie überzeugt, dass es eine bessere Möglichkeit geben muss! Glücklicherweise haben Sie herausgefunden, dass Microsoft vor Kurzem seinen App- und Data Services-Connector für Dropbox, SharePoint und andere Datendienste veröffentlicht hat. Sie können nun problemlos Connectors für Dropbox und SharePoint erstellen, sie einer Logik-App hinzufügen und zum Hochladen jeder neuen Datei  aus Ihrem Dropbox-Konto in Ihre SharePoint-Liste konfigurieren. Da der Dropbox-Connector über einen  *new message*-Trigger verfügt, können Sie ihn verwenden, um Ihre Logik-App zu benachrichtigen, dass eine neue Datei vorhanden ist. Der Dropbox-Connector kann die Datei anschließend herunterladen. Ihr SharePoint-Connector kann dann mithilfe der SharePoint-Aktion  *upload* zum Hochladen der Datei in eine SharePoint-Liste konfiguriert werden. Sie erreichen dies alles, ohne eine einzige Codezeile schreiben zu müssen.  

Lassen Sie uns anfangen. 

##Erstellen eines Connectors

Es gibt zwei Möglichkeiten zur Erstellung von Connectors:

- Über das Microsoft Azure-Verwaltungsportal (keine Codierung erforderlich!)
- Mithilfe der REST-APIs. 

### Erstellen eines SharePoint-Connectors im Microsoft Azure-Portal

1. Wählen Sie im Azure-Portal **Neu** > **Web + Mobil** > **Azure Marketplace** aus.
2. **Suchen** Sie den Connector, oder wählen Sie ihn in der Liste aus. Nach dem Auswählen wird ein neues Blatt bzw. Fenster geöffnet. Klicken Sie auf **Erstellen**. 
3. Geben Sie die folgenden Eigenschaften für den Connector ein: 
	<table>
	    <tr><th>Eigenschaft</th> <th>Beschreibung</th> </tr>
	    <tr><td>Name</td> <td>Geben Sie für Ihre API-App einen beliebigen Namen ein. Beispielsweise können Sie sie "RulesDiscountTaxCode" oder "APIAppValidateXML" nennen.</td> </tr>
	    <tr><td>App Service-Plan</td> <td>Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.</th> </td>
	    <tr><td>Preisstufe</td> <td>Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet.</td> </tr>
	    <tr><td>Ressourcengruppe</td> <td>Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Unter "Verwenden von Ressourcengruppen" wird diese Eigenschaft erläutert.</td> </tr>
	    <tr><td>Abonnement</td> <td>Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.</td> </tr>
	    <tr><td>Standort</td> <td>Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. </td></tr>
        <tr><td>Zum Startmenü hinzufügen</td> <td>Wählen Sie diese Option aus, um die API-App Ihrem Startmenü (der Startseite) hinzufügen.</td></tr>
	</table> 
4. Klicken Sie auf **Erstellen**. Ihr Connector wird erstellt. Der Vorgang kann eine Weile dauern, und während der Erstellung des Connectors wird die Startseite angezeigt. Verwenden Sie das Menüelement "Benachrichtigungen" auf der linken Seite zur Überwachung des Status des Connectors.

Nun da Sie den ersten Connector erstellt haben, sollten Sie die Erstellung einer Web-, mobilen oder Logik-App angehen. 


### Erstellen eines Connectors mit REST-APIs

[Erstellen von Connectors mit REST-APIs](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Hinzufügen Ihres Connectors zu einer Anwendung 
Microsoft Azure App Service stellt verschiedene Anwendungstypen zur Verfügung, die diese Connectors verwenden können. Sie können z. B. eine  *Logic*-App erstellen, indem Sie einen oder mehrere Ihrer Connectors  *logically* in einer einzigen Anwendung kombinieren.

Zum Verwenden Ihrer Connectors innerhalb Ihrer  *Logic*-App wählen Sie in der Liste einen vorkonfigurierten Connector aus, fügen ihn Ihrem Entwurfsworkflow hinzu, nehmen die erforderlichen Konfigurationsänderungen vor, und schon ist er einsatzbereit. 

Um diese Schritte durchführen zu können, benötigen Sie eine Web-App, mobile App oder Logik-App. Unter <> finden Sie die einzelnen Schritte. Sobald Ihre Anwendung verfügbar ist, fügen Sie die Connectors hinzu. Das geht so:

Gehen Sie folgendermaßen vor, um einen Connector einer Logik-App hinzuzufügen: 

1. Wechseln Sie im Azure-Portal im Startmenü (auf der Startseite) zu **Marketplace**, und suchen Sie Ihre  Logik, mobile oder Web-App. 

	Wenn Sie eine neue Anwendung erstellen, suchen Sie nach "Logik-App", "Mobile App" oder "Web-App". Wählen Sie die App im neuen Blatt aus, und klicken Sie auf **Erstellen**. [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md)  erläutert die Schritte. 

2. Öffnen Sie Ihre App, und wählen Sie **Trigger und Aktionen** aus. 
3. Wählen Sie in der **Galerie** den Connector aus. Er wird Ihrer App hinzugefügt.
4. Konfigurieren Sie den Connector:
5. Jeder Connector hat Eigenschaften, die spezifisch für den Dienst und die Umgebung sind, mit der er eine Verbindung herstellt. Geben Sie die Details der Eigenschaften ein. Bedenken Sie, dass einige Eigenschaften optional sind.
6. Klicken Sie zum Speichern der Änderungen auf **OK**.


## Sicherheit
Connectors verwenden entweder OAuth oder Benutzernamen und Kennwörter. 


## Weitere Informationen zu Logik- und Web-Apps
[Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md) |
[Websites und Web-Apps in Azure App Service](app-service-web-app-azure-portal.md) |



## Weitere Connectors

[BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md) |
[Enterprise-Connectors](app-service-logic-enterprise-connectors.md) |
[Business-to-Business-Connectors](app-service-logic-b2b-connectors.md) |
[Connectors für soziale Netzwerke](app-service-logic-social-connectors.md) |
[Protokollconnectors](app-service-logic-protocol-connectors.md) |
[App- und Data Services-Connectors](app-service-logic-data-connectors.md) |
[Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md)

<!--HONumber=49-->