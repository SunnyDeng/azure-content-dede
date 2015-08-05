<properties
   pageTitle="BizTalk JSON Encoder"
   description="BizTalk JSON Encoder"
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

#BizTalk JSON Encoder
Der BizTalk JSON Encode Decode-Connector unterstützt Ihre App in Bezug auf die Interoperabilität von JSON- und XML-Daten. Sie können damit eine angegebene JSON-Instanz in XML konvertieren und umgekehrt.

##Verwenden des BizTalk JSON Encoder
Um den BizTalk JSON Encoder zu verwenden, müssen Sie zunächst eine Instanz der BizTalk JSON Encoder-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk JSON-Encoder-API-App aus dem Azure Marketplace erfolgen.

##Verwenden von BizTalk JSON Encoder auf der Designeroberfläche von Logik-Apps
Führen Sie die Schritte zum [Erstellen einer Logik-App] aus. Der BizTalk JSON Encoder kann als Aktion verwendet werden. Er besitzt keine Trigger.

###Aktion
- Klicken Sie im rechten Bereich auf BizTalk JSON Encoder.

	![Aktionseinstellungen][3]
- Klicken Sie auf ->

	![Liste der Aktionen][4]
- Der BizTalk JSON Encoder unterstützt zwei Aktionen. Wählen Sie *XML zu JSON*.

	![Eingabe "XML zu JSON"][5]
- Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie.

	![Konfigurierte Elemente codieren und senden][6]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Eingabe-XML</td>
		<td>Objekt</td>
		<td>Eingabe-XML-Inhalt</td>
	</tr>
	<tr>
		<td>Äußeren Umschlag entfernen</td>
		<td>Zeichenfolge</td>
		<td>Flag ist auf das Entfernen des Root-Knotens aus dem XML-Inhalt festgelegt</td>
	</tr>
</table>


Die Aktion gibt eine JSON-Darstellung des eingegebenen Inhalts zurück.

## Optimale Nutzung Ihres Connectors
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Erstellen einer Logik-App]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO4-->