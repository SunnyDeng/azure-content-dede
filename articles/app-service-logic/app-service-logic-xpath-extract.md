<properties
   pageTitle="Verwendung des BizTalk XPath Extractor in Logik-Apps in Azure App Service | Microsoft Azure"
   description="BizTalk XPath Extractor"
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
   ms.date="12/07/2015"
   ms.author="rajram"/>

#BizTalk XPath Extractor

Mit dem BizTalk XPath Extract-Connector kann Ihre App Daten aus dem XML-Inhalt suchen und basierend auf einem bestimmten XPath extrahieren.

## Verwenden von BizTalk XPath Extractor
1. Um den BizTalk Xpath Extractor zu verwenden, müssen Sie zunächst eine Instanz der BizTalk Xpath Extractor-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk Xpath Extractor-API-App aus dem Azure Marketplace erfolgen.

	>[AZURE.NOTE]Es sind keine Konfigurationseinstellungen für BizTalk Xpath Extractor vorhanden.
2. [Erstellen einer neuen Logik-App] Öffnen Sie In Ihrer Logik-Apps „Trigger und Aktionen“, um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.
3. Im Designer werden im rechten Bereich die verfügbaren API-Apps für das Erstellen des Datenflusses aufgeführt. Suchen Sie "BizTalk XPath Extractor". Durch diese Auswahl wird der XPath Extractor Ihrem Datenfluss hinzugefügt und eine Instanz davon bereitgestellt.
4. Nach der Bereitstellung zeigt der Designer die Aktion, die der BizTalk XPath Extractor-API-App zugeordnet sind: ![BizTalk XPath Extractor Aktion auswählen][1]

5. Wählen Sie „Extract Using XPath“ aus. „Extract Using XPath“ wertet den Eingabe-XPath-Ausdruck auf eine bestimmte Eingabe-XML aus: ![BizTalk XPath Extractor Eingabe][2]

	Parameter|Typ|Beschreibung des Parameters
---|---|---
XPath|Zeichenfolge|Abfragepfad in XML
Eingabe-XML|Zeichenfolge|Eingabe-XML-Inhalt

Die Aktion gibt die Ausgabe als Zeichenfolge zurück - Ergebnis. Das Ergebnis enthält den Wert des Abfragepfads in der XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Erstellen einer neuen Logik-App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_1210_2015-->