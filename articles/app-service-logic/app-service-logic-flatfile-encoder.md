<properties 
   pageTitle="Verwenden des BizTalk Flat File Encoders in einer Logik-App| Microsoft Azure" 
   description="BizTalk Flat File Encoder-API-App oder -Connector" 
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
   ms.date="01/19/2016"
   ms.author="rajram"/>

# BizTalk Flat File Encoder

Verwenden Sie den BizTalk Flat File Encoder-Connector für die Interoperation zwischen Flatfile-Daten (z. B. einer Excel- oder CSV-Datei) und XML-Daten. Sie können damit eine Flatfile-Instanz in XML konvertieren und umgekehrt.

##Verwenden des BizTalk Flat File Encoder
Um den BizTalk Flat File Encoder zu verwenden, müssen Sie zunächst eine Instanz der BizTalk Flat File Encoder-API-App erstellen. Dies kann entweder Inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk Flat File-Encoder-API-App aus dem Azure Marketplace erfolgen. Hier werden die Schritte zum Erstellen einer BizTalk Flat File Encoder-API-App aus dem Azure Marketplace beschrieben: 1. Melden Sie sich beim Azure-Portal an (http://portal.azure.com). 2. Wählen Sie „Neu > Marketplace > Alles“. 3. Suchen Sie im Suchfeld nach „BizTalk Flat File Encoder“. 4. Wählen Sie aus der Liste der Ergebnisse „BizTalk Flat File Encoder“. 5. Wählen Sie „Erstellen“, und geben Sie einen Namen und die restlichen erforderlichen Informationen ein. 6. Wählen Sie „Erstellen“. Sie werden zur Startseite umgeleitet, wo Sie den Status der Erstellung sehen können. Dies kann eine Weile dauern. Sie erhalten eine Benachrichtigung, wenn dies abgeschlossen ist.

###Konfigurieren des BizTalk Flat File Encoder
Nachdem die API-App erstellt wurde, können Sie sie entweder direkt von der Azure-Portal-Startseite oder der Designeroberfläche aus starten, wenn Sie eine Logik-App erstellen.

Zum Starten von der Azure-Startseite aus können Sie sie suchen, indem Sie den Namen eingeben, den Sie beim Erstellen dem BizTalk Flat File Encoder zugewiesen haben. Gehen Sie wie folgt vor: 1. Geben Sie den Namen Ihres BizTalk Flat File Encoder im Azure-Portal in das Suchfeld ein, und suchen Sie danach. 2. Wählen Sie dann Ihren BizTalk Flat File Encoder in der Liste aus. Dies öffnet das API-App-Blatt, in dem Sie Ihre BizTalk Flat File-Encoder-API-App konfigurieren können. Um die Konfiguration zu starten, fügen Sie folgendermaßen ein Schema hinzu: 1. Wählen Sie die Komponente „Schemas“ aus. ![BizTalk Flat File Encoder Schemas Teil][2] 2. Wählen Sie dann „Neues Element hinzufügen“ im Blatt „Schemas“. ![BizTalk Flat File Encoder Aktionsliste][7] 3. Wählen Sie eine der drei Optionen, um Ihr Schema bereitzustellen. Die Optionen sind NEUES SCHEMA HOCHLADEN, AUS JSON GENERIEREN und AUS FLATFILE GENERIEREN. ![BizTalk Flat File Encoder Aktionsliste][8] 4. Befolgen Sie die Schritte, um Ihr Schema basierend auf Ihrer Auswahl im vorherigen Schritt anzugeben. Dann sehen Sie, dass das Schema hochgeladen wurde: ![BizTalk Flat File Encoder Aktionsliste][9]

###Verwenden des BizTalk Flat File Encoder in der Entwurfsoberfläche
Da Sie nun den BizTalk Flat File-Encoder konfiguriert haben, können Sie ihn in einer Logik-App verwenden. Erstellen Sie zum Einstieg entweder eine neue Logik-App, oder starten Sie eine vorhandene, die Sie früher erstellt haben. Gehen Sie dann folgendermaßen vor: 1. Klicken Sie in der Karte „Logik starten“ auf „Diese Logik manuell ausführen“. 2. Wählen Sie die bereits erstellte BizTalk Flat File Encoder-API-App im Katalog aus. (Sie finden den von Ihnen erstellten BizTalk Flat File Encoder in der Liste mit den API-Apps rechts auf dem Bildschirm.) 3. Wählen Sie den schwarzen Pfeil, der nach rechts zeigt. Die zwei verfügbaren Aktionen („Flatfile in XML“ und „XML in Flatfile“) werden angezeigt: ![BizTalk Flat File Encoder Aktionsliste][1] ![BizTalk Flat File Encoder Aktionsliste][4]

Befolgen Sie die folgenden Schritte gemäß der Aktion, die Sie ausgewählt haben.

####Flatfile in XML

![BizTalk Flat File Encoder Aktionsliste][5]

Parameter|Typ|Beschreibung des Parameters
---|---|---
Flatfile|string|Inhalt der Eingabeflatfile
Name des Schemas|string|Name des Schemas, das die Eingabeflatfile darstellt
Stammname|string|Der Stammknotenname des Flatfileschemas


Die Aktion gibt die Ausgabe als Zeichenfolge zurück - Ausgabe-XML. Ausgabe-XML enthält die XML-Darstellung des Inhalts der Eingabeflatfile.

####XML in Flatfile

![BizTalk Flat File Encoder Aktionsliste][6]

Parameter|Typ|Beschreibung des Parameters
---|---|---
Eingabe-XML|string|Eingabe-XML-Inhalt

Die Aktion gibt die Ausgabe als Zeichenfolge zurück - Flatfile. Ausgabe enthält die Flatfiledarstellung des Eingabe-XML-Inhalts.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG

 

<!---HONumber=AcomDC_0121_2016-->