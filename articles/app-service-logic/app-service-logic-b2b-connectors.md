<properties 
	pageTitle="Business-to-Business-Connectors und API-Apps in Microsoft Azure App Service | Azure" 
	description="Erfahren Sie mehr über das Erstellen und Konfigurieren von EDI-, EDIFACT-, AS2- und TPM-Connectors; Microservices-Architektur" 
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
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Business-to-Business-Connectors und API-Apps in Microsoft Azure App Service
Microsoft Azure App Service (oder kurz App Service) bietet viele BizTalk-API-Apps, die für die Integration von Umgebungen wichtig sind. Diese API-Apps basieren auf Konzepten und Tools, die in BizTalk Server verwendet werden, aber nun als Teil von Azure App Service verfügbar sind.

Eine Kategorie dieser API-Apps sind die Business-to-Business-API-Apps (B2B). Mithilfe dieser B2B-API-Apps können Sie problemlos Partner hinzufügen, Vereinbarungen erstellen und mit EDI, AS2 und EDIFACT alle Vorgänge genauso durchführen, als wären sie lokal.

Diese B2B-API-Apps bieten Funktionen für "Trigger" und "Aktion". Ein Trigger startet eine neue Instanz basierend auf einem bestimmten Ereignis wie dem Eingang einer X12-Nachricht von einem Partner. Eine Aktion ist das Ergebnis. Nach dem Empfang einer X12-Nachricht wird die Nachricht beispielsweise mit AS2 gesendet.


## Was sind Business-to-Business-Connectors oder API-Apps?
Die Business-to-Business-Funktion (B2B) umfasst vorhandene, vorgefertigte API-Apps, mit denen verschiedene Unternehmen, Abteilungen, Anwendungen etc. über EDI, AS2 und EDIFACT kommunizieren können.

Zu den B2B-API-Apps gehören:

Connector oder API-Apps | Beschreibung
--- | ---
BizTalk Trading Partner Management | Eine API-App, die B2B-Beziehungen mit Partnern und Vereinbarungen erstellt. Diese Beziehungen nutzen das AS2-, EDIFACT- und X12-Protokoll.<br/><br/>Die TPM-API-App ist die Basisanforderung des AS2-Connectors und der X12- oder EDIFACT-API-Apps. 
AS2-Connector | Ein Connector, der Nachrichten über den AS2-Transport empfängt und sendet. Der Connector übermittelt Daten sicher und zuverlässig über das Internet.
BizTalk EDIFACT | Eine API-App, die Nachrichten über EDIFACT empfängt und sendet. EDIFACT wird häufig auch als UN/EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport) bezeichnet und ist branchenweit verbreitet.
BizTalk X12 | Eine API-App, die Nachrichten über das X12-Protokoll empfängt und sendet. X12 wird häufig auch als ASC X12 (Accredited Standards Committee X12) bezeichnet und ist branchenweit verbreitet. 


Mithilfe dieser API-Apps können Sie verschiedene EDI-Messagingaufgaben meistern. Beispielsweise können Sie mit dem AS2-Connector verschiedene Arten von Nachrichten (EDI, XML, Flatfile und so weiter) empfangen und an einen Kunden, eine Abteilung in Ihrem Unternehmen (z. B. die Personalabteilung) oder an einen beliebigen Benutzer von AS2 senden.

Sie können beliebig viele API-Apps ohne großen Aufwand erstellen. Außerdem können Sie eine einzige API-App in mehreren Szenarien oder Workflows wiederverwenden.

Dazu müssen Sie keinen Code schreiben. Lassen Sie uns anfangen.


## Anforderungen für den Einstieg
Bei der Erstellung von B2B-API-Apps sind einige Ressourcen erforderlich. Diese Elemente müssen von Ihnen erstellt werden, bevor sie in anderen API-Apps verwendet werden können. Diese Anforderungen umfassen u. a.:

Voraussetzung | Beschreibung
--- | ---
Azure SQL-Datenbank | Speichert B2B-Elemente, einschließlich Partner, Schemas, Zertifikate und Vereinbarungen. Jede der B2B-API-Apps erfordert eine eigene Azure-SQL-Datenbank. <br/><br/>**Hinweis** Kopieren Sie die Verbindungszeichenfolge in diese Datenbank.<br/><br/>[Erstellen einer Azure SQL-Datenbank](../sql-database-create-configure.md)
Azure-Blobspeichercontainer | Speichert Nachrichteneigenschaften, wenn die AS2-Archivierung aktiviert ist. Wenn Sie die AS2-Nachrichtenarchivierung nicht benötigen, ist kein Speichercontainer erforderlich. <br/><br/>**Hinweis** Wenn Sie die Archivierung aktivieren, kopieren Sie die Verbindungszeichenfolge in diesen Blobspeicher.<br/><br/>[Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md)
Service Bus-Namespace und seine Schlüsselwerte | Speichert X12- und EDIFACT-Batchverarbeitungsdaten. Wenn Sie die Batchverarbeitung nicht benötigen, ist kein Service Bus-Namespace erforderlich.<br/><br/>**Hinweis** Wenn Sie die Batchverarbeitung aktivieren, kopieren Sie diese Werte.<br/><br/>[Erstellen eines Service Bus-Namespace](http://msdn.microsoft.com/library/azure/hh690931.aspx)
TPM-Instanz | Eine BizTalk TPM-Instanz (Trading Partner Management) ist erforderlich, um einen AS2-Connector und eine X12- oder EDIFACT-API-App zu erstellen. Beim Erstellen der TPM-API-App erstellen Sie die TPM-Instanz. <br/><br/>**Hinweis** Halten Sie den Namen Ihrer TPM-API-App bereit. 


## Erstellen der API-Apps
B2B-API-Apps können im Azure-Portal oder mithilfe von REST-APIs erstellt werden.


### Erstellen der API-Apps mithilfe von REST-APIs
[Informationen zur Verwendung der REST-APIs finden Sie in der Dokumentation.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Erstellen der B2B-API-Apps im Azure-Portal
Im Azure-Portal können Sie B2B-API-Apps beim Erstellen der Logik-Apps, Web-Apps oder mobilen Apps erstellen. Sie können sie auch auf ihrem eigenen Blatt erstellen. Beide Möglichkeiten sind einfach, weshalb der Vorgang von Ihren Anforderungen oder Einstellungen abhängt. Es gibt Benutzer, die lieber zuerst alle B2B-API-Apps mit ihren bestimmten Eigenschaften erstellen. Anschließend erstellen Sie die Logik-, Web- oder mobilen Apps und fügen die B2B-API-Apps hinzu, die Sie erstellt haben.

Über die folgenden Schritte erstellen Sie B2B-API-Apps auf dem Blatt "API-Apps":


#### Erstellen der BizTalk TPM-API-Apps (Trading Partner Management)

> [AZURE.NOTE]Eine BizTalk TPM-Instanz (Trading Partner Management) ist erforderlich, um einen AS2-Connector und eine X12- oder EDIFACT-API-App zu erstellen. Beim Erstellen der TPM-API-App erstellen Sie die TPM-Instanz.

Die TPM-Instanz wird mithilfe der folgenden Schritte erstellt:

1. Wählen Sie im Azure-Portal im Startmenü (auf der Startseite) **Marketplace** aus. **API-Apps** zeigt alle vorhandenen API-Apps und Connectors. Sie können die betreffenden B2B-API-Apps auch **suchen**.
2. Wählen Sie **BizTalk Trading Partner Management** aus. Wählen Sie im neuen Blatt **Erstellen** aus. 
3. Geben Sie die Eigenschaften ein: 

	Eigenschaft | Beschreibung
--- | ---
Name | Geben Sie einen Namen für die TPM-Instanz ein. Sie können sie z. B. *AccountsPayableTPM* nennen.
Paketeinstellungen | Geben Sie die ADO.NET-**Datenbankverbindungszeichenfolge** zur Azure-SQL-Datenbank ein, die Sie erstellt haben. <br/><br/>Wenn Sie die Verbindungszeichenfolge kopieren, wird das Kennwort nicht der Verbindungszeichenfolge hinzugefügt. Achten Sie darauf, dass das Kennwort in der Verbindungszeichenfolge eingegeben wird.
App Service-Plan | Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.
Preisstufe | Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet. 
Ressourcengruppe | Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Alle API-Apps und Connectors für Ihre Logik- Apps, Web-Apps und mobilen Apps müssen in derselben Ressourcengruppe enthalten sein. <br/><br/>Unter [Verwenden von Ressourcengruppen](../resource-group-overview.md) wird diese Eigenschaft erläutert. 
Abonnement | Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.
Standort | Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. 
Zum Startmenü hinzufügen | Wählen Sie diese Option aus, um die B2B-API-App Ihrem Startmenü (der Startseite) hinzufügen.

4. Klicken Sie auf **Erstellen**.

Nachdem die TPM-API-App (TPM-Instanz) erstellt wurde, können Sie den AS2-Connector und/oder die X12- oder EDIFACT-API-Apps erstellen.


#### Erstellen des AS2-Connectors

1. Wählen Sie im Azure-Portal im Startmenü (auf der Startseite) **Marketplace** aus. **API-Apps** zeigt alle vorhandenen API-Apps und Connectors. Sie können die betreffenden B2B-API-Apps auch **suchen**.
2. Wählen Sie **AS2-Connector** aus. Wählen Sie im neuen Blatt **Erstellen** aus. 
3. Geben Sie die Eigenschaften ein: 

	Eigenschaft | Beschreibung
--- | ---
Name | Geben Sie einen Namen für den AS2-Connector ein. Sie können ihn z. B. *AS2Connector* nennen.
Paketeinstellungen | Geben Sie die für diese API-App spezifischen Einstellungen wie den Namen der TPM-Instanz ein. <br/><br/>Unter [Hinzufügen von AS2-Paketeinstellungen](#AddAS2Conn) in diesem Thema finden Sie Informationen zu den einzelnen Eigenschaften. 
App Service-Plan | Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.
Preisstufe | Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet. 
Ressourcengruppe | Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Unter [Verwenden von Ressourcengruppen](../resource-group-overview.md) wird diese Eigenschaft erläutert. 
Abonnement | Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.
Standort | Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. 
Zum Startmenü hinzufügen | Wählen Sie diese Option aus, um die B2B-API-App Ihrem Startmenü (der Startseite) hinzufügen.

	**<a name="AddAS2Conn"></a>AS2-Connector-Paketeinstellungen**

	Eigenschaft | Beschreibung
--- | --- 
Datenbankverbindungszeichenfolge | Geben Sie die ADO.NET-Verbindungszeichenfolge zur Azure-SQL-Datenbank ein, die Sie erstellt haben. Wenn Sie die Verbindungszeichenfolge kopieren, wird das Kennwort nicht der Verbindungszeichenfolge hinzugefügt. Achten Sie vor dem Einfügen darauf, dass das Kennwort in der Verbindungszeichenfolge eingegeben wird.
Aktivieren der Archivierung für eingehende Nachrichten | Optional. Aktivieren Sie diese Eigenschaft, damit Nachrichteneigenschaften einer eingehenden AS2-Nachricht gespeichert werden, die von einem Partner empfangen wird. 
Verbindungszeichenfolge zum Azure-Blobspeicher | Geben Sie die Verbindungszeichenfolge zum Azure-Blobspeichercontainer ein, den Sie erstellt haben. Wenn die Archivierung aktiviert ist, werden die codierten und decodierten Nachrichten in diesem Speichercontainer gespeichert.
TPM-Instanzname | Geben Sie den Namen der API-App **BizTalk Trading Partner Management** ein, die Sie zuvor erstellt haben. Wenn Sie den AS2-Connector erstellen, führt dieser Connector nur die AS2-Vereinbarungen innerhalb dieser spezifischen TPM-Instanz aus.

4. Klicken Sie auf **Erstellen**.


#### Erstellen der X12- oder EDIFACT-API-Apps

1. Wählen Sie im Azure-Portal im Startmenü (auf der Startseite) **Marketplace** aus. **API-Apps** zeigt alle vorhandenen API-Apps und Connectors. Sie können die betreffenden B2B-API-Apps auch **suchen**.
2. Wählen Sie **BizTalk X12** oder **BizTalk EDIFACT** aus. Wählen Sie im neuen Blatt **Erstellen** aus. 
3. Geben Sie die Eigenschaften ein: 

	Eigenschaft | Beschreibung
--- | ---
Name | Geben Sie einen Namen für die B2B-API-App ein. Sie können sie z. B. *EDI850APIApp* nennen.
Paketeinstellungen | Geben Sie die für diese API-App spezifischen Einstellungen wie den Namen der TPM-Instanz ein. <br/><br/>Unter [X12- oder EDIFACT-Paketeinstellungen](#AddX12) in diesem Thema finden Sie Informationen zu den einzelnen Eigenschaften. 
App Service-Plan | Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.
Preisstufe | Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet. 
Ressourcengruppe | Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Unter [Verwenden von Ressourcengruppen](../resource-group-overview.md) wird diese Eigenschaft erläutert. 
Abonnement | Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.
Standort | Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. 
Zum Startmenü hinzufügen | Wählen Sie diese Option aus, um die B2B-API-App Ihrem Startmenü (der Startseite) hinzufügen.

	**<a name="AddX12"></a>Paketeinstellungen von X12- und EDIFACT-API-Apps**

	Eigenschaft | Beschreibung
--- | --- 
Datenbankverbindungszeichenfolge | Geben Sie die ADO.NET-Verbindungszeichenfolge zur Azure-SQL-Datenbank ein, die Sie erstellt haben. Wenn Sie die Verbindungszeichenfolge kopieren, wird das Kennwort nicht der Verbindungszeichenfolge hinzugefügt. Achten Sie vor dem Einfügen darauf, dass das Kennwort in der Verbindungszeichenfolge eingegeben wird.
Service Bus- Namespace | Geben Sie den von Ihnen erstellten Service Bus-Namespace ein. Dies ist nur erforderlich, wenn die Batchverarbeitung aktiviert ist. 
Shared Access-Schlüsselname des Service Bus-Namespace | Geben Sie den von Ihnen erstellten Zugriffsschlüssel für den Service Bus-Namespace ein. Dies ist nur erforderlich, wenn die Batchverarbeitung aktiviert ist. 
Shared Access-Schlüsselwert für den Service Bus-Namespace | Geben Sie den von Ihnen erstellten Zugriffsschlüsselwert für den Service Bus-Namespace ein. Dies ist nur erforderlich, wenn die Batchverarbeitung aktiviert ist. 
TPM-Instanzname | Geben Sie den Namen der API-App **BizTalk Trading Partner Management** ein, die Sie zuvor erstellt haben. Wenn Sie die X12- oder EDIFACT-API-Apps erstellen, führt diese API-App nur die X12/EDIFACT-Vereinbarungen innerhalb dieser spezifischen TPM-Instanz aus.

4. Klicken Sie auf **Erstellen**.


## Hinzufügen Ihrer Partner, Vereinbarungen, Zertifikate und Schemas 
Öffnen Sie im Azure-Verwaltungsportal Ihre TPM-API-App. Fügen Sie im Abschnitt **Komponenten** Ihre Partner, Vereinbarungen, Zertifikate und Schemas hinzu.

Sie können Ihren AS2-Connectors, X12-API-Apps und EDIFACT-API-Apps auch Vereinbarungen hinzufügen.


## Überwachen Ihrer API-Apps
Öffnen Sie im Azure-Verwaltungsportal Ihre TPM-API-App. Im Abschnitt **Vorgänge** können Sie verschiedene Verwaltungsvorgänge anzeigen. Dazu zählen z. B.:

- Anzeigen von Informations- und Fehlerereignissen
- Anzeigen der Speicherauslastung und Threadanzahl des Arbeitsprozesses (w3wp)
- Anzeigen des Anwendungs- und Webserverprotokolls

Unter [Überwachen von Logik-Apps](app-service-logic-monitor-your-logic-apps.md) finden Sie weitere Informationen.


## Hinzufügen der API-Apps zu Ihrer Anwendung 
Microsoft Azure App Service stellt verschiedene Anwendungstypen zur Verfügung, die diese B2B-API-Apps verwenden können. Sie können neue erstellen oder vorhandene B2B-API-Apps den Logik-Apps, mobilen Apps oder Web-Apps hinzufügen.

Wenn Sie in Ihrer App die B2B-API-Apps aus dem Katalog auswählen, werden sie automatisch Ihrer App hinzugefügt.

> [AZURE.IMPORTANT]Zum Hinzufügen von Connectors und API-Apps, die Sie zuvor erstellt haben, erstellen Sie die Logik-Apps, mobilen Apps oder Web-Apps in derselben Ressourcengruppe.

Über die folgenden Schritte werden B2B-API-Apps Logik-Apps, mobilen Apps oder Web-Apps hinzugefügt:

1. Wechseln Sie im Azure-Portal im Startmenü (auf der Startseite) zu **Marketplace**, und suchen Sie Ihre Logik-, mobile oder Web-App. 

	Wenn Sie eine neue Anwendung erstellen, suchen Sie nach "Logik-App", "Mobile App" oder "Web-App". Wählen Sie die App im neuen Blatt aus, und klicken Sie auf **Erstellen**. Unter [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) werden die Schritte erläutert.

2. Öffnen Sie Ihre App, und wählen Sie **Trigger und Aktionen** aus.

3. Fügen Sie über den **Katalog** die von Ihnen erstellte B2B-API-App hinzu, die automatisch Ihrer App hinzugefügt wird. Sie können auch eine neue B2B-API-App erstellen.

	> [AZURE.IMPORTANT]Der AS2-Connector und die X12- und EDIFACT-API-Apps benötigen eine TPM-Instanz. Wenn Sie daher neue B2B-API-Apps erstellen, erstellen Sie zuerst die TPM-API-App und dann den AS2-Connector, die X12-API-App oder die EDIFACT-API-App.

4. Klicken Sie zum Speichern der Änderungen auf **OK**.


## Weitere B2B-Ressourcen

[Erstellen eines B2B-Prozesses](app-service-logic-create-a-b2b-process.md)<br/> [Erstellen einer Handelspartnervereinbarung](app-service-logic-create-a-trading-partner-agreement.md)<br/> [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)


## Weitere Informationen zu Logik- und Web-Apps
[Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md)<br/> [Websites und Web-Apps in Azure App Service](../app-service-web/app-service-web-overview.md)


## Weitere Connectors
[BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md)<br/> [Enterprise-Connectors](app-service-logic-enterprise-connectors.md)<br/> [Connectors für soziale Netzwerke](app-service-logic-social-connectors.md)<br/> [Protokollconnectors](app-service-logic-protocol-connectors.md)<br/> [App und Data Services-Connectors](app-service-logic-data-connectors.md)<br/> [Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md)<br/><br/> [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=62-->