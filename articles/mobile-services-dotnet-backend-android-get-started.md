<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Mobile Services
==================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "Windows Store C#") [Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "Windows Store JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")

[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-android-get-started/ "JavaScript-Backend")

In diesem Lernprogramm erfahren Sie, wie Sie einen Cloud-basierten Backend-Service mithilfe von Azure Mobile Services zu einer Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der mobile Dienst, den Sie erstellen, verwendet die unterstützten .NET-Sprachen mithilfe von Visual Studio für die serverseitige Geschäftslogik und die Verwaltung des mobilen Dienstes. Informationen zum Erstellen eines mobilen Dienstes, mit dem Sie serverseitige Geschäftslogik in JavaScript verfassen können, finden Sie in der [JavaScript-Backend-Version](/en-us/documentation/articles/mobile-services-android-get-started/) dieses Themas.

Unten finden Sie einen Screenshot aus der fertigen App:

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

Sie benötigen die [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125), um dieses Lernprogramm abzuschließen. Diese umfassen die integrierte Entwicklungsumgebung (IDE) von Eclipse, das Android Developer Tools (ADT)-Plug-In und die neueste Android-Plattform. Android 4.2 oder eine höhere Version ist erforderlich.

Das heruntergeladene Schnellstartprojekt enthält den Mobile Services SDK für Android. Dieses Projekt erfordert Android 4.2 oder eine höhere Version. Der Mobile Services SDK benötigt jedoch nur Android 2.2 oder eine höhere Version.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie hier [Kostenloses Azure-Testkonto](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28).

Erstellen eines neuen mobilen Dienstes
--------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Herunterladen des mobilen Dienstes auf den lokalen Computer
-----------------------------------------------------------

Da Sie nun den mobilen Dienst erstellt haben, können Sie Ihr personalisiertes Mobildienstprojekt herunterladen und auf Ihrem lokalen oder auch einem virtuellen Computer ausführen.

1.  Klicken Sie auf den mobilen Dienst, den Sie gerade erstellt haben, und dann in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.

2.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) oder eine neuere Version herunter, und installieren Sie diese.

3.  Klicken Sie auf **Herunterladen** unter **Service in der Cloud herunterladen und veröffentlichen**.

    Hierdurch wird das Visual Studio-Projekt heruntergeladen, das Ihren mobilen Dienst bereitstellt. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

4.  Laden Sie außerdem Ihr Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf Ihrem lokalen Computer, und merken Sie sich, wo Sie sie gespeichert haben.

Testen des mobilen Dienstes
---------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

Veröffentlichen Ihres mobilen Dienstes
--------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

Erstellen einer neuen Android-App
---------------------------------

In diesem Abschnitt erstellen Sie eine neue Android-App, die mit Ihrem Mobile Service verbunden ist.

1.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Mobile Dienste** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.

3.  Wenn Sie dies nicht bereits getan haben, laden Sie die [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125) auf Ihren lokalen oder virtuellen Computer herunter, und installieren Sie diese.

4.  Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.

5.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

Das Projekt für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

Ausführen Ihrer Android-App
---------------------------

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.  Navigieren Sie zum Speicherort der komprimierten Projektdateien, und entpacken Sie die Dateien auf Ihrem Computer.

2.  Klicken Sie in Eclipse auf **Datei**, dann auf **Importieren**, und erweitern Sie dann **Android**. Klicken Sie auf **Existing Android Code into Workspace** und dann auf **Weiter**.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png)

1.  Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der entpackten Projektdateien, klicken Sie auf **OK**, stellen Sie sicher, dass das TodoActivity-Projekt ausgewählt ist, und klicken Sie dann auf **Fertig stellen**.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png)

    Daraufhin werden die Projektdateien in den aktuellen Arbeitsbereich importiert.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png)

1.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

    **Hinweis**

    Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

2.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Lernprogramm beenden*, und klicken Sie dann auf **Hinzufügen**.

	![][10]

	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

    **Hinweis**

    Sie können den Code überprüfen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der ToDoActivity.java-Datei.


<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png




[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/