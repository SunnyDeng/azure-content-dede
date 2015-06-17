<properties 
	pageTitle="Der Hybrid-Verbindungs-Manager verwenden für Azure-App-Dienst." 
	description="Installieren Sie und konfigurieren Sie den Hybrid Connection Manager in Azure-App-Dienst; Microservices-Architektur" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="mandia"/>

# Verwenden von Hybrid Connection Manager in Azure Anwendungsdiensts
Einige Connectors können mit einem lokalen System, wie SharePoint, SQL Server, SAP usw. verbinden. Für die Verwendung ein lokalen Systems verwendet Azure-Anwendungsdiensts Hybrid Connection Manager.

Der Hybrid Connection Manager (HCM) ist ein Klick-einmal Installationsprogramm, das auf einem IIS-Server in Ihrem Netzwerk hinter der Firewall installiert ist. Ein Azure Service Bus-Relay verwendet wird, authentifiziert HCM das lokale System mit dem Connector in Azure.

> [AZURE.NOTE]Hybrid-Verbindungs-Manager ist erforderlich, nur, wenn Sie sich hinter der Firewall auf eine lokale Ressource verbinden. Wenn Sie nicht mit einem lokalen System verbinden, nicht, müssen Sie den Hybrid Connection Manager.

Zunächst müssen Sie folgende Aktionen ausführen:

- Azure Service Bus Relay-Namespace SAS-Verbindungszeichenfolge. Finden Sie unter [Service Bus Preise](http://azure.microsoft.com/pricing/details/service-bus/) zu welcher Ebene Relays gehört.
- Lokale Anmeldung Systeminformationen, einschließlich Benutzername und Kennwort. Wenn Sie eine lokale SQL Server herstellen, benötigen Sie z. B. die SQL Server-Anmeldekonto und ein Kennwort.
- Lokale Serverinformationen, einschließlich der Port-Nummer und Servername. Wenn Sie eine lokale SQL Server herstellen, benötigen Sie z. B. die SQL Server-Namen und TCP-Portnummer.

## Abrufen der Servicebus-Verbindungszeichenfolge

Kopieren Sie in Azure-Portal den Service Bus-Stamm SAS-Verbindungszeichenfolge. Diese Verbindungszeichenfolge verbindet Ihre Azure-Connector auf Ihrem lokalen System.

1. In der [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885), wählen Sie den Service Bus-Namespace und **Verbindungsinformationen**:

	![][SB_ConnectInfo]

2. Kopieren Sie die SAS-Verbindungszeichenfolge:

	![][SB_SAS]

## Installieren Sie den Hybrid-Verbindungs-Manager

1. In der Vorschau [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), wählen Sie den Connector, die Sie erstellt haben. Sie können zum Öffnen auswählen **Durchsuchen**, wählen Sie **API-Apps**, und wählen Sie dann der Connector oder API-App. <br/><br/> In **Hybridverbindung**, das Setup ist **unvollständige**: <br/> ![][2] 

2. Wählen Sie **Hybridverbindung**. Die zuvor eingegebenen Service Bus-Verbindungszeichenfolge wird aufgeführt.
3. Kopieren der **primäre Konfigurationszeichenfolge**: <br/> ![][PrimaryConfigString]

4. Klicken Sie unter **lokalen Hybrid Connection Manager**, kann der Hybrid-Verbindungs-Manager heruntergeladen oder direkt über das Portal zu installieren. <br/><br/> Um direkt über das Portal zu installieren, gehen Sie auf dem lokalen IIS-Server, navigieren Sie zum Verwaltungsportal, und wählen Sie **herunterladen und Konfigurieren von**. <br/><br/> Informationen zum Herunterladen der Hybrid-Verbindungs-Manager zur Ihrer lokalen IIS-Server, und wechseln Sie zu den **ClickOnce-Anwendung** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). Die Installation wird automatisch gestartet, damit es ausgeführt werden kann.

5. In der **Listener Setup** Fenster, geben Sie die **primäre Konfigurationszeichenfolge** Sie zuvor eingefügt haben (in Schritt 3), und wählen Sie **Installieren**.

Wenn das Setup abgeschlossen ist, wird folgende angezeigt: <br/> ![][3]

Wenn Sie den Connector erneut aufrufen, ist der Hybrid-Verbindungsstatus **verbunden**. Sie müssen den Connector zu schließen und erneut öffnen, möglicherweise: <br/> ![][4]

> [AZURE.NOTE]Um die sekundäre Verbindungszeichenfolge zu wechseln, führen Sie die Einrichtung der Hybrid-Verbindung erneut aus, und geben Sie die **sekundären Konfigurationszeichenfolge**.


## TCP-Ports und Sicherheit

Wenn Sie eine hybridverbindung erstellen, wird eine Website auf dem lokalen IIS-Server erstellt. IIS-Server kann in einer DMZ handeln. Die TCP-Port-Anforderungen auf dem IIS-Server gehören:

TCP-Port | Grund
--- | ---
 | Keine eingehenden TCP-Ports sind erforderlich.
9350 - 9354 | Diese Ports werden für die Datenübertragung verwendet. Der Service Bus Relay Manager Prüfpunkte Port 9350, um festzustellen, ob die TCP-Konnektivität verfügbar ist. Wenn es verfügbar ist, wird davon dann, dass Port 9352 ebenfalls verfügbar ist. Datenverkehr wird über den Port 9352. <br/><br/>Ausgehende Verbindungen auf diese Ports zulassen.
5671 | Wenn Port 9352 für Datenverkehr verwendet wird, wird als Port 5671 verwendet die den Steuerungskanal. <br/><br/>Ausgehende Verbindungen an diesen Port zulassen. 
80, 443 | Wenn Ports 9352 und 5671 nicht brauchbar sind *dann* Ports 80 und 443 sind die Alternative für die Datenübertragung und den Steuerungskanal verwendeten Ports.<br/><br/>Ausgehende Verbindungen auf diese Ports zulassen.
Lokalem System port | Öffnen Sie den Port, der vom System verwendet, auf dem lokalen System. SQL Server verwendet z. B. in der Regel Port 1433. Öffnen Sie TCP-Port.

[Hosten hinter einer Firewall mit Servicebus](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## Problembehandlung

![][HCMFlow]

### Lokale Problembehandlung

1. Bestätigen Sie auf dem IIS-Server die IIS-Web-Rolle installiert ist und die IIS-Dienste werden gestartet.
2. Bestätigen Sie, dass es sich bei den Hybrid Connection Manager installiert und ausgeführt wird, auf dem IIS-Server:
 - Im IIS-Manager (Inetmgr), die ***MicrosoftAzureBizTalkHybridListener*** Website sollte aufgeführt sein und ausgeführt werden. 
 - Diese Website verwendet die ***HybridListenerAppPool*** die ausgeführt wird, als das *NetworkService* integrierte Benutzerkonto. Diese AppPool sollte auch gestartet werden.
3. Bestätigen Sie, dass der Connector installiert ist und ausgeführt wird, auf dem IIS-Server: 
 - Für Ihre App Service Connector wird eine Website erstellt. Angenommen, wenn Sie einen SQL-Connector erstellt haben, besteht eine ***MicrosoftSqlConnector_nnn*** Website. Bestätigen Sie im IIS-Manager (Inetmgr) diese Website aufgelistet und gestartet wird. 
 - Diese Website verwendet einen eigenen IIS-Anwendungspool mit dem Namen ***HybridAppPoolnnn***. Diese AppPool ausgeführt wird, als das *NetworkService* integrierte Benutzerkonto. Diese Website und AppPool sollte sowohl gestartet werden. 
 - Durchsuchen Sie den lokalen Connector. Wenn die Connector-Website Port 6569 verwendet, zum Beispiel navigieren Sie zu http://localhost:6569. Ein Standarddokument nicht konfiguriert ist also ein `HTTP Error 403.14 - Forbidden error` wird erwartet.
4. Bestätigen Sie in Ihrer Firewall die in diesem Thema aufgeführten TCP-Ports geöffnet sind.
5. Sehen Sie sich die Quell- oder Ziel-System:
 - Einige lokale Systeme erfordern zusätzliche Abhängigkeitsdateien. Angenommen, müssen Sie lokale SAP herstellen, einige zusätzlichen SAP-Dateien auf dem IIS-Server installiert werden.
 - Überprüfen Sie die Verbindung mit dem Anmeldekonto an das System. Beispielsweise muss der TCP-Port, der vom System verwendete offen, wie Port 1433 für SQL Server sein. Das Anmeldekonto in Azure-Portal eingegebene benötigen Zugriff auf das System.
6. Überprüfen Sie die Ereignisprotokolle auf Fehler, auf dem IIS-Server. 
7. Bereinigung und Hybrid Connection Manager installieren: 
 - In IIS manuell löschen der Connector-Website und den Anwendungspool. 
 - Führen Sie den Hybrid Connection Manager erneut aus, und vergewissern Sie sich, die richtige Eingabe **primäre Konfigurationszeichenfolge** des Connectors.



### In der Azure-portal

1. Bestätigen der Service Bus-Namespace verfügt über ein **Active** Zustand.
2. Wenn Sie den Connector erstellen, geben Sie die Service Bus-SAS-Verbindungszeichenfolge. Geben Sie die ACS-Verbindungszeichenfolge.


## Häufig gestellte Fragen

**Frage**: Es gibt zwei Hybrid-Verbindungs-Manager. Was ist der Unterschied?<br/> **Antwort**: ist die [Hybridverbindungen](../integration-hybrid-connection-overview.md) Technologie, die zum Herstellen einer lokalen hauptsächlich von Webanwendungen (früher Websites) und Mobile-Anwendungen (früher mobile Services) verwendet wird. Diese Verbindungen Hybrid-Manager ist eine eigene [Setup](../integration-hybrid-connection-create-manage.md) und Azure BizTalk-Dienst (im Hintergrund) verwendet. Nur TCP und HTTP-Protokolle unterstützt.

Mit Azure-Anwendungsdiensts Connectors haben wir auch ein Hybrid Connection Manager. Diese Hybrid Connection Manager hat *nicht* Azure BizTalk-Dienst (im Hintergrund) und unterstützt mehr als TCP und HTTP-Protokolle verwenden. Siehe die [Connectors und API-Apps-Liste](app-service-logic-connectors-list.md).

Beide verwenden Azure Service Bus für die Verbindung zum lokalen System.

**Frage**: beim Erstellen einer benutzerdefinierten API-App kann ich verwenden der App Service Hybrid Connection Manager zum Herstellen einer lokalen? <br/> **Antwort**: nicht im herkömmlichen Sinn. Sie können einen integrierten Anschluss, der App Service Hybrid Connection Manager für die Verbindung zum lokalen System konfigurieren. Anschließend verwenden Sie diesen Connector mit der benutzerdefinierten API-App, möglicherweise mit einer App Logik. Derzeit nicht entwickeln oder erstellen Ihre eigenen Hybrid-API-App (z. B. den SQL-Connector oder den Datei-Connector).

Wenn Ihre benutzerdefinierte API einen TCP- oder HTTP-Port verwendet, können Sie [Hybridverbindungen](../integration-hybrid-connection-overview.md) und seine Hybrid Connection Manager. In diesem Szenario wird ein Azure BizTalk-Dienst verwendet. [Verbinden mit lokalen SQL Server in einer Webanwendung](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) kann hilfreich sein.


## Erfahren Sie mehr

[Überwachen von Anwendungen Logik](app-service-logic-monitor-your-logic-apps.md)<br/> [Service Bus Preise](http://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->