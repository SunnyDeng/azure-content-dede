<properties
   pageTitle="BizTalk XML Validator"
   description="BizTalk XML Validator"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/01/2015"
   ms.author="rajram"/>

# BizTalk XML Validator

Der BizTalk XML Validate-Connector hilft Ihrer App bei der Validierung von XML-Daten gegen vordefinierte XML-Schemas. Benutzer können entweder vorhandene Schemas verwenden oder Schemas auf Grundlage einer Flatfile-Instanz, JSON-Instanz oder vorhandenen Connectors generieren.

##BizTalk XML Validator verwenden
1. Um den BizTalk XML Validator zu verwenden, müssen Sie zunächst eine Instanz der BizTalk XML Validator-API-App erstellen. Dies kann entweder Inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk XML Validator-API-App aus dem Azure Marketplace erfolgen.

###BizTalk XML Validator konfigurieren
BizTalk XML Validator verwendet Schemas als Teil der Konfiguration. Benutzer können das API-App-Konfigurationsblatt entweder durch Starten der API-App direkt aus dem Azure-Portal oder durch Doppelklicken auf die API-App auf der Designeroberfläche starten.

![BizTalk XML Validator Konfiguration][1]

Benutzer können im API-App-Blatt Schemas konfigurieren, indem Sie auf den Teil *Schemas* klicken.

![BizTalk XML Validator Schemas Teil][2]

Benutzer können Schemas von der Festplatte hochladen oder sie aus einer Flatfile-Instanz oder einer JSON-Instanz generieren.

![BizTalk XML Validator Schemas][3]


###Verwenden des BizTalk Flat File Encoder in der Entwurfsoberfläche
Nach der Konfiguration können Benutzer auf *->* klicken, und aus der Liste der Aktionen eine Aktion auswählen.

![BizTalk XML Validator Liste der Aktionen][4]

####XML validieren

Die Aktion "XML validieren" validiert eine vorhandene XML-Eingabe gegen vorkonfigurierte Schemas.

![BizTalk XML Validator XML validieren][5]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Eingabe-XML</td>
		<td>Zeichenfolge</td>
		<td>Zu validierende XML</td>
	</tr>
</table>


Die Aktion gibt die Ausgabe als Objekt zurück. Die Ausgabe enthält das Modell, das die Antwort vom XML Validator darstellt. Es besteht aus Ergebnis, Namen des Schemas, Stammknoten und Fehlerbeschreibung.

![6]

<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG
[6]: ./media/app-service-logic-xml-validator/img1.PNG
 

<!---HONumber=July15_HO4-->