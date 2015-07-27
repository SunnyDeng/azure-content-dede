<properties 
	pageTitle="Enterprise-Connectors in Microsoft Azure App Service | Azure" 
	description="Erfahren Sie mehr über das Erstellen und Konfigurieren von Enterprise-Connectors; Microservices-Architektur" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>

# Enterprise-Connectors in Microsoft Azure App Service

> [AZURE.NOTE]Dieses Thema wird eingestellt. Unter [Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md) finden Sie alle verfügbaren integrierten Connectors und API-Apps.

Microsoft Azure App Service (oder kurz App Service) bietet mehrere Connectors, die mit Enterprise-Systemen wie SAP und Marketo verwendet werden können. Bei Verwenden dieser Connectors können Sie sich problemlos mit einem Enterprise-System verbinden und verschiedene Aufgaben ausführen.

Diese Enterprise-Connectors bieten die Funktionen "Trigger" oder "Aktion". Ein Trigger startet eine neue Instanz basierend auf einem bestimmten Ereignis wie z. B. der Aktualisierung eines Eintrags in MongoDb. Eine Aktion ist das Ergebnis, wie z. B. das Abrufen eines Eintrags aus MongoDB nach dessen Aktualisierung.


## Was ist ein Enterprise-Connector?
Enterprise-Connectors sind bestehende "API-Apps", die eine Verbindung mit lokalen und cloudbasierten Enterprise-Systemen herstellen können wie z. B.:

Connector | Beschreibung
--- | ---
Marketo | Ein Connector vom Typ **Aktion**. Herstellen einer Verbindung mit Marketo und Ausführen verschiedener Aktionen wie Leads erstellen/aktualisieren, Leadänderungen abrufen, Kampagne planen, Kampagne anfordern, Leads abrufen, Kampagnen-/Listeninformationen abrufen, Leads zu Liste hinzufügen und Leads aus Liste entfernen.
MongoDB | Ein Connector vom Typ **Trigger** und **Aktion**. Verbinden mit der Mongo Server-Datenbank oder einem lokalen Mongo-Server. Für eine MongoDB-Auflistung verschiedene Aktionen ausführen, wie z. B. Erstellen, Aktualisieren, Abrufen und Löschen von Einträgen. 
QuickBooks | Ein Connector vom Typ **Aktion**. Herstellen einer Verbindung mit QuickBooks und Nutzen Ihrer Anwendungen zum Erstellen, Aktualisieren, Lesen, Löschen und Abfragen anderer Entitäten aus Intuit QuickBooks, wie z. B. Kunden, Artikel, Rechnungen usw.
Salesforce |Ein Connector vom Typ **Aktion**. Herstellen einer Verbindung mit Salesforce zum Verwalten verschiedener Entitäten wie z. B. Kunden, Leads, Verkaufschancen, Servicetickes usw. in Ihrem Salesforce-Konto. Sie können unterschiedliche Aktionen ausführen, wie z. B. Erstellen, Aktualisieren, Einfügen, Abfragen und Löschen der verschiedenen Entitäten. 
SugarCRM | Ein Connector vom Typ **Aktion**. Herstellen einer Onlineverbindung mit SugarCRM und Nutzen Ihrer Anwendungen zum Erstellen, Aktualisieren, Lesen und Löschen verschiedener Typen von Modulen, z. B. Benutzerkonten, Kontakte, Produkte usw.
SAP | Ein Connector vom Typ **Aktion**. Herstellen einer Verbindung mit einem lokalen SAP-Server. Aufrufen von RFCs, BAPIs und tRFCs und auch Senden von IDOCs.


Mithilfe dieser Connectors können Sie verschiedene Aufgaben im Enterprise-System ausführen. Mit dem QuickBooks-Connector können Sie z. B. typische Aufgaben in QuickBooks ausführen, z. B. einen neuen Kunden hinzufügen, eine Rechnung aktualisieren oder ein Element löschen.

Sie können ohne großen Aufwand beliebig viele Connectors erstellen. Sie können auch einen einzelnen Connector in mehreren Szenarien oder Workflows wiederverwenden.

Nehmen wir z. B. an, Sie sind auf einer Konferenz und treffen einen Kunden, der 10 Artikel Ihres Produkts erwerben möchte. Auf Ihrem Mobilgerät können Sie die mobile App öffnen, die den von Ihnen erstellten QuickBooks-Connector verwendet, die Details des Kunden hinzufügen und eine Rechnung erstellen.

Während Sie die Konferenz besuchen, möchte ein Kunde an Ihrem Standort eine vorhandene Rechnung bezahlen. Der Mitarbeiter öffnet eine Anwendung, die den gleichen QuickBooks-Connector verwendet, den Sie erstellt haben, sucht die Kundeninformationen und aktualisiert die Rechnung entsprechend dem neuen Saldo.

Hierbei handelt es sich um verschiedene Datenflüsse, die den gleichen QuickBooks-Connector verwenden. Sie können diese Connectors erstellen und verwenden, ohne Code zu schreiben. Lassen Sie uns anfangen.


## Anforderungen für den Einstieg
Wenn Sie einen Connector erstellen, der ein Enterprise-System verwendet , sind bestimmte Ressourcen erforderlich. Diese Elemente müssen von Ihnen erstellt werden, bevor sie im Connector verwendet werden können. Diese Anforderungen umfassen u. a.:

Voraussetzung | Beschreibung
--- | ---
Service Bus-Namespace und seine Schlüsselwerte | Wenn ein lokales SAP- oder MongoDB-System verwendet wird, werden ein Service Bus-Namespace und dessen Schlüsselwerte benötigt. Wenn Sie keine Verbindung mit einem lokalen System herstellen, ist ein Service Bus-Namespace nicht erforderlich.<br/><br/>[Erstellen eines Service Bus-Namespace](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Lokaler Hybrid Connection Manager | Wenn Sie eine Verbindung mit einem lokalen System herstellen, installieren Sie den Hybrid Connection Manager auf dem lokalen System. Wenn Sie den lokalen Connector erstellen, ist der Download in den Connectoreigenschaften oder -einstellungen verfügbar. 
Systemspezifische Eigenschaften | Wenn Enterprise-Systeme verwendet werden, gibt es systemspezifische Eigenschaften (z. B. Benutzername und Kennwort), die Sie benötigen. 


## Erstellen eines Connectors
Ein Connector kann im Azure-Portal erstellt werden.

### Erstellen eines Connectors im Azure-Portal
Im Azure-Portal können Sie beim Erstellen von Logik-Apps, Web-Apps oder mobilen Apps einen Enterprise-Connector erstellen. Sie können sie auch auf ihrem eigenen Blatt erstellen. Beide Möglichkeiten sind einfach, weshalb der Vorgang von Ihren Anforderungen oder Einstellungen abhängt. Es gibt Benutzer, die lieber zuerst alle Connectors mit ihren bestimmten Eigenschaften erstellen. Anschließend erstellen sie die Logik-, Web- oder mobilen Apps und fügen den Connector hinzu, den Sie erstellt haben.

In den folgenden Schritte erstellen Sie einen Enterprise-Connector auf dem dazugehörigen Blatt:

1. Wählen Sie im Azure-Portal im Startmenü (auf der Startseite) **Marketplace** aus. **API-Apps** zeigt alle vorhandenen Connectors. Sie können einen bestimmten Connector auch **suchen**.
2. Wählen Sie den Connector aus. Wählen Sie im neuen Blatt **Erstellen** aus. 
3. Geben Sie die Eigenschaften ein: 

	Eigenschaft | Beschreibung
--- | ---
Name | Geben Sie einen beliebigen Namen für den Connector ein. Sie können ihn beispielsweise *SAPConnector*, *SalesForceGetAccounts* oder *QuickBooksGetItems* nennen.
Paketeinstellungen | Geben Sie die Einstellungen für das Enterprise-System ein, wie z. B. *SAP-Benutzername* oder *SugarCRM-Server-URL*. Siehe [Spezifische Eigenschaften des Enterprise-Systems](#AddProperties) in diesem Thema. 
App Service-Plan | Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.
Preisstufe | Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet. 
Ressourcengruppe | Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Alle API-Apps und Connectors für Ihre Logik- Apps, Web-Apps und mobilen Apps müssen in derselben Ressourcengruppe enthalten sein. <br/><br/>Unter [Verwenden von Ressourcengruppen](../resource-group-overview.md) wird diese Eigenschaft erläutert. 
Abonnement | Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.
Standort | Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. 
Zum Startmenü hinzufügen | Wählen Sie diese Option aus, um den Connector Ihrem Startmenü (der Startseite) hinzufügen.


	**<a name="AddProperties"></a>Spezifische Eigenschaften des Enterprise-Systems**

> [AZURE.IMPORTANT]Jeder Connector verfügt über Eigenschaften, die für das jeweilige Enterprise-System spezifisch sind. Beim Herstellen einer Verbindung mit SAP geben Sie SAP-spezifische Eigenschaften ein. Beim Herstellen einer Verbindung mit Salesforce geben Sie Salesforce-spezifische Eigenschaften ein usw. In der folgenden Tabelle sind die erforderlichen Enterprise-Systemeigenschaften aufgeführt:
	
	Enterprise System | Required Properties
--- | ---
Marketo | <ul><li>Endpunkt</li><li>Name des Anbieters</li></ul>
MongoDB| <ul><li>Verbindungszeichenfolge</li><li>Host</li><li>Port</li><li>Benutzername</li><li>Kennwort</li><li>Datenbank</li><li>Verwendung der SSL-Verschlüsselung</li><li>Lokal: Geben Sie "False" ein, wenn die Lösung cloudbasiert ist. Wenn das MongoDb-System lokal ist, geben Sie "True" und außerdem die folgenden Eigenschaften ein:<ul><li>Name des freigegebenen Zugriffsschlüssels</li><li>Service Bus-Namespace</li><li>Relaypfad</li><li>Schlüssel senden</li></ul></li></ul>
QuickBooks | <ul><li>Firmen-ID</li><li>Name des Anbieters</li></ul>
SAP | <ul><li>Hostname</li><li>Sprache</li><li>Benutzername</li><li>Kennwort</li><li>Systemnummer</li><li>Service Bus-Verbindungszeichenfolge</li><li>RFC-Namen</li><li>TRFC-Namen</li><li>BAPI-Namen</li><li>IDOC-Name</li></ul>
Salesforce | <ul><li>Name des Anbieters</li><li>Instanz</li><li>Version</li><li>Entitäten (durch Trennzeichen getrennte Werte)</li></ul>
SugarCRM | <ul><li>Server-URL</li><li>Name des Anbieters</li><li>Modulnamen</li></ul>

4. Klicken Sie auf **Erstellen**.


## Installieren des lokalen Hybrid Connection Managers
Nach der Erstellung des Enterprise-Connectors, der eine Verbindung mit einem lokalen System wie SAP herstellt, installieren Sie den Hybrid Connection Manager:

1. Öffnen Sie im lokalen Enterprise-System das Azure-Verwaltungsportal, und wählen Sie Ihren Enterprise-Connector aus. 
2. Wählen Sie auf dem Blatt **Hybridverbindung** aus. 
3. Kopieren Sie den Wert von **Primäre Gateway-Konfigurationszeichenfolge**. 
4. Klicken Sie auf **Herunterladen und konfigurieren**. Fügen Sie während der Installation die kopierte Gateway-Konfigurationszeichenfolge ein, und fahren Sie mit der Installation fort. 
5. Um die Konnektivität zu bestätigen, öffnen Sie das Blatt des Enterprise-Connectors. Der Status sollte als **Verbunden** angegeben werden. 

Unter [Integration in einem lokalen SAP-Server](app-service-logic-integrate-with-an-on-premise-SAP-server.md) finden Sie ein Beispiel.


## Überwachen Ihrer API-Apps
Öffnen Sie im Azure-Verwaltungsportal Ihre API-App. Im Abschnitt **Vorgänge** können Sie verschiedene Verwaltungsvorgänge anzeigen. Dazu zählen z. B.:

- Anzeigen von Informations- und Fehlerereignissen
- Anzeigen der Speicherauslastung und Threadanzahl des Arbeitsprozesses (w3wp)
- Anzeigen des Anwendungs- und Webserverprotokolls

Unter [Überwachen von Logik-Apps](app-service-logic-monitor-your-logic-apps.md) finden Sie weitere Informationen.


## Hinzufügen des Connectors zu Ihrer Anwendung 
Microsoft Azure App Service stellt verschiedene Anwendungstypen zur Verfügung, die diese Connectors verwenden können. Sie können neue erstellen oder vorhandene Connectors verwenden und diese Logik-Apps, mobilen Apps oder Web-Apps hinzufügen.

Wenn Sie in Ihrer App den Connector im Katalog auswählen, wird er automatisch Ihrer App hinzugefügt.

Über die folgenden Schritte wird ein Enterprise-Connector Logik-Apps, mobilen Apps oder Web-Apps hinzugefügt:

1. Wechseln Sie im Azure-Portal im Startmenü (auf der Startseite) zu **Marketplace**, und suchen Sie Ihre Logik-, mobile oder Web-App.

	Wenn Sie eine neue Anwendung erstellen, suchen Sie nach "Logik-App", "Mobile App" oder "Web-App". Wählen Sie die App im neuen Blatt aus, und klicken Sie auf **Erstellen**. Unter [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) werden die Schritte erläutert.

2. Öffnen Sie Ihre App, und wählen Sie **Trigger und Aktionen** aus.

3. Fügen Sie über den **Katalog** die von Ihnen erstellten Connectors hinzu, die automatisch Ihrer App hinzugefügt werden.
4. Klicken Sie zum Speichern der Änderungen auf **OK**. 


## Sicherheit
Einige der Enterprise-Connectors verwenden die OAuth-Sicherheitsfunktionen. Wenn Sie den Connector Ihrer App hinzufügen, **autorisieren** Sie den Connector zum Herstellen einer Verbindung mit dem Enterprise-System mit Ihrem Anmeldekonto und stimmen den Nutzungsbedingungen zu. Wenn Sie dies tun, verwenden Ihre App und der Connector das Anmeldekonto zur Authentifizierung beim System.


### Zugreifen auf einen Connector mit REST-APIs
[Informationen zur Verwendung der REST-APIs finden Sie in der Dokumentation.](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Weitere Ressourcen zu Enterprise-Connectors
[Integration in einen lokalen SAP-Server](app-service-logic-integrate-with-an-on-premise-SAP-server.md)<br/> [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)


## Weitere Informationen zu Logik- und Web-Apps
[Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md)<br/> [Websites und Web-Apps in Azure App Service](../app-service-web/app-service-web-overview.md)



## Weitere Connectors

[Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md)<br/><br/>[Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO3-->