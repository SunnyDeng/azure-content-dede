<properties 
   pageTitle="SAP-Connector" 
   description="Verwenden des SAP-Connectors" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="hariag"/>


# SAP-Connector #

Connectors können in Logik-Apps als Teil eines Datenflusses verwendet werden, um Daten im Rahmen eines Datenflusses abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. In manchen Szenarien müssen Sie möglicherweise mit einem SAP-System arbeiten, das lokal und hinter der Firewall installiert ist. Mithilfe des SAP-Connectors im Datenfluss können Sie eine Vielzahl von Szenarien erzielen. Einige Beispiele:

1.	Machen Sie einen Teil der Daten in Ihrem SAP-System über ein Front-End für Web- oder mobile Benutzer verfügbar.
2.	Veröffentlichen von Daten an SAP nach der Verarbeitung
3.	Extrahieren von Daten aus SAP zur Verwendung in einem Geschäftsprozess

Für diese Szenarien muss Folgendes ausgeführt werden:

1. Erstellen einer Instanz der SAP-Connector-API-App
2. Herstellen von Hybridkonnektivität für die API-App zur Kommunikation mit dem lokalen SAP-System
3. Verwenden der erstellten API-App in einer Logik-App zum Erreichen des gewünschten Geschäftsprozesses

## Erstellen einer Instanz der SAP-Connector-API-App ##

Zur Verwendung des SAP-Connectors müssen Sie zunächst eine Instanz der SAP-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2. Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SAP-Connector".
3. Konfigurieren Sie ihn wie folgt:
	1. Geben Sie die generischen Details wie Name, App Service-Plan usw. auf dem ersten Blatt an.
	2. Geben Sie im Rahmen der Paketeinstellungen die SAP-Anmeldeinformationen ein. Geben Sie außerdem eine Azure Service Bus-Verbindungszeichenfolge an. Diese wird verwendet, um die Hybridkonnektivität mit Ihrem lokalen SAP-System herzustellen. 
	3. RFCs, TRFCs, BAPIs und IDOCs müssen den Anforderungen des Szenarios entsprechend konfiguriert werden. Wenn mehrere Werte bereitgestellt werden müssen, können diese durch Kommas getrennt werden.

![][1]

## Konfigurieren der soeben erstellten SAP-Connector-API-App ##

Navigieren Sie zu der soeben erstellten API-App über "Durchsuchen" -> "API-Apps" -> <Name of the API App just created>. Sie stellen das folgende Verhalten fest. Das Setup ist unvollständig, da die Hybridverbindung noch nicht hergestellt wurde.

![][2]

Gehen Sie zum Einrichten der Hybridkonnektivität folgendermaßen vor:

1. Kopieren Sie die primäre Verbindungszeichenfolge.
2. Klicken Sie auf den Link "Herunterladen und konfigurieren".
3. Führen Sie den gestarteten Installationsvorgang durch, und geben Sie nach Aufforderung die primäre Verbindungszeichenfolge an.
4. Sobald der Installationsvorgang abgeschlossen ist, wird ein Dialogfeld wie das Folgende angezeigt.

![][3]

Wenn Sie jetzt erneut zur erstellten API-App wechseln, stellen Sie fest, dass der Hybridverbindungsstatus "Verbunden" lautet.

![][4]

Hinweis: Für den Fall, dass Sie zur sekundären Verbindungszeichenfolge wechseln möchten, führen Sie die Hybrideinrichtung einfach erneut durch, und geben Sie anstelle der primären Verbindungszeichenfolge die sekundäre Verbindungszeichenfolge an.

## Verwendung in einer Logik-App ##

Der SAP-Connector kann nur in einer Logik-App als Aktion/Schritt verwendet werden.

Beim Erstellen/Bearbeiten einer Logik-App wählen Sie die oben erstellte SAP-Connector-API-App. Dadurch werden alle zulässigen Aktionen aufgeführt, die zur Auswahl stehen.

![][5]

Nach Auswahl einer Aktion werden die Eingabeparameter für diesen Vorgang aufgelistet. Geben Sie die entsprechenden Werte ein, und klicken Sie auf das Häkchensymbol.

![][6]

Der Schritt/die Aktion wird nun als in der Logik-App konfiguriert angezeigt. Die Ausgaben des Vorgangs werden angezeigt und können in einem nachfolgenden Schritt als Eingaben verwendet werden.

![][7]

Vervollständigen Sie die Logik-App, um den Geschäftsprozess zu definieren, und führen Sie sie dann aus, um den beabsichtigten Zweck zu erreichen.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->