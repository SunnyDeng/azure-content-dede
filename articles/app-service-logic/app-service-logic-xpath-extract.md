<properties 
   pageTitle="BizTalk XPath Extractor" 
   description="BizTalk XPath Extractor" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
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

#BizTalk XPath Extractor

Mit dem BizTalk XPath Extract-Connector kann Ihre App Daten aus dem XML-Inhalt suchen und basierend auf einem bestimmten XPath extrahieren.

##Verwenden von BizTalk XPath Extractor
1. Um den BizTalk Xpath Extractor zu verwenden, müssen Sie zunächst eine Instanz der BizTalk Xpath Extractor-API-App erstellen. Dies kann entweder Inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk Xpath Extractor-API-App aus dem Azure Marketplace erfolgen.

		NOTE: There are no configuration settings associated with BizTalk Xpath Extractor.
2. Der Designer zeigt die Aktion, die der BizTalk XPath Extractor-API-App zugeordnet sind.
	
![BizTalk XPath Extractor Aktion auswählen][1]

3. Wählen Sie "Mit XPath extrahieren".

"Mit XPath extrahieren" wertet den Eingabe-XPath-Ausdruck auf eine bestimmte Eingabe-XML aus.

![BizTalk XPath Extractor Eingabe][2]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>string</td>
		<td>Abfragepfad in XML</td>
	</tr>
	<tr>
		<td>Eingabe-XML</td>
		<td>string</td>
		<td>Eingabe-XML-Inhalt</td>
	</tr>
</table>

Die Aktion gibt die Ausgabe als Zeichenfolge zurück - Ergebnis. Das Ergebnis enthält den Wert der Abfragepfad in XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->