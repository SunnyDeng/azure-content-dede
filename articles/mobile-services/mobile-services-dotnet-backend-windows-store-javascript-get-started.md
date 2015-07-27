<properties
	pageTitle="Erste Schritte mit Mobile Services für Windows Store-Apps | Mobile Dev Center"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die Windows Store-Entwicklung in C#, VB oder JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="glenga"/>


# <a name="getting-started"></a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm wird gezeigt, wie Sie einer universellen Windows-App einen cloudbasierten Back-End-Dienst unter Verwendung von Azure Mobile Services hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App in HTML und JavaScript, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Version dieses Themas.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Dazu ist eine kostenlose Testversion verfügbar.


## Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Erstellen einer neuen universellen Windows-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue universelle Windows-App, die sich mit dem mobilen Dienst verbindet.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue Windows Store-App erstellen**.

   	![][6]

   	Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App, die mit dem mobilen Dienst verbunden ist, angezeigt.

  	![][7]

3. Falls noch nicht geschehen, müssen Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> auf den lokalen oder virtuellen Computer herunterladen und installieren.

4. Wählen Sie unter **Download and run your app and service locally** eine Sprache für die Windows Store-App aus. Klicken Sie dann auf **Herunterladen**.

  	Daraufhin wird eine Lösung heruntergeladen, die Projekte für den mobilen Dienst und für die beispielhafte _To-Do-Listen_-Anwendung, die mit dem mobilen Dienst verbunden ist, enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.


## Testen der App mit dem lokalen mobilen Dienst

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]Sie können den Code, der zur Abfrage und zum Einfügen von Daten auf Ihren mobilen Dienst zugreift, in der Datei "default.js" finden und prüfen.

## Veröffentlichen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>Öffnen Sie im Projekt mit freigegebenem Code die Datei "default.js", suchen Sie den Code, durch den eine <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx" target="_blank">WindowsAzure.MobileServiceClient</a>-Instanz erstellt wird, kommentieren Sie den Code aus, der diesen Client mit <em>localhost</em> erstellt, und heben Sie die Auskommentierung für den Code auf, der den Client mithilfe der URL des mobilen Remotediensts erstellt und etwa folgendermaßen aussieht:</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>Der Client greift jetzt auf den in Azure veröffentlichten mobilen Dienst zu.</p></li>

<li><p>Drücken Sie die <strong>F5</strong>-Taste, um das Projekt neu zu erstellen und die App zu starten.</p></li>

<li><p>Geben Sie in der App einen beschreibenden Text ein, zum Beispiel <em>Lernprogramm abschließen</em>, unter <strong>TodoItem einfügen</strong>. Klicken Sie dann auf <strong>Speichern</strong>.</p>

<p>Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet.</p>
</li>
<li><p>(Optional) Ändern Sie in einer universellen Windows-Lösung das standardmäßige Startprojekt in die andere App, und drücken Sie erneut <strong>F5</strong>.</p>

	<p>Beachten Sie, dass nach dem Starten der App im vorhergehenden Schritt gespeicherte Daten aus dem mobilen Dienst geladen werden.</p></li>
</ol>

Weitere Informationen zu universellen Windows-Apps finden Sie unter [Unterstützen mehrerer Geräteplattformen aus einem einzigen Mobile Service heraus](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: ../mobile-services-dotnet-backend-windows-universal-javascript-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript version]: ../mobile-services-windows-store-get-started.md
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
 

<!---HONumber=July15_HO3-->