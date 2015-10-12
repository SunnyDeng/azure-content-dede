<properties
   pageTitle="Verwendung des SAP-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des SAP-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Erste Schritte mit dem SAP-Connector und das Hinzufügen zur Logik-App
Stellen Sie eine Verbindung mit dem lokalen SAP-System her, um RFC aufzurufen oder Metadaten abzurufen. In verschiedenen Szenarios müssen Sie möglicherweise mit einem SAP-System arbeiten, das lokal und hinter der Firewall installiert ist. Mithilfe des SAP-Connectors im Workflow können Sie eine Vielzahl von Szenarios realisieren. Einige Beispiele:

1.	Machen Sie einen Teil der Daten in Ihrem SAP-System über ein Front-End für Web- oder mobile Benutzer verfügbar.
2.	Veröffentlichen von Daten an SAP nach der Verarbeitung
3.	Extrahieren von Daten aus SAP zur Verwendung in einem Geschäftsprozess

Connectors können in Logik-Apps verwendet werden, um Daten im Rahmen eines Datenflusses abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Sie können den SAP-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.


Für diese Szenarios muss Folgendes ausgeführt werden:

1. Erstellen einer Instanz der SAP-Connector-API-App
2. Herstellen von Hybridkonnektivität für die API-App zur Kommunikation mit dem lokalen SAP-System
3. Verwenden der erstellten API-App in einer Logik-App zum Erreichen des gewünschten Geschäftsprozesses


## Erstellen einer Instanz der SAP-Connector-API-App ##

Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "SAP-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Konfigurieren Sie ihn wie folgt:
	1. Geben Sie die generischen Details wie Name, App Service-Plan usw. auf dem ersten Blatt an.

	2. Geben Sie im Rahmen der Paketeinstellungen die SAP-Anmeldeinformationen ein. Geben Sie außerdem eine Azure Service Bus-Verbindungszeichenfolge an. Diese wird verwendet, um die Hybridkonnektivität mit Ihrem lokalen SAP-System herzustellen.

	3. RFCs, TRFCs, BAPIs und IDOCs müssen den Anforderungen des Szenarios entsprechend konfiguriert werden. Wenn mehrere Werte bereitgestellt werden müssen, können diese durch Kommas getrennt werden.

![][1]

## Konfigurieren der soeben erstellten SAP-Connector-API-App ##

Navigieren Sie zu der soeben erstellten API-App über "Durchsuchen" -> "API-Apps" -> <Name of the API App just created>. Sie stellen das folgende Verhalten fest. Die Installation ist unvollständig, da die Hybridverbindung noch nicht hergestellt wurde: ![][2]

Der SAP-Connector erfordert eine Hybridkonnektivität zum Herstellen einer Verbindung mit einem *beliebigen* SAP-Endpunkt. So richten Sie die Hybridkonnektivität ein

1. Kopieren Sie die primäre Verbindungszeichenfolge.
2. Klicken Sie auf den Link "Herunterladen und konfigurieren".
3. Führen Sie den gestarteten Installationsvorgang durch, und geben Sie nach Aufforderung die primäre Verbindungszeichenfolge an.
4. Sobald der Installationsvorgang abgeschlossen ist, wird ein Dialogfeld wie das Folgende angezeigt: ![][3]

Wenn Sie jetzt erneut zur erstellten API-App wechseln, stellen Sie fest, dass der Hybridverbindungsstatus "Verbunden" lautet: ![][4]

Hinweis: Für den Fall, dass Sie zur sekundären Verbindungszeichenfolge wechseln möchten, führen Sie die Hybrideinrichtung einfach erneut durch, und geben Sie anstelle der primären Verbindungszeichenfolge die sekundäre Verbindungszeichenfolge an.

## Verwendung in einer Logik-App ##

Der SAP-Connector kann nur in einer Logik-App als Aktion/Schritt verwendet werden.

Beim Erstellen/Bearbeiten einer Logik-App wählen Sie die oben erstellte SAP-Connector-API-App. Dadurch werden alle zulässigen Aktionen aufgeführt, die zur Auswahl stehen: ![][5]

Nach Auswahl einer Aktion werden die Eingabeparameter für diesen Vorgang aufgelistet. Geben Sie die entsprechenden Werte ein, und klicken Sie auf das Häkchensymbol: ![][6]

Der Schritt/die Aktion wird nun in der Logik-App als konfiguriert angezeigt. Die Ausgaben des Vorgangs werden angezeigt und können in einem nachfolgenden Schritt als Eingaben verwendet werden: ![][7]

Vervollständigen Sie die Logik-App, um den Geschäftsprozess zu definieren, und führen Sie sie dann aus, um den beabsichtigten Zweck zu erreichen.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=Oct15_HO1-->