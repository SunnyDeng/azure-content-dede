<properties 
   pageTitle="AS2-Connector" 
   description="AS2-Connector" 
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

#AS2-Connector
Der Microsoft Azure AS2-Connector kann in der Business-to-Business-Kommunikation Nachrichten über das AS2-Transport-Protokoll empfangen und senden. AS2 steht für „Applicability Statement 2“ (Anwendbarkeitsanweisung 2). Daten werden sicher und zuverlässig über das Internet übertragen. Die Sicherheit wird mithilfe von digitalen Zertifikaten und Verschlüsselung gewährleistet.

##Voraussetzungen
- TPM-API-App: Bevor Sie einen AS2-Connector erstellen, müssen Sie einen [BizTalk-Handelspartnerverwaltungs-Connector][1] erstellen.
- SQL Azure-Datenbank: Jede der B2B-API-Apps erfordert eine eigene Azure-SQL-Datenbank.
- Azure-Blob-Speichercontainer: Speichert Nachrichteneigenschaften, wenn die AS2-Archivierung aktiviert ist. Wenn Sie die AS2-Nachrichtenarchivierung nicht benötigen, ist kein Speichercontainer erforderlich. 

##Verwenden des AS2-Connectors
Zur Verwendung des AS2-Connectors müssen Sie zunächst eine Instanz der AS2-Connector-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der AS2-Connector-API-App aus dem Azure Marketplace erfolgen.

##Konfigurieren des AS2-Connectors
Handelspartner sind die Entitäten, die an einer B2B (Business-to-Business)-Kommunikation beteiligt sind. Wenn zwei Partner eine Beziehung eingehen, wird dies als „Vereinbarung“ bezeichnet. Diese Vereinbarung basiert auf der Kommunikation, die beide Partner wünschen, und ist protokoll- oder transportspezifisch.

Die Schritte zum Erstellen eines Handelspartnervertrags sind [hier][2] dokumentiert.

##Verwenden des AS2-Connectors auf der Designeroberfläche von Logik-Apps
Der AS2-Connector kann als Trigger oder als Aktion verwendet werden.

###Trigger
- Starten Sie den Azure-Datenfluss-Designer für Logik-Apps.
- Klicken Sie im rechten Bereich auf den AS2-Connector.

	![Triggereinstellungen][3]
- Klicken Sie auf ->

	![Triggeroptionen][4]
- Der AS2-Connector stellt einen einzelnen Trigger bereit. Wählen Sie *Empfangen und decodieren* aus.

	![Eingaben empfangen und decodieren][5]
- Dieser Trigger weist keine Eingaben auf. Klicken Sie auf ->

	![Konfigurierte Elemente empfangen und decodieren][6]
- Als Teil der Ausgabe gibt der Connector die AS2-Nutzlast sowie AS2-spezifische Metadaten zurück.

###Aktion
- Klicken Sie im rechten Bereich auf den AS2-Connector.

	![Aktionseinstellungen][7]
- Klicken Sie auf ->

	![Liste der Aktionen][8]
- Der AS2-Connector unterstützt nur eine Aktion. Wählen Sie *Codieren und senden* aus.

	![Eingaben codieren und senden][9]
- Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie.

	![Konfigurierte Elemente codieren und senden][10]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Nutzlast</td>
		<td>Objekt</td>
		<td>Nutzlast</td>
	</tr>
	<tr>
		<td>AS2-From</td>
		<td>Zeichenfolge</td>
		<td>AS2-From</td>
	</tr>
	<tr>
		<td>AS2-To</td>
		<td>Zeichenfolge</td>
		<td>AS2-To</td>
	</tr>
	<tr>
		<td>Partner-URL</td>
		<td>Zeichenfolge</td>
		<td>Partner-URL</td>
	</tr>
	<tr>
		<td>Archivierung aktivieren</td>
		<td>Boolescher Wert</td>
		<td>Archivierung aktivieren</td>
	</tr>
</table>

Die Aktion gibt bei erfolgreichem Abschluss einen HTTP 200-Antwortcode zurück.

## Optimale Nutzung Ihres Connectors
Nachdem Sie den Connector erstellt haben, können Sie ihn mit einer Logik-App einem Unternehmensdatenfluss hinzufügen. Weitere Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit für den Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->