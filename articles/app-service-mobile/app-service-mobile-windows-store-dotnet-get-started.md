<properties
	pageTitle="Erstellen einer universellen Windows Runtime 8.1-App für mobile Azure App Service-Apps | Microsoft Azure"
	description="Dieses Tutorial enthält die ersten Schritte für die Verwendung mobiler Azure-App-Back-Ends für die Windows Store-Entwicklung in C#, Visual Basic oder JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="12/19/2015"
	ms.author="glenga"/>

#Erstellen einer Windows-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht

In diesem Tutorial wird gezeigt, wie Sie einer universellen Windows-App einen cloudbasierten Back-End-Dienst hinzufügen. Weitere Informationen finden Sie unter [Was sind Mobile Apps?](app-service-mobile-value-prop.md)

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] oder höher.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Azure App Service-App erstellen](https://tryappservice.azure.com/?appServiceName=mobile). Dort können Sie direkt eine kurzzeitige mobile Start-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

##Erstellen eines neuen Azure Mobile App-Back-Ends

Führen Sie die folgenden Schritte aus, um ein neues mobiles App-Back-End zu erstellen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Sie haben nun ein Azure-Back-End für mobile Apps bereitgestellt, das von Ihren mobilen Clientanwendungen verwendet werden kann. Als Nächstes laden Sie ein Serverprojekt für ein einfaches "Aufgabenlisten"-Back-End herunter und veröffentlichen es in Azure.

## Konfigurieren des Serverprojekts

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Herunterladen und Ausführen des Clientprojekts

Sobald Sie Ihr Back-End für mobile Apps konfiguriert haben, können Sie eine neue Client-App erstellen oder eine vorhandene App so ändern, dass eine Verbindung mit Azure hergestellt wird. In diesem Abschnitt laden Sie ein Vorlagenprojekt für eine universelle Windows-App herunter, das zum Herstellen einer Verbindung mit dem Back-End für mobile Apps angepasst wird.

1. Klicken Sie auf dem Blatt **Erste Schritte** für Ihr Back-End für mobile Apps auf **Neue App erstellen** > **Herunterladen**, und entpacken Sie die komprimierten Projektdateien auf Ihrem lokalen Computer.

3. (Optional) Fügen Sie das universelle Windows-App-Projekt der Projektmappe mit dem Serverprojekt hinzu. Dies erleichtert es, bei Bedarf sowohl die App als auch das Back-End in der gleichen Visual Studio-Projektmappe zu debuggen.

4. Drücken Sie bei einer Windows Store-App als Startprojekt F5, um das Projekt neu zu erstellen, und starten Sie die Windows Store-App.

5. Geben Sie in der App unter **TodoItem einfügen** einen beschreibenden Text ein, zum Beispiel *Abschließen des Tutorials*. Klicken Sie dann auf **Speichern**.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Dadurch wird eine POST-Anforderung an das neue in Azure gehostete mobile App-Back-End gesendet.

6. Um das Debuggen zu beenden, klicken Sie mit der rechten Maustaste auf das `<your app name>.WindowsPhone`-Projekt, klicken Sie auf **Als Startprojekt festlegen**, und drücken Sie dann erneut F5.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	Beachten Sie, dass nach dem Starten der Windows-App die im vorhergehenden Schritt gespeicherte Daten aus der mobilen App geladen werden.

##Nächste Schritte

* [Hinzufügen von Authentifizierung zur App](app-service-mobile-windows-store-dotnet-get-started-users.md) <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Hinzufügen von Pushbenachrichtigungen zur App](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>Erfahren Sie, wie Sie eine einfache Pushbenachrichtigung an Ihre App senden können.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1223_2015-->