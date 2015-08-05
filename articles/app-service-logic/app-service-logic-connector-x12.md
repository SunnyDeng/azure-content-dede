<properties 
   pageTitle="BizTalk X12-Connector" 
   description="BizTalk X12-Connector" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/14/2015"
   ms.author="rajram"/>

#BizTalk X12-Connector
Der Microsoft Azure X12-Dienst kann bei der Business-to-Business-Kommunikation Nachrichten über das X12-Protokoll empfangen und senden. X12 wird häufig auch als "ASC X12" oder "Accredited Standards Committee X12" bezeichnet. Der Dienst wird branchenübergreifend verwendet.

##Voraussetzungen
- TPM-API-App: Bevor Sie einen X12-Connector erstellen, müssen Sie einen [BizTalk Trading Partner Management-Connector][1] erstellen (Trading Partner Management = Handelspartnervereinbarung).
- SQL Azure-Datenbank: Jede der B2B-API-Apps erfordert eine eigene Azure-SQL-Datenbank.
- Azure Service Bus: Optional, wird nur bei der Batchverarbeitung verwendet.

##Verwenden des BizTalk X12-Connectors
Um den BizTalk X12-Connector zu verwenden, müssen Sie zunächst eine Instanz der BizTalk X12-Connector-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk X12-Connector-API-App aus dem Azure Marketplace erfolgen.

##Konfigurieren des BizTalk X12-Connectors
Handelspartner sind die Entitäten, die an einer B2B-Kommunikation (Business-to-Business) beteiligt sind. Wenn zwei Partner eine Beziehung eingehen, wird dies als "Vereinbarung" bezeichnet. Diese Vereinbarung basiert auf der Kommunikation, die beide Partner wünschen, und ist protokoll- oder transportspezifisch.

Die Schritte zum Erstellen einer Handelspartnervereinbarung sind [hier][2] dokumentiert.

##Verwenden des X12-Connectors auf der Designeroberfläche von Logik-Apps
Der X12-Connector kann als Trigger oder als Aktion verwendet werden.

###Trigger
- Starten Sie den Azure-Datenfluss-Designer für Logik-Apps.
- Klicken Sie im rechten Bereich auf den X12-Connector.

	![Triggereinstellungen][3]
- Klicken Sie auf ->

	![Triggeroptionen][4]
- Der BizTalk X12-Connector stellt einen einzelnen Trigger bereit. Wählen Sie *Batch veröffentlichen*.

	![Eingabe für Release Batch][5]
- Dieser Trigger weist keine Eingaben auf. Klicken Sie auf ->

	![Release Batch konfiguriert][6]
- Im Rahmen der Ausgabe gibt der Connector die X12-Nutzlast, die Vereinbarungs-ID sowie Informationen dazu zurück, ob die Nachricht als Batch ausgeführt wird.

###Aktion
- Klicken Sie im rechten Bereich auf den X12-Connector.

	![Aktionseinstellungen][7]
- Klicken Sie auf ->

	![Liste der Aktionen][8]
- Der X12-Connector unterstützt zahlreiche Aktionen. Wählen Sie *Codieren* aus.

	![Eingabe codieren][9]
- Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie.

	![Konfigurierte Elemente codieren][10]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Inhalt</td>
		<td>Zeichenfolge</td>
		<td>XML-Nachricht</td>
	</tr>
	<tr>
		<td>Vereinbarungs-ID</td>
		<td>Zeichenfolge</td>
		<td>Vereinbarungs-ID</td>
	</tr>
	<tr>
		<td>Batchnachricht</td>
		<td>Zeichenfolge</td>
		<td>Batchnachricht</td>
	</tr>
</table>

Die Aktion gibt ein Objekt mit der X12-Nutzlast zurück.

## Optimale Nutzung Ihres Connectors
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

<!---HONumber=July15_HO4-->