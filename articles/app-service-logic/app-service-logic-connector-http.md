<properties
   pageTitle="Verwendung des HTTP-Listeners und -Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Connectors für HTTP-Listener und -Aktionen oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="prkumar"/>


# Erste Schritte mit HTTP-Listenern und HTTP-Aktionen und das Hinzufügen zur Logik-App
Stellen Sie eine direkte Verbindung mit HTTP-Ressourcen her, um HTTP-Anforderungen zu überwachen und HTTP-Webanfragen zu konfigurieren. Es gibt einige Szenarios, in denen Sie möglicherweise mit direkten HTTP-Verbindungen arbeiten müssen, einschließlich:

1.	Zum Entwickeln einer Logik-App, die ein interaktives Front-End für Web- oder mobile Benutzer unterstützt.
2.	Zum Abrufen und Verarbeiten von Daten von einem Webdienst, der keinen vorgefertigten Connector aufweist.
3.	Zum Ausführen von Aktionen, die bereits als Webdienst verfügbar gemacht wurden, aber nicht als API-App verfügbar sind.

Für diese Szenarios stehen zwei Optionen zur Verfügung:

1. **HTTP-Listener**: Dieser Connector fungiert als Trigger und lauscht auf HTTP-Anforderungen an einem konfigurierten Endpunkt. Wenn am konfigurierten Endpunkt ein Aufruf empfangen wird, wird eine neue Instanz des Datenflusses ausgelöst, und die in der Anforderung empfangenen Daten werden zur Verarbeitung an den Datenfluss weitergegeben. Er kann auch so konfiguriert werden, dass automatisch auf die eingehende Anforderung geantwortet wird, wenn der Datenfluss gestartet wurde, oder Sie können eine Antwort auf Grundlage der Datenflussausführung erstellen und eine Antwort an den Aufrufer senden.
2. **HTTP-Aktion**: Diese ermöglicht Ihnen das Konfigurieren einer Webanforderung an jeden verfügbaren Endpunkt im Internet, erhält eine Antwort zurück und stellt diese für weitere Aktionen im Datenfluss zur Nutzung zur Verfügung.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den HTTP-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Erstellen eines HTTP-Listeners für Ihre Logik-App
Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "HTTP", wählen Sie den HTTP-Listener, und wählen Sie dann **Erstellen** aus.
3.	Konfigurieren Sie den HTTP-Listener wie folgt: ![][1]

4.	Beim Einrichten der Paketeinstellungen wird Ihnen die folgende Option angezeigt, mit der Sie festlegen, ob der Listener automatisch reagieren oder Sie zum Senden einer expliziten Antwort auffordern soll. Legen Sie diesen Wert auf **False** fest, um eine eigene Antwort zu senden: ![][2]

5.	Klicken Sie zum Erstellen auf **OK**.
6.	Sobald die API-App-Instanz erstellt wurde, öffnen Sie die Einstellungen zum Konfigurieren der Sicherheit. Der HTTP-Listener unterstützt derzeit die Standardauthentifizierung. Sie können dies über die Sicherheitsoption beim Öffnen des HTTP-Listeners konfigurieren: ![][3]
  
	**Bekanntes Problem** *Die Sicherheitseinstellungen zeigen "Kein" als Standardwert an, der jedoch nicht definiert ist. Sie müssen die Einstellung vor dem Speichern in "Basic" und dann wieder in "Kein" ändern, um sicherzustellen, dass der HTTP-Listener ordnungsgemäß konfiguriert ist.*

7. Abschließend legen Sie die Sicherheitseinstellungen der API-App auf "Öffentlich (Anonym)" fest, um externen Clients den Zugriff auf den Endpunkt zu erlauben. Sie finden diese Einstellung unter "Alle Einstellungen > Anwendungseinstellungen" der HTTP-Listener-API-App: ![][10]

Sobald Sie fertig sind, können Sie jetzt eine Logik-App zur Verwendung des HTTP-Listeners erstellen.

## Verwenden des HTTP-Listeners in Ihrer Logik-App
Sobald Ihre API-App erstellt wurde, können Sie jetzt den HTTP-Listener als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

4.	Erstellen Sie eine neue Logik-App.
5.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. Der HTTP-Listener wird im Katalog aufgeführt. Wählen Sie dies aus.
6.	Sie können jetzt die HTTP-Methode und die relative URL festlegen, an der der Listener zum Auslösen des Datenflusses erforderlich ist: ![][4] ![][5]

7.	Zum Abrufen des vollständigen URI doppelklicken Sie auf den HTTP-Listener, um die Konfigurationseinstellungen anzuzeigen, und kopieren Sie die URL für den "Host" der API-App: ![][6]
8.	Sie können nun die in der HTTP-Anforderung empfangenen Daten wie folgt in anderen Aktionen im Datenfluss verwenden: ![][7] ![][8]
9.	Um schließlich eine Antwort zu senden, fügen Sie einen weiteren HTTP-Listener hinzu, und wählen Sie die Aktion zum Senden der HTTP-Antwort aus. Legen Sie die Anforderungs-ID auf die vom HTTP-Listener erhaltene "RequestID" fest, und füllen Sie den Antworttext und den HTTP-Status auf, der zurückgegeben werden soll: ![][9]

## Verwenden der HTTP-Aktion
Die HTTP-Aktion wird systemeigen von Logik-Apps unterstützt. Zu ihrer Verwendung ist es nicht erforderlich, zuerst eine API-App zu erstellen. Sie können eine HTTP-Aktion an einem beliebigen Punkt in Ihrer Logik-App einfügen und den URI, die Header und den Text für den Aufruf auswählen. Die HTTP-Aktion unterstützt mehrere Optionen für die clientseitige Sicherheit. Informationen zu deren Verwendung finden Sie in [diesem](http://aka.ms/logicapphttpauth) Artikel.

Die Ausgabe der HTTP-Aktion besteht aus Headern und Text und kann in ähnlicher Weise wie die Ausgabe anderer Aktionen und Connectors später im Datenfluss herangezogen werden.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=Oct15_HO3-->