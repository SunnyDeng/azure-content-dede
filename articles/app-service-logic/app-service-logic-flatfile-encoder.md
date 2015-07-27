<properties 
   pageTitle="BizTalk Flat File Encoder" 
   description="BizTalk Flat File Encoder" 
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

# BizTalk Flat File Encoder

Der BizTalk Flat File Encode Decode-Connector hilft Ihrer App bei der Interoperation zwischen Flatfile-Daten (Beispiel-Excel, CSV) und XML-Daten Sie können damit eine Flatfile-Instanz in XML konvertieren und umgekehrt.

##Verwenden des BizTalk Flat File Encoder
1. Um den BizTalk Flat File Encoder zu verwenden, müssen Sie zunächst eine Instanz der BizTalk Flat File Encoder-API-App erstellen. Dies kann entweder Inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk Flat File-Encoder-API-App aus dem Azure Marketplace erfolgen.

###Konfigurieren des BizTalk Flat File Encoder
BizTalk Flat File Encoder verwendet Schemas als Teil der Konfiguration. Benutzer können das API-App-Konfigurationsblatt entweder durch Starten der API-App direkt aus dem Azure-Portal oder durch Doppelklicken auf die API-App auf der Designeroberfläche starten.

![Konfiguration des BizTalk Flat File Encoder][1]

Benutzer können im API-App-Blatt Schemas konfigurieren, indem Sie auf den Teil *Schemas* klicken.

![BizTalk Flat File Encoder Schemas Teil][2]

Benutzer können Schemas von der Festplatte hochladen oder sie aus einer Flatfile-Instanz oder einer JSON-Instanz generieren.

![BizTalk Flat File Encoder Schemas Teil][3]


###Verwenden des BizTalk Flat File Encoder in der Entwurfsoberfläche
Nach der Konfiguration können Benutzer auf *->* klicken, und wählen Sie aus der Liste der Aktionen eine Aktion aus.

![BizTalk Flat File Encoder Aktionsliste][4]

####Flatfile in XML

![BizTalk Flat File Encoder Aktionsliste][5]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Flatfile</td>
		<td>string</td>
		<td>Inhalt der Eingabeflatfile</td>
	</tr>
	<tr>
		<td>Name des Schemas</td>
		<td>string</td>
		<td>Name des Schemas, das die Eingabeflatfile darstellt</td>
	</tr>
	<tr>
		<td>Stammname</td>
		<td>string</td>
		<td>Der Stammknotenname des Flatfileschemas</td>
	</tr>
</table>


Die Aktion gibt die Ausgabe als Zeichenfolge zurück - Ausgabe-XML. Ausgabe-XML enthält die XML-Darstellung des Inhalts der Eingabeflatfile.

####XML in Flatfile

![BizTalk Flat File Encoder Aktionsliste][6]

<table>
	<tr>
		<th>Parameter</th>
		<th>Typ</th>
		<th>Beschreibung des Parameters</th>
	</tr>
	<tr>
		<td>Eingabe-XML</td>
		<td>string</td>
		<td>Eingabe-XML-Inhalt</td>
	</tr>
</table>

Die Aktion gibt die Ausgabe als Zeichenfolge zurück - Flatfile. Ausgabe enthält die Flatfiledarstellung des Eingabe-XML-Inhalts.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
 

<!---HONumber=July15_HO3-->