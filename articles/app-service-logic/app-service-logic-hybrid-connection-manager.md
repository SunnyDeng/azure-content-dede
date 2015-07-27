<properties 
	pageTitle="Verwenden des Hybrid Connection Managers für Azure App Service." 
	description="Installieren und konfigurieren Sie den Hybrid Connection Manager in Azure App Service; Microservices-Architektur" 
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
	ms.date="07/08/2015" 
	ms.author="mandia"/>

# Verwenden des Hybrid Connection Managers in Azure App Service.
Einige Connectors können mit einem lokalen System eine Verbindung herstellen, wie SharePoint, SQL Server, SAP usw.. Für die Verwendung eines lokalen Systems, greift Azure App Service auf den Hybrid Connection Manager zurück.

Der Hybrid Connection Manager (HCM) ist ein ClickOnce-Installationsprogramm, das auf einem IIS-Server in Ihrem Netzwerk hinter der Firewall installiert ist. Mithilfe eines Azure Service Bus Relays authentifiziert der HCM das lokale System mit dem Connector in Azure.

> [AZURE.NOTE]Der Hybrid Connection Manager wird nur benötigt, wenn Sie auf eine lokale Ressource hinter der Firewall eine Verbindung herstellen wollen. Wenn Sie keine Verbindung mit einem lokalen System herstellen möchten, benötigen Sie den Hybrid Connection Manager nicht.

Zunächst benötigen Sie Folgendes:

- Azure Service Bus Relay-Namespace SAS-Verbindungszeichenfolge. Unter [Service Bus-Preise](http://azure.microsoft.com/pricing/details/service-bus/) können Sie nachschauen, welcher Tarif Relays umfasst.
- Anmeldeinformationen für das lokale System, einschließlich Benutzername und Kennwort. Wenn Sie z. B. eine Verbindung zu einem lokalen SQL Server herstellen, benötigen Sie das SQL Server-Anmeldekonto und ein Kennwort.
- Lokale Serverinformationen, einschließlich der Port-Nummer und dem Servernamen. Wenn Sie z. B. eine Verbindung zu einem lokalen SQL Server herstellen, benötigen Sie den SQL Server-Namen und eine TCP-Portnummer.

## Abrufen der Service Bus-Verbindungszeichenfolge

Kopieren Sie im Azure-Portal die Stamm-SAS-Verbindungszeichenfolge für Service Bus. Diese Verbindungszeichenfolge verbindet Ihren Azure-Connector mit Ihrem lokalen System.

1. Wählen Sie im [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885) den Service Bus-Namespace und dann die **Verbindungsinformationen**:

	![][SB_ConnectInfo]

2. Kopieren Sie die SAS-Verbindungszeichenfolge:

	![][SB_SAS]

## Den Hybrid Connection Manager installieren

1. Wählen Sie in der [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)-Vorschau den erstellten Connector aus. Um ihn zu öffnen, wählen Sie **Durchsuchen** aus, dann **API-Apps**, und anschließend Ihren Connector oder Ihre API-App. <br/><br/> In der **Hybridverbindung** ist das Setup **unvollständig**: <br/> ![][2] 

2. Wählen Sie **Hybridverbindung** aus. Die zuvor eingegebenen Service Bus-Verbindungszeichenfolge wird aufgeführt.
3. Kopieren Sie die **Primäre Konfigurationszeichenfolge**: <br/> ![][PrimaryConfigString]

4. Unter dem **lokalen Hybrid Connection Manager** können Sie den Hybrid Connection Manager herunterladen oder direkt über das Portal installieren. <br/><br/> Für die Installation direkt vom Portal wechseln Sie auf Ihren lokalen IIS-Server, navigieren Sie zum Portal und wählen Sie **Herunterladen und Konfigurieren**. <br/><br/> Zum Herunterladen des Hybrid Connection Managers wechseln Sie zur Ihrem lokalen IIS-Server, und gehen Sie dann zur **ClickOnce-Anwendung** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). Die Installation wird automatisch gestartet, damit sie ausgeführt werden kann.

5. Geben Sie im Fenster **Listener Setup** die **primäre Konfigurationszeichenfolge** ein, die Sie zuvor eingefügt haben (in Schritt 3), und wählen Sie **Installieren**.

Wenn das Setup abgeschlossen ist, wird Folgendes angezeigt: <br/> ![][3]

Wenn Sie nun erneut den Connector aufrufen, sehen Sie, dass der Hybridverbindungsstatus **Verbunden** lautet. Sie müssen den Connector möglicherweise schließen und erneut öffnen: <br/> ![][4]

> [AZURE.NOTE]Um zur sekundären Verbindungszeichenfolge zu wechseln, führen Sie die Einrichtung der Hybrid-Verbindung erneut aus, und geben Sie die **sekundären Konfigurationszeichenfolge** ein.


## TCP-Ports und Sicherheit

Wenn Sie eine Hybridverbindung erstellen, wird eine Website auf dem lokalen IIS-Server erstellt. Der IIS-Server kann sich in einer DMZ befinden. Die TCP-Port-Anforderungen auf dem IIS-Server umfassen:

TCP-Port | Grund
--- | ---
 | Es sind keine eingehenden TCP-Ports erforderlich.
9350 - 9354 | Diese Ports werden für die Datenübertragung verwendet. Der Service Bus Relay-Manager prüft Port 9350, um festzustellen, ob die TCP-Konnektivität verfügbar ist. Wenn sie verfügbar ist, wird davon ausgegangen, dass Port 9352 ebenfalls verfügbar ist. Der Datenverkehr geht über Port 9352. <br/><br/>Ausgehende Verbindungen auf diese Ports zulassen.
5671 | Wenn Port 9352 für den Datenverkehr verwendet wird, wird Port 5671 als Steuerungskanal verwendet. <br/><br/>Ausgehende Verbindungen auf diesen Port zulassen. 
80, 443 | Wenn Ports 9352 und 5671 nicht brauchbar sind, *dann* sind die Ports 80 und 443 die Alternative für die Datenübertragung und den Steuerungskanal.<br/><br/>Ausgehende Verbindungen auf diese Ports zulassen.
Lokaler System-Port | Öffnen Sie im lokalen System den Port, der vom System verwendet wird. SQL Server verwendet z. B. in der Regel Port 1433. Öffnen Sie diesen TCP-Port.

[Hosten hinter einer Firewall mit Service Bus](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## Problembehandlung

![][HCMFlow]

### Lokale Problembehandlung

1. Bestätigen Sie auf dem IIS-Server, dass die IIS-Webrolle installiert ist und die IIS-Dienste gestartet werden.
2. Bestätigen Sie auf dem IIS-Server, dass der Hybrid Connection Manager installiert ist und ausgeführt wird:
 - Im IIS-Manager (inetmgr) sollte die ***MicrosoftAzureBizTalkHybridListener***-Website aufgeführt sein und ausgeführt werden. 
 - Diese Website verwendet den ***HybridListenerAppPool***, der als lokales, integriertes Benutzerkonto *NetworkService* ausgeführt wird. Dieser AppPool sollte auch gestartet werden.
3. Bestätigen Sie auf dem IIS-Server, dass der Connector installiert ist und ausgeführt wird: 
 - Für Ihren App Service-Connector wird eine Website erstellt. Wenn Sie beispielsweise einen SQL-Connector erstellt haben, gibt es eine ***MicrosoftSqlConnector_nnn***-Website. Bestätigen Sie im IIS-Manager (inetmgr), dass diese Website aufgelistet ist und gestartet wird. 
 - Diese Website verwendet einen eigenen IIS-Anwendungspool mit dem Namen ***HybridAppPoolnnn***. Dieser AppPool wird als lokales, integriertes Benutzerkonto *NetworkService* ausgeführt. Diese Website und der AppPool sollten beide gestartet werden. 
 - Durchsuchen Sie den lokalen Connector. Wenn die Connector-Website z. B. Port 6569 verwendet, navigieren Sie zu http://localhost:6569. Ein Standarddokument ist nicht konfiguriert, also wird ein Fehler `HTTP Error 403.14 - Forbidden error` erwartet.
4. Bestätigen Sie in Ihrer Firewall, dass die in diesem Thema aufgeführten TCP-Ports geöffnet sind.
5. Sehen Sie sich das Quell- oder Ziel-System an:
 - Einige lokale Systeme erfordern zusätzliche Abhängigkeitsdateien. Wenn Sie beispielsweise eine lokale SAP-Verbindung herstellen, müssen einige zusätzliche SAP-Dateien auf dem IIS-Server installiert werden.
 - Überprüfen Sie die Konnektivität zum System mit dem Anmeldekonto. Beispielsweise muss der vom System verwendete TCP-Port offen sein, wie Port 1433 für SQL Server. Das Anmeldekonto, das Sie im Azure-Portal eingegeben haben, benötigt Zugriff auf das System.
6. Überprüfen Sie auf dem IIS-Server die Ereignisprotokolle auf Fehler. 
7. Den Hybrid Connection Manager bereinigen und installieren: 
 - Löschen Sie in IIS manuell die Connector-Website und deren Anwendungspool. 
 - Führen Sie den Hybrid Connection Manager erneut aus, und vergewissern Sie sich, die richtige **primäre Konfigurationszeichenfolge** für Ihren Connector einzugeben.



### Im Azure-Portal

1. Bestätigen Sie, dass der Service Bus-Namespace über im Zustand **Active** ist.
2. Wenn Sie den Connector erstellen, geben Sie die Service Bus-SAS-Verbindungszeichenfolge ein. Geben Sie nicht die ACS-Verbindungszeichenfolge ein.


## Häufig gestellte Fragen

**Frage**: Es gibt zwei Hybrid Connection Manager. Was ist der Unterschied?<br/> **Antwort**: Es gibt die [Hybridverbindungs-](../integration-hybrid-connection-overview.md)Technologie, die hauptsächlich von Web-Apps (früher Websites) und Mobile Apps (früher Mobile Services) zum Herstellen einer lokalen Verbindung verwendet wird. Dieser Hybrid Connection Manager ist ein eigener [Setup](../integration-hybrid-connection-create-manage.md) und verwendet den Azure BizTalk-Dienst (im Hintergrund). Nur TCP und HTTP-Protokolle werden unterstützt.

Mit Azure App Service-Connectors liegt auch ein Hybrid Connection Manager vor. Dieser Hybrid Connection Manager verwendet *keinen* Azure BizTalk-Dienst (im Hintergrund) und unterstützt nicht nur TCP- und HTTP-Protokolle. Informationen finden Sie in der [Liste der Connectors und API-Apps](app-service-logic-connectors-list.md)

Beide verwenden Azure Service Bus für die Verbindung zum lokalen System.

**Frage**: Kann ich beim Erstellen einer benutzerdefinierten API-App den App Service Hybrid Connection Manager zum Herstellen einer lokalen Verbindung nehmen? <br/> **Antwort**: Nicht im herkömmlichen Sinn. Sie können einen integrierten Connector verwenden und den App Service Hybrid Connection Manager so konfigurieren, dass eine Verbindung zum lokalen System hergestellt wird. Anschließend verwenden Sie diesen Connector mit der benutzerdefinierten API-App, möglicherweise mithilfe einer Logik-App. Derzeit können Sie keine eigene hybride API-App entwickeln oder erstellen (wie den SQL-Connector oder den Datei-Connector).

Wenn Ihre benutzerdefinierte API einen TCP- oder HTTP-Port verwendet, können Sie [Hybridverbindungen](../integration-hybrid-connection-overview.md) und den Hybrid Connection Manager verwenden. In diesem Szenario wird ein Azure BizTalk-Dienst verwendet. [Verbinden mit einem lokalen SQL Server von einer Web-App aus](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) könnte helfen.


## Weitere Informationen

[Überwachen Ihrer Logik-Apps](app-service-logic-monitor-your-logic-apps.md)<br/> [Service Bus-Preise](http://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=July15_HO3-->