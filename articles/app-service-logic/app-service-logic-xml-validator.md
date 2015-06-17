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
   ms.date="03/20/2015"
   ms.author="rajram"/>

# BizTalk XML Validator

Connector BizTalk XML überprüfen kann Ihre app, die XML-Daten mit vordefinierten XML-Schemas zu überprüfen. Benutzer können entweder vorhandene Schemas oder Generieren von Schemas, die außerhalb einer Flatfile-Instanz, die JSON-Instanz oder vorhandener Connectors.

##Verwenden die BizTalk-XML-Bestätigung
1. Um den XML-Validator BizTalk zu verwenden, müssen Sie zunächst eine Instanz in der BizTalk-XML-Validator-API erstellen. Dies kann entweder Inline beim Erstellen einer app Logik oder durch Auswählen der BizTalk-XML-Validator-API-app aus dem Azure Marketplace erfolgen.

###Konfigurieren der BizTalk-XML-Bestätigung
BizTalk-XML-Validator nimmt Schemas als Teil seiner Konfiguration. Benutzer kann die API-App-Konfiguration-Blade starten, indem Sie entweder das Starten der App-API direkt von der Azure-Portal oder über durch Doppelklicken auf die App-API auf der Designeroberfläche.

![BizTalk-XML-Validator-Konfiguration][1]

In der API-App-Blade, kann Benutzer durch Klicken auf Schemas konfigurieren *Schemas* Teil

![BizTalk-XML-Validator Schemas Teil][2]

Benutzer können Schemas von der Festplatte hochladen oder sie aus einer Flatfile-Instanz oder einer JSON-Instanz generieren.

![Bestätigung für die BizTalk-Schemas][3]


###Verwenden des BizTalk Flat File Encoder in der Entwurfsoberfläche
Nach der Konfiguration können Benutzer auf klicken *->* und wählen Sie eine Aktion aus einer Liste von Aktionen.

![BizTalk-XML-Validator-Liste der Aktionen][4]

####Überprüfen von Xml

XML-Aktion überprüfen, ob eine angegebene Xml-Eingabe auf vorkonfigurierte Schemas zu überprüfen.

![BizTalk-XML-Bestätigung validieren Xml][5]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Eingabe-XML</td>
		<td>string</td>
		<td>Der zu überprüfende XML-Eingabedatei</td>
	</tr>
</table>


Die Aktion wird die Ausgabe als Objekt. Ausgabe enthält das Modell, die Antwort des XML-Bestätigung darstellt. Er besteht aus Ergebnis, Schemaname, Root-Knoten und Fehler Beschreibung.

![6]

<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG
[6]: ./media/app-service-logic-xml-validator/img1.PNG
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->